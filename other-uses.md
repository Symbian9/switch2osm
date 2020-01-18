---
layout: page
title: Інші приклади використання
permalink: /other-uses/
---

Ми зосереджувались на тайлах, проте, OpenStreetMap – це унікальне джерело інформації – дає нам доступ до ‘сирих’ даних, які можна використовувати як для створення будь-якого застосунку з геопозиціюванням, так і для роботи з просторовими даними. Ось кілька речей для початку; повний перелік доступний в [OpenStreetMap Wiki](http://wiki.openstreetmap.org/wiki/Frameworks).

## Загальні інструменти

*   [Osmosis](http://wiki.openstreetmap.org/wiki/Osmosis) – універсальний застосунок Java для завантаження даних OSM в базу даних. Більшість застосунків певним чином використовують Osmosis для завантаження даних OSM з метою подальшого використання.
*   [Osmium](http://wiki.openstreetmap.org/wiki/Osmium) – гнучкий інструмент, що швидко набув відомості, пропонує багато різних налаштувань, є альтернативою Osmosis.
*   [Mapbox Studio](https://www.mapbox.com/mapbox-studio/) – збірка інструментів для створення ‘векторних тайлів’, які можуть використовуватись як на сервері, так і на клієнті.

## Служби геокодування

*   [Gisgraphy](https://www.gisgraphy.com) – геокодер з відкритими сирцями, який надає API/вебсервіси для прямого та зворотнього геокодування з автодоповненням, інтерполяцією, з відхиленням розташування, пошуком поруч, все це можна запускати як офлайн, так и у вигляді веб-служби. Він надає можливість імпортувати дані не тільки з OpenStreetMap, але й з Openadresses, Geonames та інших джерел.
*   [Nominatim](https://nominatim.org) – програмне забезпечення, що використовується для геокодінгу на сайті <https://openstreetmap.org/> (місце <--> координати).
*   [OpenCage](https://opencagedata.com/) – надає API для геокодінгу, що агрегує дані з Nominatim та інших відкритих джерел.
*   [OSMNames](https://osmnames.org/) – містить перелік місць з OpenStreetMap. Доступний для завантаження. Посортований. З описом територій (bbox) та ієрархій. Придатний для геокодування.

## Рушії та сервіси прокладання маршрутів

*   [OSRM](http://project-osrm.org/) – швидкий рушій прокладання маршрутів, який працює з даними з OSM.
*   [Gosmore](http://sourceforge.net/projects/gosmore/) – перевірений часом рушій прокладання маршуртів.
*   [Graphhopper](http://graphhopper.com/) – швидких рушій прокладання маршрутів написаний на Java.
*   Публічні API для прокладання маршрутів на основі даних OSM від [MapQuest Open](http://open.mapquestapi.com/directions/) та [Mapbox](https://www.mapbox.com/directions/).
*   Спеціалізовані API прокладання маршрутів, до яких належить [CycleStreets](https://www.cyclestreets.net/api/) (сервіс прокладання маршрутів для велосипедистів у Великій Британії та за її межами)

## Бібліотеки для векторних мап (mobile)

*   Бібліотеки для Android: [Mapbox Android SDK](https://www.mapbox.com/android-sdk/), [mapsforge](http://mapsforge.org/), [Nutiteq Maps SDK](https://developer.nutiteq.com/), [Skobbler Android SDK](http://developer.skobbler.com/) та [Tangram ES](https://github.com/tangrams/tangram-es/).
*   Бібліотеки для iOS: [Mapbox iOS SDK](https://www.mapbox.com/ios-sdk/), [Nutiteq Maps SDK](https://developer.nutiteq.com/), [Skobbler iOS SDK](http://developer.skobbler.com/) та [Tangram ES](https://github.com/tangrams/tangram-es/).

## Бібліотеки для векторних мап (Web)

*   [Kothic JS](https://github.com/kothic/kothic-js) – перетворює дані OSM в мапу "на ходу" використовуючи  HTML5, дозволяє відмовитись від створення растрових тайлів.
*   [Mapbox GL JS](https://www.mapbox.com/mapbox-gl-js/) та [Tangram](http://tangrams.github.io/tangram/) – працюють з векторними тайлами, створеними з даних OSM, використовуючи WebGL для покращення продуктивності.
