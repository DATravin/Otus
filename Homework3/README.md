Я скопировал не все файлы, а 3 (для экономии времени)

Файлы на s3 и на hdfs

Скрипт для очистки данных лежит на прокси и мастер ноде

Для вызова небходимо запустить python3 cleaning_data.py -fn '2022-11-04.txt'
передав в качестве параметра названия файла на hdfs

![image](https://github.com/user-attachments/assets/4a6aff37-17d2-4125-aa67-93a9f73821bf)




Логика обработки:
1) парсим данные из txt
2) далее удаляем выброс tx_amount с помощью 1% и 99% перцентилей (откидываем 2% данных снизу и сверху)
3) далее удаляем всякие "мутные" значения из полей, где не должно быть текста. Это всякие записи типа "error"
4) удаляем данные, где есть пропущенне значения
5) форматируем все столбцы, приводя их к правильным типам данных
6) записываем в ввиде паркетов с названием типа /user/ubuntu/data/20221104.parquet


![image](https://github.com/user-attachments/assets/a03997e0-0784-4e1c-94cb-84fa687168af)

После обработки мы видим паркеты на hdfs вместе с оригинальными данными:

![image](https://github.com/user-attachments/assets/d25324a0-29cd-496e-83ac-364c0767bca1)


Далее еще один скрипт upload_data_from_hdfs перекладывает файлы обратно в S3

![image](https://github.com/user-attachments/assets/bfbaac05-ccfa-405d-aff7-3afaeb628587)

так же можем посмотреть из UI

![image](https://github.com/user-attachments/assets/8400e6e5-63f3-47a7-953a-1d48f2e2e1e8)

Внутри все разбито на 10 партиций:

![image](https://github.com/user-attachments/assets/b4472c8c-d949-4f78-9821-b7e678537c3e)


Весь код со скриптами и main.tf лежит в [https://github.com/DATravin/otus-hw3-valid-data ](https://github.com/DATravin/otus-hw3-valid-data)

Файл main.tf: https://github.com/DATravin/otus-hw3-valid-data/blob/main/infrastructure/main.tf

Файл со скриптом очистки: https://github.com/DATravin/otus-hw3-valid-data/blob/main/infrastructure/scripts/cleaning_data.py

Файл со скриптом для перекладывания на s3: https://github.com/DATravin/otus-hw3-valid-data/blob/main/infrastructure/scripts/upload_data_from_hdfs.sh


