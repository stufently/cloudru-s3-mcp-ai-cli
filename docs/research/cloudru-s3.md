# Cloud.ru Evolution S3 — официальный ресерч

Проверено 2026-10-10 только по официальной документации. **[предположение]** — непроверенные сведения/консервативные решения. Живой аккаунт не использовался; `verified:false`. Выбран **Evolution Object Storage**; отдельный Advanced OBS имеет другой контракт и в этот профиль не входит. [Evolution](https://cloud.ru/docs/s3e/ug/topics/api__getting-started), [Advanced OBS](https://cloud.ru/docs/obs/ug/index).

## Подключение и адресация

Endpoint `https://s3.cloud.ru`, SigV4 region `ru-central-1`. Access key имеет формат `<tenant_id>:<key_id>`, secret — пароль ключа; tenant берётся из настроек API аккаунта. При `auth set` вводите полный идентификатор, включая tenant и двоеточие. [Quickstart API](https://cloud.ru/docs/s3e/ug/topics/api__getting-started), [Signature V4](https://cloud.ru/docs/s3e/ug/topics/api__aws-sig-v4).

Path-style с базовым именем: `s3.cloud.ru/bucket`; path-style с глобальным именем: `global.s3.cloud.ru/global-name`; virtual-host: `dns-name.s3.cloud.ru`. Для virtual-host необходимо настроить доменное имя бакета, для анонимного доступа — глобальное или доменное имя. Профиль использует базовое имя и path-style. [Модели адресации](https://cloud.ru/docs/s3e/ug/topics/concepts__storage-access-styles).

## Классы и цены

Классы S3: `STANDARD`, `COLD`, `ICE`, `SINGLE`. Для первых трёх данные хранятся в двух копиях в разных зонах, SINGLE — одна копия в одной зоне. Дата-центры находятся в Москве. `multi_az:true` пула описывает стандартное хранение и **не распространяется на SINGLE**. [Классы](https://cloud.ru/docs/s3e/ug/topics/concepts__storage-class), [Действующее описание услуги](https://cloud.ru/documents/contracts/terms-of-service/evolution/object-storage/description-object-storage).

Тариф версии **260918**, действует с 28.09.2026: STANDARD сверх 15 ГБ — 1,83915 ₽/ГБ·месяц; COLD — 0,97905; ICE — 0,489525; SINGLE — 1,1346, с НДС. В профиле именно эти справочные ставки (`verified:false`). [Действующие тарифы](https://cloud.ru/documents/tariffs/evolution/object-storage), [Полная таблица PDF](https://cdn.cloud.ru/docs/legal/tariffs/evolution/current-version/object-storage.pdf).

Учитываются хранение, API-запросы и исходящий трафик; расчёт почасовой по максимальному занятому объёму в час. Холодные/ледяные объекты тарифицируются минимум как 128 КБ. Доступен free tier, включая 15 ГБ STANDARD, 10 ТБ исходящего трафика и указанные бесплатные операции. **[предположение]** льготы/цена конкретного аккаунта требуют проверки; профиль не рассчитывает счёт. [Тарификация](https://cloud.ru/docs/s3e/ug/topics/pricing).

## Условия и возможности API

| Возможность | Документация и решение | Источник |
|---|---|---|
| If-None-Match / If-Match | GET/HEAD и Copy-source условия описаны; для PUT/Complete и **целевого** Copy условие отсутствия не перечислено. **[предположение]** контракт записи не подтверждён, `conditional_writes:false` | [Заголовки](https://cloud.ru/docs/s3e/ug/topics/api__headers-request) |
| Условный Delete | If-Match перечислен, но его текст описывает возврат объекта; также перечислен Range с описанием HEAD. **[предположение]** атомарность не установлена, `conditional_delete:false` | [Заголовки](https://cloud.ru/docs/s3e/ug/topics/api__headers-request), [Delete](https://cloud.ru/docs/s3e/ug/topics/api__deleteobject) |
| Object и bucket tags | Get/Put/DeleteTagging для обоих ресурсов | [Методы](https://cloud.ru/docs/s3e/ug/topics/api__methods) |
| Lifecycle | Get/Put/Delete конфигурации | [Методы](https://cloud.ru/docs/s3e/ug/topics/api__methods) |
| ACL / policy | Bucket ACL и Get/Put/DeleteBucketPolicy | [Методы](https://cloud.ru/docs/s3e/ug/topics/api__methods) |
| Versioning | Get/PutBucketVersioning, ListObjectVersions | [Методы](https://cloud.ru/docs/s3e/ug/topics/api__methods) |
| Object Lock | Get/Put конфигурации, retention и legal hold | [Методы](https://cloud.ru/docs/s3e/ug/topics/api__methods) |
| CORS | Get/Put/DeleteBucketCors | [Методы](https://cloud.ru/docs/s3e/ug/topics/api__methods) |
| Multipart | Create/Upload/List/Complete/Abort, UploadPartCopy | [Методы](https://cloud.ru/docs/s3e/ug/topics/api__methods) |

Защита fail-closed: запись по умолчанию отказывает до запросов, согласованная перезапись требует `overwrite:true` и общего гейта; move отсутствует. Неясность Delete не мешает безопасному профилю с выключенной возможностью. Lifecycle, ACL, bucket tagging и retention отражены как API-возможности; текущий kit не предоставляет инструменты их настройки.

## Presign и ограничения

AWS Signature V4, `X-Amz-Expires` от 1 до **604800 секунд (7 дней)**. Нужна проверка GET/PUT и срока действия на живом облаке. [Подпись](https://cloud.ru/docs/s3e/ug/topics/api__aws-sig-v4).

По умолчанию 100 бакетов, объём не ограничен, изменяемые квоты на организацию/проект. Технические лимиты: 5 ГБ за PUT/Copy/UploadPart, объект до 5 ТБ; 10000 частей, минимум 5 МБ кроме последней; HTTP-заголовок 1 МБ, policy 64 КБ. На тенант RPS: GET/HEAD 1000, PUT/UploadPart 1000, листинги 1000, все операции 2000; read/write bandwidth по 8 Гбит/с. [Ограничения](https://cloud.ru/docs/s3e/ug/topics/overview__limitations).

**[предположение]** `max_put_bytes:5000000000` — консервативное десятичное представление 5 ГБ. Граница, XML-код для превышения PUT/числа бакетов и фактические квоты аккаунта требуют живого теста. [Ограничения](https://cloud.ru/docs/s3e/ug/topics/overview__limitations).

Превышение RPS — `429 TooManyRequests`; превышение квоты места — `403 QuotaExceeded`; превышение установленного размера бакета — `400 InvalidRequest`; неисполненное условие — `412 PreconditionFailed`, конфликт — `409 OperationAborted`, неподдерживаемый заголовок — `501 NotImplemented`. [Ответы](https://cloud.ru/docs/s3e/ug/topics/api__response-codes).

## Нужна живая проверка

Ключ владельца с полным tenant:key ID; endpoint/подпись; защитные PUT/Copy/Complete/Delete; классы/тарифы, presign, поддержка bucket API и реальные квоты/ошибки. Docker/versitygw не подтверждает контракт Cloud.ru Evolution.
