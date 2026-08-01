# Astro — сборка MaxGraph

> Используется Astro 6. Тестировалось на node.js 20+

По любым ошибкам можете писать issue либо обращаться в телеграм-сообщество <a href="https://t.me/maxgraph_chat" target="_blank">MaxGraph</a>

## Начало работы

Для работы с данной сборкой в новом проекте, склонируйте все содержимое репозитория <br>
`git clone <this repo>`
Затем, находясь в корне проекта, запустите команду `npm i`, которая установит все находящиеся в package.json зависимости.
После этого вы можете использовать любую из предложенных команд сборки (подробнее — ниже, в разделе __npm-скрипты__).

## Структура папок и файлов

```
├── public/                        # Статические файлы (копируются как есть в сборку)
│   ├── fonts                      # Шрифты в формате woff2
│   ├── img                        # Статические изображения (не через import)
│   ├── mail.php                   # PHP-файл отправки формы
│   ├── phpmailer                  # PHPMailer (Exception.php, PHPMailer.php, SMTP.php)
│   └── favicon.ico                # Фавикон
├── src/                           # Исходники
│   ├── assets                     # Ассеты, обрабатываемые Astro
│   │   ├── icons                  # SVG-иконки для astro-icon (авто-спрайт)
│   │   ├── img                    # Изображения (импортируются в компоненты)
│   │   ├── js                     # Скрипты
│   │   │   └── main.js            # Главный скрипт (точка входа)
│   │   │   ├── components         # js-компоненты
│   │   ├── scss                   # Стили (препроцессор sass в scss-синтаксисе)
│   │   │   └── main.scss          # Главный файл стилей
│   │   │   └── vendor.scss        # Файл для подключения стилей библиотек
│   │   │   └── _fonts.scss        # Файл для подключения шрифтов
│   │   │   └── _vars.scss         # CSS- и scss-переменные
│   │   │   └── _settings.scss     # Глобальные стили
│   │   │   ├── components         # scss-компоненты
│   │   │   ├── mixins             # scss-миксины
│   │   │   ├── vendor             # Локальные css-стили библиотек (normalize.css)
│   ├── components                 # Astro-компоненты
│   │   ├── base                   # Базовые компоненты (Head, Header, Footer)
│   │   ├── partials               # Переиспользуемые html-части (секции)
│   ├── layouts                    # Layout-обёртки
│   │   └── BaseLayout.astro       # Базовый layout (html, head, body, header, footer)
│   └── pages                      # Страницы (файловая маршрутизация)
│       └── index.astro            # Главная страница
├── astro.config.mjs               # Конфигурация Astro
├── tsconfig.json                  # Настройки TypeScript (алиас @/ -> src/)
├── package.json                   # Зависимости и npm-скрипты
├── .editorconfig                  # Настройки форматирования кода
├── .stylelintrc.json              # Настройки stylelint
├── .stylelintignore               # Исключения для stylelint
├── .htmlhintrc                    # Настройки htmlhint
└── README.md                      # Документация сборки
```

## Оглавление
1. [npm-скрипты](#npm-скрипты)
2. [Работа с HTML и компонентами](#работа-с-html-и-компонентами)
3. [Работа с CSS](#работа-с-css)
4. [Работа с JavaScript](#работа-с-javascript)
5. [Работа со шрифтами](#работа-со-шрифтами)
6. [Работа с изображениями](#работа-с-изображениями)
7. [Работа с SVG-иконками](#работа-с-svg-иконками)
8. [Работа с иными ресурсами](#работа-с-иными-ресурсами)
9. [Работа со страницами (роутинг)](#работа-со-страницами-роутинг)
10. [Режим backend-сборки](#режим-backend-сборки)
11. [Рекомендуемые плагины VS Code](#рекомендуемые-плагины-для-vs-code)
12. [Заключение](#заключение)

## npm-скрипты

Все команды сборки запускаются через npm.

* `npm run stylelint` — проверка всех scss-файлов на соответствие stylelint.
* `npm run style-fix` — проверка и автофикс scss-файлов.
* `npm run dev` — запуск сервера разработки с горячей перезагрузкой (HMR).
* `npm run build` — продакшн-сборка. Включена проверка stylelint, при ошибках сборка не выполнится.
* `npm run build:backend` — сборка для бэкенда (без минификации HTML/CSS/JS).
* `npm run preview` — локальный предпросмотр готовой сборки.
* `npm run astro` — вызов CLI Astro (например, `npm run astro -- --help`).

## Работа с HTML и компонентами

Сборка использует нативный компонентный подход Astro. Каждый файл `.astro` — это компонент, который может включать в себя HTML, JS (в `---`) и CSS/JS-импорты.

Базовые компоненты (шапка, подвал, head) хранятся в `src/components/base/`. Переиспользуемые секции — в `src/components/partials/`.

Layout-файл `BaseLayout.astro` оборачивает страницу в общий каркас (`<html>`, `<head>`, `<body>`, `<Header />`, `<Footer />`, `<slot />`). Для создания новой страницы достаточно создать файл в `src/pages/` и использовать layout:

```astro
---
import Layout from "@/layouts/BaseLayout.astro";
---
<Layout title="Заголовок">
  <!-- контент страницы -->
</Layout>
```

При `npm run build` HTML минифицируется (кроме режима backend).

## Работа с CSS

В сборке используется препроцессор **sass** в синтаксисе **scss**. Подключение через `@use` (вместо `@import`).

Файлы в `src/assets/scss/components/` подключаются в `main.scss` через `@use`. Стили библиотек можно подключать прямо в `main.scss`.

Переменные проекта — в `_vars.scss` (css-переменные в `:root` или scss-переменные, удалив `:root`). Глобальные стили — в `_settings.scss`. Шрифты — в `_fonts.scss`.

### Миксины

В `src/assets/scss/mixins/` доступны готовые миксины:

* `breakpoint` — брейкпоинты: `large` (≤1700), `desktop` (≤1199.98), `tablet` (≤1023.98), `small-tablet` (≤767.98), `mobile` (≤575.98)
* `font-face` — подключение шрифтов: `@include font-face("Name", "filename", 400, normal)`
* `flex-layout` — гибкая flex-сетка с настройкой кол-ва элементов на разных разрешениях
* `custom-checkbox` — стилизация кастомного чекбокса
* `hover` — ховер только для устройств с hover (без мобильного эмулятора)
* `utils` — вспомогательные функции: `rem()`, `image-set()`, `footerToBottom`

При `npm run build` CSS минифицируется в один файл `css/main-[hash].css`.

## Работа с JavaScript

JS собирается через Vite (встроен в Astro). Точка входа — `src/assets/js/main.js`, который импортируется в `Head.astro` через тег `<script>`.

JS-код лучше делить на компоненты в `src/assets/js/components/` и импортировать их в `main.js`:

```js
import "./components/example.js";
```

Библиотеки устанавливаются через npm и импортируются в нужном компоненте:

```js
import "swiper";
import "graph-modal";
```

При `npm run build` JS минифицируется в один файл `js/main-[hash].js`.

### Доступные пакеты

* `graph-modal` — модальные окна
* `swiper` — слайдеры
* `simplebar` — кастомный скролл
* `inputmask` — маски ввода
* `just-validate` — валидация форм
* `nouislider` — ползунки (range)
* `fslightbox` — галереи/лайтбоксы
* `js-cookie` — работа с куки
* `resize-observer-polyfill` — полифил для ResizeObserver

## Работа со шрифтами

Поддерживается только формат **woff2**. Файлы кладите в `public/fonts/`, затем подключайте через миксин `font-face` в `_fonts.scss`:

```scss
@include font-face("Nunito Sans", "NunitoSansRegular", 400, normal);
```

Шрифты автоматически копируются в итоговую папку.

## Работа с изображениями

Изображения можно размещать двумя способами:

1. **В `src/assets/img/`** — импортируются через Astro (оптимизация, хеширование в имени файла):
   ```astro
   import logo from "@/assets/img/logo.png";
   <img src={logo} alt="" />
   ```

2. **В `public/img/`** — копируются как есть (без обработки), удобно для фонов, SEO-изображений:
   ```astro
   <img src="/img/cover.jpg" alt="" />
   ```

Формат webp поддерживается через тег `<picture>` или через `image-set` миксин в CSS.

## Работа с SVG-иконками

SVG-иконки кладите в `src/assets/icons/`. Они автоматически собираются в спрайт через интеграцию `astro-icon`:

```astro
---
import { Icon } from "astro-icon/components";
---
<Icon name="filename" />
```

Атрибуты `fill` и `stroke` автоматически удаляются при сборке.

## Работа с иными ресурсами

PHP-файлы, favicon, видео и прочие статические ассеты храните в `public/`. Они будут скопированы в итоговую сборку как есть. Для отправки форм используется `public/mail.php` (с PHPMailer в `public/phpmailer/`).

## Работа со страницами (роутинг)

Astro использует файловую маршрутизацию. Каждый файл `.astro` в `src/pages/` создаёт соответствующую страницу:

```
src/pages/index.astro      → /
src/pages/about.astro      → /about
src/pages/catalog.astro    → /catalog
```

Для вложенных маршрутов создавайте подпапки:

```
src/pages/blog/post-1.astro → /blog/post-1
```

## Режим backend-сборки

Команда `npm run build:backend` собирает проект без минификации HTML/CSS/JS, чтобы бэкендеру было удобно работать с кодом. Сборка происходит в ту же папку `app/`.

## Рекомендуемые плагины VS Code

При открытии папки со сборкой редактор предложит установить рекомендованные плагины (список в `.vscode/extensions.json`):

* `astro-build.astro-vscode` — поддержка `.astro` файлов
* `Syler.sass-indented` — подсветка SCSS
* `stylelint.vscode-stylelint` — линтинг стилей
* `EditorConfig.EditorConfig` — поддержка editorconfig
* `htmlhint.vscode-htmlhint` — проверка HTML
* `dbaeumer.vscode-eslint` — проверка JS
* `rebornix.project-snippets` — поддержка локальных сниппетов (если созданы)
* `jerryhong.autofilename` — автоподстановка имён файлов

## Заключение

Сборка создана на основе gulp-maxgraph, но переведена на современный Astro 6 с Vite под капотом. Если вы заметили ошибку — создайте issue с описанием проблемы. Спасибо!
