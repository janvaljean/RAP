```
  " Determination Part of Behaviour Definition

behavior definition for zi_rap_flight alias Flight
persistent table zflight
lock master
{
  create;
  update;
  delete;

  association _booking { create; }

  field (readonly) seatsmax;
  field (mandatory) currency;

  action (features: instance) acceptFlight result [1] $self;
  action (features: instance) rejectFlight result [1] $self;

  determination setSeatsMax on save { create; }
  validation validateCurrency on save { field currency; create; }

  mapping for sflight {
    carrid    = carrid;
...
 ```
```abap
METHOD setSeatsMax.

  " Uçuş verilerini oku
  READ ENTITIES OF zi_rap_flight
    IN LOCAL MODE
    ENTITY flight
    FIELDS ( seatsmax )
    WITH CORRESPONDING #( keys )
    RESULT DATA(flights).

  " Seatsmax dolu ise ilgili kayıtları temizle
  DELETE flights WHERE seatsmax IS NOT INITIAL.

  IF flights IS INITIAL.
    RETURN. " Hiçbir uçuş verisi yoksa metottan çık
  ENDIF.

  " Boş "seatsmax" değerlerine yeni değerler yaz
  MODIFY ENTITIES OF zi_rap_flight
    IN LOCAL MODE
    ENTITY flight
    UPDATE FIELDS ( seatsmax )
    WITH VALUE #(
      FOR _flight IN flights
      ( %tky = _flight-%tky
        seatsmax = 99 ) " Örnek olarak tüm boş değerleri 99 ile dolduruyor
    )
    REPORTED DATA(flight_reported).

  " Güncellenmiş değerleri raporla
  reported = CORRESPONDING #( DEEP flight_reported ).

ENDMETHOD.
