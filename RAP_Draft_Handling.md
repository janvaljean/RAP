#Behaviour Definition for draft table
```abap
managed implementation in class zbp_i_rap_activity unique;
#### with draft;

define behavior for ZI_RAP_ACTIVITY alias Activity
persistent table ZRAP_ACTIV

#### draft table ZRAP_ACTIVD
#### lock master total etag LastChangedAt
#### etag master LocalLastChangedAt

create;
update;
delete;

field ( numbering: managed, readonly ) ActivityGuid;

mapping for zrap_activ {
    ActivityDate = act_date;
    ActivityGuid = guid;
    ActivityTime = act_time;
    Consultant = bname;
    CreatedAt = created_at;
    CreatedBy = created_by;
    LastChangedAt = last_changed_at;
}

```
# ZI_RAP_ACTIVITY

```abap
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


