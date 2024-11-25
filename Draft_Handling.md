# Güvenli Kayıt İstisna Sınıfı (ZCX_EC_GUVENLI_KAYIT)
```abap
managed implementation in class zbp_i_rap_activity unique;
```
## with draft;
```abap
define behavior for ZI_RAP_ACTIVITY alias Activity
persistent table ZRAP_ACTIV
```
## draft table ZRAP_ACTIVD
## lock master total etag LastChangedAt
## etag master LocalLastChangedAt
```abap
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
