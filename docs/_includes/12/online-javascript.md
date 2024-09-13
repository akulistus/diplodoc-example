В директории с исходным кодом проекта необходимо создать `main.js` и импортировать необходимые элементы.
```js
import { OnlineVisualizatorProvider } from "webgraphicvisualizator";
import { SignalGenerator } from '../submodules/signal-generator-js/signal-generator';
import generatorConfig from "./generator.json";
import VisualSettings from './VisualSettings.json'
```
Для инициализации графика и генератора сигнала в файле `main.js` создается функция init.
```js
const init = async () => {
  // Получение ссылок на DOM элементы контейнера
  const wrapRef = document.getElementById("wrapRef");
  const canvasRef = document.getElementById("canvasWrap");

  // Создание экземпляров класса графического полотна и генератора сигналов
  const visualizatorProvider = new OnlineVisualizatorProvider();
  const generator = new SignalGenerator();

  // Загрузка конфигурации генератора сигналов
  generator.setSettings(SignalGenerator);
  generator.onFrameIsReady = message => {
    visualizatorProvider.pushAdcMessage(message, 1372205);
    // visualizatorProvider.pushMessage(message);
  }
  // Запуск генератора сигнала
  generator.start();

  // Очистка колбэков и загрузка конфигурацию графического полотна
  visualizatorProvider.clearParserCallbacks();
  visualizatorProvider.init(VisualSettings);

  // Связывание графического полотна с DOM элементами обертки
  visualizatorProvider.addParserCallback("beforeSettingsParsing", null, () => {
    visualizatorProvider.setCanvasParentCallback(canvasRef, wrapRef);
  });

  // Запуск создания графического полтона по загруженной конфигурации
  await visualizatorProvider.create();

  // Установка калибровки для сигнала ЭКГ
  visualizatorProvider.setCalibration(undefined, "ecg", [0], [1000], [0]);

  // Обновление графика
  setTimeout(() => {
      visualizatorProvider.refreshGraphics();
  }, 0);
};
```
В файл `index.html` необходимо добавить следущие элементы.
\
\
{% note info %}

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
{% note alert %}

  Важно отметить, что у script должен быть прописан type=module.

{% endnote %}