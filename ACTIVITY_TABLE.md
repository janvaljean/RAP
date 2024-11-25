# Tabelle: Z_RAP_ACTIV

Die Tabelle `Z_RAP_ACTIV` speichert die Aktivitäten und zugehörigen Informationen. Hier ist die Beschreibung der Felder:

| Feldname                | Datenelement     | Datentyp | Länge | Beschreibung                     |
|-------------------------|------------------|----------|-------|-----------------------------------|
| **MANDT**               | MANDT           | CLNT     | 3     | Mandant                          |
| **GUID**                | SYSUUID_X16     | RAW      | 16    | Eindeutige GUID                  |
| **BNAME**               | XUBNAME         | CHAR     | 12    | Benutzername                     |
| **CARRID**              | S_CARR_ID       | CHAR     | 3     | Projekt-ID                       |
| **ACT_DATE**            | DATUM           | DATS     | 8     | Aktivitätsdatum                  |
| **ACT_TIME**            | TIMS            | TIMS     | 6     | Aktivitätszeit                   |
| **STEXT**               | STEXT           | CHAR     | 40    | Beschreibung der Aktivität       |
| **CREATED_BY**          | SYUNAME         | CHAR     | 12    | Erstellt von                     |
| **CREATED_AT**          | TIMESTAMPL      | DEC      | 21    | Erstellt am                      |
| **LAST_CHANGED_BY**     | SYUNAME         | CHAR     | 12    | Geändert von                     |
| **LAST_CHANGED_AT**     | TIMESTAMPL      | DEC      | 21    | Letzte Änderung                  |
| **LOCAL_LAST_CHANGED_AT** | TIMESTAMPL    | DEC      | 21    | Letzte lokale Änderung           |

## Beispiel für die View-Entity:

```ABAP
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Aktivitäten View'
define root view entity ZI_RAP_ACTIVITY
  as select from zrap_activ
  association [0..1] to ZI_RAP_CARRIER as _project on _project.ProjectId = $projection.Project
{
  key guid                      as ActivityGuid,
  bname                         as Consultant,
  carrid                        as Project,
  act_date                      as ActivityDate,
  act_time                      as ActivityTime,
  stext                         as WorkDone,
  @Semantics.user.createdBy: true
  created_by                    as CreatedBy,
  @Semantics.systemDate.createdAt: true
  created_at                    as CreatedAt,
  @Semantics.user.lastChangedBy: true
  last_changed_by               as LastChangedBy,
  @Semantics.systemDate.lastChangedAt: true
  last_changed_at               as LastChangedAt,
  local_last_changed_at         as LocalLastChangedAt,
  _project
}
