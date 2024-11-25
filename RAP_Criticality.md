# RAP Flight View: ZC_RAP_FLIGHT

Dieses Projekt enthält eine RAP-View für Flüge mit Fokus auf die Preis-Kritikalität, basierend auf einem festgelegten Schwellenwert.

## Funktionen
- **Preis-Kritikalität:** Klassifiziert den Preis in drei Kategorien:
  - **1 (Rot):** Preis ist `0`.
  - **2 (Gelb):** Preis liegt unter `2000`.
  - **3 (Grün):** Preis ist `2000` oder höher.

## Felder
| Feldname          | Beschreibung                                   |
|--------------------|-----------------------------------------------|
| `carrid`          | Fluggesellschaft-ID                          |
| `connid`          | Verbindungs-ID                               |
| `fldate`          | Flugdatum                                    |
| `price`           | Flugpreis                                    |
| `currency`        | Währungscode                                 |
| `planetype`       | Typ des Flugzeugs                            |
| `seatsmax`        | Maximale Sitzplätze                          |
| `seatsocc`        | Belegte Sitzplätze                           |
| `paymentsum`      | Gesamtsumme der Zahlungen                    |
| `status`          | Status des Flugs                             |
| `price_criticality` | Klassifizierung der Preis-Kritikalität       |

## Preis-Kritikalität
Das Feld `price_criticality` zeigt die Kritikalität eines Flugpreises basierend auf vordefinierten Bedingungen:
- **Rot (1):** Kein Preis gesetzt.
- **Gelb (2):** Preis liegt unter `2000`.
- **Grün (3):** Preis liegt bei `2000` oder höher.

## Anforderungen
- SAP ABAP Platform >= 2020
- RAP-Modellierung aktiviert

## Installation
1. ZI_cds criticality
2. ZC_ criticality hinzufügen
3. zc_ meta data extention criticality hinzufügen
4. draft table diese field hinzufügen



## Beispiel
```sql
SELECT carrid, connid, fldate, price, price_criticality
  FROM ZC_RAP_FLIGHT
 WHERE price_criticality = 2;  -- Alle Flüge mit "gelber" Kritikalität


```Abap
@EndUserText.label: 'RAP Flight View with Criticality'  " Benutzerfreundliche Beschreibung
@AccessControl.authorizationCheck: #NOT_REQUIRED  " Autorisierungsprüfung deaktiviert
@Search.searchable: true  " Ermöglicht die Suche in der View
@Metadata.allowExtensions: true  " Erweiterungen auf Basis der View zulassen
@ObjectModel.semanticKey: ['planetype']  " Semantischer Schlüssel: Flugzeugtyp

define root view entity ZC_RAP_FLIGHT
  as projection on ZI_RAP_FLIGHT  " Projiziert auf die Basis-Entität ZI_RAP_FLIGHT
{
  key carrid,                " Fluggesellschaft-ID
  key connid,                " Verbindungs-ID
  key fldate,                " Flugdatum

  @Semantics.amount.currencyCode: 'currency'
  price,                     " Flugpreis
  currency,                  " Währungscode
  planetype,                 " Flugzeugtyp
  seatsmax,                  " Maximale Sitzplatzanzahl
  seatsocc,                  " Belegte Sitzplätze

  @Semantics.amount.currencyCode: 'currency'
  paymentsum,                " Gesamte Zahlungssumme

  seatsmax_b as status,      " Flugstatus

  /* Berechnung der Preis-Kritikalität */
  case 
    when price = 0 then 1    // Rot (kirmizi)
    when price < 2000 then 2 // Gelb (sari)
    else 3                   // Grün (yesil)
  end as price_criticality,  " Preis-Kritikalität (rot, gelb, grün)

  /* Assoziationen */
  _booking redirected to composition child ZC_RAP_BOOKING,
  _carrier.carrname,
  _schedule.cityfrom
}
```
## Metadata extention
```Abap
    identification: [{ position: 4, label: 'Price' }],  " Identifikation im UI
    lineItem       : [{ position: 10, criticality: 'price_criticality' }],  " Zeigt Preis-Kritikalität
    selectionField : [{ position: 10 }]  " Ermöglicht Selektion
  },
  price;
```
