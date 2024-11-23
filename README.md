# Домашнее задание к занятию "`Конфигурация приложений`" - `Макаров Денис`



---

### Задание 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу

1. Создать Deployment приложения, состоящего из контейнеров nginx и multitool.
2. Решить возникшую проблему с помощью ConfigMap.
3. Продемонстрировать, что pod стартовал и оба контейнера работают.
4. Сделать простую веб-страницу и подключить её к Nginx с помощью ConfigMap. Подключить Service и показать вывод curl или в браузере.
5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

####  Решение

Создадим namespace задания представлен на скриншоте ниже:

![ns kubdz2-3](https://github.com/user-attachments/assets/ef413804-be75-435c-998e-49a6b940214f)

Service представлен на скриншоте ниже:

![service](https://github.com/user-attachments/assets/8e2dd3cc-ebdb-47f0-9dfd-65a417c7fbfd)

Поднимаем сервис представлен на скриншоте ниже:

![apply service](https://github.com/user-attachments/assets/80b10581-4508-4eba-abaf-4ba3438c2aed)

ConfigMap представлен на скриншоте ниже:

![configmap](https://github.com/user-attachments/assets/42c16b8d-eb3e-423f-a9c8-52726835f517)


Поднимаем configMap представлено на скриншоте ниже:

![apply config map](https://github.com/user-attachments/assets/814a39e1-dd9e-4490-9e5c-75048a1bcf5c)

Deployment представлен на скриншоте ниже:

![deployment](https://github.com/user-attachments/assets/8a33665e-2a66-48cc-adcb-d94def04d567)

Поднимаем deployment представлено на скриншоте ниже:

![apply deployment](https://github.com/user-attachments/assets/e8774aa5-a0da-43de-b752-b57492ee1f47)

![describe deployment1](https://github.com/user-attachments/assets/c7bb515c-c0a7-4d31-97f1-21c56e8fe744)

Проверяем curl представлено на скриншотах ниже:

![curl 32000](https://github.com/user-attachments/assets/71a2320a-f3ce-4942-a16e-9a15b08f7f82)

![curl 32001](https://github.com/user-attachments/assets/40a54465-9e8b-4ab7-ab51-88fdac0e569b)

И через браузер:

![curl 32000 brose](https://github.com/user-attachments/assets/afbe211a-c23d-4889-aeb5-c15efc652725)

![curl 32001 brose](https://github.com/user-attachments/assets/3339a690-758a-42ec-965d-d91cfc0b6d2e)

------

### Задание 2. Создать приложение с вашей веб-страницей, доступной по HTTPS 

1. Создать Deployment приложения, состоящего из Nginx представлен на скриншоте ниже:

![deployment_https](https://github.com/user-attachments/assets/b8636009-825b-4c9b-8d61-35d47fa3b5dc)

2. Создать собственную веб-страницу и подключить её как ConfigMap к приложению представлен на скриншоте ниже:

![configmap_https](https://github.com/user-attachments/assets/6183d8ea-4f85-4602-8563-8ee83261a6ad)

3. Выпустить самоподписной сертификат SSL. Создать Secret для использования сертификата представлено на скриншотах ниже:

![cert](https://github.com/user-attachments/assets/ef6c9019-b54d-4b33-a66d-5dc302a75f29)

![ls cert](https://github.com/user-attachments/assets/8be64b0d-1348-4b52-b0c4-eef620c2c3b7)

4. Создать Ingress и необходимый Service, подключить к нему SSL в вид. Продемонстрировать доступ к приложению по HTTPS.
Создаем secret представлено на скриншотах ниже:

![secret](https://github.com/user-attachments/assets/11b6961a-7cee-4575-94e2-a66ed07e5fb3)

![secret file](https://github.com/user-attachments/assets/cd714874-d5ac-4183-b151-5470e923b397)

Ingress представлен на скриншоте ниже:

![ingress_https](https://github.com/user-attachments/assets/63eb0b65-cef4-4bd4-9d58-091e3e677596)


Service представлен на скриншоте ниже:

![service_https](https://github.com/user-attachments/assets/4be13e8e-0f4c-4324-a362-1994d123e2be)

Запускаем все и проверяем представлено на скриншотах ниже:

![zapusk](https://github.com/user-attachments/assets/84174e0e-6f42-4ee6-86eb-a71ea55d9327)

Прописываем DNS test.local в файл ```hosts``` и проверяем доступность измененной страницы представлено на скриншотах ниже:

![hosts](https://github.com/user-attachments/assets/bd5b2859-232c-4317-a234-c726cb830e45)

![curl test](https://github.com/user-attachments/assets/b60cc07c-2022-4117-b6e0-3b88909b8fbb)

И в браузере представлено на скриншоте ниже:

![curl test local sertificat](https://github.com/user-attachments/assets/fc411f7a-5028-4c86-8291-662a940fa17f)


5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

