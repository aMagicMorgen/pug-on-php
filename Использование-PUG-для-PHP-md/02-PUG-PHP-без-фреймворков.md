# Использование PUG в PHP без фреймворков

Вы можете использовать PUG в чистом PHP проекте без Laravel или Symfony, используя библиотеку `pug-php`. Вот подробное руководство:

## 1. Установка

Сначала установите библиотеку через Composer:

```bash
composer require pug-php/pug:^3.0
```

## 2. Базовое использование

### Рендеринг из строки:

```php
<?php
require 'vendor/autoload.php';

$pug = new Pug\Pug();

echo $pug->render('
h1= title
p Welcome to my PUG-powered PHP site
', [
    'title' => 'Hello World'
]);
?>
```

### Рендеринг из файла:

1. Создайте файл `views/template.pug`:
```pug
// views/template.pug
doctype html
html
  head
    title= pageTitle
  body
    h1= header
    if user.isLoggedIn
      p Welcome back, #{user.name}!
    else
      a(href="/login") Please log in
    ul
      each item in menu
        li: a(href=item.url)= item.name
```

2. Используйте в PHP:
```php
<?php
require 'vendor/autoload.php';

$pug = new Pug\Pug(['pretty' => true]);

$data = [
    'pageTitle' => 'My Site',
    'header' => 'Welcome!',
    'user' => [
        'isLoggedIn' => true,
        'name' => 'John Doe'
    ],
    'menu' => [
        ['url' => '/', 'name' => 'Home'],
        ['url' => '/about', 'name' => 'About'],
        ['url' => '/contact', 'name' => 'Contact']
    ]
];

echo $pug->renderFile('views/template.pug', $data);
?>
```

## 3. Продвинутые возможности

### Кэширование шаблонов

Для повышения производительности включите кэширование:

```php
$pug = new Pug\Pug([
    'cache' => 'cache/pug', // Директория для кэша
    'pretty' => true       // Красивый вывод HTML
]);
```

### Наследование шаблонов

1. Базовый шаблон (`views/layout.pug`):
```pug
doctype html
html
  head
    title= pageTitle
    block styles
  body
    block content
    block scripts
```

2. Дочерний шаблон (`views/home.pug`):
```pug
extends layout.pug

block content
  h1= header
  p This is the home page content

block scripts
  script(src="/js/home.js")
```

### Миксины (функции шаблонов)

```pug
//- Определение миксина
mixin listItem(name, url)
  li
    a(href=url)= name

//- Использование миксина
ul
  +listItem('Home', '/')
  +listItem('About', '/about')
```

## 4. Интеграция с чистым PHP проектом

Создайте простую систему роутинга:

```php
// index.php
require 'vendor/autoload.php';

$pug = new Pug\Pug(['cache' => 'cache/pug']);

$page = $_GET['page'] ?? 'home';

function renderPage($pug, $page, $data = []) {
    $template = "views/pages/{$page}.pug";
    if (file_exists($template)) {
        return $pug->renderFile($template, $data);
    }
    return $pug->renderFile('views/pages/404.pug');
}

$commonData = [
    'user' => [
        'isLoggedIn' => false
    ],
    'menu' => [...] // общие данные меню
];

echo renderPage($pug, $page, $commonData);
```

## 5. Альтернатива: PHUG

Если вам нужна более легковесная альтернатива, рассмотрите `phug/phug`:

```bash
composer require phug/phug
```

Пример использования:
```php
<?php
include 'vendor/autoload.php';

use Phug\Phug;

echo Phug::renderFile('template.pug', [
    'variable' => 'value'
]);
?>
```

## Советы по использованию

1. Организуйте шаблоны в папке `views` с подпапками для компонентов
2. Используйте миксины для повторяющихся элементов (хедеры, футеры)
3. Передавайте общие данные через единую точку входа
4. Включите кэширование в продакшн-окружении
5. Для дебага используйте `'debug' => true` в настройках Pug

Таким образом, вы можете эффективно использовать PUG в любом PHP-проекте без тяжелых фреймворков, получая все преимущества чистого и лаконичного синтаксиса шаблонов.