# Market bot

Данный скрипт автоматизирует сбор заказов на https://megamarket.ru/

Скрипт выполняет следующие шаги:
1. Открывает браузерную сессию в chrome с персистентным профилем;
2. Авторизуется на https://megamarket.ru/ с помощью номера телефона из https://sms-activate.org/ru;
3. Собирает корзину товаров;
4. Оформляет заказ.

### Запуск

Чтобы запустить скрипт необходимо установить [Node18+](https://nodejs.github.io/nodejs.dev/en/download/)

После установки ноды откройте терминал или командную строку и выполните там следующую команду:

```shell
cd <путь до папки проекта>
```

Далее запустите в папке проекта для установки зависимостей:

```shell
npm i
```

Далее там же выполните команду

```shell
npm run init
```

Она создаст необходимые конфигурационные файлы: [config.yml](#configyml), [cart.csv](#cartcsv), [promo.csv](#promocsv)

Заполните их в соответствии со спецификацией ниже

После заполнения всех файлов запустите скрипт командой

```shell
npm run start
```

Скрипт создаст папку пользователя в [user_data_dir](#userdatadir).

Чтобы открыть сохраненный профиль выполните команду:

```shell
npm run profile <название папаки профиля>
```

## config.yml

В файле конфига необходимо определить поля для работы скрипта

### user_data_dir

В данном поле необходимо указать путь до директории, в которой будут сохраняться профили `chrome`.
Чтобы узнать где находится эта папка выполните следующие шаги 
1. Откройте `chrome` и перейдите на `chrome://version`; 
2. Там увидите поле "Путь к профилю".
Например: `C:\Users\Username\AppData\Local\Google\Chrome\User Data\Profile 1`;
3. Нас интересует родительская директория, т.е `C:\Users\Username\AppData\Local\Google\Chrome\User Data`.

Вставьте данный путь в конфиг с экранированием обратных слешей `\\`:
```
C:\\Users\\Username\\AppData\\Local\\Google\\Chrome\\User Data
```

В последствии в этой папке будут сохраняться профили в папках в формате:
```
<дата>-<случайный хеш>
```
### sms_activate_params

Данный сервис работает с api сервиса [sms-activate](https://sms-activate.org) для активации нового пользователя через смс.
В этой структуре хранятся поля для работы скрипта.

#### api_key

Обязательное поле. 
Для взаимодействия с api сгенерируйте ключ [тут](https://sms-activate.org/ru/api2) и введите его в это поле.

#### maxPrice

Иногда по обычной цене нет номеров. Если вы хотите покупать номер в любом случае, немного переплачивая,
то укажите в этом поле максимальную сумму которые готовы уплатить за номер.
Подробнее [тут](https://sms-activate.org/ru/api2) и [тут](https://sms-activate.org/ru/info/freeprice).

Пример: `60`.

### postfix

Данный параметр поможет кастомизировать название папки профиля.
Значение данного параметра будет добавлено в конце названия папки с профилем.
Таким образом если указан этот параметр, то название папки будет выглядеть так:

```
<дата>-<случайный хеш>-<postfix>
```

### launch_params

В данном поле содержится структура с параметрами запуска браузера.

#### headless

Опциональный параметр. Принимает `boolean` значения, т.е `true` или `false`.

Если `true`, то браузер будет запущен без ui -- тихо отработает в консоли.

Если `false`, то браузер запустится как обычно с ui.

По умолчанию: `false`.

#### executable_path

Обязательный параметр указаывает путь на приложение браузера `chromium` которое необходимо запустить.
Узнать его можно также на `chrome://version`

Пример: `C:\\Program Files (x86)\\Google\\Chrome\\Application\\chrome.exe`.
Обратите внимание на удвоение слешей в пути - это обязательное требование.

#### pause_page_on_end

Опциональный параметр. Принимает `boolean` значения, т.е `true` или `false`.

Если `true`, то в конце выполнения скрипта браузер останется открытым.

Если `false`, то браузер будет закрыт в конце выполнения скрипта.

По умолчанию: `false`.

### address

Адрес доставки, на котором будут получать товары.

Пример: `Волгоград, улица 64 Армии, 12"`.

### payment_method

Обязательный параметр.

Оплата происходит через `SberPay`. Укажите в этом поле номер телефона по которому будет проходить оплата.

Пример: 9055653535. Обратите внимание на формат: номер начинается с `9` без кода страны.

### comment

Опциональный параметр с комментарием для доставки. Значение по умолчанию отсутствует.

### new_user_data:

В данном поле содержится структура с данными, которые будут введены в форму регестрации нового пользователя.

Структура содержит следующие поля.

#### password

Обязательное поле: пароль для аккаунта.

Пример: `Vfrcbv99+`.

#### first_name

Опциональный параметр. Имя пользователя.

По умолчанию: Олег.

#### last_name

Опциональный параметр. Фамилия пользователя.

По умолчанию: Иванов.

#### dob

Опциональный параметр. Дата рождения пользователя.

По умолчанию: 01012001. Обратите внимание формат без точек и прочих разделителей

#### email

Опциональный параметр. Email пользователя.

По умолчанию: `oleg.<случайный хеш>@bla.ru`.

За счет случайного хеша мейл каждый раз будет уникальным.
Если вы введете в это поле конкретное значение, то его придется обновлять при каждом запуске.

---

Пример заполненного полностью конфига:
```yaml
user_data_dir: "C:\\Users\\Username\\AppData\\Local\\Google\\Chrome\\User Data"
sms_activate_params:
  api_key: "c7beA07c7414cbb32325574ed52fc054"
  maxPrice: 60
launch_params:
  headless: false
  executable_path: "C:\\Program Files (x86)\\Google\\Chrome\\Application\\chrome.exe"
  pause_page_on_end: true
postfix: "click"
address: "Волгоград, улица Пушкина, 10"
comment: "Звонить сюда: 88005553535"
payment_method: "8005553535"
new_user_data:
  password: "Vfcrbv99+"
  first_name: "Иван"
  last_name: "Петров"
  dob: "010120001"
  email: "vova@mail.ru"
```

Пример заполненного конфига, только обязательные поля:
```yaml
user_data_dir: "C:\\Users\\Username\\AppData\\Local\\Google\\Chrome\\User Data"
sms_activate_params:
  api_key: "c7beA07c7414cbb32325574ed52fc054"
launch_params:
  executable_path: "C:\\Program Files (x86)\\Google\\Chrome\\Application\\chrome.exe"
address: "Волгоград, улица Пушкина, 10"
payment_method: "88005553535"
new_user_data:
  password: "Vfcrbv99+"
```

---

## cart.csv

В этом файле нужно разместить товары, которые нужно купить в результате работы скрипта.

Заголовок файла трогать нельзя. Это важно для работы скрипта:

```
url,count,adult
```

После заголовка напишите через запятую без пробелов

1. Ссылку на товар в мегамаркете;
2. Целое число - количество товара которое нужно купить;
3. Опциональное поле `adult`. Поставьте `1` если товар 18+, иначе оставьте пустым.
При наличии данного флага скрипт будет знать что при попытке купить товар нужно будет кликнуть в подтверждение возраста;

Примеры:

Товар 18+
```
https://megamarket.ru/catalog/details/napitok-adrenaline-game-fuel-energeticheskiy-449ml-100025762909/#?related_search=adrenaline%20rush,19,1
```

Обычный товар:
```
https://megamarket.ru/catalog/details/penal-bez-napolneniya-canbi-555546-shkolnyy-belyy-600012869551_82730/#?related_search=пеналы,5,
```

Итоговый файл:
```
url,count,adult
https://megamarket.ru/catalog/details/penal-bez-napolneniya-canbi-555546-shkolnyy-belyy-600012869551_82730/#?related_search=пеналы,5,
https://megamarket.ru/catalog/details/napitok-adrenaline-game-fuel-energeticheskiy-449ml-100025762909/#?related_search=adrenaline%20rush,19,1
```

Помните, что вы всегда можете сделать выгрузку в формате `csv` из `Google Sheets` или `Excel`

| ❗️❗️️⚠️⚠️ **WARNING** ⚠️⚠️❗️❗️                                                             |
|:-------------------------------------------------------------------------------------------|
| **ОБРАТИТЕ ВНИМАНИЕ**: перед запуском скрипта убедитесь в корректности заполненной корзины |

## promo.csv

В данном файле разместите промокоды, которые будут использоваться при оформлении заказов.

Заголовок файла трогать нельзя. Это важно для работы скрипта:

```
code,used
```

Поле `used` заполняется автоматически по мере работы скрипта.
После каждого запуска для использованого промокода в этом поле будет выставлено название папки профиля chrome,
который применил (или попытался применить) данный промокод.

Пример заполненного файла:

```
code,used
gdhsaj,
ashdsahjk,
dhsjakdhaj,
dhsajkdha,
```

После первого запуска содержимое файла изменится примерно так:

```
code,used
gdhsaj,13.02.2024-8646cf7ce5b
ashdsahjk,
dhsjakdhaj,
dhsajkdha,
```

Рядом с названием промокода будет указано название профиля, который попытался его применить.
Таким образом если зайдя в профиль вы увидите, что произошла ошибка и корзина не была собрана, вы можете попытаться применить этот промокод снова.

Если на момент запуска скрипта неактивированных промокодов не будет, то будет выброшено исключение и скрипт не запустится.

В отличие от [cart.csv](#cartcsv) данный файл можно не проверять перед каждым запуском скрипта,
а только лишь дополнять по мере использования промокодов
