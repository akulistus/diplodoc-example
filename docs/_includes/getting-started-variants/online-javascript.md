В директории с исходным кодом проекта необходимо создать `main.js` и импортировать необходимые элементы.
```js
import { RawDataMessageParser, RawOnlineDataVisualizatorProvider } from "@incartdev/jagm-visualizator";
import { SignalGeneratorV1, GeneratorSettingsParser } from "@incartdev/signal-generator-js-core";
import generatorConfig from "./generator.json";
import VisualSettings from './VisualSettings.json'
```
Для инициализации графика и генератора сигнала в файле `main.js` создается функция init.
```js
const init = async () => {
  // Получение ссылок на DOM элементы контейнера
  const canvasRef = document.getElementById("canvasWrap");

  // Создание экземпляров класса графического полотна и генератора сигналов
  const visProvider = new RawOnlineDataVisualizatorProvider();
  const generator = new SignalGeneratorV1();

  // Загрузка конфигурации генератора сигналов
  const generatorSettings = GeneratorSettingsParser.parse(generatorConfig);
  if (generatorSettings === undefined) {
    return;
  }

  generator.init(generatorSettings);
  generator.onFrameIsReady = message => {
    if (visProvider === undefined) {
      return;
    }
    const typedMessage = RawDataMessageParser.parse(message);
    if (typedMessage === undefined) {
      return;
    }
    visProvider.pushMessage(typedMessage);
  };

  // Проинициализировать библиотеку конфигурацией и запустить её
  visProvider.init(VisualSettings);
  visProvider.setParentHtml(canvasRef);

  await visProvider.create();

  // Установка калибровки для сигнала ЭКГ
  visProvider.setCalibration(undefined, "ecg", [0], [1000], [0]);

  // Запуск генератора сигнала
  generator.start();
};
```
В файл `index.html` необходимо добавить следущие элементы.

{% note warning %}

  Важно отметить, что контейнер **НЕ** должен быть нулевой высоты.

{% endnote %}

```html
<div class="vis-wrap" id="wrapRef">
    <div class="canvas-wrap" id="canvasWrap"/>
</div>

<script type="module">
  import { main } from './components/main.js'
  document.addEventListener("DOMContentLoaded", () => {
    main();
  });
</script>

<style>
  .canvas-wrap {
  height: 100%;
  width: 100%;
  position: absolute;
  }
  .vis-wrap {
    position: relative;
    height: 500px;
    width: 500px;
  }
</style>
```
{% note warning %}

  Важно отметить, что у script должен быть прописан type=module.

{% endnote %}