```Abap
  METHOD validateAge.

     READ ENTITIES OF ZI_Team IN LOCAL MODE
        ENTITY Team
          FIELDS ( Age ) WITH CORRESPONDING #( keys )
        RESULT DATA(members).


    LOOP AT members INTO DATA(member).

      IF member-Age < 21.
        APPEND VALUE #( %tky = member-%tky ) TO failed-team.
      ENDIF.

    ENDLOOP.

  ENDMETHOD.
