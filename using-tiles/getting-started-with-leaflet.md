---
layout: docs
title: Використання Leaflet
permalink: /using-tiles/getting-started-with-leaflet/
---

# Вступ
[Leaflet](http://leafletjs.com/)&nbsp;– легковісна бібліотека JavaScript для показу мап. Вона розповсюджується на умовах дозвільної Ліцензії BSD для коду з відкритими сирцями, тож її можна використовувати на будь-якому сайті без побоювань порушення юридичних норм. Сирці бібліотеки доступні на [GitHub](http://github.com/Leaflet/Leaflet).

Тут, ми обмежимо себе невеличким самодостатнім прикладом, що демонструє можливості бібліотеки. Радимо ознайомитись з детальними [прикладами](http://leafletjs.com/examples.html) та [документацією](http://leafletjs.com/reference.html) з офіційного сайта для більш детального опрацювання.

# Початок роботи
Перенесіть наступний код у файл, наприклад `leaflet.html`, і відкрийте його у вашому веб-оглядачі або перейдіть за посиланням щоб відкрити файл [leaflet.html]({{site.baseurl}}/assets/leaflet.html):

{% highlight html %}
{% include leaflet.html %}
{% endhighlight %}

Докладні пояснення щодо коду дивіться на офіційному сайті. [^1]

# Додаткові посилання
Якщо ви бажаєте:

*   використовувати інше тло?&nbsp;→ Leaflet має підтримку [TMS](https://en.wikipedia.org/wiki/Tile_Map_Service) та [WMS](https://uk.wikipedia.org/wiki/Web_Map_Service). Подивіться [тут](http://leafletjs.com/reference.html#tilelayer), які інші можливості має Leaflet.
*   додати розташування всіх офісів вашої компанії?&nbsp;→ Збережіть координати у файл [GeoJSON](http://geojson.org/) та [додайте їх](http://leafletjs.com/examples/geojson.html) на мапу.
*   використовувати іншу картографічну проєкцію?&nbsp;→ Втулок [Proj4Leaflet](https://github.com/kartena/Proj4Leaflet) допоможе вам в цьому.

----

[^1]: Швидкий старт&nbsp;– <https://leafletjs.com/examples/quick-start/>
