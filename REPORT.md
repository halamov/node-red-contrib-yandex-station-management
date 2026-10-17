# Отчёт: форк node-red-contrib-yandex-station-management

Форк плагина Node-RED для управления Яндекс-станциями по локальному Glagol-протоколу (WSS).
Версия **0.3.14**, имя npm `node-red-contrib-yandex-station-management-halamov`.

## Задача
При долгой работе плагина в нём накапливалась «борода» из предыдущих запросов:
`lastState`/`fullMessage` в `yandex-login.js` копились, и узел гонял на станцию
огромные пакеты. Нужна была команда **полного сброса соединения с колонкой без
деплоя** Node-RED.

## Что сделано
- **`reinitDevice(deviceId, nodeId)` в `nodes/yandex-login.js`:**
  - `clearTimeout` всех таймеров (4 шт.) + сброс ping-таймера
  - обнуление `lastState = {}`, `fullMessage = null`, флагов состояния
  - `ws.terminate()` + `removeAllListeners()` — жёсткий рывок старого WS
  - получение **нового glagol-токена** (`getLocalToken`)
  - `makeConn()` — новое соединение с колонкой
- **Новый вход `reinit` в `nodes/local-out.js`:** срабатывает по
  `input === 'reinit'` (выбор «Reinit connection» в ноде OUT) **или**
  `msg.reinit === true` (из любого режима входа).
- **UI:** option `value="reinit"` в `local-out.html` + локали ru/en (`command.reinit`).
- **README:** раздел «Reinit connection».

## Коммиты
| Коммит | Что |
|---|---|
| `5ca08eb` | Команда reinit: полный сброс соединения с колонкой без деплоя |
| `3f52aa2` | reinit срабатывает также по `msg.reinit === true` (в любом режиме входа) |
| `5cc7558` | Опции выбора станции: пробел между именем и идентификатором |

## Проверка
- `node --check` на всех правленых `.js`
- `node --test test/*.test.js` — **6/6 pass**

## Репозитории
- GitHub: `halamov/node-red-contrib-yandex-station-management` (пуш по SSH, ключ `id_ed25519_github`)
- Forgejo: `Home/node-red-contrib-yandex-station-management` (home-nuc2:3000, org Home)
  - Пуш по git-HTTP: access-токен `node-red-sync` (write:repository) как
    `http://ТОКЕН:ТОКЕН@...` (пароль юзера по git-HTTP не работает)

## Установка у пользователя
```
cd ~/.node-red && npm install git+https://github.com/halamov/node-red-contrib-yandex-station-management.git
```
