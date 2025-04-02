библиотека PHUG в php проекте требует подключение еще каких то библиотек или нет?

Библиотека **PHUG** для работы в PHP-проекте требует минимальные зависимости, но есть нюансы в зависимости от ваших потребностей. Вот детальный разбор:

---

### **1. Минимальная установка (базовый рендеринг)**
Достаточно только основного пакета:
```bash
composer require phug/phug
```
**Что включено:**
- Сам движок PHUG
- Базовый рендерер Pug-шаблонов
- Поддержка синтаксиса Pug 3.x

**Что НЕ включено:**
- Кэширование
- Поддержка расширений (фильтры, макросы)
- Интеграция с фреймворками

---

### **2. Рекомендуемая установка (для production)**
Для оптимальной работы добавьте:
```bash
composer require phug/phug phug/ast phug/compiler phug/dependency-injection phug/event phug/filter phug/formatter phug/lexer phug/parser phug/reader phug/renderer phug/util
```
Или одной командой:
```bash
composer require phug/phug:^2.0 phug/*
```

**Зачем столько пакетов?**
- `phug/ast` - оптимизация AST-дерева
- `phug/compiler` - компиляция в PHP-код
- `phug/dependency-injection` - DI-контейнер
- `phug/filter` - поддержка фильтров (`:markdown`, `:scss` и т.д.)

---

### **3. Дополнительные зависимости**
**Если нужно:**
- **Кэширование** → `symfony/cache` (рекомендуется)
- **Markdown-поддержка** → `michelf/php-markdown`
- **SCSS-компиляция** → `scssphp/scssphp`
- **Интеграция с Twig** → `phug/twig-adapter`

Пример установки с markdown-поддержкой:
```bash
composer require phug/phug michelf/php-markdown
```

---

### **4. Проверка зависимостей**
PHUG автоматически подтянет только **обязательные** зависимости:
- `js-phpize` (для JS-подобных выражений в шаблонах)
- `pug-php` (альтернативный рендерер)

Остальные пакеты требуют ручной установки.

---

### **5. Пример минимального рабочего кода**
```php
<?php
require 'vendor/autoload.php';

use Phug\Phug;

echo Phug::render('h1= title', ['title' => 'Hello PHUG!']);

// Или из файла
echo Phug::renderFile('template.pug', ['name' => 'World']);
```
Файл `template.pug`:
```pug
//- template.pug
doctype html
html
  body
    h1 Hello #{name}!
```

---

### **6. Важные нюансы**
1. **Кэширование**  
   Без `symfony/cache` шаблоны компилируются при каждом запросе (медленно для production).

2. **Файловая система**  
   Для `renderFile()` нужны права на чтение файлов.

3. **Безопасность**  
   PHUG по умолчанию экранирует HTML в переменных (аналог `htmlspecialchars`).

4. **Производительность**  
   В production включите кэш и прекомпиляцию:
   ```php
   Phug::setOption('cache_dir', 'path/to/cache');
   ```

---

### **Вывод**
- **Для тестов/демо** → достаточно `phug/phug`
- **Для production** → установите `phug/*` + `symfony/cache`
- **Для специфических задач** (Markdown/SCSS) → добавьте соответствующие пакеты

Библиотека разработана как модульная, поэтому вы можете подключать только то, что действительно нужно вашему проекту.