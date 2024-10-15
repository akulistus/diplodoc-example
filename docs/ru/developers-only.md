# Для разработчиков
[Документация](../_assets/api/index.html) API.

> Дальше устарело
## Подключение графических библиотек

Для упрощения разработки рекомендуется подключать графические библиотеки в виде git-подмодулей.
Выполнение этих команд рекомендуется делать из папки: <b>&lt;project-path&gt;/src/submodules</b>

```sh
$ git submodule add git@github.com:IncartDev/web-graphics.git
$ git submodule add git@github.com:IncartDev/web-visualizator.git
$ git submodule add git@github.com:IncartDev/visualizator-common-components.git
$ git submodule add git@github.com:IncartDev/jsfilters.git
```
Далее, в файл <b>package.json</b> в <b>dependencies</b> необходимо добавить следующие строки:

```json
"web-graphics": "file:./src/submodules/web-graphics",
"web-visualizator": "file:./src/submodules/web-visualizator",
"visualizator-common-components": "file:./src/submodules/visualizator-common-components",
"jsfilters": "file:./src/submodules/jsfilters"
```

***Для обновления подмодулей можно использовать команду (из папки: &lt;project-path&gt;):
git submodule update --remote --merge
При изменении содержимого библиотек, настроенных на работу с typescript (сейчас это jsfilters и
visualizator-common-components),
следует использовать команду npx tsc (из папки
&lt;project-path&gt;/src/submodules/&lt;submodule-name&gt;).
Эта команда собирает из исходных файлов на typescript/javascript файлы на javascript (.js) + файлы с описанием
типов (.d.ts),
которые уже непосредственно используются другими модулями.***

## Запустить это приложение c примерами

```sh
$ git clone --recurse-submodules git@github.com:IncartDev/web-graphics-doc.git
$ npm install
$ npm run dev
```
или

```sh
$ git clone git@github.com:IncartDev/web-graphics-doc.git
$ cd online-visualizator-example
$ git submodule update --init --recursive
$ npm install
$ npm run dev
```

## Обновить субмодули

```sh
$ git submodule update --remote --merge
```