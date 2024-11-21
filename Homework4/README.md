

Ход выполнения работы:

1) была создана VM
2) сделал окружение, поднял Feast
3) на базе дефолтного примера сделал 3 новых группы фичей. 2 из on demand, и одна из FeatureView
4) после этого они были оттестированы и выведены в jupyter notebook


Промежуточной задачей стало подцепить новый паркет с агрегированными данными. Я его собрал в юпитере и положил паркетом. На лету у меня сделать это не получилось.
Будем считать, что я сделал некий ETL процесс, где вывел как-будто вторую витрину данных.

![image](https://github.com/user-attachments/assets/69ba2b0f-4cb8-4ce9-9610-414dba62d426)

В данном агрегате выведены мин-среднее-макс по каждой фичей для каждого drive_id. Мне пришлось оставить поле event_timestamp, без него все ломалось. Видимо, оно обязательного для джойнов данных внутри feast.

Далее положил паркет в папку data:

![image](https://github.com/user-attachments/assets/6b3f8cd5-17d1-4b81-a9f7-2157ec4bfe5f)

Далее подцепил его в Source

![image](https://github.com/user-attachments/assets/c55eea98-7065-4f18-a416-f848d00c8577)

а дальше собрал из него FeatureView

![image](https://github.com/user-attachments/assets/6c193bfc-a1ba-4333-bb36-cdd1808dcaca)

После этого сделал 2 on demand группы фичей:

Первая -  производит нормолизацию данных по трем фичам ( (X-mean)/ std)

![image](https://github.com/user-attachments/assets/821e01c8-25ce-4593-9c0b-78162a914f6f)

Вторая делит текущиее значение, на среднее по данному driver_id. Получаем относительную фичу, которая показывает выше/ниже оно для данного driver_id и насколько

![image](https://github.com/user-attachments/assets/16d20623-347f-4579-bfdb-b42c74df7baf)

На это все.
Файл с юпитерем, и функциями приложе.

