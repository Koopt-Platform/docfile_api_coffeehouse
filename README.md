# API v1

Всё взаимодействие происходит в формате JSON (как запросы к серверу, так и ответы от сервера).

Во все запросы нужно добавлять следующие заголовки:

* `Content-Type: application/json`
* `Accept: application/json`
* `Authorization`: token-пользователя, который генерируется при регистрации (кроме запроса на регистрацию)
* `X-Timezone: -5` (для определения таймзоны я использую скрипт https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Date/getTimezoneOffset)

### Ошибки

    {
      "error": {
        "code": 0,
        "message": "string"
      },
      "success": false
    }

## Пользователь
### Отправка СМС
    POST /api/v1/user/verify
    AUTH no
    PARAMS (POST body)
      {
          "phone": "+79999999999"
      }
    RESPONSE
    {
      "success": true,
      "payload": {}
    }

### Авторизация
    POST /api/v1/user/auth
    AUTH no
    PARAMS (POST body)
      {
        "phone": "+79999999999",
        "code": "0000"
      }
    RESPONSE
    {
      "success": true,
      "payload": {
        "token": "string"
      }
    }

### Информация о текущем пользователе
    GET /api/v1/user
    AUTH yes
    PARAMS no
    RESPONSE
    {
      "success": true,
      "payload": {
        "id": 1,
        "name": "Ivan Ivanov",
        "phone": "+79999999999"
      }
    }

## Кофейни
### Список кофейн
    GET /api/v1/coffeehouse
      AUTH yes
      PARAMS (QUERY STRING)
        lat=55.55 (lat и lon используете для подсчета растояния)
        lon=55.55 
        page=1 (сортируете по дистанции)
        search="Пат" (поиск по строке)
      RESPONSE
      {
        "success": true,
        "payload": [
          {
            "id": 1,
            "name": "Патрики",
            "address": "Большой Патриарший переулок, 8к2",
            "coordinates": {
              "lat": 56.2235232 (число),
              "lon": 35.5463434
            },
            "time_work": {
              "rondo_color": "red | yellow | green",
              "value": "открыто до 20:00",
              "access_mode": true (пропускной режим) 
            },
            "image": "https://...png", (миниатура 100х100, обязательно по https) 
            "distance": 1.221, (расстояние до кофейни в км.)
            "active": true (обслуживает ли в данный момент кофейня)
          }
        ]
      }
### На карте
    GET /api/v1/coffeehouse/maps
      AUTH yes
      PARAMS (QUERY STRING)
        lat=55.55
        lon=55.55
        latitudeDelta=0.0922 (используя эти данные вы вычисляете область видимости на карте)
        longitudeDelta=0.0421 
      RESPONSE
      {
          "success": true,
          "payload": [
              { 
                "id": 1,
                "coordinates": {
                  "lat": 56.2235232,
                  "lon": 35.5463434
                },
                "active": true (обслуживает ли в данный момент кофейня)
              }
          ]

      }
### Карточка кофейни
    GET /api/v1/coffeehouse/:id
      AUTH yes
      PARAMS no
      RESPONSE
      {
          "success": true,
          "payload":  [
             {
                "id": 1,
                "coordinates": {
                  "lat": 56.2235232,
                  "lon": 35.5463434
                },
                "name": "Патрики",
                "address": "Большой Патриарший переулок, 8к2",
                "title": "Как добраться",
                "description": "Выходите из метро «Чистые пруды» ...",
                "phone": "+7999999999",
                "time_work": {
                  "rondo_color": "red | yellow | green",
                  "value": "открыто до 20:00",
                  "access_mode": true (пропускной режим) 
                },
                "active": true (обслуживает ли в данный момент кофейня)
              }
          ]

      }
