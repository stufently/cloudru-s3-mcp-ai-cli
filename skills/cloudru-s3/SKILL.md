---
name: cloudru-s3
description: "Use when working with Cloud.ru Object Storage: Cloud.ru s3, бакет Cloud.ru, загрузи в s3, presigned, бэкап в s3. Для чтения, загрузки, обмена файлами и обслуживания бакетов Cloud.ru через MCP или CLI."
---

# Cloud.ru S3

Сначала прочитай `cloudru-s3://config` и `cloudru-s3://capabilities`: инструменты зависят от уровней read/write/delete/admin, возможностей провайдера и файлового корня. Если операция не зарегистрирована, объясни нужную настройку владельцу.

Для поиска используй `s3_list_buckets`, `s3_list_objects` с токеном продолжения, `s3_head_object`, `s3_preview_object`. Инлайн get/put ограничены 256 КиБ; preview — 64 КиБ. Передавай большие байты через presigned URL, multipart или файловые инструменты. Не помещай большие файлы в контекст модели.

Перед удалением, перемещением, перезаписью, PUT-ссылкой, изменением политики или Object Lock опиши человеку эффект и получи согласие. Следуй политике confirm/elicit/block/allow; `confirm:true` отражает уже полученное согласие. При отказе не меняй политику ради повторной попытки. По умолчанию запись не перезаписывает существующую цель. Если conditional_writes выключен, запись по умолчанию отказывает до S3-запроса: для записи требуется явный overwrite:true после согласия человека; HEAD не заменяет атомарную защиту. Если conditional_delete выключен, move недоступен. Проверяй скоуп и у источника, и у цели.

Не выводи ключи или подпись presigned URL в журналы. Креды задаёт владелец через `cloudru-s3 auth set`; MCP-конфиги не содержат секретов. Файловые инструменты работают только с относительными путями внутри FILE_ROOT.

Регион подписи и эндпоинт выбираются из профиля; пул по умолчанию — ru-central-1. Проверяй ресурс capabilities перед использованием versioning, CORS, политики, Object Lock или тегов. Официальная документация: https://cloud.ru/docs/s3e/ug/topics/api__getting-started https://cloud.ru/docs/s3e/ug/topics/api__methods https://cloud.ru/docs/s3e/ug/topics/api__headers-request https://cloud.ru/docs/s3e/ug/topics/api__aws-sig-v4 https://cloud.ru/docs/s3e/ug/topics/concepts__storage-class https://cloud.ru/docs/s3e/ug/topics/overview__limitations https://cloud.ru/docs/s3e/ug/topics/api__response-codes https://cloud.ru/documents/tariffs/evolution/object-storage. Цены и ограничения с verified:false требуют проверки; эмулятор не подтверждает совместимость облака.

Для соответствующего сценария прочитай:

- [Бэкап и восстановление](references/backup-restore.md).
- [Обмен файлом](references/share-file.md).
- [Минимальные права](references/least-privilege.md).
- [Объём и расходы](references/cost-hygiene.md).
- [Миграция](references/migration.md).
- [Инцидент](references/incident-response.md).
