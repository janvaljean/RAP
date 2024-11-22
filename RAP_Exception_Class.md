# ZCM_RAP_FLIGHT Exception Class

This class is designed for handling specific exceptions in the RAP model.
It supports T100 messages and RAP behavior messages.
if_abap_behv_message: RAP modeline özgü davranış mesajlarını destekler.
if_t100_dyn_msg ve if_t100_message: T100 mesajlarını dinamik ve sabit bir şekilde işlemek için kullanılıyor.

## Features
- Field validation errors
- Authorization errors
- Supports chaining exceptions

severity: Mesajın şiddet seviyesini belirtir (hata, uyarı, bilgi vb.).
textid: T100 mesaj anahtarını alır ve atanır.
fieldname: Geçersiz bir alan belirtmek için kullanılır.

## Example
```abap
TRY.
  RAISE EXCEPTION TYPE zcm_rap_flight
    EXPORTING
      textid = zcm_rap_flight=>field_invalid-msgid.
CATCH zcm_rap_flight INTO DATA(lx_exception).
  WRITE: lx_exception->fieldname.
ENDTRY.
```
## My Exteption Class 
```abap
CLASS zcm_rap_flight DEFINITION
  PUBLIC
  INHERITING FROM cx_static_check
  FINAL
  CREATE PUBLIC.

  PUBLIC SECTION.
    INTERFACES if_t100_dyn_msg. " Dinamik T100 mesajları
    INTERFACES if_t100_message. " T100 mesaj desteği
    INTERFACES if_abap_behv_message. " RAP davranış mesajları

    CONSTANTS:
      BEGIN OF field_invalid, " Alanın geçersiz olduğu hata bilgisi
        msgid TYPE symsgid VALUE 'ZKKM_RAP', " Mesaj ID
        msgno TYPE symsgno VALUE '000',     " Mesaj Numarası
        attr1 TYPE scx_attrname VALUE 'FIELDNAME', " Hatalı alanın adı
        attr2 TYPE scx_attrname VALUE '',         " Ek bilgi için boş
        attr3 TYPE scx_attrname VALUE '',         " Ek bilgi için boş
        attr4 TYPE scx_attrname VALUE ''          " Ek bilgi için boş
      END OF field_invalid.

    CONSTANTS:
      BEGIN OF no_auth, " Yetkilendirme hatası bilgisi
        msgid TYPE symsgid VALUE 'ZKKM_RAP', " Mesaj ID
        msgno TYPE symsgno VALUE '001',     " Mesaj Numarası
        attr1 TYPE scx_attrname VALUE '',   " Ek bilgi için boş
        attr2 TYPE scx_attrname VALUE '',   " Ek bilgi için boş
        attr3 TYPE scx_attrname VALUE '',   " Ek bilgi için boş
        attr4 TYPE scx_attrname VALUE ''    " Ek bilgi için boş
      END OF no_auth.

    DATA fieldname TYPE fieldname READ-ONLY. " Hatalı alan bilgisi

    METHODS constructor
      IMPORTING
        !severity TYPE if_abap_behv_message=>t_severity
          DEFAULT if_abap_behv_message=>severity-error " Varsayılan olarak 'Hata'
        !textid LIKE if_t100_message=>t100key OPTIONAL " T100 mesaj anahtarı
        !previous LIKE previous OPTIONAL              " Önceki istisna
        !fieldname TYPE fieldname OPTIONAL.           " Hatalı alan bilgisi

  PROTECTED SECTION.
  PRIVATE SECTION.
ENDCLASS.

CLASS zcm_rap_flight IMPLEMENTATION.

  METHOD constructor.
    " Üst sınıfın yapıcı metodunu çağır
    CALL METHOD super->constructor
      EXPORTING
        previous = previous.

    CLEAR me->textid.

    " Text ID kontrolü
    IF textid IS INITIAL.
      if_t100_message=>t100key = if_t100_message=>default_textid.
    ELSE.
      if_t100_message=>t100key = textid.
    ENDIF.

    " Alan bilgisi atanıyor
    me->fieldname = fieldname.
  ENDMETHOD.

ENDCLASS.
