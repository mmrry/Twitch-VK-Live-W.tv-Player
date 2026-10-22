# Политика конфиденциальности

**Twitch → VK Video Live / W.tv / Kick Player** (userscript для Tampermonkey)
Версия скрипта: 2.8.0 · Дата редакции: 22 октября 2026

[English version below](#privacy-policy-english)

---

## Коротко

- У скрипта **нет собственного сервера**. Автор не получает никаких ваших данных: нет аналитики, телеметрии, счётчиков и отчётов об ошибках.
- Скрипт **не читает** пароли, cookies (`document.cookie`), `localStorage`/`sessionStorage`, сообщения чата, данные вашего аккаунта Twitch и содержимое форм.
- Скрипт обращается **только к VK Video Live, W.tv, Kick и их видеосерверам**, чтобы узнать, идёт ли стрим, и показать его. Эти сервисы видят ваш IP-адрес и, для запросов к своему API, свои cookies в вашем браузере.
- Настройки хранятся **только локально** в Tampermonkey и удаляются вместе со скриптом.

---

## 1. Что скрипт делает

На страницах каналов Twitch, для которых настроено соответствие (по умолчанию `twitch.tv/fasoollka`), скрипт проверяет, идёт ли стрим на VK Video Live или W.tv. Статус показывается на плашках справа в плеере Twitch. Плеер сервиса открывается поверх плеера Twitch **только после вашего клика** по плашке. Пока показан этот плеер, видео Twitch ставится на паузу и выключается звук. Когда вы его закрываете, скрипт возвращает плеер Twitch в прежнее состояние.

На всех остальных страницах Twitch скрипт ничего не показывает и никуда не обращается.

## 2. Какие данные скрипт использует на странице Twitch

| Что | Зачем | Куда уходит |
|---|---|---|
| Адрес страницы (`/имя_канала`) | Понять, какой канал Twitch открыт | Никуда |
| Элементы `<video>` плеера Twitch | Поставить на паузу или выключить звук и потом вернуть как было | Никуда |
| Нажатия Alt+Shift+D, первый клик или клавиша на странице | Включить debug-режим; включить звук W.tv, если браузер запретил автозвук | Никуда. Содержимое ввода не записывается |

## 3. Сетевые запросы

Скрипт отправляет запросы **только** на перечисленные ниже адреса. Других доменов в списке разрешений Tampermonkey (`@connect`) нет. Каждый адрес дополнительно проверяется в коде: только HTTPS, стандартный порт, без IP-адресов, без редиректов на другие домены.

| Адрес | Когда | Что получает сервис | Cookies |
|---|---|---|---|
| `api.live.vkvideo.ru` | Раз в минуту, пока открыта настроенная страница и вкладка видима | Имя VK-канала, ваш IP, User-Agent, заголовок `Referer: https://live.vkvideo.ru/<канал>` | **Да**, cookies VK из вашего браузера (если вы вошли в VK, запрос может быть связан с вашим аккаунтом) |
| `profiles-service.w.tv`, `streams-search-service.w.tv` | Раз в минуту, на тех же условиях | Ник W.tv-канала и его ID, язык интерфейса (`ru`/`en`), ваш IP, User-Agent, `Referer: https://w.tv/` | **Да**, cookies W.tv из вашего браузера |
| `kick.com` (только `/api/v2/channels/…`) | Раз в минуту, на тех же условиях | Имя Kick-канала, ваш IP, User-Agent, заголовки `Referer: https://kick.com/<канал>`, `X-App-Platform: web` | **Да**, cookies Kick из вашего браузера (включая cookies защиты Cloudflare) |
| `*.live-video.net` (Amazon IVS, видеосерверы W.tv и Kick) | Пока вы смотрите W.tv или Kick | Запросы плейлистов и видеофрагментов, ваш IP | **Нет** |
| `okcdn.ru`, `mycdn.me`, `vkuser.net` (видеосерверы VK) | Только в скрытом debug-режиме HLS | Запросы плейлистов и видеофрагментов, ваш IP | **Нет** |
| `cdn.jsdelivr.net` | Один раз при установке и при обновлениях скрипта (это делает Tampermonkey) | Загрузка библиотеки hls.js 1.6.19, ваш IP | — |

Cookies к API VK, W.tv и Kick передаются намеренно: скрипт не трогает вашу авторизацию и работает так же, как если бы вы открыли сайт сам. Скрипт не читает и не сохраняет эти cookies, их подставляет браузер или Tampermonkey.

В фоновой или свёрнутой вкладке проверки не выполняются, а уже начатые отменяются. Они возобновятся, когда вы вернётесь на вкладку. Единственное исключение: если в фоновой вкладке играет W.tv или Kick и ссылка на поток устарела, скрипт один раз запрашивает новую, чтобы видео не остановилось.

## 4. Встроенный плеер VK (Embed)

Стримы VK показываются через **официальный embed-плеер VK** (`live.vkvideo.ru/app/embed/<канал>`) во встроенном фрейме. Страница VK загружается **только после вашего клика** по плашке VK, а не при заходе на канал. Это страница VK, и на неё распространяется **политика конфиденциальности VK**, а не этого скрипта:

- VK получает ваш IP-адрес, имя канала и источник перехода (`https://www.twitch.tv/`).
- VK может использовать свои cookies, если браузер разрешает cookies во встроенных фреймах.
- Страница VK работает со своей аналитикой. При тестировании она обращалась к счётчикам статистики VK и Mail.ru и отправляла отметки просмотра, по которым вы учитываетесь как зритель стрима.

Внутри этого фрейма скрипт делает только три вещи: скрывает надпись «Смотреть на VK Видео Live», убирает полосу прокрутки и запускает видео, если оно не стартовало само. Скрипт ничего не читает со страницы VK и никуда не отправляет.

Если вы не хотите загружать страницу VK, удалите VK-канал из соответствий (см. раздел 7).

## 5. Что хранится и где

Всё хранится **только на вашем устройстве**, в хранилище Tampermonkey для этого скрипта.

| Ключ | Содержимое |
|---|---|
| `channelMap2` | Соответствия «канал Twitch → каналы VK / W.tv / Kick» |
| `volume_wtv`, `volume_kick`, `volume_vk` | Последняя выбранная громкость |
| `quality` | Выбранное качество видео |
| `mode` | Режим плеера VK (используется только в debug) |
| `debug` | Включён ли debug-режим постоянно (по умолчанию нет) |
| `tvkOverride2` (хранилище вкладки) | Каналы, временно вписанные в debug-режиме. Живут, пока открыта вкладка |
| `channelMap`, `volume`, `muted` | Могут остаться от старых версий, больше не используются |

Скрипт не хранит историю просмотров, ссылки на потоки, cookies или идентификаторы аккаунтов. В памяти страницы, пока она открыта, кэшируется только ID канала W.tv (до 6 часов).

## 6. Debug-режим

По умолчанию выключен. Если его включить (Alt+Shift+D или ключ `debug: true`), скрипт пишет технический лог **только в консоль вашего браузера**. Параметры ссылок, подписи и токены в логе маскируются (`?***`, `/sig/***`). Лог никуда не отправляется.

## 7. Как ограничить или удалить данные

- **Отключить скрипт на канале.** В меню Tampermonkey выберите «⚙ Каналы Twitch → VK / W.tv / Kick» и удалите нужную строку. Если очистить поле целиком, скрипт перестанет работать везде.
- **Не загружать страницу VK.** Оставьте в соответствии только W.tv, например `fasoollka=wtv:fasolka`.
- **Ограничить cookies.** Выйдите из аккаунта VK, W.tv или Kick либо заблокируйте сторонние cookies в настройках браузера.
- **Удалить все данные.** Удалите скрипт в Tampermonkey: его хранилище удаляется вместе с ним. Можно также очистить ключи во вкладке «Хранилище» скрипта.

## 8. Сторонние сервисы

Скрипт работает с сервисами, которые не принадлежат автору и не контролируются им. На обработку данных этими сервисами распространяются их собственные политики конфиденциальности:

- VK Video Live (VK): <https://vk.com/privacy>
- W.tv: политика на сайте <https://w.tv>
- Kick: <https://kick.com/privacy-policy>
- Amazon IVS (видеодоставка W.tv и Kick): <https://aws.amazon.com/privacy/>
- jsDelivr (загрузка hls.js): <https://www.jsdelivr.com/terms/privacy-policy>
- Twitch: <https://legal.twitch.com/legal/privacy-notice>

Скрипт не связан с Twitch, VK, W.tv, Kick или Amazon и не одобрен ими.

## 9. Изменения

Если поведение скрипта в отношении данных изменится, этот файл будет обновлён вместе с версией скрипта. Актуальная редакция всегда лежит в репозитории.

## 10. Вопросы

Вопросы и сообщения о проблемах присылайте через Issues этого репозитория.

---
---

## Privacy Policy (English)

**Twitch → VK Video Live / W.tv / Kick Player** (Tampermonkey userscript)
Script version: 2.8.0 · Last updated: October 22, 2026

### Summary

- The script has **no server of its own**. The author receives **no data** from you: no analytics, telemetry, counters or crash reports.
- The script **does not read** passwords, cookies (`document.cookie`), `localStorage`/`sessionStorage`, chat messages, your Twitch account data or form contents.
- It only contacts **VK Video Live, W.tv, Kick and their video servers**, to check whether a stream is live and to play it. These services see your IP address and, for requests to their own API, their own cookies stored in your browser.
- Settings are stored **locally only** in Tampermonkey and are deleted together with the script.

### 1. What the script does

On Twitch channel pages that have a mapping configured (by default `twitch.tv/fasoollka`), the script checks whether the streamer is live on VK Video Live or W.tv. The status is shown on badges on the right of the Twitch player; the service's player opens on top of it **only after you click** a badge. While that player is shown, the Twitch video is paused and muted; when you close it, the script puts the Twitch player back the way it was. On every other Twitch page the script shows nothing and makes no requests.

### 2. Data used on the Twitch page

| What | Why | Sent anywhere? |
|---|---|---|
| Page path (`/channel_name`) | Find out which Twitch channel is open | No |
| `<video>` elements of the Twitch player | Pause or mute, then restore their previous state | No |
| Alt+Shift+D, first click or key press on the page | Toggle debug mode; unmute W.tv if the browser blocked autoplay with sound | No. Input contents are not recorded |

### 3. Network requests

Requests go **only** to the hosts below. Tampermonkey's `@connect` list contains no other domains. Each URL is also checked in code: HTTPS only, default port, no IP addresses, no redirects to other domains.

| Host | When | What the service receives | Cookies |
|---|---|---|---|
| `api.live.vkvideo.ru` | Once a minute while a mapped page is open and the tab is visible | VK channel name, your IP, User-Agent, `Referer: https://live.vkvideo.ru/<channel>` | **Yes**, your browser's VK cookies (if you are signed in to VK, the request may be linked to your account) |
| `profiles-service.w.tv`, `streams-search-service.w.tv` | Same as above | W.tv channel nickname and ID, UI language (`ru`/`en`), your IP, User-Agent, `Referer: https://w.tv/` | **Yes**, your browser's W.tv cookies |
| `kick.com` (only `/api/v2/channels/…`) | Same as above | Kick channel name, your IP, User-Agent, `Referer: https://kick.com/<channel>`, `X-App-Platform: web` | **Yes**, your browser's Kick cookies (including Cloudflare protection cookies) |
| `*.live-video.net` (Amazon IVS, W.tv and Kick video delivery) | While you watch W.tv or Kick | Playlist and video segment requests, your IP | **No** |
| `okcdn.ru`, `mycdn.me`, `vkuser.net` (VK video servers) | Only in the hidden debug HLS mode | Playlist and video segment requests, your IP | **No** |
| `cdn.jsdelivr.net` | Once at install and on script updates (performed by Tampermonkey) | Download of hls.js 1.6.19, your IP | — |

Cookies are sent to the VK, W.tv and Kick APIs on purpose: the script leaves your sign-in alone and behaves as if you had opened the site yourself. The script never reads or stores these cookies; the browser or Tampermonkey attaches them. No checks run while the tab is hidden, and checks already in progress are cancelled; they resume when you return to it. The only exception: if W.tv or Kick is playing in a background tab and its stream link expires, the script requests a new one once so playback doesn't stop.

### 4. VK embedded player

VK streams are shown with VK's **official embed player** (`live.vkvideo.ru/app/embed/<channel>`) inside a frame. The VK page loads **only after you click** the VK badge, not when you open the channel. That page belongs to VK and is covered by **VK's privacy policy**, not this one:

- VK receives your IP address, the channel name and the referring origin (`https://www.twitch.tv/`).
- VK may use its own cookies if your browser allows cookies in embedded frames.
- The page runs VK's own analytics. During testing it contacted VK and Mail.ru statistics counters and sent viewing heartbeats, which count you as a viewer of the stream.

Inside that frame the script does only three things: hides the "Watch on VK Video Live" label, removes the scrollbar, and starts the video if it did not start on its own. It reads nothing from the VK page and sends nothing anywhere. If you don't want the VK page to load, remove the VK channel from your mappings (see section 7).

### 5. What is stored and where

Everything is stored **only on your device**, in this script's Tampermonkey storage:

| Key | Contents |
|---|---|
| `channelMap2` | Twitch channel → VK / W.tv / Kick channel mappings |
| `volume_wtv`, `volume_kick`, `volume_vk` | Last chosen volume |
| `quality` | Chosen video quality |
| `mode` | VK player mode (debug only) |
| `debug` | Whether debug mode is permanently on (off by default) |
| `tvkOverride2` (tab storage) | Channels entered in debug mode; kept only while the tab is open |
| `channelMap`, `volume`, `muted` | May remain from older versions; no longer used |

No viewing history, stream URLs, cookies or account identifiers are stored. Only the W.tv channel ID is cached in page memory while the page is open (up to 6 hours).

### 6. Debug mode

Off by default. When enabled (Alt+Shift+D or the `debug: true` key), the script writes a technical log **only to your browser console**. URL parameters, signatures and tokens are masked (`?***`, `/sig/***`). The log is never sent anywhere.

### 7. Limiting or deleting data

- **Turn the script off for a channel:** in the Tampermonkey menu choose "⚙ Twitch → VK / W.tv / Kick channels" and remove the line. Clearing the field entirely turns the script off everywhere.
- **Don't load the VK page:** map W.tv only, e.g. `fasoollka=wtv:fasolka`.
- **Limit cookies:** sign out of VK, W.tv or Kick, or block third-party cookies in your browser.
- **Delete all data:** uninstall the script in Tampermonkey; its storage is removed with it. You can also clear the keys in the script's Storage tab.

### 8. Third-party services

The script works with services the author does not own or control; their own privacy policies apply:

- VK Video Live (VK): <https://vk.com/privacy>
- W.tv: see <https://w.tv>
- Kick: <https://kick.com/privacy-policy>
- Amazon IVS (W.tv and Kick video delivery): <https://aws.amazon.com/privacy/>
- jsDelivr (hls.js download): <https://www.jsdelivr.com/terms/privacy-policy>
- Twitch: <https://legal.twitch.com/legal/privacy-notice>

This script is not affiliated with or endorsed by Twitch, VK, W.tv, Kick or Amazon.

### 9. Changes

If the script's data handling changes, this file is updated together with the script version. The current version is always in the repository.

### 10. Contact

Please send questions and reports through this repository's Issues.
