# highload_avito
## РПЗ по предмету "Проектирование высоконагруженных систем". Технопарк, осень 2021
Авито - интернет-сервис для размещения объявлений о товарах, недвижимости, вакансиях и резюме на рынке труда, а также услугах от частных лиц и компаний [1].

### 1. Тема и целевая аудитория
#### MVP
Основной функционал сервиса:
* Поиск объявлений (по словам, категориям, в определенном радиусе поиска).
* Просмотр объявления.
* Публикация объявления, закрытие.
* Просмотр и редактирование личного профиля.
* Чат между покупателем и продавцом.
* Оставление отзыва на продавца.
#### Целевая аудитория
На состояние июня 2021 года месячная аудитория сервиса (desktop&mobile) составляет 41 444.9 тыс. чел. Дневная - 9 989.2 тыс. чел. [2].
Основной целевой аудиторией являтся люди в возрасте от 25 до 44 лет, большей частью специалисты и рабочее население. Сервис ориентирован на жителей РФ.

#### 2. Расчет нагрузки

##### Продуктовые метрики

* **Месячная аудитория** - 41 444.9 тыс. чел.
* **Дневная аудитория** - 9 989.2 тыс. чел.
* **Средний размер хранилища пользователя**
Существуют следующие типы хранимой информации:
1. **Личный профиль**, содержащий аватар а также данные - уникальный идентификатор, адрес электронной почты, имя, город, номер телефона, пароль.

| Аватар       | Личные данные      | Всего данных  |
| :-------------:|:------------------:| :-------:|
| 500 Кб       | 500 б              | 0.49 Мб |

3. **Чаты**. В среднем, для того, чтобы уточнить вопросы по товару, а также договориться о встрече, требуется около 10 сообщений от одного человека. Каждое сообщение в контексте чата занимает ~100 символов -> 100 байт. Вместе с дополнительной возможной информацией (id получателя и отправителя, время и проч.) получается ~1200 байт на человека. 

| Сообщение | Дополнительная информация |  Количество сообщений | Всего данных  |
| :-------------:|:------------------:| :-------:| :---: |
| 100 б       | 20 б  |  10 |   0.00114 Мб |

5. **Объявления**. Среднее описание товара составляет ~600 символов -> 600 байт + ~20 байт на дополнительную информацию (категория, состояние, продавец и проч.)  => 620 байт. Также в каждом объявлении в среднем содержится 5 изображений. Если взять средний размер за 500 Кб, то еще 2 560 000 байт. Итого 2 560 620 байт.

| Описание | Дополнительная информация |  Изображение | Количество изображений | Всего данных  |
| :-------------:|:------------------:| :-------:| :---: | :---: |
| 600 б       | 20 б  |  500 Кб |   5  | 2.44 Мб |

7. **Отзывы**. В отзывах ~150 символов + дополнительные данные об отправителе/о ком оставили отзыв. ~170 байт.

| Данные       | Дополнительная информация     | Всего данных  |
| :-------------:|:------------------:| :-------:|
| 150 б       | 20 б              | 0.00016 Мб |

Если считать, что за время существования авито (10 лет) [3] публиковалось примерно по 400 тыс. новых объявлений в день, то текущее количество закрытых объявлений составляет ~1.46 млрд - около 35 закрытых объявлений на пользователя, если считать месячную аудиторию. Можно убедиться, что эта цифра приблизительна равна действительной, так как в среднем у пользователя есть 10-15 закрытых объявлений; в то же время попадаются как пользователи, закрывшие 50-100 объявлений, так и пользователи, которые закрыли 1-5 объявлений.

На 19.09.2021 На авито размещено 78 971 675 объявлений [3]. С той же месячной аудиторией это около 2 актуальных объявлений на человека

Всего получаем 37 объявлений на человека. 

| Размер объявления       | Закрытые объявления     | Актуальные объявления   | Всего данных
| :-------------:|:------------------:| :-------:|:-------:|
| 2.44 Мб      | 35              | 2 | 90 Мб

Если на каждое объявление существовал чат с ~3 покупателями, то это 111 чатов у одного пользователя. (111 * 1200) / (1024 * 1024) = 0.13 Мб

| Количество чатов у пользователя     | Размер чата | Всего данных
| :------------------:| :-------:|:-------:|
| 111              | 0.00114 Мб |  0.13 Мб

Пусть на каждое закрытое объявление пользователю оставляли отзыв. Тогда у каждого человека по 35 отзывов => 0.005 Мб.

| Количество отзывов у пользователя     | Размер отзыва|  Всего данных
| :------------------:| :-------:|:-------:|
| 35              | 0.00016 Мб |  0.005 Мб

Таким образом, среднее хранилище пользователя представляет из себя следующее
| Профиль | Чаты | Объявления | Отзывы | Всего
|:---:|:---:|:---:|:---:|:---:|
| 0.49 Мб | 90 Мб | 0.13 Мб | 0.005 Мб | **90.625 Мб**|

##### Среднее количество действий пользователя по типам в день 

Всего в месяц на авито на момент августа 2021 года приходится 296.69 млн. посещений => 9.57 млн. посещений в день. Среднее время сессии составляет 11:25, за это время посещаются 11.79 страниц [4].

###### Поиск
В среднем, человек обычно ищет какую-то определенную вещь, также возможно, его не устраивает результат запроса и он отправляет уточненный запрос, то есть осуществляет 2 запроса по поиску.

###### Просмотр объявления
Исходя из данных выше, пользователь просматривает ~10 объявлений.

###### Публикация объявления, закрытие.
На авито каждый день публикуется 400 000 объявлений и совершается 120 сделок в минуту[3]. 
400 000 / 24 / 60 / 60 = 5 объявлений/с. Если делить на дневную аудиторию, то 0.04 объявления на человека в день, то есть 4 человека из 100 в день опубликуют объявления.
120 / 60 = 2 сделки/с => закрытие. Если делить на дневную аудиторию, то 0.017 объявления на человека в день, то есть ~2 человека из 100 закроют объявление.

###### Просмотр и редактирование личного профиля.
Нагрузка от редактирования личного профиля будет минимальной, пользователи редко правят свои данные. Но при этом покупатель чаще всего будет интересоваться профилем продавца. Если считать по просмотренным страницам объявлений, то пользователь посетит примерно 3-5 профилей.

###### Чаты
Пусть чаты привязаны к просмотренным объявлением. Допустим, пользователь заинтересовался третью просмотренных товаров, то есть начал 3 чата. В среднем, получится 6 сообщений на пользователя в сессию. У пользователя в день чаще всего одна сессия (отношения посещений к дневной аудитории ~ 1).

##### Оставление отзыва на продавца
Привязано к совершенным сделкам. Допустим, что каждый покупатель оставляет отзыв на продавца. Это будут те же 0.017 отзывов на человека в день, то есть ~2 человека из 100 оставляют отзыв.

**Итого**
| Поиск | Просмотр объявления | Публикация объявления | Закрытие объявления | Просмотр профиля | Написание сообщения | Написание отзыва |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 2 | 10 | 0.04 | 0.017 | 4 | 6 | 0.017 |

##### Технические метрики
* Размер хранения в разбивке по типам данных (в Тб) - для существенных блоков данных.
Существенные блоки данных - объявления, профили, сообщения, отзывы.
1. Объявления = (78 971 675 + 1 460 000 000) * 2.44 Мб / 1024 / 1024 = 3 581 Тб
2. Профили (возьмем месячную аудиторию за существующую) = 41 444 900 * 0.49 Мб / 1024 / 1024 = 19.4 Тб
3. Сообщения (аналогично) = 41 444 900 * 0.13 Мб / 1024 / 1024 = 5.14 Тб
4. Отзывы (аналогично) = 41 444 900 * 0.05 Мб / 1024 / 1024 = 2 Тб

* Сетевой трафик
Основной трафик
1. Загрузка информации об объявлении (в т.ч на странице поиска) = (10 {объявления, просмотренные одним человеком} * 9 989 200 * 2.44 Мб  + 9 {картинок на странице поиска} * 2 {запросы поиска} * 5 {в среднем пролистнул страниц пользователь} * 0.49 Мб {картинка + данные короткие об объявлении} * 9 989 200) / 24 / 60 / 60 / 1024 * 8 = (243 736 480 + 440 523 720) / 24 / 60 / 60 / 1024 * 8 = 61.6 Гбит/с
2. Загрузка профилей = 9 989 200 * 4 {профили} * 0.49 / 24 / 60 / 60 / 1024 * 8 = 1.76 Гбит/c
Итого 5 474 304 Гбит/cутки 
* RPS в разбивке по типам запросов

| Тип запроса | RPS |
| :---: | :---: |
| Авторизация | 145 |  
| Поиск | 231 |
| Объявления (страницы) | 1155 |
| Объявления (отдельное) | 1156 |
| Публикация объявления | 5 |
| Закрытие объявления | 2 |
| Просмотр профиля | 462 |
| Просмотр отзывов | 462 |
| Оставление отзыва | 2 |
| Написание сообщения | 694 |
| Получение сообщений чата | 694 |
| **Итого** | **5008** |

### 3. Логическая схема
![logic drawio_v2](https://user-images.githubusercontent.com/59224300/136846288-8b1796d0-b30d-44d2-977b-d691a791937f.png)
### 4. Физическая схема
#### Профили и данные пользователей
Информация о пользователях включает себя текстовые данные и аватар. При этом, как было рассчитано ранее, изображение занимает большую часть хранилища пользователя. Таким образом, все изображения можно держать в специализированном хранилище, к примеру, в **Amazon S3**, который поддерживает не менее 5500 запросов в секунду на извлечение данных и не менее 3500 запросов в секунду на добавление данных[5] (что не только отвечает требованиям по rps, но и позволяет использовать это хранилище для хранения изображений объявлений).

Остальные данные (чаты, отзывы и прочее) хранятся рядом с пользователями в реляционных базах - имеют постоянную схему данных. По размерам, к примеру, подходит тот же PostgreSQL - максимальный размер таблицы в нём составляет 32 Тб [7]. Если рассчитывать размеры таблиц, вынеся картинки в отдельное хранилище, получится
```
500 б * 41 444 900 / 8 / 1024 / 1024 = 19.29 Гб (пользователи)
```
В сообщениях и отзывах можно ориентироваться на уже рассчитанные данные.

#### Объявления

Большую часть данных составляют объявления - они нуждаются в шардировании. Все объявления разделяются на категории и по местоположению. 

Если смотреть по городам и регионам, значительная часть актуальных объявлений размещена в Москве - 14.52 млн на 18.10.2021. Вместе с МО - 19.66 млн. Вынося изображения в отдельное хранилище и беря 620 байт как размер одного объявления, получаем размер в 
```
19 660 000 * 620 / 1024 / 1024 / 1024 = 11.35 Гб
```
**11.35 Гб** для базы данных актуальных объявлений. Структура полей объявления не попадает под стандартную реляционную схему (отличается в зависимости от категории). Также есть необходимость в шардировании. В этом случае есть смысл использовать дисковую noSQL базу данных, хранящую объекты в виде JSON файлов, к примеру, MongoDB. Максимальный размер документа в MongoDB составляет **16 Мб**. Максимальный размер базы для 64-х битного сервера - **32 Тб**, что покрывает необходимые объемы[6].
Однако также существует необъодимость хранить уже закрытые объявления. Если считать, что процентное соотношение объявлений для регионов все время оставалось приблизительно одинаковым (~25% от общего числа), то количество закрытых объявлений должно составлять ~365 млн. Рассчитаем размер базы данных в этом случае
```
365 000 000 * 620 / 1024 / 1024 / 1024 = 210 Гб
Итого: 221.35 Гб
```
С учетом максимального размера базы данных, в ближайшее время дополнительное разделение из-за превышения объема не потребуется.
 
Таким образом, базу данных можно шардировать по регионам - балансировщик нагрузки при обращении дополнительно будет получать ключ, по которому будет отправлять запрос на соответствующий сервер. 

Учитывая специфику сервиса (небольшая нагрузка на запись и большая нагрузка на чтение), а также допущение, что объявление может появиться в ленте пользователя не сразу, можно сделать несколько реплик на каждый шард, чтобы провести балансировку запросов на чтение.

Также рядом с объявлениями можно хранить в реляционной базе смежные данные - количество просмотров, жалобы. Таблицы категорий рекомендуется денормализовать, указав непосредственно текстовое значение в соответствующем поле.


### 5. Технологии

#### Бэкенд

Для **бэкенда** данного сервиса лучше всего будет использовать язык **Golang** и **микросервисную архитектуру**. Основными преимуществами для использования данного языка в Avito являются:
* Средства параллельного программирования: встроенные в язык потоки (go routines), взаимодействие потоков через каналы и другие средства организации многопоточных программ.
* Достаточно лаконичный и простой синтаксис, основанный на Си, но существенно доработанный, с большим количеством синтаксического сахара.
Также Golang используется в e-commerce проектах - Wildberries, Ozon [вакансии с hh.ru и оф. сайтов]. 

Микросервисная архитектура позволяет ускорить разработку. Быстрое добавление новых фич становится критически необходимым, особенно с учетом наличия крупных конкурентов (к примеру, Юлы). Также она позволяет увеличить гибкость проекта, а также удобнее организовать рабочий процесс, когда отдельные команды работают над независимыми блоками. В случае Авито эти блоки легко выделяются - можно отделить микросервис отзывов, чатов, блока объявлений и т.д, то есть на каждый существенный блок данных определить свой микросервис, который будет работать с ним.

Для **фронтенда** можно использовать фреймворк **React.js**. Он остается одним из самых популярных фреймворков в последние годы [8]. Из плюсов можно выделить высокую производительность, так как он основан на виртуальной модели DOM, а также высокую переиспользуемость компонентов - всё это дает преимущества в выборе при использовании в проектах типа досок объявлений.

Для **баз данных**:
* В качестве базы данных **для объявлений** стоит рассматривать, как было сказано выше, дисковую noSQL базу данных. **MongoDB** прекрасно подходит для этих целей - она легко масшабируется, подходит по объемам, рассчитанным в пункте выше, а также используется в проекте схожей направленности (той же Юле) [9].
* **Для прочих данных** следует использовать **PostgreSQL**. В отличие от своего ближайшего "соперника" MySQL, Postgres активно развивается в последние годы. MySQL принадлежит компании Oracle, поэтому, при использовании в non-open-source проектах необходимо оплатить лицензию. Также подходит для текущей нагрузки.
* **Сессии** будут храниться в key-value хранилище, **Redis**. В качестве ключа будет email пользователя, в качестве значения - id пользователя, его куки и времени начала сессии в json-объекте, хранящемся в виде строки. Тогда требуемое количество оперативной памяти:
```
email - 256 символов
JSON:
      {
           "id": XXXXXXXXXX,
           "cookie": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
           "time": "YYYY-MM-DDTHH:MM:SS"
      }
Результат выполнения MEMORY USAGE с максимально возможным email: 398 байт
Общий требуемый объем для месячной аудитории: 15.36 Гб
```

* **Просмотры**, как часто обновляющуюся сущность, также лучше поместить в базу In-Memory базу. После закрытия объявления просмотры уже не обновляются - при закрытии необходимо будет дополнительно посылать запрос, который перенес бы запись в реляционную базу. Ключ - id объявления, можно объявить счетчик, который будет увеличиваться командой incr. 
Для хранения изображений используется **Amazon S3**.

### 6. Схема проекта





### 7. Список серверов


===work in progress===

### Список использованных источников

1. https://ru.wikipedia.org/wiki/%D0%90%D0%B2%D0%B8%D1%82%D0%BE
2. https://webindex.mediascope.net/report?byGeo=2&byDevice=3&byDevice=1&byDevice=2&byMonth=202106&id=15828
3. https://www.avito.ru/company
4. https://www.similarweb.com/ru/website/avito.ru/#overview
5. https://aws.amazon.com/ru/s3/features/?nc=sn&loc=2
6. https://docs.mongodb.com/manual/reference/limits/#data
7. https://wiki.postgresql.org/wiki/FAQ#What_is_the_maximum_size_for_a_row.2C_a_table.2C_and_a_database.3F
8. https://ru.wikipedia.org/wiki/Go
9. https://trends.google.com/trends/explore?cat=5&date=today%205-y&q=React,Angular,Vue,Backbone,Ember
10. http://backendconf.ru/moscow-rit/2019/abstracts/5319
