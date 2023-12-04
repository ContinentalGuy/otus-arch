## Контекстная диаграма

```mermaid
C4Context
    Boundary(subsidiaries, "ДЗО", "дочерние и <br>зависимые общества") {
        Person(admin, "Администратор", "")
        Person(business, "Владелец бизнеса", "")
    }
    Person(dev, "Команда разработки", "")

    System(erp, "ERP", "")

    Person(client, "Клиент", "")
    Person(empl, "Сотрудник", "")
    Container(inv, "Инвентарь", "")

    Rel(admin, erp, "Ведет учет ДДС <br> фиксирует состояние и перемещения инвентаря <br> заводит данные о договорах, объектах и заказчиках")
    Rel(business, erp, "Анализирует отчеты")
    Rel(dev, erp, "Обновляет систему<br>Интегрирует со сторонними приложениями")
    Rel(client, erp, "Делает заказы, <br>оплачивает их")
    Rel(admin, client, "Согласует договор")
    Rel(admin, empl, "Планирует загруженность сотрудников")
    Rel(admin, inv, "Следит за инвентарем")

    UpdateRelStyle(admin, erp, $offsetY="-100", $offsetX="-170")
    UpdateRelStyle(business, erp, $offsetY="-60", $offsetX="-60")
    UpdateRelStyle(dev, erp, $offsetY="-90", $offsetX="-150")
    UpdateRelStyle(client, erp, $offsetY="-40", $offsetX="-40")
    UpdateRelStyle(admin, client, $offsetY="-60", $offsetX="90")
    UpdateRelStyle(admin, inv, $offsetY="-60", $offsetX="-70")
```

## Диаграмма контейнеров

```mermaid
C4Container
    Container_Boundary(subsidiaries, "ДЗО", "дочерние и <br>зависимые общества") {
        Person(admin, "Администратор", "")
        Person(business, "Владелец бизнеса", "")
    }

    Container_Boundary(erp, "Bubble.IO") {
        Container_Boundary(b_controllers, "Контроллеры") {
            Container(controllers, "Контроллер", "")
        }
        Container_Boundary(b_database, "База данных") {
            Container(database, "База данных", "Пользователи, Права")
        }
        Container_Boundary(b_visual, "Визуальные элементы") {
            Container(admin_pannel, "ERP", "Административная панель")
        }
        Container_Boundary(plugins, "Plugins") {
            Container(connector, "Plugin's", "Плагины Bubble.io")
        }
    }

    Container_Boundary(aws, "AWS Cloud") {
        Container(gateway, "API Gateway", "Шлюз для взаимодействия <br>с сервисами Amazon")
        ContainerDb(mysql, "MySQL<br>Percona XtraDB Cluster", "Хранение заказов, договоров, <br> объектов, счетов и пр.")
    }

    Container_Boundary(openai_cloud, "OpenAI") {
        Container(openai_chat, "ChatGPT", "Ассистент <br>OpenAI ChatGPT")
    }

    Container_Boundary(stripe_cloud, "Stripe") {
        Container(stripe_gw, "Stripe API", "Платежный шлюз")
    }
    
    Container_Boundary(google_cloud, "Google") {
        Container(google_gw, "Google OAuth2.0", "Авторизация")
    }

    Rel(admin, admin_pannel, "Ведет учет ДДС <br> фиксирует состояние и перемещения инвентаря <br> заводит данные о договорах, объектах и заказчиках")
    Rel(business, admin_pannel, "Анализирует отчеты")
    Rel(admin_pannel, controllers, "Редактирование моделей")
    Rel(controllers, database, "")
    Rel(admin_pannel, connector, "Интеграция со <br>сторонними <br>приложениями")
    Rel(connector, openai_chat, "")
    Rel(connector, stripe_gw, "")
    Rel(connector, gateway, "")
    Rel(connector, google_gw, "")
    Rel(gateway, mysql, "")

    UpdateRelStyle(admin, admin_pannel, $lineColor="#8073ac", $offsetY="-70", $offsetX="-150")
    UpdateRelStyle(business, admin_pannel, $lineColor="#8073ac", $offsetY="120", $offsetX="-20")
    UpdateRelStyle(admin_pannel, connector, $lineColor="#8073ac", $offsetY="-10", $offsetX="-40")
```

## Диаграмма компонентов

```mermaid
C4Component
    Boundary(erp, "Bubble.IO") {
        Boundary(b_controllers, "Контроллеры") {
            Component(controllers, "Контроллер", "")
        }
        Container_Boundary(b_database, "База данных") {
            ComponentDb(database, "База данных", "Пользователи, Права")
        }
        Container_Boundary(b_visual, "Визуальные элементы") {
            Component(admin_pannel, "ERP", "Административная панель")
        }
        Container_Boundary(plugins, "Plugins") {
            Component(connector, "API Connector", "Bubble.io коннектор")
            Component(auth, "Google OAuth 2.0", "плагин для <br>авторизации пользователей")
            Component(csv_import, "CSV Excel Importer", "плагин для выгрузки <br>отчетов в Excel")
            Component(openai, "OpenAI Assistant API", "плагин для <br>работы ассистента")
            Component(stripe, "Stripe", "плагин для приема <br>карточных платежей")
            Component(paypal, "PayPal Checkout", "плагин для приема <br>платежей через PayPal")
        }
    }

    Container_Boundary(aws, "AWS Cloud") {
        Component_Ext(gateway, "API Gateway", "Шлюз для взаимодействия <br>с сервисами Amazon")
        ContainerDb(mysql, "MySQL<br>Percona XtraDB Cluster", "Хранение заказов, договоров, <br> объектов, счетов и пр.")
    }

    Container_Boundary(openai_cloud, "OpenAI") {
        Component_Ext(openai_chat, "ChatGPT", "Ассистент <br>OpenAI ChatGPT")
    }

    Container_Boundary(stripe_cloud, "Stripe") {
        Component_Ext(stripe_gw, "Stripe API", "Платежный шлюз")
    }
    
    Container_Boundary(paypal_cloud, "PayPal") {
        Component_Ext(paypal_gw, "PayPal API", "Платежный шлюз")
    }
    
    Container_Boundary(google_cloud, "Google") {
        Component_Ext(google_gw, "Google OAuth2.0", "Авторизация")
    }

    Rel(admin_pannel, connector, "")
    Rel(connector, gateway, "")
    Rel(gateway, mysql, "")
    Rel(admin_pannel, auth, "Авторизация")
    Rel(auth, admin_pannel, "Доступ <br>к ERP")
    Rel(admin_pannel, csv_import, "Выгрузка отчетов")
    Rel(admin_pannel, openai, "Чат с <br>ассистентом")
    Rel(admin_pannel, stripe, "Обработка <br>карточных платежей")
    Rel(admin_pannel, paypal, "Обработка <br>платежей PayPal")
    
    Rel(openai, openai_chat, "")
    Rel(stripe, stripe_gw, "")
    Rel(paypal, paypal_gw, "")
    Rel(auth, google_gw, "")

    UpdateRelStyle(admin, admin_pannel, $lineColor="#8073ac", $offsetY="-70", $offsetX="-150")
    UpdateRelStyle(business, admin_pannel, $lineColor="#8073ac", $offsetY="130", $offsetX="-20")
    UpdateRelStyle(admin_pannel, auth, $offsetY="-30", $offsetX="0")
    UpdateRelStyle(auth, admin_pannel, $offsetY="30", $offsetX="0")
    UpdateRelStyle(admin_pannel, csv_import, $offsetY="70", $offsetX="0")
    UpdateRelStyle(admin_pannel, openai, $offsetY="70", $offsetX="-20")
    UpdateRelStyle(admin_pannel, stripe, $offsetY="70", $offsetX="-50")
    UpdateRelStyle(admin_pannel, paypal, $offsetY="70", $offsetX="-50")
```

## Диаграмма развертывания

```mermaid
C4Deployment
    Deployment_Node(comp, "Компьютер администратора", "Любая ОС, на которой запускается браузер") {
        Deployment_Node(browser, "Web Browser", "Google Chrome, Mozilla Firefox,<br/> Apple Safari or Microsoft Edge") {
            Container(client, "Web Browser", "", "")
        }
    }

    Deployment_Node(plc, "Bubble.IO", "Cloud") {
        Container(api, "ERP", "Визуальные элементы")
        Container(connector, "API Connector", "Bubble.io коннектор")
    }

    Deployment_Node(aws, "AWS", "Amazon Web Services") {
        Deployment_Node(a_gateway, "API Gateway", "API") {
            ContainerDb(gateway, "Шлюз", "Шлюз для интеграции <br>с сервисами Amazon")
        }
        Deployment_Node(cluster, "Percona XtraDB Cluster", "master master") {
            Deployment_Node(mysql_a, "MySQL", "DB") {
                ContainerDb(db, "База данных", "Реляционная БД", "Хранение заказов, договоров, <br> объектов, счетов и пр.")
                ContainerDb(db_1, "База данных")
                ContainerDb(db_2, "База данных")
            }
        }
    }

    Rel(client, api, "Делает запросы в API", "TCP/IP")
    Rel(api, connector, "Отправляет запрос в сторонние API", "")
    Rel(connector, gateway, "Пробрасывает запрос к сервисам Amazon", "")
    Rel(gateway, db, "Читает, пишет, удаляет <br> и обновляет строки", "")
    Rel(db, db_1, "Репликация данных", "")
    Rel(db_1, db_2, "Репликация данных", "")
    UpdateRelStyle(connector, gateway, $offsetY="-50")
    UpdateRelStyle(gateway, db, $offsetX="-40", $offsetY="-20")
    UpdateRelStyle(client, api, $offsetX="-40", $offsetY="-40")
```

Остальные приложения находятся вне нашей зоны контроля, поэтому на диаграмме развертывания их нет.

## Структура хранилища данных

![db.png](sources%2Fimages%2Fdb.png)
