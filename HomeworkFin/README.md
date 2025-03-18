
# Построение сервиса прогнозирования биржевой стоимости актива»

Ссылка на проект:
https://github.com/DATravin/otus-final-project/tree/main 

## Цель работы

Построить рабочий сервис по прогнозированию стоимости биткоина. 
В данном случае, мы получаем не саму стоимость, а торговое указание sell/buy/hold

## Общая архитектура

Архитектурно рабочая система разделена на 2 блока.

### БЛОК 1. infra_model 

#### элементы сборки

https://github.com/DATravin/otus-final-project/tree/main/infra_model 

Данна инфраструктура представляем собой связку:

- Mlflow + S3 + Postgree для храненения моделей и артифактов
- VM машины с Airflow
- Spark cluster (on demand)

Цель данного блока: по расписанию

- забрать с S3 дата сет с 4-х часовыми данными стоимости актива (в реально ситуации, данный забирались бы из БД, куда в свою очередь данные лились бы с биржи, но в данном случае у нас условно-боевая ситуция. Данные фиксированные)

- собрать кластер,
- запустить 2 дага:
- расчет фичей
- обучение/оптимимизация модели
  
Даги: https://github.com/DATravin/otus-final-project/blob/main/dags/btc_two_step.py 

результат отработки дага на скрине:

![image](https://github.com/user-attachments/assets/1f261026-e7a4-4e46-8d10-7f9b4e95bc17)

Предполагается, что переобучаем не в зависимости от метрик, а просто регулярно по расписанию (допустим каждую неделю)

Опять же в боевой ситуации сервисы MLFLOW и AIRFLOW не тушаться. Поэтому модели мы складываем последовательно на MLFLOW, откуда в проде берется просто наиболее актуальная модель.

Поэтому в учебных целях модель забирается не через API Mlflow, а из хранилища артефатов (S3)

#### оптимизация модели

https://github.com/DATravin/otus-final-project/blob/main/src/model_optimisation.py 

Модель оптимизируется по 2-м направлениям:

- На первом шаге запускается отбор признаков с помощью Permutation
- На втором шаге с помощью модуля optuna подбираются оптимальные параметры
- результаты записываеются в Mlflow 

 ![image](https://github.com/user-attachments/assets/c4f2b4ed-0221-4492-8030-15c560c35e16)

В итоге лучшая модель записывается в MLFLOW

 ![image](https://github.com/user-attachments/assets/50a5f961-770d-4375-a33b-dfc2ce6dcfd6)

### БЛОК 2. infra_deploy

https://github.com/DATravin/otus-final-project/tree/main/infra_deploy 

#### элементы сборки

Данная архитектура состоит из 3х элементов:

- VM с установленным docker
- K8s cluster
- dockerhub (хранилище образов)
- также дополнительно были подняты Grafana и Prometheus

#### Механика

При сборке происходит настройка всего окружение.
А далее одним скриптом происходит:
1) сборка свежего контейнера (туда подкладываются свежие ключи от S3)
2) Push свежего образа на dockerhub ( https://github.com/DATravin/otus-final-project/blob/main/infra_deploy/modules/compute/scripts/setup_kuber.sh )
3) поднятие сервиса на k8s

Инструкции для k8s: https://github.com/DATravin/otus-final-project/tree/main/infra_deploy/k8s

#### inference

В данном случае модулируем ситуацию, при которой мы передаем свежую строку со значениями 
body5 = {
        "TimeKey":['2021-07-07T20:00:00Z'],
        "High":[34628.71],
        "Low":[33777.77],
        "Open":[34628.71],
        "Close":[33862.12],
        "Volume":[7923.75]
        }
далее происходит:

-расчет фичей
-скачивание модели c Mlflow (в данном случае с s3)
-примерение модели
-вердикт

![image](https://github.com/user-attachments/assets/0d21dbe8-bfb3-4002-8194-1df523366cb5)

В примереме выше мы реализовали ingress сервисе, который позволяли обращаться к приложению прямо по URL

#### метрики

В данном случае реализованы пара счетчиков в самом приложении:
https://github.com/DATravin/otus-final-project/blob/main/infra_deploy/app/app.py 

Но они в целом не играют существенной роли. так как не являются триггерами для переобучения модели.
Модель, напомню, переобучается на регулярной основе вне зависимости от. 

Тем не менее ниже продемонстрированы UI Grafana и Prrometheus

![image](https://github.com/user-attachments/assets/640803bd-49d4-494e-9ddf-f79347cfe03b)

и

![image](https://github.com/user-attachments/assets/50866f11-a55e-40fd-bc16-29fd6fbf0ee0)

и

![image](https://github.com/user-attachments/assets/e8dd770f-f75f-4d0b-82c2-3ced4fc30d3d)


# CICD

так же есть процесс по сборки контейнера и запуска приложения через github actions

Он описан тут:

https://github.com/DATravin/otus-final-project/blob/docker/.github/workflows/main.yml 

В его процессе собирается контейнер. Пушится на docker hub

Потом считывается и поднимается приложение

![image](https://github.com/user-attachments/assets/2099b03a-9231-410a-a29b-8f878c114306)

pipline отрабатывает. 

Это альтернативный вариант запуска приложения без кубера





