# ABAP Method: `CHECKBOOKINGS`

This ABAP method checks if a customer has active bookings in the database and reports errors accordingly.

# Explanation for ABAP Method: `CHECKBOOKINGS`

This document explains the functionality and logic behind the `CHECKBOOKINGS` method in ABAP.

## Purpose

The `CHECKBOOKINGS` method is used to validate if a specific customer has active bookings in the database. If bookings are found, the method reports errors and prevents further operations, such as deletion.

---

## Steps in the Code

### 1. Initialization

Two local variables are declared:
- **`LV_CUSTOMERID`**: Stores the customer ID retrieved from the input table.
- **`LV_ANZAHL`**: Stores the count of bookings for the given customer.

```abap
DATA :
  LV_CUSTOMERID TYPE S_CUSTOMER,
  LV_ANZAHL     TYPE I.


## Code

```abap
METHOD CHECKBOOKINGS.

  DATA :
    LV_CUSTOMERID TYPE S_CUSTOMER,
    LV_ANZAHL     TYPE I.

  " Read the first entry from the table KEYS
  READ TABLE KEYS INTO DATA(LV_KEY) INDEX 1.
  LV_CUSTOMERID = LV_KEY-(CL_ABAP_BEHV=>CO_TECHFIELD_NAME-TKY).

  " Query the database to count bookings for the given customer
  SELECT COUNT( * ) INTO @LV_ANZAHL FROM SBOOOK WHERE CUSTOMID = @LV_CUSTOMERID.

  " If bookings exist, process further
  IF LV_ANZAHL > 0.
    " Add the customer ID to the FAILED-CUSTOMER table
    APPEND VALUE #( CUSTOMER = LV_CUSTOMERID ) TO FAILED-CUSTOMER.

    " Add a new error message for the customer
    APPEND VALUE #(
      CUSTOMER = LV_CUSTOMERID
      %MSG = NEW_MESSAGE_WITH_TEXT(
        SEVERITY = MS-ERROR
        TEXT = |Löschen nicht möglich!| )
    ) TO REPORTED-CUSTOMER.

    " Add another message indicating bookings exist for this customer
    APPEND VALUE #(
      CUSTOMER = LV_CUSTOMERID
      %MSG = NEW_MESSAGE_WITH_TEXT(
        SEVERITY = MS-ERROR
        TEXT = |Buchungen für { LV_CUSTOMERID } vorhanden!| )
    ) TO REPORTED-CUSTOMER.
  ENDIF.

ENDMETHOD.
