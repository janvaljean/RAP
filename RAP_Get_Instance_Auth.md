```abap
METHOD get_instance_authorizations.

  " Ekrandaki veriyi oku
  READ ENTITIES OF zi_rap_flight
    IN LOCAL MODE
    ENTITY Flight
    FIELDS ( status ) WITH CORRESPONDING #( keys )
    RESULT DATA(flights)
    FAILED failed.

  IF flights IS INITIAL.
    RETURN.
  ENDIF.

  " Veritabanından önceki halini oku
  SELECT FROM sflight
    FIELDS carrid, connid, fldate, seatsmax_b AS status
    FOR ALL ENTRIES IN @flights
    WHERE carrid = @flights-carrid AND
          connid = @flights-connid AND
          fldate = @flights-fldate
    ORDER BY primary key
    INTO TABLE @DATA(flights_db).

  " Kullanıcı ne yapmak istedi?
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

    IF guncelleme_var = abap_true.
      IF onceden_var = abap_true.
        " TODO: authority-check 02
        DATA(guncelleyebilir) = abap_true.
      ELSE.
        " TODO: authority-check 01
        guncelleyebilir = abap_true.
      ENDIF.
    ENDIF.

    IF guncelleyebilir = abap_false.
      APPEND VALUE #(
        %tky = <flight>-%tky
        %fail-cause = if_abap_behv=>cause-unauthorized
      ) TO failed-flight.

      APPEND VALUE #(
        %tky = <flight>-%tky
        %msg = NEW zcm_rap_flight(
          severity = if_abap_behv_message=>severity-error
          textid = zcm_rap_flight=>no_auth
        )
      ) TO reported-flight.
    ENDIF.

    IF silme_var = abap_true.
      " TODO: Delete logic
    ENDIF.

  ENDLOOP.

ENDMETHOD.

