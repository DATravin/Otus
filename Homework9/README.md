
Выполнение ДЗ:

# Архитектура
Архитектура решения представляем собой сборку terraform из:

VM с установленными Docker и приблудами для работы с кубером
https://github.com/DATravin/otus-hw9-k8/blob/stage-0.0.1/infra/modules/compute/main.tf 

Сам куберкластер:
https://github.com/DATravin/otus-hw9-k8/blob/stage-0.0.1/infra/modules/kuber/main.tf 

Файлов с приложением:
https://github.com/DATravin/otus-hw9-k8/blob/stage-0.0.1/infra/app/app.py

и самого рабочего мудуля с предсказанием
https://github.com/DATravin/otus-hw9-k8/blob/stage-0.0.1/infra/app/infrerence_app.py 

# инференс

## docker

Специфика решения в том, что в работе inference у нас используется артифакт их Mlflow.
Но мы не поднимаем и не ходим в ML-flow, а работаем уже с артефактом, который был до этого создан ml-flow
и лежит на s3 (модель). В него подается тестовый семлп, на который модель должна веруть score

далее был собран контейнер, содержащий:
-app
-inference.py
-Dockerfile
-requarments

особенность контейнера в том, что он довольно тяжелый. Для инференса "близкого к проду" нам нужно затащить туда спарк, млфлоу, панду, jvm
на легкую основу установить не получилось. Не ставилась jvm
Пришлось ставить на ubuntu 22.

После контейнер был загружена в репозиторий на docker.hub
Все по контейнере лежит тут:

https://github.com/DATravin/otus-hw9-k8/tree/stage-0.0.1/infra/app 

## kuber

Далее были поднятые все сущности на Kuber'е
подняты сервис, ингресс и деплоймент с указанием на контейнер с приложением.
Все лежит тут:
https://github.com/DATravin/otus-hw9-k8/tree/stage-0.0.1/k8s 

## результат

Кластер развернут. 4 ноды:
![image](https://github.com/user-attachments/assets/0f336e43-ae23-404b-885a-2e50c2ac5395)

поды
![image](https://github.com/user-attachments/assets/112cf276-1845-43a7-9037-a6c5b79084ee)


Тестовый режим работы приложения описан в ручке /predict
он моделирует тестовый запрос к модели, куда кидает семпл данных.
Скачивает модель из s3, осуществлеяет инференс и возвращает скоринг данного значения.

В итоге дергаем ручку и получаем результат:

![image](https://github.com/user-attachments/assets/ac21c356-591d-49c0-b27a-c9e4542018b0)

это означает, что инференс штатно сработал

Вся репа с заданием лежиим тут:
https://github.com/DATravin/otus-hw9-k8/tree/stage-0.0.1 

## разобраться с cicd сил уже не хватило:(


