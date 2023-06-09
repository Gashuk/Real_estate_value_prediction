# Описание тестового задания 

1) Разработать на основе алгоритмов машинного обучения систему оценивающую объекты недвижимости по их параметрам

2) Разработать систему подготовки данных из представленной  выборки объектов

3) Для обучения системы предоставляется выборка проданных объектов (около 71к)

4) Допускается использование любого типа моделей машинного обучения или их ансамблей

5) Плюсом будет оценка важности атрибутов объекта для определения цены

6) Описание полей выборки:

- id              - ИД объекта
- status          - статус объекта (у всех sold - продан)
- city_id         - идентификатор города
- district_id     - идентификатор района
- street_id       - идентификатор улицы
- price           - последняя цена по которой объект был выставлен на продажу
- date_sold       - дата продажи объекта
- sold_price      - цена за которую объект был продан
- metro_station_id- идентификатор ближайшей станции метро
- flat_on_floor   - положение квартиры на этаже (подъездной площадке)
- floor_num       - этаж
- floors_cnt      - количество этажей в доме
- rooms_cnt       - количество комнат
- bedrooms_cnt    - количество спален
- building_year   - год постройки дома
- area_total      - общая площадь
- area_live       - жилая площадь
- area_kitchen    - площадь кухни
- area_balcony    - площадь балконов
- builder_id      - идентификатор застройщика (можно игнорировать)
- type            - тип объекта (квартира, комната, общежитие, пансионат, малосемейка)
- two_levels      - двухуровневая
- levels_count    - количество уровней
- bathroom        - тип сан. узла
- bathrooms_cnt   - количество сан. узлов (если NULL - значит 1)
- plate           - тип плиты
- windows         - тип окон
- territory       - описание придомовой территории, мнемоники объектов перечисленные через запятую
- keep            - тип отделки квартиры
- komunal_cost    - средняя стоимость коммунальных платежей
- series_id       - серия дома
- wall_id         - тип стен дома
- balcon          - тип балкона/балконов
- loggia          - наличие лоджии
- ceiling_height  - высота потолков
- closed_yard     - закрытый двор
- longitude       - долгота объекта
- latitude        - широта объекта

# Структура репозитория 

**Описание файлов:**
* TZ_Esoft_analiz_ver_final.ipynb - файл с анализом датасета
* TZ_Esoft_preprocessing_ver_final.ipynb - файл с предобработкой датасета
* TZ_Esoft_model_CatBoostRegressor_ver_final.ipynb - файл с обучение модели CatBoostRegressor
* sold_flats_2020-09-30.csv - файл с исходным  датасетом
* data_sold_flats_location.xlsx - файл с исходным  датасетом в котором добавлены столбцы "city", "region", "country"
* data_sold_flats_result.xlsx - файл с предобработаным датасетом
* catboost_model.dump - обученная модель CatBoostRegressor



# Анализ датасета

**Выполненные пункты:**

1.   Импортируем датасет и смотрим информацию о структуре и содержимом данных
2.   Находим дубликаты
3.   Анализируем типы признаки
4.   Анализируем признаки на NaN
5.   Строим матрицу корреляции

 



<p align="center">
<!--  ![Матрица_1](https://github.com/Gashuk/Real_estate_value_prediction/assets/66191970/e8ca672c-ec98-4e5b-aad3-90fbac4bd388) -->
   <img src="https://github-production-user-asset-6210df.s3.amazonaws.com/66191970/239244553-e8ca672c-ec98-4e5b-aad3-90fbac4bd388.png">
 <p align="center"><strong>Матрица корреляции</strong></p>
</p>




**Вывод:**
* С целевой переменной 'sold_price' не коррелируется не один признак


# Предобработка датасета

**Выполненные пункты:**

1.   Импортируем датасет и смотрим информацию о структуре и содержимом данных
2.   Узнаем названия городов и областей, где находиться недвижимость, по координатам
3.   Открываем обновленный датасет с названиями городов и регионов, где находить недвижимость
4.   Удаляем дубликаты
5.   Находим список стран, где находиться проданная недвижимость
6.   Находим список 'city_id' иностранных городов
7.   Проверяем список 'city_id' иностранных городов на ошибки
8.   Удаляем иностранные города
9.   Определяем название городам и регионам у которых значение NaN
10.  Обновляем  столбцы 'city', 'region', чтобы не было NaN
11.  Определяем  список 'city_id', где есть метро
12.  Создаем новый столбец  'isMetro' - находиться в городе метро (1) или нет (0)
13.  Меняем всем городом, где нет метро, значение metro_station_id на 0, а для городов, где есть метро меняем -1 и NaN на 0
14.  Удаляем столбцы
15.  Заменяем значение "-1" у столбца 'district_id' на NaN
16.  Заменяем "0" у столбцов 'rooms_cnt','wall_id', 'series_id','floors_cnt' на NaN
17.  Удаляем строки, где малое количество пропущенных значений, в столбцах 'area_total', 'two_levels', 'balcon', 'bathroom', 'windows', 'keep', 'floors_cnt', 'floor_num', 'sold_price', 'region', 'district_id', 'wall_id', 'series_id'
18.  Делаем Label Encoding для столбца 'region'
19.  Меняем название столбца 'region' на 'region_id'
20.  Добавляем новые столбцы 'id_rigion_city','id_city_district', 'id_city_street' с информацией о привязке недвижимости к региону, городу, району и улицы
21.  Меняем  пропущенные значения (Null или 0) на 1 в столбце 'bathrooms_cnt' по условию задачи
22.  Делаем Label Encoding в столбцах 'id_rigion_city','id_city_district', 'id_city_street', 'keep', 'balcon', 'two_levels', 'type', 'bathroom', 'windows'
23.  Заменяем список типов в столбце 'territory', который содержит информацию о территории, на их количество в ячейке, игнорируя пропущенные значения (NaN)
24.  Находим список некорректных значений в столбце 'building_year'
25.  Исправляем  некорректные значения в столбце 'building_year'
26.  Анализируем выбросы
27.  Приводим значение в столбце 'sold_price' к одной единице измерения
28.  Удаляем выбросы в столбце 'sold_price', где значение меньше 1000 или больше 10 000 000
29.  Удаляем выбросы в столбце 'area_total', где значение меньше 10 и больше 130
30.  Удаляем выбросы в столбцах 'floor_num', 'floors_cnt', 'rooms_cnt', 'bedrooms_cnt', 'building_year', 'bathrooms_cnt' , 'territory'
31.  Добавляем столбец  'price_per_sqm' - цена за квадратный метр
32. Заменяем  NaN на медианное значение в столбцах  'bedrooms_cnt', 'building_year', 'territory', 'rooms_cnt' отсортированным  по столбцу 'series_id'
33.  Строим матрицу корреляции
34.  Сохраняем пред обработанный датасет в Excel



<p align="center">
<!--  ![239222031-26fd5c18-fa96-4bf3-bc23-dc9d8297a153](https://github.com/Gashuk/Real_estate_value_prediction/assets/66191970/193f8f6e-6b00-482c-831e-c94ebcf343b9) -->

  <img src="https://github-production-user-asset-6210df.s3.amazonaws.com/66191970/239244732-193f8f6e-6b00-482c-831e-c94ebcf343b9.png">
  <p align="center"><strong>Матрица корреляции</strong></p>
</p>


**Вывод**:
* С целевой переменной 'sold_price' коррелирую следующие признак: 'floors_cnt','rooms_cnt','building_year','area_total','price_per_sqm'
* После предобработки получился датасета длиной 58430 строк и 27 столбцами:
  * city_id         - идентификатор города
  * district_id     - идентификатор района
  * street_id       - идентификатор улицы
  * sold_price      - цена за которую объект был продан
  * metro_station_id- идентификатор ближайшей станции метро
  * floor_num       - этаж
  * floors_cnt      - количество этажей в доме
  * rooms_cnt       - количество комнат
  * bedrooms_cnt    - количество спален
  * building_year   - год постройки дома
  * area_total      - общая площадь
  * type            - тип объекта (квартира, комната, общежитие, пансионат, малосемейка)
  * two_levels      - двухуровневая
  * bathroom        - тип сан. узла
  * bathrooms_cnt   - количество сан. узлов (если NULL - значит 1)
  * windows         - тип окон
  * territory       - описание придомовой территории, мнемоники объектов перечисленные через запятую
  * keep            - тип отделки квартиры
  * series_id       - серия дома
  * wall_id         - тип стен дома
  * balcon          - тип балкона/балконов
  * region_id       - идентификатор региона 
  * isMetro         - наличие метро 
  * id_rigion_city  - идентификатор регион/город
  * id_city_district- идентификатор город/район
  * id_city_street  - идентификатор город/улица
  * price_per_sqm   - цена за квадратный метр


# Обучение модели



**Выполненные пункты:**

1.   Импортируем датасет и смотрим информацию о структуре и содержимом данных
2.   Разделяем датасет на тренировочную и тестовую выборки (70/30)
3.   Находим лучшие гипер параметры для модели CatBoostRegressor
4.   Выводим лучший score и гипер параметры
5.   Обучаем модель CatBoostRegressor на лучших гипер параметров
6.   Выводим метрики модель
7.   Выводим график "Кривая обучения"  модели
8.   Сохраняем лучшую модель
9.   Выводим график "Важности признаков"
10.  Выводим систему оценивания объектов недвижимости по их параметрам




<p align="center">
  <p align="center"><strong>Лучшие гипер параметры модели CatBoostRegressor</strong></p>
  <p align="center">{'depth': 6, 'iterations': 1000, 'l2_leaf_reg': 5, 'learning_rate': 0.1}</p>
</p>

<p align="center">
  <p align="center"><strong>Метрики</strong></p>
<table>
  <tr>
    <th>Метрика</th>
    <th>Значение</th>
  </tr>
  <tr>
    <td>Train score</td>
    <td>0.9996</td>
  </tr>
  <tr>
    <td>Test score</td>
    <td>0.9951</td>
  </tr>
  <tr>
    <td>Train RMSE</td>
    <td>23418.7746</td>
  </tr>
  <tr>
    <td>Test RMSE</td>
    <td>79587.4214</td>
  </tr>
  <tr>
    <td>Train R^2</td>
    <td>0.9996</td>
  </tr>
  <tr>
    <td>Test R^2</td>
    <td>0.9951</td>
  </tr>
  <tr>
    <td>Train MAE</td>
    <td>13725.2026</td>
  </tr>
  <tr>
    <td>Test MAE</td>
    <td>18338.5094</td>
  </tr>
  <tr>
    <td>Train MSE</td>
    <td>548439004.5111</td>
  </tr>
  <tr>
    <td>Test MSE</td>
    <td>6334157648.2144</td>
  </tr>
  <tr>
    <td>Train MAPE</td>
    <td>0.0057%</td>
  </tr>
  <tr>
    <td>Test MAPE</td>
    <td>0.0067%</td>
  </tr>
</table>

</p>


<p align="center">
<!--  ![239224873-f26d7432-540d-4057-baf9-c8b8c9706a69](https://github.com/Gashuk/Real_estate_value_prediction/assets/66191970/e67c5cb8-dfcc-44cb-9c61-7e9ffd317dff) -->
  <img src="https://github-production-user-asset-6210df.s3.amazonaws.com/66191970/239244003-e67c5cb8-dfcc-44cb-9c61-7e9ffd317dff.png">
  <p align="center"><strong>График "Кривая обучения"  модели</strong></p>
</p>

<p align="center">
<!--   ![239225265-405f41e5-966c-431e-b8a9-62269cfbec3f](https://github.com/Gashuk/Real_estate_value_prediction/assets/66191970/44a72014-f8d8-4f14-8318-3c253f713c26) -->
 <img src="https://github-production-user-asset-6210df.s3.amazonaws.com/66191970/239244468-44a72014-f8d8-4f14-8318-3c253f713c26.png">
  <p align="center"><strong>График "Важности признаков"</strong></p>
</p>

<p align="center">
  <p align="center"><strong>Система оценивания объектов недвижимости по их параметрам (первые десять недвижимостей)</strong></p>
  <table>
  <tr>
    <th>№</th>
    <th>Исходная цена продажи (Руб.)</th>
    <th>Предсказанная цена продажи (Руб.)</th>
  </tr>
  <tr>
    <td>0</td>
    <td>4880000</td>
    <td>4876390</td>
  </tr>
  <tr>
    <td>1</td>
    <td>2550000</td>
    <td>2540276</td>
  </tr>
  <tr>
    <td>2</td>
    <td>2200000</td>
    <td>2217496</td>
  </tr>
  <tr>
    <td>3</td>
    <td>2200000</td>
    <td>2194485</td>
  </tr>
  <tr>
    <td>4</td>
    <td>2000000</td>
    <td>1979107</td>
  </tr>
  <tr>
    <td>5</td>
    <td>4300000</td>
    <td>4309244</td>
  </tr>
  <tr>
    <td>6</td>
    <td>3450000</td>
    <td>3466887</td>
  </tr>
  <tr>
    <td>7</td>
    <td>9600000</td>
    <td>9869064</td>
  </tr>
  <tr>
    <td>8</td>
    <td>3100000</td>
    <td>3112621</td>
  </tr>
  <tr>
    <td>9</td>
    <td>3500000</td>
    <td>3521237</td>
  </tr>
</table>

</p>

**Вывод**:
* Исходя из представленных данных, можно сделать следующий вывод:

Плавное снижение значения MSE на линии кросс-валидации и низкие значения MSE на линии обучения, которые почти не увеличиваются, а также высокие значения других метрик (R^2, MAE, RMSE) на обучающей и тестовой выборках говорят о стабильной и высокой производительности модели.

Такая ситуация указывает на то, что модель успешно обобщает данные и способна эффективно обрабатывать сложности, присутствующие в обучающих и тестовых данных. Это свидетельствует о том, что модель не переобучена и не недообучена, а демонстрирует стабильное и качественное предсказание на новых данных.
* Самые важные признаки:
  * area_total - общая площадь (51,66 %)
  * price_per_sqm - цена за квадратный метр (47,68 %)
  * id_rigion_city - идентификатор регион/город (0,28 %)

# Программист
* Одинаев Г. А. ([@Gashuk](https://github.com/Gashuk))
