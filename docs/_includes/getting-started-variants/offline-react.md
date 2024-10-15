Для данного примера необходимо установить дополнительную зависимость:
```sh
$ npm install @incartdev/jagm-chart
```
Из данного пакета нам потребуется структура данных для отображения отсчетов сигнала на графике.

В директории src необходимо создать поддиректорию graph. Внутри graph необходимо создать файл `graph.jsx`.
В `graph.jsx` требуется импортировать необходимые элементы.
```js
import React, { useRef, useEffect } from 'react';
import { JagmLineSignalData } from "@incartdev/jagm-chart";
import { RawOfflineDataVisualizatorProvider } from "@incartdev/jagm-visualizator";
import VisualSettings from "./VisualSettings.json";
```
В файл `graph.jsx` необходимо создать элемент Graph.
```js
const Graph = () => {
  const canvasRef = useRef(null);
  useEffect(() => {
    const install = () => {
      // Создание тестовых данных для отображения на графическом полотне
      // Происходит генерация графика синуса с частотой 500Гц и амплитудоай 1000
      const plotData = Array.from({ length: 5000 }, (_, index) => Math.sin((index * Math.PI) / 250) * 1000);
      const signalData = new JagmLineSignalData(plotData);

      // Создать экземпляр класса для рисования графиков
      visProvider = new RawOfflineDataVisualizatorProvider();

      // Проинициализировать библиотеку конфигурацией и запустить её
      visProvider.init(VisualSettings);
      visProvider.setParentHtml(canvasRef.current);

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