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
│   ├── styles.css              # Глобальные стили (WP-тема)
│   ├── images/                 # Статические картинки
│   └── favicon.svg
├── src/
│   ├── layouts/
│   │   └── Base.astro          # Основной layout: шапка, подвал, навигация
│   ├── pages/
│   │   ├── index.astro         # Главная (hero, сервисы, посты, CTA)
│   │   ├── [page].astro        # Динамический рендер страниц из content/pages/*.md
│   │   └── blog/
│   │       ├── index.astro     # Список постов (WP-стиль + sidebar)
│   │       └── [slug].astro    # Рендер поста из content/blog/*.md
│   ├── content/
│   │   ├── pages/              # ← Страницы, редактируемые менеджером
│   │   └── blog/               # ← Блог-посты, редактируемые менеджером
│   └── data/
│       └── navigation.json     # ← Меню навигации, редактируемое менеджером
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

### Страницы (не блог)
- `.md` файлы из `src/content/pages/` рендерятся через `src/pages/[page].astro`.
- Имя файла = URL: `services.md` → `/services/`.
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
