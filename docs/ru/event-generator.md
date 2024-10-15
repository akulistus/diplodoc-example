# Модуль генератора событий
Данный модуль используется в примерах для моделирования работы графического полотна с сигналами,
изменяющимися в реальном времени. Данный модуль имитирует отправку данных о события в реальном времени.

Исходный код модуля располагается по [ссылке](https://github.com/IncartDev/signal-generator-js).

## Установка

Модуль устанавливается как git submodule. Рекомендуется производить установку в директорию `src/submodules`.

```sh
  $ git submodule add git@github.com:IncartDev/signal-generator-js.git
```

## Файл конфигурации
Файл конфигурации требуется для установки режимов работы генератора событий. Конфигурация описывается в файле формата json
и состоит из двух основных свойств.
* `interval` - \{**int > 0**\} - интервал отправки данных о событии в мс.
* `marks` - \{**array**\} - содержит описания параметров меток, которые будут генерироваться.
```json
{
    "interval": 1000,
    "marks": []
}
```
### Свойство marks
```json
{
    "marks": [
        {
            "id": 0,
            "name": "RR",
            "freq": 1,
            "type": "int",
            "min_value": 1000,
            "max_value": 1001,
        }
    ]
}
```
* `id` - \{**int**\} уникальный идентификатор события.
* `name` - \{**string**\} уникальное? имя события. Используется для привязки события генератора событий к источникам данных графического полотна.
* `freq` - \{**int**\} частота событий за интервал?. **НЕ РАБОТАЕТ**
* `type` - \{**"int"**\} - тип генерирующейся метки.
* `min_value` - \{**int**\} - минимальное значение генерирующегося события.
* `max_value` - \{**int**\} - максимальное значение генерирующегося события.

{% note info %}

  [Пример](https://github.com/IncartDev/signal-generator-js/blob/typescript/EventGeneratorSettingsExample.json) полной конфигурации.

{% endnote %}

## Подключение генератора сигналов
Импорт и инициализацию генератора сигнала рекомендуется производить в файл инициализации графического плотна.
```js
// Импорт объекта генератора сигнала.
import { SignalGenerator } from "<путь до подмодуля>/signal-generator-js/signal-generator";
import { EventGenerator } from "<путь до подмодуля>/signal-generator-js/event-generator";

// Внутри функции инициализации графического полотна
{
  // Создание экземпляра класса генератора сигналов
  const eventGenerator = new EventGenerator();
  const generator = new SignalGenerator();

  // Загрузка конфигурации генератора сигналов
  eventGenerator.setSettings(eventGeneratorSettings);
  generator.onFrameIsReady = message => {
    let pointIndex = generator.getChannelPointCounter(0, 0);
    eventGenerator.setPointIndex(pointIndex);
    visualizatorProvider.pushMessage(message);
  }

  eventGenerator.onFrameIsReady = message => {
     visualizatorProvider.pushMessage(message);
  };

  // Запуск генератора сигнала
  generator.start();
  eventGenerator.start();
}
```

{% note info %}

  [Пример](/examples/text_marks)использования генератора событий.

{% endnote %}

## Методы
* `setSettings(settings: string)` - метод, загружающий конфигурацию в созданный ранее класс.
* `onFrameIsReady(handler: func)` - эвент, срабатывающий в момент готовности блока данных. Эвент передаёт подготовленный блок данных в хэндлер.
* `setPointIndex(index: int)` - метод, устанавливающий координату события на графическом полотне.
* `start()` - запуск генерации сигналов.
* `stop()` - завершение генерации сигналов.