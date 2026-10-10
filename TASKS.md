# Статус

- Завершено: ресерч официальной документации и консервативный профиль Evolution; README/compat/skill/Registry/Docker/release/CI.
- Завершено: Docker build/vet/race/lint (0 issues), versitygw conformance, MCP smoke (все 8 сочетаний уровней), мутационные ворота 87/87 (100%) и self-tests, check_docs, actionlint, GoReleaser snapshot; образ uid 65532 запущен.
- Доставка завершена: main опубликован, описание/topics настроены; [CI реализации `234e56c`](https://github.com/stufently/cloudru-s3-mcp-ai-cli/actions/runs/38065022701) зелёный.
- Нужна живая проверка: эндпоинты/регионы, условные заголовки, классы/цены, presign, API и ошибки на реальном тестовом бакете с ключом владельца.
- Релиз v0.1.0 и публикация OCI/Registry отложены до живой проверки.

- **[предположение]**: атомарные условия записи/удаления, точная байтовая граница/коды квот и тариф аккаунта; профиль Evolution выбран из двух отдельных платформ Cloud.ru.
