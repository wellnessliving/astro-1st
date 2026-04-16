# Документация для разработчика

## Технологический стек

| Компонент | Технология | Версия |
|-----------|-----------|--------|
| Фреймворк | [Astro](https://astro.build) | 4.x |
| Хостинг | GitHub Pages | - |
| CI/CD | GitHub Actions | v5/v6 |
| Контент | Markdown (.md) | - |
| Стили | Чистый CSS | - |
| Пакетный менеджер | npm | - |

## Структура проекта

```
astro/
├── public/
│   ├── styles.css              # Глобальные стили (WP-тема + dropdown/sidebar)
│   ├── images/                 # Статические картинки
│   └── favicon.svg
├── src/
│   ├── layouts/
│   │   └── Base.astro          # Основной layout: шапка (dropdown), sidebar, подвал
│   ├── pages/
│   │   ├── index.astro         # Главная (hero, сервисы, посты, CTA)
│   │   ├── [...page].astro     # Catch-all рендер страниц (плоские + вложенные)
│   │   └── blog/
│   │       ├── index.astro     # Список постов (WP-стиль + sidebar)
│   │       └── [slug].astro    # Рендер поста из content/blog/*.md
│   ├── content/
│   │   ├── pages/              # ← Страницы, редактируемые менеджером
│   │   │   ├── about.md
│   │   │   ├── pricing.md      #   Обзор раздела
│   │   │   ├── pricing/        #   Подстраницы (пункты dropdown)
│   │   │   │   ├── starter.md
│   │   │   │   ├── professional.md
│   │   │   │   └── enterprise.md
│   │   │   ├── services.md
│   │   │   └── services/
│   │   │       ├── scheduling.md
│   │   │       ├── payments.md
│   │   │       └── marketing.md
│   │   └── blog/               # ← Блог-посты, редактируемые менеджером
│   └── data/
│       └── navigation.json     # ← Меню навигации (поддерживает children)
├── doc/                        # Документация
├── astro.config.mjs            # Конфигурация Astro
├── package.json
└── .github/
    └── workflows/
        └── deploy.yml          # Автодеплой при push в main
```

## Как это работает

### Навигация
- `src/data/navigation.json` определяет пункты меню.
- `src/layouts/Base.astro` читает JSON и рендерит навбар.
- **Последний элемент** массива становится CTA-кнопкой (синяя).
- Активная страница подсвечивается автоматически.
- Элементы с массивом `children` рендерятся как **выпадающие меню** при наведении.

#### Схема navigation.json

```json
// Простой пункт (без dropdown):
{ "label": "About", "path": "/about/" }

// Пункт с выпадающим меню:
{
  "label": "Pricing",
  "path": "/pricing/",       // Родительская страница (обзор)
  "children": [
    { "label": "Starter", "path": "/pricing/starter/" },
    { "label": "Professional", "path": "/pricing/professional/" }
  ]
}
```

#### Архитектура Dropdown и Sidebar

- **Dropdown в шапке:** CSS hover (`.nav-dropdown` / `.nav-dropdown-menu`), без JS.
- **Sidebar страницы:** `Base.astro` автоматически определяет, принадлежит ли текущая страница разделу с `children`. Если да — рендерит `<aside class="page-sidebar">` со ссылками на все подстраницы + «Overview» (родительская).
- **Хлебные крошки:** Автогенерация из текущего URL.
- **Определение раздела:** `findSection()` в `Base.astro` сравнивает путь URL с пунктами навигации.
- **Мобильная версия:** Dropdown раскрывается inline. Sidebar складывается над контентом.

### Страницы (не блог)
- `.md` файлы из `src/content/pages/` (и подпапок) рендерятся через `src/pages/[...page].astro`.
- **Плоские страницы:** `about.md` → `/about/`
- **Вложенные страницы:** `pricing/starter.md` → `/pricing/starter/`
- Catch-all `[...page].astro` использует `import.meta.glob('../content/pages/**/*.md')` для рекурсивного обнаружения всех страниц.
- Обязательные поля в frontmatter: `title`, `description`.

### Блог-посты
- `.md` файлы из `src/content/blog/` рендерятся через `src/pages/blog/[slug].astro`.
- Slug берётся из `frontmatter.slug` или из имени файла.
- Список постов (`src/pages/blog/index.astro`) — сортировка по дате.
- На главной — 3 последних поста.

### Сборка и деплой
- Каждый `git push` в `main` запускает GitHub Actions.
- Actions выполняет `npm run build`, загружает `dist/` на GitHub Pages.
- Деплой занимает ~30-40 секунд.

## Локальная разработка

```bash
cd /root/astro
npm install          # Установка зависимостей (первый раз)
npm run dev          # Dev-сервер на http://localhost:4321
npm run build        # Сборка в dist/
npm run preview      # Просмотр собранного сайта
```

## Подключение своего домена

1. DNS: Добавить `CNAME` запись субдомена на `wellnessliving.github.io`
2. Создать файл `public/CNAME` с содержимым: `yourdomain.com`
3. Обновить `astro.config.mjs`:
   ```js
   export default defineConfig({
     site: 'https://yourdomain.com',
     // Убрать base если сайт на корневом домене
   });
   ```
4. GitHub Settings → Pages → Custom domain → вписать домен
5. Включить "Enforce HTTPS"

## CSS-классы

| Класс | Назначение |
|-------|------------|
| `.container` | Обёртка 1200px |
| `.section` | Секция страницы |
| `.section-alt` | Секция с серым фоном |
| `.hero` | Тёмный градиентный баннер |
| `.cards` | Сетка карточек сервисов |
| `.blog-cards` | Сетка карточек постов |
| `.cta-section` | Синий CTA-блок |
| `.post-content` | Тело поста/страницы (760px) |
| `.nav-dropdown` | Обёртка пункта с выпадающим меню |
| `.nav-dropdown-trigger` | Кликабельная ссылка со стрелкой |
| `.nav-dropdown-menu` | Выпадающая панель (скрыта, показ по hover) |
| `.page-with-sidebar` | Grid-layout: sidebar 260px + контент |
| `.page-sidebar` | Левая боковая панель навигации раздела |
| `.sidebar-nav` | Список ссылок в sidebar |
| `.breadcrumbs` | Хлебные крошки |

## Добавление нового раздела с подстраницами (заметки разработчика)

Когда менеджер создаёт новый раздел:

1. Добавляет `.md` файлы в `src/content/pages/имя-раздела/`
2. Добавляет `children` в `navigation.json`
3. Изменения кода **не требуются** — `[...page].astro` автоматически подхватывает вложенные пути

**Как работает catch-all маршрут:**

```
[...page].astro
  └── getStaticPaths()
       └── import.meta.glob('../content/pages/**/*.md')
            └── Извлекает относительный путь → разбивает на сегменты → объединяет в slug
```

**Как работает определение sidebar:**

```
Base.astro
  └── findSection(navItems, currentPath)
       └── Перебирает пункты навигации с children
            └── Сравнивает сегмент пути родителя с текущим URL
                 └── Совпадение → рендерит sidebar + хлебные крошки
```

**Ключевые файлы реализации:**
- `src/layouts/Base.astro:26-33` — логика `findSection()`
- `src/layouts/Base.astro:70-89` — HTML рендеринг dropdown
- `src/layouts/Base.astro:97-124` — условный рендеринг sidebar + хлебных крошек
- `src/pages/[...page].astro:6-18` — catch-all `getStaticPaths()`
- `public/styles.css` — `.nav-dropdown*`, `.page-with-sidebar`, `.page-sidebar`, `.breadcrumbs`
