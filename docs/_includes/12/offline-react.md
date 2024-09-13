В директории src необходимо создать поддиректорию graph. Внутри graph необходимо создать файл `graph.jsx`.
В `graph.jsx` требуется импортировать необходимые элементы.
```js
import React, { useRef, useEffect } from 'react';
import { OfflineVisualizatorProvider } from 'webgraphicvisualizator';
import VisualSettings from "./VisualSettings.json";
```
В файл `graph.jsx` необходимо создать элемент Graph.
```js
const Graph = () => {
  const canvasWrapRef = useRef(null);
  useEffect(() => {
    const install = () => {

      // Создание экземпляра класса графического полотна
      const visualizatorProvider = new OfflineVisualizatorProvider();
      
      // Очистка колбэков и загрузка конфигурацию графического полотна
      visualizatorProvider.clearParserCallbacks();
      visualizatorProvider.init(VisualSettings);
      
      // Связывание графического полотна с DOM элементами обертки
      visualizatorProvider.addParserCallback("beforeSettingsParsing", null, () => {
        visualizatorProvider.setCanvasParentCallback(canvasWrapRef);
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