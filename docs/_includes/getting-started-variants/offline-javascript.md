Для данного примера необходимо установить дополнительную зависимость:
```sh
$ npm install @incartdev/jagm-chart
```
Из данного пакета нам потребуется структура данных для отображения отсчетов сигнала на графике.

В директории с исходным кодом проекта необходимо создать `main.js` и импортировать необходимые элементы.
```js
import { JagmLineSignalData } from "@incartdev/jagm-chart";
import { RawOfflineDataVisualizatorProvider } from "@incartdev/jagm-visualizator";
import VisualSettings from './VisualSettings.json'
```
Для инициализации графика и генератора сигнала в файле `main.js` создается функция init.
```js
const init = async () => {
  // Получение ссылок на DOM элементы контейнера
  const canvasRef = document.getElementById("canvasWrap");

  // Создать экземпляр класса для рисования графиков
  visProvider = new RawOfflineDataVisualizatorProvider();

  // Создание тестовых данных для отображения на графическом полотне
  // Происходит генерация графика синуса с частотой 500Гц и амплитудоай 1000
  const plotData = Array.from({ length: 5000 }, (_, index) => Math.sin((index * Math.PI) / 250) * 1000);
  const signalData = new JagmLineSignalData(plotData);

  // Проинициализировать библиотеку конфигурацией и запустить её
  visProvider.init(VisualSettings);
  visProvider.setParentHtml(canvasRef);

  await visProvider.create();
  
  // Добавление сгенерированных данных на графическое полотно
  const firstPlot = visProvider.getFirstPlot();
  if (firstPlot !== undefined) {
    firstPlot.addEventListener("BufferSizeChanged", async () => {
      visProvider?.addPoints(0, 0, 0, signalData);
    });
  }
  visProvider.addPoints(0, 0, 0, signalData);

  // Обновление графика
  visProvider.refreshGraphics();
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