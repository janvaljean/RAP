##### @ObjectModel.text.element: ['plane_producer'] --
 - @ObjectModel.text.element: Verweist auf Textfelder, die zusätzliche Informationen liefern (z. B. plane_producer für den Hersteller).
#####  @ObjectModel.semanticKey: ['planetype']  " Semantischer Schlüssel: Flugzeugtyp
 - Die Annotation @ObjectModel.semanticKey wird verwendet, um eine oder mehrere Spalten als semantischen Schlüssel zu markieren. Im Kontext dieser View könnte planetype beispielsweise verwendet werden, um den Typ des Flugzeugs (z. B. "A320", "B737") als eindeutige Kennung für die Flugzeugart zu definieren. 
```Abap
@EndUserText.label: 'Uçuş Projection View'  " Benutzerfreundliche Beschreibung
@AccessControl.authorizationCheck: #NOT_REQUIRED  " Autorisierungsprüfung deaktiviert
@Search.searchable: true  " Ermöglicht die Suche in der View
@Metadata.allowExtensions: true  " Erweiterungen auf Basis der View zulassen
@ObjectModel.semanticKey: ['planetype']  " Semantischer Schlüssel: Flugzeugtyp

define root view entity ZC_RAP_FLIGHT
  as projection on ZI_RAP_FLIGHT  " Projiziert auf die Basis-Entität ZI_RAP_FLIGHT
{
  @Search.defaultSearchElement: true
  @ObjectModel.text.element: ['cityfrom']  " Text-Beschreibung für connid
  key carrid,          " Fluggesellschaft-ID
  key connid,          " Verbindungs-ID
  key fldate,          " Flugdatum

  @Semantics.amount.currencyCode: 'currency'
  price,               " Flugpreis
  currency,            " Währungscode

 ##### @ObjectModel.text.element: ['plane_producer']
  planetype,           " Flugzeugtyp
  _airplane.producer as plane_producer,  " Hersteller des Flugzeugs

  seatsmax,            " Maximale Sitzplatzanzahl
  seatsocc,            " Belegte Sitzplätze

  @Semantics.amount.currencyCode: 'currency'
  paymentsum,          " Gesamte Zahlungssumme
  status,              " Status des Flugs (z. B. geplant, aktiv, abgesagt)

  /* Associations */
  _carrier.carrname,   " Fluggesellschaftsname
  _schedule.cityfrom,  " Startstadt aus dem Zeitplan

  /* Redirection to Composition Child */
  _booking redirected to composition child ZC_RAP_BOOKING,  " Weiterleitung an Buchungs-Entität

  _carrier,            " Assoziation zu Fluggesellschaft
  _schedule,           " Assoziation zu Zeitplan
  _airplane            " Assoziation zu Flugzeug
}
