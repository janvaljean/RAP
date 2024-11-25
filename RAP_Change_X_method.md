```Abap 
METHOD changeSalary.

     " Read relevant UXTeam instance data
    READ ENTITIES OF zi_team IN LOCAL MODE
      ENTITY Team
        FIELDS ( Role ) WITH CORRESPONDING #( keys )
      RESULT DATA(members).

    LOOP AT members INTO DATA(member).

      IF member-Role = 'UX Developer'.

        " Change salary to hard coded value
        MODIFY ENTITIES OF zi_team IN LOCAL MODE
        ENTITY Team
          UPDATE
            FIELDS ( Salary )
            WITH VALUE #(
                          ( %tky         = member-%tky
                            Salary = 7000
                            ) ).

      ENDIF.

      IF member-Role = 'UX Lead'.

        " Change salary to hard coded value
        MODIFY ENTITIES OF zi_team IN LOCAL MODE
        ENTITY Team
          UPDATE
            FIELDS ( Salary )
            WITH VALUE #(
                          ( %tky         = member-%tky
                            Salary = 9000
                            ) ).

      ENDIF.

    ENDLOOP.
  ENDMETHOD.
