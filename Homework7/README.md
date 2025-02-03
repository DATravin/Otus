Решение на базе HW 6

Репа:

https://github.com/DATravin/otus-hw7-valid/tree/stage-0.0.1

# общее описание логики

Логика реализована в ввиде двух dag'ов

1) первый dag запускает файл скрипт model_fit_ver3.py
   ссылка: 
   В рамках него мы обучаем модель на данных.
   Далее с помощью hyperopt находим лучшие параметры
   регистрируем лучшую модель в mlflow со статусом Staging (либо Production, если это version 1)

2) шаг 2. AB тестирование моделей.
   для этого из зарегестрированных моделей выбираются 2. Текущая (production) и кандидат (Staging)
   После этого на верификационной выборке собирается 2 выбоки с результатами тестов.
   Для этого мы берем случайную подвыборку из тестового сета, считаем результат обоих моделей (auc) и после этого с помощью t-теста сравниваем качество
   Если новая модель значимо (0.05) лучше старой, то Staging -> Production, а та, которая была в статусе Production -> None
   Во всех остальных случаях Staging -> None. Старая версия не изменяется

# пошаговая демонстрация

## шаг 1: 

на первой итерации у нас всего одна модель (поставил всего 2 эсперимента)

![image](https://github.com/user-attachments/assets/b9780dd8-49c2-44e2-a0b5-4d04f1d4340f)

Эта модель сразу идет в Production
![image](https://github.com/user-attachments/assets/67e1fe39-40a8-408c-941a-5c24ef911930)

## шаг 2: 

Теперь идем на вторую итерацию. Мы получили еще одну модель:

![image](https://github.com/user-attachments/assets/55363721-973f-4c26-97c8-b550283f8723)

Новая модель - Версия 2. Становится Staging

![image](https://github.com/user-attachments/assets/cbc75955-3458-4756-912d-2e7b3cc5d2ce)



# Ссылки:

Ссылка на dag

https://github.com/DATravin/otus-hw7-valid/blob/stage-0.0.1/dags/model_fitting.py

Ссылка на модуль обучения и оптимизации ГП:

https://github.com/DATravin/otus-hw7-valid/blob/stage-0.0.1/src/model_fit_ver3.py

Ссылка на модуль АБ-теста

https://github.com/DATravin/otus-hw7-valid/blob/stage-0.0.1/src/model_verif.py


Смотрим, что получилось




