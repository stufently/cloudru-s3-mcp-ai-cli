# Cloud.ru S3 compatibility

Generated from the embedded provider profile.

| Feature | Supported |
|---|---|
| acl | true |
| bucket_policy | true |
| bucket_tagging | true |
| conditional_delete | false |
| conditional_writes | false |
| cors | true |
| lifecycle | true |
| multipart | true |
| object_lock_config | true |
| object_retention | true |
| object_tagging | true |
| presign | true |
| versioning | true |

| Pool | Endpoint | Location | Multi-AZ | Classes |
|---|---|---|---|---|
| ru-central-1 | https://s3.cloud.ru | Москва, Evolution | true | STANDARD, COLD, ICE, SINGLE |

| Class | Name | RUB/GB/month (from) | Verified | Checked |
|---|---|---|---|---|
| STANDARD | Стандартное | 1.84 | false | 2026-10-10 |
| COLD | Холодное | 0.98 | false | 2026-10-10 |
| ICE | Ледяное | 0.49 | false | 2026-10-10 |
| SINGLE | Однозонное | 1.13 | false | 2026-10-10 |

Presign maximum: 604800 seconds; verified: false. Evolution Object Storage, до 7 дней. Защитные условия записи/удаления не подтверждены; SINGLE не Multi-AZ. Нужна живая проверка.
