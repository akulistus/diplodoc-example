В директории с исходным кодом проекта необходимо создать `main.js` и импортировать необходимые элементы.
```js
import { OfflineVisualizatorProvider } from "webgraphicvisualizator";
import VisualSettings from './VisualSettings.json'
```
Для инициализации графика и генератора сигнала в файле `main.js` создается функция init.
```js
const init = async () => {
  // Получение ссылок на DOM элементы контейнера
  const wrapRef = document.getElementById("wrapRef");
  const canvasRef = document.getElementById("canvasWrap");

  // Создание экземпляра класса графического полотна
  const visualizatorProvider = new OfflineVisualizatorProvider();

  // Очистка колбэков и загрузка конфигурацию графического полотна
  visualizatorProvider.clearParserCallbacks();
  visualizatorProvider.init(VisualSettings);

  // Связывание графического полотна с DOM элементами обертки
  visualizatorProvider.addParserCallback("beforeSettingsParsing", null, () => {
    visualizatorProvider.setCanvasParentCallback(canvasRef, wrapRef);
  });

  // Запуск создания графического полтона по загруженной конфигурации
  await visualizatorProvider.create();

  // Создание тестовых данных для отображения на графическом полотне
  // Происходит генерация графика синуса с частотой 500Гц и амплитудоай 1000
  const data = Array.from({ length: 500 }, (_, index) => Math.sin(index * Math.PI / 250) * 1000);
  
  // Добавление сгенерированных данных на графическое полотно
  visualizatorProvider.addPoints(0, 0, 0, data);

  // Обновление графика
  visualizatorProvider.refreshGraphics();
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