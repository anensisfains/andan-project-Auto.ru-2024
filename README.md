# Проект для предсказания цен "Лучший друг перекупщика"
---

Наш проект "Лучший друг перекупщика" - это полезный инструмент по предсказанию рыночной цены на автомобиль китайского производства для людей, занимающихся перепродажей авто. При выборе темы и цели проекта наша команда учитывала два важных тренда:

▶️ Китайский автопром на текущий момент занимает большую долю автомобильного рынка в РФ, при этом бурный рост китайских авто произошёл совсем недавно, в 2022 году. По этой причине порой сложно определить нормальную рыночную цену для автомобилей, марка которых ранее не была представлена в нашей стране

▶️ В России количество людей, занимающихся перепродажей авто, растёт огромными темпами. Согласно Росстату и его данным по ОКВЭД, автомобильная отрасль имеет наивысшие темпы роста по сравнению с другими сферами.

Именно основываясь на этих двух предпосылках мы сформулировали цель проекта: создать инструмент по вычислению рыночной цены на поддержанные авто китайского производства, основываясь на характеристиках авто, а также его состоянии.

За основу мы взяли российскую доску объявлений [Авто.ру](https://auto.ru/moskva/cars/vendor-chinese/used/)

---

# Шаг №1: [Парсинг данных](./parser_clean.ipynb)

Чтобы получить данные о китайских поддержанных автомобилях, мы запарсили объявления на Авто.ру. Брали по 500 объявлений из шести городов: Москва, Санкт-Петербург, Ростов-на-Дону, Новосибирск, Екатеринбург и Владивосток. Все данные были перенесены в [табличку](./data/parsed_cars.csv) 

---
# Шаг №2: [Обработка, EDA и первичная визуализация](./preprocessing&EDA.ipynb)

После мы занялись первичной обработкой данных: почистили выбросы, заполнили пропуски средним значением по классу авто. Сделали описание каждой переменной и, наконец, поменяли типы переменных там, где необходимо. Получили финальный датасет и перенесли его [сюда](./data/final_df.csv).

Также уделили внимание визуализации данных: нарисовали гистограммы распределения для непрерывных признаков, сделали соответствующие выводы. То же самое сделали для категориальных признаков. Также создали корреляционную матрицу для непрерывных переменных и таргета, которая показала довольно хорошую корреляцию между признаками, что является сигналом для более точного дальнейшего прогнозирования значений.

---
# Шаг №3: [ОНЕ-кодирование и подготовка данных к машинному обучению](./ОНЕ_preprocessing_for_ML.ipynb)

На этом шаге мы продолжили чистить данные от выбросов: для каждого числового признака построили гистограммы распределения, а также ящик с усами. Это помогло увидеть выбросы, которые в дальнейшем были почищены. До чистки и после нее смотрели на количество строк датасета, чтобы не убрать значительное количество автомобилей. Далее заполнили образовавшиеся после сплита пропуски, заполнили их модой. Последним этапом на этом шаге было само ОНЕ-кодирование. Выделили категориальные признаки, использовали кодировщик OneHotEncoder и вуаля - получили готовый для ML [датасет](./data/df_for_ML.csv). Но это еще не всё, кое-что в этот датасет нужно добавить авторского... нопример, новые переменные!

---
# Шаг №4: [Создание новых переменных](./new_var.ipynb)

Мы решили создать следущие новые переменные:

- Объём авто по размерам;
- Удельная мощность автомобиля;
- Плотность пассажирского пространства.

Переменные достаточно важны для понимания рыночной цены. Приведём абстрактный пример: есть два автомобиля одной марки и одной модели, однако вторая модель со спортивными модификациями, а первая - гражданская версия. По переменной "Удельная мощность автомобиля" второй авто будет существенно отличаться от первого по стоимости, имея в себе бОльшую мощность с теми же размерами кузова.

Для каждой переменной также сделали чистку от длинных хвостов для будущего улучшения результатов машинного обучения. Теперь окончательно готовый датасет сохранили и выгрузили [сюда](./data/df_for_ML_upd).

---
# Шаг №5: [Проверка гипотез](./hypothesis.ipynb)
Правда ли, что за Уралом машины стоят дешевле чем в западной части России ? Правда ли, что полноприводные автомобили разгоняются до 100 км/ч быстрее чем переднеприводные? Существует ли значимая взаимосвязь между типом двигателя и классом автомобиля?

Мы проверили четыре гипотезы с помощью u-test'а Манна-Уитни и t-теста. Спойлер - все наши предположения подтвердились. Поэтому, если хотите китайца побыстрее - берите полный привод!

---
# Шаг №6: [Машинное обучение](./auto_ru_ML_price.ipynb)

Самая интересная часть нашего проекта - предсказание цены автомобилей с помощью машинного обучения. Давайте вспомним, для каких целей вообще можно использовать предсказывание цен на авто и кому это будет полезно?

Во-первых, это обычные покупатели, которые могут примерно понять ценовой сегмент автомобиля, если у них есть особые потребности по конфигурации авто. Во-вторых, для нашей так сказать ЦА - ресселеров/перекупщиков, для которых модель позволит понять примерную рыночную цену авто исходя из его характеристик. Это поможет ускорить бизнес процессы, а также сократить затраты на оценивание стоимости автомобиля.

Для начала, мы разбили выборку на трейновую и тестовую в соотношении 80/20, подготовили функции для оценки качества моделей, и далее мы начали обучать нашу модельку - сначала использовали линейную регресиию, потом Ridge и Lasso. Сравнив результаты по MAE, RMSE и MAPE поняли, что между различными моделями особой разницы по качеству нет. Средняя ошибка по цене - 11%, что является достаточно хорошим результатом. Объясняется такая немалая ошибка тем, что автомобили могут также очень сильно отличаются по комплектации (например, авто с хорошей комплектацией может стоить на 15, порой даже на 20 процентов выше, чем "пустая" машина без доп. опций). Также оценка состояния авто, которая достаточно сильно коррелирует с ценой, не ограничивается только лишь на пробеге. Машины часто попадают в ДТП, ремонтируются, поэтому цены на такие машины могут значительно падать.

---

# Вывод

Наш продукт - это одназначно очень полезный ассистент для ресейлеров китайских автомобилей. Он может сэкономить кучу времени на анализ рыночной цены машины, тем более, что новые модели китайских авто появляются чуть ли не каждый день на российском рынке. Однако полностью заменить человеческую оценку стоимости машины этот сервис не может, как и не могут сервисы оценивания цены у таких крупных досок объявлений, как Авито или Авто.ру









