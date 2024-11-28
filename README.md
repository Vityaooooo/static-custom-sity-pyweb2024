# Кастомизация статического сайта

Для выполнения данного задания были придприняты следующие шаги

1. Создаем папку my-theme, в которой будут находиться все файл шаблоны, в themes
    структура папки my-theme
    - static
        - css
            - style.css
    - layouts
        - _default
            - baseof.html
            - index.html
        - partials
            - header.html
            - footer.html
        - posts
            - single.html
2. Добавляем разметку для baseof.html
    ```
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta name="description" content="{{ .Site.Params.description }}">
        <meta name="author" content="{{ .Site.Params.author }}">
        <title>{{ .Site.Title }}</title>
        <link rel="stylesheet" href="css/style.css">
        {{ block "head" . }}{{ end }}
    </head>
    <body class="page">
        <hr>
        {{ block "header" . }}{{ end }}
        <main role="main">
        {{ block "main" . }}{{ end }}
        </main>
        <hr>
        {{ block "footer" . }}{{ end }}
    </html>
    ```
3. Добавляем разметку для index.html
    ```
    {{ define "main" }}
        <section class="content">
            <div class="container">
            <h2>Latest Posts</h2>
            <div class="posts">
                {{ range first 10 .Site.RegularPages }}
                <a href="{{ .Permalink }}" class="read-more">
                <div class="post-preview">
                    <p>{{ .Summary }}</p>
                </div>
                </a>  
                {{ end }}
            </div>
            </div>
        </section>
    {{ end }}
    ```
4. Добавляем разметку для footer.html
    ```
    {{ block "footer" . }}
        <footer>
            <p>&copy; 2024 {{ .Site.Params.author }}</p>
        </footer>
    {{ end }}

    ```
5. Добавляем разметку для header.html
    ```
    {{ block "header" . }}
        <header>
            <h1>{{ .Site.Title }}</h1>
        </header>
    {{ end }}
    ```
6. Добавляем разметку для single.html
     ```
    {{ define "main" }}
        <article>
            <header class="post__header">
            <h2 class="post__title">{{ .Title }}</h2>
            <p class="post__date"><small>{{ .Date.Format "January 2, 2006" }}</small></p>
            </header>
            <section class="post__content">
            {{ .Content }}
            </section>
        </article>
    {{ end }}
    ```
7. Добавляем стили в style.css
    ```
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    body {
        font-family: Arial, sans-serif;
        color: black;
        line-height: 1.6;
        background-color: white;
    }

    hr {
        margin: 20px 10px;
    }

    header {
        padding: 10px 20px;
        margin-bottom: 30px;
    }

    header h1 {
        font-size: 40px;
        text-align: left;
    }

    footer {
        padding: 20px 0;
        text-align: center;
    }

    .container {
        width: 90%;
        margin: 0 auto;
        display: flex;
    }

    .container h2 {
        transform-origin: center center;
        transform: rotate(-90deg);
    }

    .posts {
        display: flex;
        gap: 30px;
        flex-basis: 500px;
        flex-grow: 1;
        flex-shrink: 1;
    }

    .post-preview {
        border: 1px solid black;
        border-radius: 10px;
        padding: 20px 20px;
        max-height: 500px;
    }

    a {
        color: black;
        text-decoration: none;
    }

    a:visited {
        color: black;
    }

    article {
        margin: 20px 20px;
    }

    article h2 {
        font-size: 2rem;
        margin-bottom: 10px;
    }

    article p {
        margin-bottom: 15px;
    }
    ```
8. Создаем файл postcss.congig.js
    ```
    module.exports = {
        plugins: [
            require('autoprefixer'),
            require('cssnano')
        ]
    };
    ```
9. Создаем скрипт для запуск postcss в package.json
    ```
    "scripts": {
        "build:css": "postcss themes/my-theme/static/css/*.css -d static/css/"
    },
    ```
10. Создаем yml файл для автоматизации деплоя 
    ```
    name: Build and Deploy Hugo Site

    on:
    push:
        branches:
        - main
        pull_request:
    jobs:
    deploy:
        runs-on: ubuntu-22.04
        steps:
        - name: Checkout code
        uses: actions/checkout@v3

        - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
            node-version: '20' 

        - name: Install Node.js dependencies
            run: npm run build:css

        - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
            hugo-version: "latest"
            extended: true
        
        - name: Build the site
        run: hugo --minify
        
        - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
            github_token: ${{ secrets.HUGO_DEPLOY }}
            publish_dir: ./public
    ```
11. Делаем push в удаленный репозиторий
12. При успешном выполнении всех шагов по адресу https://<username>.github.io/<repository-name>/ будет доступен наш статический сайт