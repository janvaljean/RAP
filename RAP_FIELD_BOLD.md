# RAP View: ZC_RAP_FLIGHT

Dieses Projekt enthält die Definition einer RAP-Projection-View für Fluginformationen.

## Inhalt

- **View Name:** `ZC_RAP_FLIGHT`
- **Basistabelle:** `ZI_RAP_FLIGHT`
- **Beschreibung:** Die View dient zur Darstellung von Fluginformationen, einschließlich Fluggesellschaft, Flugzeugtyp, Sitzplatzkapazität und Zahlungsdetails.

## View-Details

| Feldname      | Beschreibung                     |
|---------------|----------------------------------|
| `carrid`      | Fluggesellschaft-ID             |
| `connid`      | Verbindungs-ID                  |
| `fldate`      | Flugdatum                       |
| `price`       | Flugpreis                       |
| `currency`    | Währungscode                    |
| `planetype`   | Typ des Flugzeugs               |
| `seatsmax`    | Maximale Sitzplatzanzahl        |
| `seatsocc`    | Belegte Sitzplätze              |
| `paymentsum`  | Gesamte Zahlungssumme           |
| `status`      | Status des Flugs                |

## Eigenschaften
- **Suche:** Aktiviert für `carrid` und `connid`.
- **Erweiterungen:** Zulässig.
- **Semantischer Schlüssel:** `planetype`.

## Anforderungen
- ABAP Platform 2020 oder neuer
- RAP Kenntnisse für die Entwicklung

## Installation
1. Importiere den Code in dein SAP-System.
2. Aktiviere die View und verknüpfe sie mit der Basistabelle.
3. Führe Tests für die Abfragen durch.

```Abap
 @EndUserText.label: 'Uçuş Projection View'  " Benutzerfreundliche Beschreibung
 @AccessControl.authorizationCheck: #NOT_REQUIRED  " Autorisierungsprüfung deaktiviert
 @Search.searchable: true  " Ermöglicht die Suche in der View
@Metadata.allowExtensions: true  " Erweiterungen auf Basis der View zulassen
@ObjectModel.semanticKey: ['planetype']  " Hinweis: 'planetype' wird als semantischer Schlüssel verwendet, um die Flugzeugtyp-Information eindeutig zu identifizieren

define root view entity ZC_RAP_FLIGHT
  as projection on ZI_RAP_FLIGHT  " Projiziert auf die Basis-Entität ZI_RAP_FLIGHT
{
  @Search.defaultSearchElement: true  " Standard-Suchfeld
  @ObjectModel.text.element: ['carrname']  " Text-Beschreibung für carrid
  key carrid,  " Fluggesellschaft-ID

  @Search.defaultSearchElement: true
  @ObjectModel.text.element: ['cityfrom']  " Text-Beschreibung für conid
  key connid,  " Verbindungs-ID

  key fldate,  " Flugdatum

  @Semantics.amount.currencyCode: 'currency'  " Währung für den Preis definieren
  price,  " Flugpreis
  currency,  " Währungscode

  planetype,  " Flugzeugtyp

  seatsmax,  " Maximale Sitzplätze
  seatsocc,  " Belegte Sitzplätze

  @Semantics.amount.currencyCode: 'currency'  " Währung für Zahlungssumme definieren
  paymentsum,  " Gesamtsumme der Zahlungen

  status  " Flugstatus (z. B. geplant, aktiv, abgesagt)
}

