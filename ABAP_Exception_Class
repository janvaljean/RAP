# Güvenli Kayıt İstisna Sınıfı (ZCX_EC_GUVENLI_KAYIT)

Bu sınıf, güvenli bir şekilde kayıt işlemleri sırasında hata durumlarını ele almak için tasarlanmıştır.

## Özellikler
- T100 tablosu ile dinamik hata mesajları
- Derleme zamanında istisna kontrolü (`cx_static_check`)

## Kullanım
Örnek:
```abap
TRY.
  RAISE EXCEPTION TYPE zcx_ec_guvenli_kayit
    EXPORTING
      textid = 'KAYIT_ATILAMADI'.
CATCH zcx_ec_guvenli_kayit INTO DATA(lx_exception).
  " Hata mesajını işle
ENDTRY.
```abap

CLASS zcx_ec_guvenli_kayit DEFINITION
  PUBLIC
  INHERITING FROM cx_static_check
  CREATE PUBLIC.

  PUBLIC SECTION.
    INTERFACES if_t100_dyn_msg.
    INTERFACES if_t100_message.

    CONSTANTS:
      BEGIN OF kayit_atilmadi,
        msgid TYPE symsgid VALUE 'ZKKM', " Mesaj ID
        msgno TYPE symsgno VALUE '007',  " Mesaj Numarası
        attr1 TYPE scx_attrname VALUE '', " Ek nitelik 1
        attr2 TYPE scx_attrname VALUE '', " Ek nitelik 2
        attr3 TYPE scx_attrname VALUE '', " Ek nitelik 3
        attr4 TYPE scx_attrname VALUE ''  " Ek nitelik 4
      END OF kayit_atilmadi.

    METHODS constructor
      IMPORTING
        !textid LIKE if_t100_message=>t100key OPTIONAL " Hata mesajı anahtarı
        !previous LIKE previous OPTIONAL. " Önceki istisna

  PROTECTED SECTION.
  PRIVATE SECTION.
ENDCLASS.

CLASS zcx_ec_guvenli_kayit IMPLEMENTATION.

  METHOD constructor.
    " Üst sınıfın yapıcı metodunu çağır
    CALL METHOD super->constructor
      EXPORTING
        previous = previous.

    CLEAR me->textid.
  ENDMETHOD.

ENDCLASS.
