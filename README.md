# API v1

Всё взаимодействие происходит в формате JSON (как запросы к серверу, так и ответы от сервера).
Все ресурсы должны отдаваться по HTTPS
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
            "preview_image": "https://...png", (миниатура 100х100, обязательно по https) 
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
                "image": "https://...png", (детальное изображение, обязательно по https),
                "time_work": {
                  "rondo_color": "red | yellow | green",
                  "value": "открыто до 20:00",
                  "access_mode": true (пропускной режим) 
                },
                "active": true (обслуживает ли в данный момент кофейня)
              }
          ]

      }

## Магазин
### Категории
    GET /api/v1/categoryes
      AUTH yes
      PARAMS (QUERY STRING)
        cafe_id=1
      RESPONSE
      {
          "success": true,
          "payload":  [
              {
                "id": 1,
                "name": "Все напитки",
                "in_search": false (чтоб я мог исключить из поиска данный каталог, см. Макет Поиска по напиткам)
              },
              {
                "id": 2,
                "name": "Недавние напитки", (содержит ранее приобретенные напитки, уникален для каждого пользователя)
                "in_search": false
              },
              {
                "id": 3,
                "name": "Черный кофе",
                "in_search": true
              }
          ]
      }
### Товары
    GET /api/v1/categories/:category_id/products
      AUTH yes
      PARAMS (QUERY STRING)
        cafe_id=1
        search="Кофе"
      RESPONSE
      {
          "success": true,
          "payload":  [
              {
                "id": 1,
                "name": "Большой капучино",
                "description": "Двойная порция эспрессо и молоко: насыщенный кофейный вкус c ...",
                "price": 350,
                "image": [
                    "https://..../image.png"
                ],
                "label": [
                    {
                        color: "#F8B878",
                        value: "Новинка"
                    },
                    {
                        color: "red",
                        value: "Хит продаж"
                    }
                ]
              }
          ]
      }  
### Товар
    GET /api/v1/product/:id
      AUTH yes
      PARAMS (QUERY STRING)
        cafe_id=1
      RESPONSE
      {
          "success": true,
          "payload":  {
                "id": 1,
                "name": "Большой капучино",
                "description": "Двойная порция эспрессо и молоко: насыщенный кофейный вкус c ...",
                "price": 350,
                "image": [
                    "https://..../image.png"
                ],
                "label": [
                    {
                        color: "#F8B878",
                        velue: "Новинка"
                    }
                ],
                "offer": {
                    "title": "Выберите вкус зерна | Выберите молоко",
                    "items": [
                        {
                            "id": 1,
                            "name": "Нейтральный",
                            "price": 0,
                            "active": true, (активным может быть только у одного из списка)
                            "images": [
                                "https:.../1.png",
                                "https:.../2.png",
                            ]
                        }
                    ]
                },
                "more_products": {
                    "title": "Добавить в напиток",
                    "products": [
                        {
                            "title": "Выбрать молоко",
                            "price": 50,
                            "image": "https://..",
                            "options": [
                                {
                                    "name": "Подогреть",
                                    "value": false
                                }
                            ],
                            "max_quantity": 2,
                            "items": [
                                {
                                    "id": 1,
                                    "name": "Овсяное 2%",
                                    "price": 50,
                                    "images": [
                                        "https:.../1.png",
                                        "https:.../2.png",
                                    ]
                                }
                            ]
                        }
                    ]
                }
         }
          
      }      
### Добавление в Корзину
так же требуется изменение кол-ва, Удаление

    POST /api/v1/baskets
        AUTH yes
        PARAMS (POST body)
          {
            "cafe_id": 1,
            "product_id": 1,
            "price": 300,
            "quantity": 1,
            "offer": {
                "id": 1,
                "?name": "Нейтральный"
            },
            "more_products": [
                {
                    "id": 1
                    "?name": "Сахар",
                    "price": 50
                    "quantity": 1
                }
            ],
            "sum_price": 350
          }
        RESPONSE (возвращает Корзину)
### Изменение кол-ва в Корзине
    PUT /api/v1/baskets/:id
        AUTH yes
        PARAMS (POST body)
          {
            "quantity": 1
          }
        RESPONSE (возвращает Корзину)
###  Удаление из корзины
    DELETE /api/v1/baskets/:id
        AUTH yes
        PARAMS (POST body)
          { }
        RESPONSE (возвращает Корзину)
### Корзина
    GET /api/v1/baskets
      AUTH yes
      PARAMS (QUERY STRING)
        cafe_id=1
      RESPONSE
      {
          "success": true,
          "payload":  [
              {
                "id": 112,
                "product_id": 1,
                "name": "Большой капучино",
                "price": 350, (с учетом всех доп товаров)
                "quantity": 1,
                "image": "https://..../image.png",
                "offer": {
                    "id": 1,
                    "name": "Нейтральный"
                },
                "more_products": [
                    {
                        "id": 1
                        "name": "Доп. экспрессо",
                        "price": 50
                        "quantity": 1
                    }
                ]
              }
          ]
      }  
