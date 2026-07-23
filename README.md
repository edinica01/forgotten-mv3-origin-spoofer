# Forgotten Promo Connector (Chromium)

Крошечное расширение. Делает **ровно одно**: выставляет заголовок
`Origin: https://nekto.me` на запросах к `*.nekto.me` (включая WebSocket-хендшейк).

Без этого голосовой мост forgotten-promo не находит собеседника («вечный поиск») —
браузер шлёт `Origin` вашего домена, а nekto ждёт свой.

## Механизм
`declarativeNetRequest`, одно правило. Ключевое (на чём все спотыкаются):
- условие через **`regexFilter`** (`nekto\.me`) — `urlFilter "||"` НЕ матчит `wss://`;
- **`resourceTypes` явно перечислены, включая `websocket`** (иначе WS выпадает);
- в `host_permissions` есть **`wss://*.nekto.me/*`** — `*://` не покрывает `wss`.

## Что оно НЕ делает
Не читает данные, не ходит в сеть, не трогает другие сайты (только `*.nekto.me`).
Весь код — `manifest.json` + `rules.json`. Читается за минуту.

## Установка (Chrome / любой Chromium)
1. Скачать папку, распаковать.
2. `chrome://extensions` → включить **Developer mode** → **Load unpacked** → выбрать папку.
3. Убедиться, что на карточке нет ошибок (правило валидно).
4. **⚠️ Если сидите в инкогнито** — открыть **Details / Подробнее** у расширения и включить
   **«Allow in Incognito» / «Разрешить в режиме инкогнито»**. Иначе расширение молча не работает.
5. Открыть панель forgotten-promo, обновить (Ctrl+Shift+R), искать.

## Проверка
DevTools → Network → фильтр **Сокет/WS** → соединение `audio.nekto.me` / `audiochat.nekto.me` →
Request Headers → `Origin` = `https://nekto.me`. Дальше поиск находит собеседника
(если IP не в бане у nekto).
