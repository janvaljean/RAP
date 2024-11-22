## Get Instance Authorizations Method

This method handles authorization checks for flights in the RAP model. It retrieves data from the database and validates user actions based on their permissions.

### Features
- Reads entity data
- Validates update, delete, and custom actions
- Reports unauthorized actions with detailed error messages

### Example Usage
- Update or delete actions are validated using requested authorizations.
- Unauthorized actions are logged in `failed-flight` and `reported-flight`.

### Yetkilendirme Kontrolü:

- Kullanıcının yapmak istediği işlemler (update, delete, acceptFlight, rejectFlight) kontrol edilir.
- İzin durumları xsdbool kullanılarak true veya false olarak saklanır.

### Yetki Kontrol Döngüsü:

- Her bir uçuş kaydı için veritabanındaki eşleşen kayda göre yetki kontrolü yapılır.
- Eğer yetki yoksa, failed-flight ve reported-flight tablolarına hata detayları eklenir.

```abap
METHOD get_instance_authorizations.

  " Ekrandaki veriyi oku
  READ ENTITIES OF zi_rap_flight
    IN LOCAL MODE
    ENTITY flight
    FIELDS ( status )
    WITH CORRESPONDING #( keys )
    RESULT DATA(flights)
    FAILED failed.

  " Eğer veri yoksa metodu sonlandır
  IF flights IS INITIAL.
    RETURN.
  ENDIF.

  " Veritabanından önceki halini oku
  SELECT FROM sflight
    FIELDS carrid, connid, fldate, seatsmax_b AS status
    FOR ALL ENTRIES IN @flights
    WHERE carrid = @flights-carrid
      AND connid = @flights-connid
      AND fldate = @flights-fldate
    ORDER BY PRIMARY KEY
    INTO TABLE @DATA(flights_db).

  " Kullanıcının yapmak istediği işlemleri kontrol et
  DATA(guncelleme_var) = xsdbool(
      requested_authorizations-%update = if_abap_behv=>mk-on OR
      requested_authorizations-%delete = if_abap_behv=>mk-on OR
      requested_authorizations-%action-acceptFlight = if_abap_behv=>mk-on OR
      requested_authorizations-%action-rejectFlight = if_abap_behv=>mk-on
    ).

  DATA(silme_var) = xsdbool(
      requested_authorizations-%delete = if_abap_behv=>mk-on
    ).

  " Yetki kontrolü
  LOOP AT flights ASSIGNING FIELD-SYMBOL(<flight>).
    ASSIGN flights_db[ carrid = <flight>-carrid
                        connid = <flight>-connid
                        fldate = <flight>-fldate ]
      TO FIELD-SYMBOL(<flight_db>).

    DATA(onceden_var) = xsdbool( sy-subrc = 0 ).

    " Güncelleme kontrolü
    IF guncelleme_var = abap_true.
      IF onceden_var = abap_true.
        " Yetki kontrolü yapılacak (TODO)
        DATA(guncelleyebilir) = abap_true.
      ELSE.
        " Yeni bir yetki kontrolü yapılabilir (TODO)
        guncelleyebilir = abap_true.
      ENDIF.
    ENDIF.

    " Eğer yetki yoksa hata mesajını ekle
    IF guncelleyebilir = abap_false.
      APPEND VALUE #(
        %tky        = <flight>-%tky
        %fail-cause = if_abap_behv=>cause-unauthorized
      ) TO failed-flight.

      APPEND VALUE #(
        %tky = <flight>-%tky
        %msg = NEW zcm_rap_flight(
          severity = if_abap_behv_message=>severity-error
          textid   = zcm_rap_flight=>no_auth
        )
      ) TO reported-flight.
    ENDIF.
  ENDLOOP.

  " Silme işlemi kontrolü
  IF silme_var = abap_true.
    " Silme işlemi ile ilgili kodlar eklenecek (TODO)
  ENDIF.

ENDMETHOD.

