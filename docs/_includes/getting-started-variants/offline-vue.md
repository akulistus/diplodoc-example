Для данного примера необходимо установить дополнительную зависимость:
```sh
$ npm install @incartdev/jagm-chart
```
Из данного пакета нам потребуется структура данных для отображения отсчетов сигнала на графике.

В директории src необходимо создать поддиректорию graph. Внутри graph необходимо создать файл `graph.js`.
В `graph.js` требуется импортировать необходимые элементы.
```js
import { JagmLineSignalData } from "@incartdev/jagm-chart";
import { RawOfflineDataVisualizatorProvider } from "@incartdev/jagm-visualizator";
import VisualSettings from './VisualSettings.json';
```
В файл `graph.js` необходимо создать элемент Graph.
```js
// Создать экземпляр класса для рисования графиков
visProvider = new RawOfflineDataVisualizatorProvider();

export const Graph = async (canvasRef) => {
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
};
```

В этой же директории необходимо создать файл `Graph.vue`.
```html
<script setup>
  // Импорт созданной функции и необходимых компонентов
  import { init, destroy } from './graph.js';
  import { onBeforeUnmount, onMounted, ref } from 'vue';

  // Создание реактивных ссылок на DOM элементы
  const wrapRef = ref(null);
  const canvasRef = ref(null);

  // Вызов функции инициализации при монтировании компонента
  onMounted(() => init(wrapRef.value, canvasRef.value));
</script>

<template>
  <div class="vis-wrap" ref="wrapRef">
    <div class="canvas-wrap" ref="canvasRef"></div>
  </div>
</template>

<style scoped>
  /* Стили для контейнера canvas */
  .canvas-wrap {
    height: 200px; /* Высота canvas */
    /* width: 100%; */ /* Ширина canvas */
    position: relative; /* Относительное позиционирование */
  }

  /* Стили для обертки визуализации */
  .vis-wrap {
    position: relative; /* Относительное позиционирование */
    height: 200px; /* Высота обертки */
    width: 1000px; /* Ширина обертки */
  }
</style>
```
В файле `main.js` требуется закомментировать строку с импортом стилей и произвести импорт созданного элемента графического полотна.
```js
import { createApp } from 'vue';
import Graph from './graph/graph.vue';

createApp(Graph).mount('#app');
```