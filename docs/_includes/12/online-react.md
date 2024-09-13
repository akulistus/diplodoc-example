В директории src необходимо создать поддиректорию graph. Внутри graph необходимо создать файл `graph.jsx`.
В `graph.jsx` требуется импортировать необходимые элементы.
```js
import React, { useRef, useEffect } from 'react';
import { OnlineVisualizatorProvider } from 'webgraphicvisualizator';
import { SignalGenerator } from '../submodules/signal-generator-js/signal-generator';
import generatorConfig from "./generator.json";
import VisualSettings from "./VisualSettings.json";
```
В файл `graph.jsx` необходимо создать элемент Graph.
```js
const Graph = () => {
  const canvasWrapRef = useRef(null);
  useEffect(() => {
    const install = () => {

      // Создание экземпляров класса графического полотна и генератора сигналов
      const visProvider = new OnlineVisualizatorProvider();
      const generator = new SignalGenerator();

      // Очистка колбэков и загрузка конфигурацию графического полотна
      visProvider.clearParserCallbacks();
      visProvider.init(visualizatorSettings);

      // Загрузка конфигурации генератора сигналов
      generator.setSettings(SignalGenerator);
      generator.onFrameIsReady = message => {
        visualizatorProvider.pushAdcMessage(message, 1372205);
        // visualizatorProvider.pushMessage(message);
      }
      // Запуск генератора сигнала
      generator.start();

      // Связывание графического полотна с DOM элементом обертки
      visProvider.addParserCallback("beforeSettingsParsing", null, () => {
        visProvider.setCanvasParentHtmlObject(canvasWrapRef.current);
      });

      // Запуск создания графического полотна по загруженной конфигурации
      visProvider.create();

      // Установка калибровки для сигнала ЭКГ
      visProvider.setCalibration(undefined, "ecg", [0], [1000], [0]);
    };

    install();
  }, []);
};
```
Созданный элемент необходимо импортировать в `main.jsx` и добавить в рендер.
```jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import Graph from './src/graph/graph.jsx'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <Graph />
  </React.StrictMode>,
);
```