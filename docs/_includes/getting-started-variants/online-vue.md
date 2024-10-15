В директории src необходимо создать поддиректорию graph. Внутри graph необходимо создать файл `graph.js`.
В `graph.js` требуется импортировать необходимые элементы.
```js
import { RawDataMessageParser, RawOnlineDataVisualizatorProvider } from "@incartdev/jagm-visualizator";
import { SignalGeneratorV1, GeneratorSettingsParser } from "@incartdev/signal-generator-js-core";
import generatorConfig from './generator.json';
import VisualSettings from './VisualSettings.json';
```
В файл `graph.js` необходимо создать элемент Graph.
```js
// Создание экземпляров класса графического полотна и генератора сигналов
const visProvider = new RawOnlineDataVisualizatorProvider();
const generator = new SignalGeneratorV1();

export const Graph = async (canvasRef) => {
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
В этом же файле можно создать функцию остановки генератора сигнала при выгрузке DOM компонента.
_(Ничего страшного не случится, если её не создавать, но в консоль будут валиться ошибки.)_
```js
export const destroy = () => {
  generator?.stop();
  generator = undefined;
  visProvider?.clearChannels(0);
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