# Использование PUG (ранее Jade) в PHP

PUG (ранее известный как Jade) - это популярный шаблонизатор для HTML, который можно интегрировать с PHP. Вот как это сделать:

## Установка

1. Сначала установите Node.js, так как PUG требует его для работы.
2. Установите PUG через npm:
```bash
npm install pug-cli -g
```

## Основные способы использования PUG с PHP

### 1. Компиляция PUG в PHP-файлы

Вы можете компилировать PUG-файлы в HTML, который затем будет использоваться в PHP:

```bash
pug ваш-файл.pug -o php/views/ --pretty
```

Затем в PHP просто включаете сгенерированный HTML:
```php
<?php include 'views/ваш-файл.html'; ?>
```

### 2. Использование пакета pug-php

Установите пакет pug-php через Composer:
```bash
composer require pug-php/pug:^3.0
```

Пример использования:
```php
<?php
require 'vendor/autoload.php';

$pug = new Pug\Pug();

// Рендеринг строки
echo $pug->render('h1= title', ['title' => 'Hello World']);

// Рендеринг файла
echo $pug->renderFile('template.pug', ['name' => 'John']);
?>
```

### 3. Интеграция с фреймворками

#### Для Laravel:
Установите пакет:
```bash
composer require bkwld/pug
```

Затем зарегистрируйте сервис-провайдер в `config/app.php`:
```php
'providers' => [
    // ...
    Bkwld\Pug\PugServiceProvider::class,
];
```

#### Для Symfony:
Используйте пакет `phug/phug`:
```bash
composer require phug/phug
```

## Пример PUG-шаблона

`template.pug`:
```pug
doctype html
html
  head
    title= pageTitle
  body
    h1= header
    if user.isLoggedIn
      p Добро пожаловать, #{user.name}!
    else
      p Пожалуйста, войдите.
```

## Пример использования в PHP

```php
<?php
require 'vendor/autoload.php';

$pug = new Pug\Pug();

$data = [
    'pageTitle' => 'Мой сайт',
    'header' => 'Главная страница',
    'user' => [
        'isLoggedIn' => true,
        'name' => 'Иван'
    ]
];

echo $pug->renderFile('template.pug', $data);
?>
```

## Преимущества использования PUG с PHP

1. Чистый, лаконичный синтаксис
2. Поддержка миксинов и наследования шаблонов
3. Автоматическое экранирование HTML
4. Поддержка условий и циклов
5. Лучшая читаемость по сравнению с чистым HTML в PHP

PUG может значительно ускорить разработку фронтенд-части вашего PHP-приложения.
