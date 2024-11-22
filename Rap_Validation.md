## Explaining

Kodun Açıklamaları
READ ENTITIES:
zi_rap_flight varlığından currency alanını okuyarak uçuş bilgilerini flights değişkenine atar.
Bu işlem, yalnızca ihtiyaç duyulan alanı alarak performansı artırır.

FIELD-SYMBOL Kullanımı:
Döngü içinde ASSIGNING FIELD-SYMBOL kullanılarak bellekten tasarruf edilir ve doğrudan flights üzerindeki verilere erişilir.

Özel Mesajlar:
zcm_rap_flight sınıfı kullanılarak özel bir istisna mesajı (msg) oluşturulur.
Bu mesaj, hatalı alanı ve mesajın türünü belirtir (severity-error).

Hata Alanı Belirleme:
%state_area alanında doğrulama kaynağını belirtmek için "VALIDATECURRENCY" kullanılmıştır.

## validateCurrency Method

This method validates the `currency` field of flight records. Only `USD` and `EUR` are accepted as valid currencies.
CONSTANTS:
  valid_currencies TYPE TABLE OF char3 WITH DEFAULT VALUE ('USD' 'EUR').

### Example
- Invalid currencies are flagged in the `failed-flight` and `reported-flight` tables.
- Error messages are generated using the `zcm_rap_flight` exception class.

validation validateCurrency on save { field currency; create; }


```abap
METHOD validateCurrency.

  " Ekrandaki değerleri al
  READ ENTITIES OF zi_rap_flight
    IN LOCAL MODE
    ENTITY flight
    FIELDS ( currency )
    WITH CORRESPONDING #( keys )
    RESULT DATA(flights).

  " Validasyon
  LOOP AT flights ASSIGNING FIELD-SYMBOL(<flight>).
    " Geçersiz para birimlerini kontrol et
    IF <flight>-currency <> 'USD' AND <flight>-currency <> 'EUR'.
      
      " Hata mesajını oluştur ve 'failed-flight' tablosuna ekle
      APPEND VALUE #(
        %tky = <flight>-%tky
      ) TO failed-flight.

      " Özel istisna mesajını oluştur
      DATA(msg) = NEW zcm_rap_flight(
        severity = if_abap_behv_message=>severity-error
        textid   = zcm_rap_flight=>field_invalid
        fieldname = 'CURRENCY'
      ).

      " Raporlanacak mesajı ekle
      APPEND VALUE #(
        %tky       = <flight>-%tky
        %state_area = 'VALIDATECURRENCY' " Doğrulama alanı
        %msg       = msg
      ) TO reported-flight.
    ENDIF.
  ENDLOOP.

ENDMETHOD.
