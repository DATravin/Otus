# Итоговые график


План презы:

1) цель продукта (некий торговый бот, который прогнозирует цену актива. А точнее дает сигнал, short, long, hold

2) Архитектура решения:

Продукт разделен на 2 блока. 

2.1) Обучение модели 
Схема архитектуры: S3 бакет - Mlflow (S3 + Postgree)  - Airflow - кластер   
Цель - обучить модель (оптимизация) - положить ее в Mlflow / S3

2.2) Deploy model
Схема архитектуры S3 бакет - Vm (Docker) - Dockerhub - Kuber 

Схема движения данных (загрузка модели S3 -> Vm(docker) -> Dockerhub - Kuber 

3) отработка первого этапа: расчет фичей - отбор признаков - оптимизация гипер параметров:

   Он реализован в виде 2-х дагов в airflow

   Здесь демонстрация штатной отработки всех дагов:

  ![image](https://github.com/user-attachments/assets/1f261026-e7a4-4e46-8d10-7f9b4e95bc17)


   Модели загружены в mlflow:

   ![image](https://github.com/user-attachments/assets/c4f2b4ed-0221-4492-8030-15c560c35e16)


   сами модели

   ![image](https://github.com/user-attachments/assets/50a5f961-770d-4375-a33b-dfc2ce6dcfd6)


4) кубер поднять

   Вердикты выдаются:

![image](https://github.com/user-attachments/assets/0d21dbe8-bfb3-4002-8194-1df523366cb5)


   ![image](https://github.com/user-attachments/assets/640803bd-49d4-494e-9ddf-f79347cfe03b)


   ![image](https://github.com/user-attachments/assets/e8dd770f-f75f-4d0b-82c2-3ced4fc30d3d)



![image](https://github.com/user-attachments/assets/50866f11-a55e-40fd-bc16-29fd6fbf0ee0)


