Использование шаблонизатора для PUG-генерации как это реализовать?

Вот как реализовать PUG-генерацию через шаблонизатор в PHP-проекте с полным контролем над процессом:

### 1. Базовая реализация шаблонизатора

```php
<?php
// core/TemplateEngine.php
class TemplateEngine {
    private $basePath;
    private $variables = [];
    private $cacheEnabled;
    private $cacheDir;

    public function __construct(string $basePath, bool $cacheEnabled = false, string $cacheDir = 'cache') {
        $this->basePath = rtrim($basePath, '/') . '/';
        $this->cacheEnabled = $cacheEnabled;
        $this->cacheDir = $cacheDir;
        
        if ($cacheEnabled && !is_dir($cacheDir)) {
            mkdir($cacheDir, 0755, true);
        }
    }

    public function render(string $template, array $data = []): string {
        $pugFile = $this->basePath . $template . '.pug';
        $cacheKey = $this->cacheEnabled ? md5($pugFile . serialize($data)) : null;
        $cacheFile = $this->cacheEnabled ? $this->cacheDir . '/' . $cacheKey . '.html' : null;

        // Возвращаем кэш если есть
        if ($this->cacheEnabled && file_exists($cacheFile)) {
            return file_get_contents($cacheFile);
        }

        // Рендеринг через PHUG
        $content = Phug::renderFile($pugFile, array_merge($this->variables, $data));

        // Сохраняем в кэш
        if ($this->cacheEnabled) {
            file_put_contents($cacheFile, $content);
        }

        return $content;
    }

    public function addGlobal(string $key, $value): void {
        $this->variables[$key] = $value;
    }
}
```

### 2. Регистрация компонентов

```php
// components/Button.php
class Button {
    public static function render(string $text, string $type = 'primary'): string {
        return Phug::render(<<<PUG
button(type="button" class="btn btn-$type") $text
PUG
        );
    }
}

// components/Card.php
class Card {
    public static function render(string $title, string $body): string {
        return Phug::render(<<<'PUG'
div.card
  h3.card-title= title
  .card-body= body
PUG
        , ['title' => $title, 'body' => $body]);
    }
}
```

### 3. Использование в проекте

```php
// index.php
require 'vendor/autoload.php';
use Phug\Phug;

// Инициализация
$engine = new TemplateEngine('views/', true, 'cache');
$engine->addGlobal('siteName', 'Мой сайт');

// Рендеринг страницы
echo $engine->render('home', [
    'title' => 'Главная страница',
    'content' => Card::render('Приветствие', 'Добро пожаловать!')
]);

// Использование компонента
echo Button::render('Кликни меня', 'success');
```

### 4. Структура файлов проекта

```
project/
├── views/
│   ├── home.pug
│   ├── layout.pug
│   └── partials/
│       └── header.pug
├── components/
│   ├── Button.php
│   └── Card.php
├── core/
│   └── TemplateEngine.php
├── cache/
├── vendor/
└── index.php
```

### 5. Расширенные возможности

**Наследование шаблонов:**

```pug
// views/layout.pug
doctype html
html
  head
    title= siteName + ' | ' + title
  body
    include partials/header
    block content
```

```pug
// views/home.pug
extends layout

block content
  h1= title
  != content
```

**Фильтры и миксины:**

```php
// Расширение функционала
Phug::addFilter('markdown', function ($code) {
    $parsedown = new Parsedown();
    return $parsedown->text($code);
});

// Использование в шаблоне
//- :markdown
  ## Это markdown
  - Пункт 1
  - Пункт 2
```

### 6. Оптимизация производительности

```php
// В production
Phug::setOptions([
    'cache_dir' => 'cache/phug',
    'up_to_date_check' => false,
    'strict_variables' => true
]);

// Предварительная компиляция шаблонов
if ($isProduction) {
    $compiler = new Phug\Compiler();
    $compiler->compileDirectory('views', 'compiled');
}
```

### Ключевые преимущества:
1. **Гибкость** - можно смешивать PHP-логику и PUG-шаблоны
2. **Производительность** - кэширование на нескольких уровнях
3. **Компонентный подход** - переиспользуемые UI-элементы
4. **Безопасность** - автоматическое экранирование переменных
5. **Масштабируемость** - поддержка сложных структур проектов

Такой подход особенно эффективен для:
- Проектов с динамическим интерфейсом
- Командной разработки (разделение логики и представления)
- Систем с часто изменяемым UI