```abap
METHOD get_instance_features.

  " Button is appeared?
  READ ENTITIES OF zi_rap_flight
    IN LOCAL MODE
    ENTITY Flight
    FIELDS ( status )
    WITH CORRESPONDING #( keys )
    RESULT DATA(flights)
    FAILED failed.

  result = VALUE #(
    FOR _flight IN flights
    LET accept_enable = COND #( WHEN _flight-status = 1
                                THEN if_abap_behv=>fc-o-disabled
                                ELSE if_abap_behv=>fc-o-enabled )
        reject_enable = COND #( WHEN _flight-status <> 1
                                THEN if_abap_behv=>fc-o-disabled
                                ELSE if_abap_behv=>fc-o-enabled )
    IN #( %tky = _flight-%tky
          %action-acceptFlight = accept_enable
          %action-rejectFlight = reject_enable )
  ).

ENDMETHOD.
```
```abap
  METHOD get_instance_features.
     " Read the active flag of the existing members
    READ ENTITIES OF zi_team IN LOCAL MODE
        ENTITY Team
          FIELDS ( Active ) WITH CORRESPONDING #( keys )
        RESULT DATA(members)
        FAILED failed.

    result =
      VALUE #(
        FOR member IN members
      
            LET status =   COND #( WHEN member-Active = abap_true
                                      THEN if_abap_behv=>fc-o-disabled
                                      ELSE if_abap_behv=>fc-o-enabled  )

                                      IN


            ( %tky                 = member-%tky
              %action-setActive = status
             ) ).
  ENDMETHOD.
