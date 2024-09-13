# Быстрый старт
* [Online-график](#online-график)
* [Offline-график](#offline-график)
* [Pauseable-график](#pauseable-график)

В данной статье демонстрируется процесс создания простейшего [примера](/examples/simple) online-графика.
В данном статье используется сборщик [Vite](https://vitejs.dev).

## Создание проекта
Для создания vite-проекта потребуется выполнить следующие команды в CLI.
{% list tabs %}
  - JavaScript
      ```sh
      $ npm create vite@latest my-app --template vanilla my-app
      $ cd my-app

      $ npm install
      $ npm run dev
      ```

  - React
      ```sh
      $ npm create vite@latest my-app --template react my-app
      $ cd my-app

      $ npm install
      $ npm run dev
      ```

  - Vue
      ```sh
      $ npm create vite@latest my-app --template vue my-app
      $ cd my-app

      $ npm install
      $ npm run dev
      ```
{% endlist %}

### Установка библиотеки
Для того, чтобы установить библиотеку, необходимо выполнить следующую команду в CLI.
```sh
$ npm install git@github.com:IncartDev/WebGraphicVisualizator.git#v0.1.0
```

{% note info %}

  Подробнее об установке в разделе [УСТАНОВКА](/install)

{% endnote %}

## Online-график

### Конфигурация графика
Для настройки отображения графика необходимо создать файл конфигурации.

{% note info %}

  Подробнее о файле конфигурации в разделе [Файл конфигурации](fundamentals/config-file)

{% endnote %}

В директории с исходным кодом проекта необходимо создать файл `VisualSettings.json`. Далее, необходимо [скопировать](/examples/simple) конфигурацию графика в файл `VisualSettings.json`.

### Настройка генератора сигналов
Online-график &mdash; график, отображающий сигнал, изменяющийся в реальном времени. Для отображения будет использован демонстрационный сигнал, генерирующийся в реальном времени
с помощью генератора сигнала. Генератор сигнала также требуется сконфигурировать в отдельном файле конфигурации. В данном примере не рассматривается вариант создания конфигурации с нуля.

Для настройки генератора сигналов в директории с исходным кодом проекта необходимо создать файл `generator.json`. Далее, необходимо [скопировать](/examples/simple) конфигурацию генератора сигналов в файл `generator.josn`.

### Инициализация графика
{% list tabs %}

  - React

    {% include [test](../_includes/12/online-react.md) %}
  - Vue

    {% include [test](../_includes/12/online-vue.md) %}  
  - JavaScript

    {% include [test](../_includes/12/online-javascript.md) %}  
  
{% endlist %}

## Offline-график

### Конфигурация графика
Для настройки отображения графика необходимо создать файл конфигурации.

{% note info %}

  Подробнее о файле конфигурации в разделе [Файл конфигурации](fundamentals/config-file)

{% endnote %}

В директории с исходным кодом проекта необходимо создать файл `VisualSettings.json`. Далее, необходимо [скопировать](/examples/offline_simplefundamentals/config-file) конфигурацию графика в файл `VisualSettings.json`.

### Инициализация графика

## Pauseable-график

> Статья в разработке