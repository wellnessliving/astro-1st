# Implementation Report: WellnessLiving Corporate Website (Astro + GitHub Pages)
# Отчёт о внедрении: корпоративный сайт WellnessLiving (Astro + GitHub Pages)

---

## 1. Solution Overview / Обзор решения

**EN:** The solution is a static corporate website built with the Astro framework, hosted on GitHub Pages, and deployed automatically on every content change. Content managers edit Markdown files and a JSON configuration file directly in the GitHub repository (via the browser or VS Code), then commit changes — the site rebuilds and publishes itself within 30–60 seconds with no developer involvement required. The site supports pages, blog posts, nested navigation with dropdown menus, and is fully responsive.

**RU:** Решение представляет собой статический корпоративный сайт на фреймворке Astro, размещённый на GitHub Pages и автоматически разворачиваемый при каждом изменении контента. Контент-менеджеры редактируют Markdown-файлы и JSON-конфигурацию прямо в репозитории GitHub (через браузер или VS Code), фиксируют изменения — сайт пересобирается и публикуется автоматически за 30–60 секунд без участия разработчиков. Сайт поддерживает страницы, блог, вложенную навигацию с выпадающими меню и полностью адаптивен.

### Workflow Diagram / Схема процесса

```
Manager edits .md / .json file
         │
         ▼
   git commit & push
   (GitHub UI or VS Code)
         │
         ▼
  GitHub Actions triggered
  (automatic build ~30 sec)
         │
         ▼
  Static site deployed
  to GitHub Pages
         │
         ▼
  Live site updated
  (no server, no CMS, no database)
```

---

## 2. Key Implementation Details / Ключевые особенности внедрения

### 2.1 GitHub Pages & Repository Visibility / GitHub Pages и видимость репозитория

**EN:**
GitHub Pages on **free organization plans** only works with **public repositories**. The repository `wellnessliving/astro-1st` contains no application code, secrets, or sensitive data — only website content (text, images, styles). There are two options:

| Option | Cost | Tradeoff |
|--------|------|----------|
| **(a) Make the repository public** | Free | Website content (marketing text, blog posts) is visible to anyone on GitHub. No secrets or proprietary code are exposed. |
| **(b) Upgrade to GitHub Team plan** | $4/user/month | Repository stays private. GitHub Pages works with private repos on paid plans. |

During implementation, option (a) was chosen — the repository was made public. This is the standard approach for marketing/corporate websites where the content is already publicly visible on the site itself.

**RU:**
GitHub Pages на **бесплатных организационных планах** работает только с **публичными репозиториями**. Репозиторий `wellnessliving/astro-1st` не содержит исходного кода приложений, секретов или конфиденциальных данных — только контент сайта (тексты, изображения, стили). Два варианта:

| Вариант | Стоимость | Компромисс |
|---------|-----------|-----------|
| **(а) Сделать репозиторий публичным** | Бесплатно | Контент сайта (маркетинговые тексты, посты блога) виден всем на GitHub. Секреты и проприетарный код не затрагиваются. |
| **(б) Обновить план до GitHub Team** | $4/пользователь/месяц | Репозиторий остаётся приватным. GitHub Pages работает с приватными репо на платных планах. |

При внедрении был выбран вариант (а) — репозиторий сделан публичным. Это стандартный подход для маркетинговых/корпоративных сайтов, где контент и так публично доступен на самом сайте.

---

### 2.2 Domains & Subdomains / Домены и поддомены

**EN:**

| Configuration | DNS Record | Notes |
|--------------|-----------|-------|
| Subdomain (e.g. `www.example.com`) | CNAME → `wellnessliving.github.io` | Recommended. Simple setup. |
| Apex domain (e.g. `example.com`) | A records → GitHub IPs (4 addresses) | Works, but no CDN benefits on some DNS providers. |
| Subpath (current: `wellnessliving.github.io/astro-1st/`) | No DNS needed | Default free option. Suitable for testing/staging. |

Key considerations:
- Each GitHub Pages site per organization occupies one subdomain or the `orgname.github.io` root.
- Multiple subdomains (e.g. `blog.example.com`, `docs.example.com`) require separate repositories — each repository is a separate GitHub Pages site.
- HTTPS is enforced automatically by GitHub for custom domains.
- When switching to a custom domain, the `base` path in `astro.config.mjs` must be updated (removed) and internal links adjusted. This is a one-time developer task (~15 minutes).

**RU:**

| Конфигурация | DNS-запись | Примечания |
|-------------|-----------|-----------|
| Поддомен (напр. `www.example.com`) | CNAME → `wellnessliving.github.io` | Рекомендуется. Простая настройка. |
| Корневой домен (напр. `example.com`) | A-записи → IP-адреса GitHub (4 адреса) | Работает, но без CDN-преимуществ у некоторых DNS-провайдеров. |
| Подпуть (текущий: `wellnessliving.github.io/astro-1st/`) | DNS не требуется | Бесплатный вариант по умолчанию. Подходит для тестирования. |

Ключевые моменты:
- Каждый GitHub Pages сайт организации занимает один поддомен или корень `orgname.github.io`.
- Несколько поддоменов (напр. `blog.example.com`, `docs.example.com`) требуют отдельных репозиториев — каждый репозиторий это отдельный GitHub Pages сайт.
- HTTPS обеспечивается автоматически GitHub для пользовательских доменов.
- При переходе на свой домен необходимо обновить (убрать) параметр `base` в `astro.config.mjs` и скорректировать внутренние ссылки. Это разовая задача разработчика (~15 минут).

---

### 2.3 Manager Limitations & Operational Complexity / Ограничения для менеджеров и сложность эксплуатации

**EN:**

| Area | What managers CAN do | What managers CANNOT do |
|------|---------------------|------------------------|
| **Content** | Edit text, add pages, add blog posts, add images, create sections with dropdowns | Change page layout, add interactive features (forms, widgets), modify design |
| **Navigation** | Add/remove/reorder menu items, create dropdown sub-menus | Change header/footer structure, modify CTA button behavior |
| **Deployment** | Content auto-deploys on commit | No manual deploy control, no staging environment, no preview before publish |

Operational complexity factors:
- **Markdown knowledge required.** Managers must learn basic Markdown syntax (bold, headings, links, images, tables). A formatting cheat sheet is included in the documentation.
- **GitHub account required.** Every content manager needs a GitHub account with write access to the repository.
- **No visual editor.** There is no WYSIWYG editor — content is edited as plain text. VS Code with Markdown preview partially addresses this, but it is not a drag-and-drop experience.
- **JSON editing is error-prone.** Navigation changes require editing a JSON file. A missing comma or bracket breaks the site build. Managers must follow the documented format exactly.
- **No content preview before publish.** Changes go live immediately after commit. There is no "draft" or "staging" mode unless the manager runs the site locally with `npm run dev` (advanced workflow, requires Node.js).
- **No rollback UI.** If a manager breaks something, recovery requires either reverting the commit via GitHub (moderately technical) or developer assistance.

**RU:**

| Область | Что менеджер МОЖЕТ | Что менеджер НЕ МОЖЕТ |
|---------|--------------------|-----------------------|
| **Контент** | Редактировать текст, добавлять страницы, посты, картинки, создавать разделы с выпадающими меню | Менять макет страницы, добавлять интерактивные элементы (формы, виджеты), изменять дизайн |
| **Навигация** | Добавлять/удалять/перемещать пункты меню, создавать подменю | Менять структуру шапки/подвала, изменять поведение CTA-кнопки |
| **Деплой** | Контент публикуется автоматически при коммите | Нет ручного контроля деплоя, нет staging-окружения, нет предпросмотра перед публикацией |

Факторы операционной сложности:
- **Необходимо знание Markdown.** Менеджеры должны освоить базовый синтаксис Markdown (жирный, заголовки, ссылки, картинки, таблицы). Шпаргалка включена в документацию.
- **Необходим аккаунт GitHub.** Каждому контент-менеджеру нужен GitHub-аккаунт с правами записи в репозиторий.
- **Нет визуального редактора.** Нет WYSIWYG-редактора — контент редактируется как обычный текст. VS Code с предпросмотром Markdown частично решает проблему, но это не drag-and-drop интерфейс.
- **Редактирование JSON подвержено ошибкам.** Изменение навигации требует редактирования JSON-файла. Пропущенная запятая или скобка ломает сборку сайта. Менеджеры должны точно следовать документированному формату.
- **Нет предпросмотра перед публикацией.** Изменения публикуются немедленно после коммита. Режима «черновик» или «staging» нет, если только менеджер не запускает сайт локально через `npm run dev` (продвинутый workflow, требует Node.js).
- **Нет интерфейса отката.** Если менеджер что-то сломал, восстановление требует либо отката коммита через GitHub (умеренно техническая операция), либо помощи разработчика.

---

## 3. Risks / Риски

**EN:**

| # | Risk | Impact | Mitigation |
|---|------|--------|-----------|
| 1 | **Manager breaks the site** by invalid JSON or malformed Markdown | Site build fails, current version stays live, new changes don't appear | Documentation with templates provided; developers can revert commits in <5 min |
| 2 | **No staging/preview** — errors go live immediately | Visitors may briefly see broken content | Local preview available for advanced managers (VS Code + `npm run dev`); build failure prevents deployment of broken code |
| 3 | **Vendor lock-in to GitHub** | Migration effort if GitHub changes pricing or policies | Astro generates standard HTML; site can be rehosted on any static hosting (Netlify, Cloudflare Pages, S3) with minimal effort |
| 4 | **Public repository exposes content history** | Deleted or edited content remains visible in git history | Acceptable for marketing content; sensitive information should never be placed in the repository |
| 5 | **Manager adoption** — Markdown + GitHub may be too technical | Slow content updates, continued dependence on developers | Training session (1–2 hours); comprehensive documentation provided in EN and RU; VS Code workflow with visual preview as intermediate option |
| 6 | **Single point of failure: GitHub** | Site unavailable if GitHub Pages has an outage | GitHub has 99.9%+ uptime historically; for mission-critical sites, consider CDN mirror (Cloudflare) |

**RU:**

| # | Риск | Влияние | Снижение |
|---|------|---------|---------|
| 1 | **Менеджер ломает сайт** невалидным JSON или Markdown | Сборка падает, текущая версия остаётся live, новые изменения не появляются | Документация с шаблонами предоставлена; разработчик может откатить коммит за <5 мин |
| 2 | **Нет staging/предпросмотра** — ошибки публикуются сразу | Посетители могут кратковременно видеть сломанный контент | Локальный предпросмотр для продвинутых менеджеров (VS Code + `npm run dev`); ошибка сборки предотвращает деплой сломанного кода |
| 3 | **Привязка к GitHub** | Усилия на миграцию при изменении ценовой политики GitHub | Astro генерирует стандартный HTML; сайт можно перенести на любой статический хостинг (Netlify, Cloudflare Pages, S3) с минимальными усилиями |
| 4 | **Публичный репозиторий показывает историю изменений** | Удалённый или отредактированный контент остаётся видимым в истории git | Приемлемо для маркетингового контента; конфиденциальная информация не должна размещаться в репозитории |
| 5 | **Адаптация менеджеров** — Markdown + GitHub может быть слишком техническим | Медленное обновление контента, продолжение зависимости от разработчиков | Обучение (1–2 часа); полная документация предоставлена на EN и RU; workflow через VS Code с визуальным предпросмотром как промежуточный вариант |
| 6 | **Единая точка отказа: GitHub** | Сайт недоступен при сбое GitHub Pages | Историческая доступность GitHub 99.9%+; для критически важных сайтов — CDN-зеркало (Cloudflare) |

---

*Repository: [github.com/wellnessliving/astro-1st](https://github.com/wellnessliving/astro-1st)*
*Live site: [wellnessliving.github.io/astro-1st](https://wellnessliving.github.io/astro-1st/)*
*Documentation: see `doc/` folder in the repository (MANAGER, MANAGER-ADVANCED, DEVELOPER — EN & RU)*
