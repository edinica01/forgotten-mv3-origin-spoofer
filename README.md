# forgotten-mv3-origin-spoofer

Крошечное расширение (Manifest V3, ~2 файла кода), которое делает **ровно одно**: ставит
заголовок `Origin: https://nekto.me` на соединениях к `*.nekto.me`, **включая WebSocket-хендшейк**.

Репозиторий: <https://github.com/edinica01/forgotten-mv3-origin-spoofer>

## Зачем
Голосовой мост forgotten-promo работает через зеркало страницы nekto на своём домене. Из-за
этого браузер шлёт на WebSocket `Origin` вашего домена, и nekto не находит собеседника
(«вечный поиск»). Chrome MV3 не даёт менять Origin обычными хедер-расширениями на большинстве
конфигураций — здесь это решено **корректным DNR-правилом**.

## Механизм (на чём все спотыкаются)
`declarativeNetRequest`, одно правило:
- условие через **`regexFilter`** (`nekto\.me`) — `urlFilter "||"` НЕ матчит `wss://`;
- **`resourceTypes` перечислены явно, включая `websocket`** — иначе WS выпадает из матча;
- в `host_permissions` есть **`wss://*.nekto.me/*`** — схема `*://` не покрывает `wss`.

## Что оно НЕ делает
Не ходит в сеть, не собирает данные, не трогает сторонние домены — только подмена одного
заголовка для `*.nekto.me`. Весь код — `manifest.json` + `rules.json`, читается за минуту.

## Установка (Chrome / любой Chromium)
1. Скачать/склонировать репозиторий.
2. `chrome://extensions` → включить **Developer mode** → **Load unpacked** → выбрать папку.
3. Убедиться, что на карточке нет ошибок (правило валидно).
4. **⚠️ Если сидите в инкогнито** — открыть **Details / Подробнее** и включить
   **«Allow in Incognito» / «Разрешить в режиме инкогнито»**. Иначе расширение молча не работает.
5. Открыть панель forgotten-promo, обновить (Ctrl+Shift+R), искать.

## Проверка
DevTools → Network → фильтр **Сокет/WS** → соединение `audio.nekto.me` / `audiochat.nekto.me` →
Request Headers → `Origin` = `https://nekto.me`. Дальше поиск находит собеседника
(если ваш IP не в бане у nekto).

## Лицензия
MIT — см. [LICENSE](LICENSE).
