




# План работы

Комментарий: я пока не понял, как отдельно создать S3, чтобы не убивать хранилище при разборке кластера. Ищу примеры реп, где это делает. Пока не нашел.
Потому как копировать 120 GB каждый раз - это очень долго. Но при этом хранить их постоянно...это же тоже будет кушать деньги. Очень хочется посмотреть реализацию репы с разбивкой по модулям. Самостоятельно пока не получается сделать все, как надо:( 

Скрипт main.tf и variebles.tf лежат рядом. 
на отдельные модули я не разбивал. Пока изучаю, как это сделать. 

## Создать бакет S3

Бакет создается в общем процессе внутри main.tf
bucket_name = "otus-bucket-b1gcs058gd4fa8eeviec"
Он публичный:

![image](https://github.com/user-attachments/assets/6a7b20fe-531f-4b4d-8bbb-190dc63ebce5)

Посмотреть содержимое:
s3cmd --config=/home/ubuntu/.s3cfg ls s3://otus-bucket-b1gcs058gd4fa8eeviec


## скопировать данные 

Данные копируются внутри скрипта user_date.sh

![image](https://github.com/user-attachments/assets/556eadb2-b59b-4ebf-8c0e-766c4774c842)

Результат:

![image](https://github.com/user-attachments/assets/21ec9da3-7538-4663-9b3b-d7275b3ab5e2)


## поднять Spark-cluster

![image](https://github.com/user-attachments/assets/03b689fe-9ea6-4fbd-8587-d143265c6e47)

Поднят. Но пока с ограниченным ресурсом (40 GB на хост)

Тут параметры:

![image](https://github.com/user-attachments/assets/a5a09787-2991-4710-9cbb-7f0d94520496)


## подрубится к мастер-ноде. И скопировать из S3 в HDFS

Сделано. Вот результаты:
Все 40 объектов на HDFS

![image](https://github.com/user-attachments/assets/ab8ccb39-ccbd-4ef0-9e6a-3c93253c45de)

## биллинг.

Кластер в текущей конфигурации стоит:

![image](https://github.com/user-attachments/assets/83acd90b-c26f-45f8-8734-e1f262cba330)

Если 35 в час, то это около 25К в месяц.
В случае S3 я не нашел четкой тарификации. В тарифной сетке, как-будто стоимость зависит от обращений к нему.
Если же сложить туда наши 120GB и не трогать, то сколько это будет стоит - не понятно.
По факту, пока стоимость содержания у меня = 0. (по S3). Но, очевидно S3 сильно дешевле.

## Предложить способы для оптимизации затрат

Ничего кроме прерываемых VM не знаю:(

## занести в канбан

https://github.com/users/DATravin/projects/3/views/1 
добавил 2 таски ДО основного пайплана. Загрузить данные на hdfs с s3

## Разрушить все 

![image](https://github.com/user-attachments/assets/7ad239a6-2e62-4ed0-8ac0-4f0111a3f9ed)



Загрузку всех файлов на s3 делал командой:

s3cmd cp \
    --config=/home/ubuntu/.s3cfg \
    --acl-public \
    s3://otus-mlops-source-data/* \
    s3://otus-bucket-b1gcs058gd4fa8eeviec/


    


