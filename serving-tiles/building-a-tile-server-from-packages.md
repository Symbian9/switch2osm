---
layout: docs
title: Розгортання сервера з пакунків
permalink: /serving-tiles/building-a-tile-server-from-packages/
---

Якщо вам треба створити власний тайловий сервер, але ви користуєтесь старішою версією Ubuntu, використання пакунків, що вже є в системі, дозволить спростити процес встановлення.

Пакунки, про які йдеться далі, працюють для Ubuntu Linux від версії 12.04 LTS (Precise Pangolin) до 14.04 LTS (Trusty Tahr). Після встановлення, ви матиме власний тайловий сервер зі стандартним стилем OpenStreetMap, до якого можна імпортувати дані OSM для створення тайлів.

Сервер побудовано на такому ж програмному забезпеченні, що й тайловий сервер OpenStreetMap:

*   mod_tile&nbsp;– для обслуговування запитів
*   renderd&nbsp;– фонова служба генерації тайлів
*   mapnik&nbsp;– інструмент, що й генерує тайли

Основна перевага використання пакунків&nbsp;– спрощення процесу встановлення, через використання сценаріїв автоматизації менеджера пакунків.

# Встановлення

Всі наступні команди потрібно виконувати в терміналі:

Якщо у вас не встановлено `add-apt-repository`, встановіть цей пакунок:

Ubuntu 12.04

```sh
sudo apt-get install python-software-properties
```

Ubuntu 12.10 та пізніше

```sh
sudo apt-get install software-properties-common
```

Додайте репозиторій з пакунками:

```sh
sudo add-apt-repository ppa:kakrueger/openstreetmap
```

Оновіть перелік доступних в системі пакунків:

```sh
sudo apt-get update
```

Встановіть пакунок `libapache2-mod-tile` разом з залежностями. Під час налаштування, система встановлення поставить вам кілька питань. Щоб бути впевненим, що сценарій автоматичного встановлення працює як треба, залишайте запропоновані типові відповіді. Однак, у відповідь на запитання щодо встановлення дозволів на доступ до бази даних, ви можете додати себе, через пробіл, після користувача www-data, щоб мати змогу імпортувати дані використовуючи свій обліковий запис.

```sh
sudo apt-get install libapache2-mod-tile
```

# Імпорт даних мапи

Завантажте дані OpenStreetMap, на основі яких ви збираєтесь робити тайли (повний файл з даними для всієї планети знаходиться на <https://planet.openstreetmap.org/>, дані розділені на країни та великі території&nbsp;– дивіться на <https://download.geofabrik.de/>), наприклад так:

```sh
wget https://download.geofabrik.de/europe/ireland-and-northern-ireland-latest.osm.pbf
```

Перенесіть дані у вашу базу postgresql за допомогою osm2pgsql. Існує кілька різних параметрів, які можна використовувати з osm2pgsql, в залежності від наявного обладнання та розміру даних, які ви хочете імпортувати. Ймовірно, вам знадобляться `-C`, `--slim` та `--number-processes`. `-C`&nbsp;– зазначає обсяг оперативної пам’яті в мегабайтах (Мб), що буде використовуватись osm2pgsql для кешування даних. Тож він залежить від доступної вам оперативки. `--slim`&nbsp;– для збереження всіх даних OSM на диску, потрібно для подальшого оновлення даних та у випадках невеликого обсягу оперативної пам’яті. `--number-processes`&nbsp;– визначає кількість паралельних процесів, які використовуються для деяких частин процесу імпорту. Оптимальне значення, здебільше, залежить від швидкості вашої дискової системи та наявних процесорних ядер.

```sh
osm2pgsql --slim -C 1500 --number-processes 4 ireland-and-northern-ireland.osm.pbf
```

Залежно від розміру даних, які ви імпортуєте, та потужності комп’ютера, це може тривати від декількох хвилин, для невеликих за обсягом даних, до декількох днів, на повільному апаратному забезпеченні, для всієї планети! Якщо ви імпортуєте планету, рекомендується встановити `-C 18000` (18 Гб оперативної пам’яті під кеш і може більше, в міру зростання бази даних OSM). Це може призвести до інтенсивного використання swap на вашому сервері під час імпорту, якщо у вас немає достатньої кількості оперативної пам’яті, але в багатьох випадках це все одно буде швидше, ніж використання менших значень для кешу точок (Node). Однак вам потрібно переконатися, що у вас достатній розмір swap.

У разі імпорту файлу планети, можливо вам знадобиться параметр `--flat-nodes`. Він використовує спеціальний формат файлів на відміну від стандартного для баз даних postgis, що покращує ефективність, але він не даватиме ефекта для регіональних даних. Є сенс тимчасово змінити налаштування вашого сервера PostgreSQL на час імпорту. (Наприклад, збільшити кількість контрольних точок, зменшити розмір буферу.)

Багато часу імпорту, а також місця в базі даних, йде на створення індексів для відстежування оновлень. Якщо ви не плануєте постійно оновлювати базу даними з diff-файлів, ви можете імпортувати їх з опцією `--drop`, яка після імпорту вилучить таблиці “slim”, не потрібні для створення тайлів, та не буде створювати індекси, які використовуються лише для підтримки оновлення даних за допомогою diff-файлів. Якщо ви не оновлюєте дані регулярно, десь раз на 1&nbsp;– 2 тижні, може бути ефективнішим робити повний реімпорт кожного разу, ніж оновлення даних.

mod_tile – призначений для підтримання тайлів в актуальному стані (дивіться про оновлення даних нижче). Оскільки, як правило, неможливо перегенерувати всі змінені тайли під час зміни даних, mod_tile ініціює повторну генерацію застарілих тайлів під час отримання запитів. Тож mod_tile має знати коли дані планети були імпортовані. Це відбувається шляхом зміни часу (timestamp) файла planet-import-complete.

```sh
touch /var/lib/mod_tile/planet-import-complete
```

Нарешті, вам потрібно перезапустити фонову службу генерації тайлів, після чого все має бути готовим.

```sh
sudo /etc/init.d/renderd restart
```

Якщо все працює так як очікувалось, у вас має запуститись тайловий сервер і ви зможете переглянути результат його роботи за посиланням <http://localhost/osm/slippymap.html>.

Якщо у вас не відкривається файл slippymap.html на localhost або ви бачите рожеве тло замість мапи, вам треба змінити localhost в коді сторінки html на правильну адресу вашого сервера в ‘new OpenLayers.Layer.OSM(“Local Tiles”…’ або перетворити URL на відносну адресу.

# Оновлення

Наступні команди використовуються для регулярного оновлення даних вашого сервера свіжими даними з OSM.

Після створення бази даних та першого імпорту за допомогою osm2pgsql, дивись про це вище (потрібно використовувати параметр `--slim` для першого імпорту щоб уможливити наступне оновлення даних), виконайте наступні кроки:

Встановіть osmosis

```sh
sudo apt-get install osmosis
```

Надайте права на оновлення таблиць користувачу www-data

```sh
sudo /usr/bin/install-postgis-osm-user.sh gis www-data
```

Ініціалізуйте процес реплікації osmosis для даних, які ви імпортували раніше. Вкажіть дату, з якої потрібно починати оновлювання даних

```sh
sudo -u www-data /usr/bin/openstreetmap-tiles-update-expire 2012-04-21
```

Оскільки сценарій з пакунка використовує застарілі дані для визначення правильної початкової точки реплікації, вам потрібно буде вручну вибрати та завантажити правильний файл state.txt з base_url (див. нижче), який  трохи старіший за дату даних у вашій базі, щоб переконатись що всі зміни будуть включені до вашої бази. Його потрібно скопіювати в /var/lib/mod_tile/.osmosis/state.txt

Далі вам треба оновити типові налаштування osmosis. У файлі /var/lib/mod_tile/.osmosis/configuration.txt замініть base_url на `https://planet.openstreetmap.org/replication/minute/`

Зачекайте на оновлення даних на вашому тайловому сервері, десь впродовж години, та зробіть ваші тайли застарілими командою

```sh
sudo -u www-data /usr/bin/openstreetmap-tiles-update-expire
```

Якщо наявні дані вашого тайлового сервера відстають більше ніж на годину, вам доведеться запустити сценарій openstreetmap-tiles-expire кілька разів. Якщо ви бажаєте підтримувати дані на сервері в актуальному стані, додайте сценарій openstreetmap-tiles-expire в розклад crontab.

Підтримання даних в актуальному стані вимагає багато ресурсів, переважно через те, що одразу після імпорту ваші дані будуть старіші за поточні на кілька днів. Розгляньте можливість змінення параметра maxInterval в /var/lib/mod_tile/.osmosis/configuration.txt на 21600 (шість годин) доки ви не наздоженете відставання. Потім, додайте параметр `--number-processes 2` до комнади osm2pgsql в /usr/bin/openstreetmap-tiles-update-expire, або більше, якщо це дозволяє ваша апаратна частина.

Після першого встановлення даних берегової лінії, є сенс час від часу робити їх оновлення до нової версії:

```sh
wget http://tile.openstreetmap.org/processed_p.tar.bz2
```

розпакуйте архів в

```sh
/etc/mapnik-osm-data/world_boundaries/
```

# Розв’язання проблем

Є ряд речей, які можуть піти не так. Ось декілька поширених проблем та способи їх вирішення:

## Помилка підключення до бази даних в osm2pgsql

Якщо ви отримуєте таке повідомлення про помилку під час імпорту даних в osm2pgsql, ви, ймовірно, забули додати користувача у розділ користувачів, яким дозволений доступ, під час налаштування.

  > Connection to database Failed: FATAL: Ident authentication failed for user “user_name”

Ви можете виправити помилку двома способами:

Або ви переналаштуєте пакунок, на цей раз включивши як data-www, так і власне ім’я користувача у список, розділивши їх пробілом,

```sh
sudo dpkg-reconfigure openstreetmap-postgis-db-setup
```

Або, вручну надайте відповідний дозвіл вашому обліковому запису наступною командою:

```sh
sudo /usr/bin/install-postgis-osm-user.sh gis user_name
```

# Залізо

Вимоги до апаратного забезпечення можуть бути досить високими, якщо ви збираєтесь обробляти великі території, але не настільки, якщо вас цікавить невеликий регіон. Стандартний комп’ютер (4 Гб оперативної пам’яті, звичайний жорсткий диск, багатоядерний процесор) впорається з даними в 100 - 300 Гб, це десь пів України; імпорт триватиме десь близько години.

Якщо у вас на меті імпортувати дані для всього світу та створити з них мапу, вам знадобиться набагато більший сервер, ніж типовий робочий комп’ютер. Наприклад, оперативної пам’яті треба 24Гб і більше. Також ви не обійдетесь без SSD для бази даних, чи швидкого RAID масиву. Повний імпорт планети вимагатиме більше 256 Гб, але для того щоб зберігати всі дані, вам знадобиться не менше 512 Гб SSD. Дані, що не потребуватимуть оновлення, імпортовані з параметром `--drop`, з іншого боку, мають вміститись на 256 Гб SSD. Ви також можете розташувати найбільш важливі частини вашої бази даних на SSD, в той час як інші – на повільніших дисках. Для цього Osm2pgsql підтримує використання окремих табличних просторів для різних частин бази даних.

# Часті питання

## Де знаходяться файли (БД / Тайли, й т.і.)?

*   Таблиці стилів та дані берегової лінії&nbsp;– /etc/mapnik-osm-data
*   Тайли&nbsp;– /var/lib/mod_tile
*   Налаштування renderd&nbsp;– /etc/renderd.conf
*   Налаштування mod_tile&nbsp;– /etc/apache2/sites-available/tileserver_site
*   Сценарії&nbsp;– /usr/bin
*   Налаштування бази даних&nbsp;– /etc/postgresql/X.X/main (де X.X це версія PostgreSQL, наприклад 9.1)
*   Налаштування osm2pgsql та state.txt&nbsp;– /var/lib/mod_tile/.osmosis

## Як створити тайли наперед?

Ви можете скористатись render_list для попереднього створення тайлів:

```text
Usage: render_list [OPTION] ...
 -a, --all render all tiles in given zoom level range instead of reading from STDIN
 -f, --force render tiles even if they seem current
 -m, --map=MAP render tiles in this map (defaults to 'default')
 -l, --max-load=LOAD sleep if load is this high (defaults to 5)
 -s, --socket=SOCKET unix domain socket name for contacting renderd
 -n, --num-threads=N the number of parallel request threads (default 1)
 -t, --tile-dir tile cache directory (defaults to '/var/lib/mod_tile')
 -z, --min-zoom=ZOOM filter input to only render tiles greater or equal to this zoom level (default is 0)
 -Z, --max-zoom=ZOOM filter input to only render tiles less than or equal to this zoom level (default is 18)
```

Під час використання параметра `--all`, ви можете обмежити територію створення тайлів додавши ці параметри:

```text
-x, --min-x=X minimum X tile coordinate
-X, --max-x=X maximum X tile coordinate
-y, --min-y=Y minimum Y tile coordinate
-Y, --max-y=Y maximum Y tile coordinate
```

Без `--all`, надішліть перелік тайлів, які має обробити rendered через STDIN в форматі X Y Z, наприклад

```sh
0 0 1
0 1 1
1 0 1
1 1 1
```

Це означатиме, що будуть створені всі 4 тайли для маштабу 1.

Зауважте, що ви маєте вказати `--socket=/var/run/renderd/renderd.sock`

# Подяка

Дякуємо Kai Krueger за підтримку пакунків та підготовку документації.
