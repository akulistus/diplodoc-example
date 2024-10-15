В директории src необходимо создать поддиректорию graph. Внутри graph необходимо создать файл `graph.jsx`.
В `graph.jsx` требуется импортировать необходимые элементы.
```js
import React, { useRef, useEffect } from 'react';
import { RawDataMessageParser, RawOnlineDataVisualizatorProvider } from "@incartdev/jagm-visualizator";
import { SignalGeneratorV1, GeneratorSettingsParser } from "@incartdev/signal-generator-js-core";
import generatorConfig from "./generator.json";
import VisualSettings from "./VisualSettings.json";
```
В файл `graph.jsx` необходимо создать элемент Graph.
```js
const Graph = () => {
  const canvasRef = useRef(null);
  useEffect(() => {
    const install = () => {
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
      visProvider.setParentHtml(canvasRef.current);

      await visProvider.create();

      // Установка калибровки для сигнала ЭКГ
      visProvider.setCalibration(undefined, "ecg", [0], [1000], [0]);

      // Запуск генератора сигнала
      generator.start();
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