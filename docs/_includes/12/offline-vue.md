В директории src необходимо создать поддиректорию graph. Внутри graph необходимо создать файл `graph.js`.
В `graph.js` требуется импортировать необходимые элементы.
```js
import { OfflineVisualizatorProvider } from "webgraphicvisualizator";
import VisualSettings from './VisualSettings.json';
```
В файл `graph.js` необходимо создать элемент Graph.
```js
// Создание экземпляров класса графического полотна и генератора сигналов
const visualizatorProvider = new OfflineVisualizatorProvider();

export const Graph = async (wrapRef, canvasRef) => {

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