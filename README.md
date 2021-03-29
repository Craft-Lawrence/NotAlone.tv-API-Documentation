# Документация по использования API сайта NotAlone.tv
Данная документация несёт в себе набор всех доступных методов, их назначение и описание работы.

## Использование
Все запросы необходимо направлять на адрес `https://api.notalone.tv/`  
Но сразу же в этой ссылке задаётся и метод, который вы собираетесь использовать. К примеру: `https://api.notalone.tv/getUser`  
Формат запросов: `POST`  
В каждом вашем запросе есть обязательный параметр - `apiKey`. Он содержит уникальный ключ, который можно получить только напрямую связавшись с администрацией сайта. Надёжнее всего это сделать через личные сообщения в группе в ВК: [https://vk.com/notalonetv](https://vk.com/notalonetv)  
  
API отвечает в формате `JSON`. Первым параметром всегда возвращается `result`, который имеет занчение `true` либо `false`. В случае `false`, всегда появляется дополнительный параметр `errorText`, объясняющий суть проблемы. Остальные параметры ситуативны и зависят от конкретного метода.

## Список доступных методов
### 1. Авторизация и регистрация
1. [auth](#auth)
1. [register](#register)
### 2. Работа с пользователями
1. [getUser](#getUser)
2. [getUserRooms](#getUserRooms)
3. [getFavorites](#getFavorites)
### 3. Работа с каталогом
1. [getNewest](#getNewest)
2. [getPopular](#getPopular)


## Описание методов

### auth <a name="auth"></a> 
Авторизация.  по логину и паролю. Возвращает объект `data` с данными пользователя (вместе с токеном, который необходимо сохранить в клиенте) в случае успеха и `result: false` в случае ошибки (с описанием оной в параметре `errorText`)
#### Доступные параметры:
`login` - Логин *(обязательно)*  
`password` - Пароль *(обязателен при условии авторизации через логин+пароль)*  
`vk_id` - id пользователя из ВКонтакте (только цифры, без `id` в начале);  
`fb_id` - id пользователя в Фейсбуке;  
`g_id` - id пользователя в Google.  
Один из последних трёх параметров *обязателен* в случае если авторизация проиходит **НЕ** через логин+пароль.
***
### register <a name="register"></a> 
Регистрация. Здесь важно передать либо `password`, либо `vk_id`/`fb_id`/`g_id`. Второй вариант подходит в случае если авторизация/регистрация производится через соц.сеть. Вам сперва необходимо произвести запрос в саму соц.сеть, получить необходимые данные, и лишь после этого - отправить запрос тут.
#### Доступные параметры:
`login` - Логин. Регулярка для проверки корректности: `/^[a-z]+[a-z0-9]{3,19}$/iu` *(обязательно)*  
`password` - Пароль. Регулярка для проверки корректности: `/^[a-z0-9%_+-=]{5,20}$/i` *(обязательно при условии если регистрация производится по логину и паролю, а не соц.сети)*  
`nickname` - Никнейм. Регулярка для проверки корректности: `/^[а-яеЁa-z]+[а-яеЁa-z0-9-_]{3,19}$/iu` *(обязательно)*  
`email` - Электронная почта. Регулярка для проверки корректности: `/^[-._a-z0-9]+@[-a-z0-9]+[\.][a-z]{2,10}$/i` *(обязательно)*  
`gender` - Пол. Доступные значения `male` и `female`;  
`vk_id` - id пользователя из ВКонтакте (только цифры, без `id` в начале);  
`fb_id` - id пользователя в Фейсбуке;  
`g_id` - id пользователя в Google.  
Один из последних трёх параметров *обязателен* в случае если регистрация проиходит **НЕ** через логин+пароль, а через соц.сеть. Обычно при авторизации/регистрации через соц.сеть она отдаёт данные пользователя а-ля email, пол, имя и т.д. Эти данные необходимо использовать для отправки в API `login`, `nickname`, `email` и `gender`. Если соц.сеть не отдала хотя бы емейл (например, часто люди не вводят свой email в вк), то регистрацию произвести по данной соц.сети **невозможно**.
***
### getUser <a name="getUser"></a> 
Получение всей информации о пользователе по его id.
#### Доступные параметры:
`userID` - id пользователя *(обязательно)*
***
### getUserRooms (необходимо доработать) <a name="getUserRooms"></a> 
Получение массива из id всех комнат, в которых есть пользователь.
#### Доступные параметры:
`userID` - id пользователя *(обязательно)*
***
### getFavorites <a name="getFavorites"></a> 
Получение избранных списков пользователей. Если передать лишь `userID`, API вернёт по `10` избранных материалов пользователя в каждой из четырёх категорий (`Смотрю сейчас`, `Буду смотреть`, `Посмотрел`, `Перестал смотреть`). Можно конкретизировать, какой именно список нужно получить с помощью параметра `status`. В таком случае становится доступным ещё один параметр - `page`.  
  
В результате запроса возвращается объект с набором ключ-значение. В качестве ключей выступают идентификаторы избранного списка `1-4`, а значениями - массивы с элементами избранного списка.
#### Доступные параметры:
`userID` - id пользователя *(обязательно)*  
`status` - Какой именно статус нужно подгрузить. Доступные значения (int): 0 - все четыре списка, 1 - `Смотрю сейчас`, 2 - `Буду смотреть`, 3 - `Посмотрел`, 4 - `Перестал смотреть` *(default: 0)*  
`page` - Страница избранного списка. API возвращает по `10` элементов, поэтому чтобы увидеть больше 10 элементов необходимо запрашивать их постранично *(default: 1)*  
***