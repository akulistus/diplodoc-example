В директории src необходимо создать поддиректорию graph. Внутри graph необходимо создать файл `graph.js`.
В `graph.js` требуется импортировать необходимые элементы.
```js
import { OnlineVisualizatorProvider } from "webgraphicvisualizator";
import { SignalGenerator } from '../submodules/signal-generator-js/signal-generator';
import generatorConfig from './generator.json';
import VisualSettings from './VisualSettings.json';
```
В файл `graph.js` необходимо создать элемент Graph.
```js
// Создание экземпляров класса графического полотна и генератора сигналов
const generator = new SignalGenerator();
const visualizatorProvider = new OnlineVisualizatorProvider();

export const Graph = async (wrapRef, canvasRef) => {
  // Загрузка конфигурации генератора сигналов
  generator.setSettings(generatorConfig);
  generator.onFrameIsReady = message => {
    visualizatorProvider.pushMessage(message);
  }
  // Запуск генератора сигнала
  generator.start();

  // Очистка колбэков и загрузка конфигурации графического полотна
  visualizatorProvider.clearParserCallbacks();
  visualizatorProvider.init(VisualSettings);

  // Связывание графического полотна с DOM элементами обертки
  visualizatorProvider.addParserCallback("beforeSettingsParsing", null, () => {
    visualizatorProvider.setCanvasParentCallback(canvasRef, wrapRef);
  });

  // Запуск создания графического полтона по загруженной конфигурации
  await visualizatorProvider.create();

  // Установка калибровки для сигнала ЭКГ
  visualizatorProvider.setCalibration(undefinde, "ecg", [0], [1000], [0]);
};
```
В этом же файле можно создать функцию остановки генератора сигнала при выгрузке DOM компонента.
_(Ничего страшного не случится, если её не создавать, но в консоль будут валиться ошибки.)_
```js
export const destroy = () => {
  // Остановка генератора сигнала
  generator.stop();

  // Очистка канала отображения графического полотна
  visualizatorProvider.clearChannels(0);
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

  // Вызов функцию остановки перед удалением компонента
  onBeforeUnmount(() => destroy());
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