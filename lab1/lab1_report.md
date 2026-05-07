# Лабораторная работа №1  
## Обзор Google Cloud и исследование основных сервисов

University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Cloud platforms as the basis of technology entrepreneurship](https://)  
Year: 2025/2026  
Group: U4125  
Author: Voroshnina Eva Pavlovna  
Lab: Lab1  
Date of create: 05.05.2026  
Date of finished:  

---

## Цель работы

Ознакомиться с основными возможностями и преимуществами облачной платформы Google Cloud, а также получить практические навыки работы с IAM, Service Accounts, Compute Engine и Cloud Storage.

---

## Ход работы

### 1. Получение доступа к Google Cloud

Для выполнения лабораторной работы был получен доступ к проекту Google Cloud `cloud-platforms-as-the-basis`.

После входа в Google Cloud Console был выбран учебный проект, в рамках которого выполнялась лабораторная работа.

---

### 2. Создание service account

В разделе **IAM & Admin → Service Accounts** был создан service account с именем:

```text
evoroshnina-sa-lab1
```
<img width="1470" height="724" alt="Снимок экрана 2026-05-07 в 6 49 11 PM" src="https://github.com/user-attachments/assets/d9f8e8ce-8795-42c8-8c68-735a356dd03c" />


### 3. Создание виртуальной машины
#### В разделе Compute Engine → VM instances была создана виртуальная машина со следующими параметрами:
Name: evoroshnina-vm-lab1
Machine type: e2-micro
Provisioning model: Spot
OS: Debian GNU/Linux 12
Service account: evoroshnina-sa-lab1

<img width="1466" height="669" alt="Снимок экрана 2026-05-07 в 6 48 05 PM" src="https://github.com/user-attachments/assets/a3277b95-ad48-4e65-b2d5-0a41ad53d69e" />



<img width="810" height="713" alt="Снимок экрана 2026-05-07 в 6 59 01 PM" src="https://github.com/user-attachments/assets/23d1a9bf-ab6a-4f2a-ab46-f9070be2862e" />



<img width="604" height="755" alt="Снимок экрана 2026-05-07 в 6 59 18 PM" src="https://github.com/user-attachments/assets/40eb281b-1789-4781-b85e-3a0071e020ab" />



### 4. Подключение к VM через SSH

После создания виртуальной машины было выполнено подключение к ней через встроенный SSH-клиент Google Cloud.

В терминале была создана локальная папка для файлов лабораторной работы:

```bash
mkdir ~/lab1-files
cd ~/lab1-files
```

### 5. Просмотр содержимого бакета Cloud Storage
С помощью утилиты gcloud был просмотрен бакет lab1-bucket-itmo:
gcloud storage ls gs://lab1-bucket-itmo
В результате выполнения команды были найдены следующие файлы:
gs://lab1-bucket-itmo/pic1.jpg
gs://lab1-bucket-itmo/pic2.jpg
gs://lab1-bucket-itmo/pic3.jpeg
Таким образом, с помощью gcloud было подтверждено наличие трех файлов в бакете Cloud Storage.

<img width="934" height="662" alt="Снимок экрана 2026-05-07 в 7 01 58 PM" src="https://github.com/user-attachments/assets/8a99a2d9-2556-419b-9957-6fc71097fbcf" />


### 6. Копирование файлов из бакета на виртуальную машину
Далее файлы были скопированы из Cloud Storage в локальную папку на виртуальной машине:
gcloud storage cp gs://lab1-bucket-itmo/* ~/lab1-files/
После копирования была выполнена проверка содержимого локальной папки:
ls -lah ~/lab1-files
В результате в папке lab1-files были отображены три файла:
pic1.jpg
pic2.jpg
pic3.jpeg
Это подтверждает, что файлы были успешно скопированы из бакета Cloud Storage на виртуальную машину.

<img width="769" height="521" alt="Снимок экрана 2026-05-07 в 7 13 49 PM" src="https://github.com/user-attachments/assets/dabfb132-eefa-4be3-8b95-92796d4660c5" />


### 7. Изменение роли service account
После успешного копирования файлов роль service account была изменена в разделе IAM & Admin → IAM.
Изначальная роль service account:
Storage Admin
Новая роль service account:
Compute Viewer
Роль Compute Viewer предназначена для просмотра ресурсов Compute Engine и не предоставляет прямых прав на управление объектами Cloud Storage.

<img width="1298" height="460" alt="Снимок экрана 2026-05-07 в 7 53 43 PM" src="https://github.com/user-attachments/assets/3a4e3076-698d-45eb-95f1-76b2d8766949" />


### 8. Повторная попытка копирования файлов
После изменения роли service account была выполнена повторная попытка скопировать файлы из бакета Cloud Storage:
```bash
mkdir ~/lab1-files-test
gcloud storage cp gs://lab1-bucket-itmo/* ~/lab1-files-test/
```

Ожидалось, что после изменения роли с Storage Admin на Compute Viewer доступ к Cloud Storage будет запрещен, так как новая роль не предназначена для работы с объектами Cloud Storage.
Однако повторное копирование файлов продолжило выполняться успешно. После этого была выполнена дополнительная попытка обновить авторизацию:
gcloud auth revoke --all

Команда завершилась ошибкой:
ERROR: (gcloud.auth.revoke) Cannot revoke GCE-provided credentials.
Это означает, что виртуальная машина использовала учетные данные, предоставляемые Google Compute Engine. После перезапуска виртуальной машины повторная попытка копирования также завершилась успешно.
Возможная причина такого результата заключается в том, что доступ к бакету lab1-bucket-itmo может быть дополнительно разрешен на уровне самого бакета, проекта или через используемые учетные данные виртуальной машины. Это показывает, что итоговый доступ к ресурсам Google Cloud определяется не только одной ролью service account, но и совокупностью IAM-политик, настроек доступа к бакету и учетных данных VM.

<img width="1290" height="681" alt="Снимок экрана 2026-05-07 в 7 54 25 PM" src="https://github.com/user-attachments/assets/e4b2dd26-1a89-4fe4-bddb-9a76d1f87b6a" />

### 9. Удаление созданных ресурсов
После завершения лабораторной работы созданные ресурсы были удалены:
evoroshnina-vm-lab1
evoroshnina-sa-lab1
Удаление ресурсов было выполнено для предотвращения дальнейшего использования ресурсов Google Cloud.

<img width="1177" height="756" alt="Снимок экрана 2026-05-07 в 7 19 26 PM" src="https://github.com/user-attachments/assets/39241429-9293-441e-b858-362e517e7938" />

<img width="1227" height="694" alt="Снимок экрана 2026-05-07 в 7 21 45 PM" src="https://github.com/user-attachments/assets/3d97a6a6-0410-4c63-aa0d-2a4047b45ca1" />


## Вывод

В ходе лабораторной работы были изучены основные сервисы Google Cloud: IAM, Service Accounts, Compute Engine и Cloud Storage.
Был создан service account evoroshnina-sa-lab1, которому была назначена роль Storage Admin. Затем была создана виртуальная машина evoroshnina-vm-lab1 с типом e2-micro в режиме Spot. После подключения к виртуальной машине через SSH с помощью утилиты gcloud был найден бакет lab1-bucket-itmo, а также были скопированы три файла из Cloud Storage в локальную папку на VM.
Также была проверена работа IAM-ролей при изменении прав service account. После замены роли Storage Admin на Compute Viewer повторное копирование файлов продолжило выполняться. Возможной причиной является наличие дополнительных разрешений на уровне бакета, проекта или использование учетных данных виртуальной машины.
В результате лабораторной работы были получены практические навыки работы с основными сервисами Google Cloud, а также было рассмотрено влияние IAM-ролей на доступ к облачным ресурсам. После завершения работы все созданные ресурсы были удалены.
