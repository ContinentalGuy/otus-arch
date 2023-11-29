## Контекстная диаграма

![context.png](https://github.com/ContinentalGuy/otus-arch/blob/master/project/sources/images/context.png)

## Диаграмма контейнеров

```mermaid
C4Container
    Container_Boundary(erp, "Bubble.IO") {
        Container_Boundary(b_visual, "Визуальные элементы") {
            Container(admin_pannel, "ERP", "Административная панель")
        }
        Container_Boundary(b_database, "База данных") {
            Container(admin_pannel, "ERP", "")
        }
    }
    
    Container_Boundary(aws, "AWS Cloud") {
        ContainerDb(postgre, "PostgreSQL", "Хранение заказов, договоров, <br> объектов, счетов и пр.")
    }

    ContainerDb(gateway, "API Gateway", "Шлюз для взаимодействия с сервисами Amazon")

    Person(admin, "Администратор", "")
    Person(business, "Владелец бизнеса", "")
    Rel(admin_pannel, gateway, "")
    Rel(gateway, postgre, "")
    Rel(admin, admin_pannel, "Ведет учет ДДС <br> фиксирует состояние и перемещения инвентаря <br> заводит данные о договорах, объектах и заказчиках")
    Rel(business, admin_pannel, "Анализирует отчеты")
    UpdateRelStyle(admin, admin_pannel, $offsetY="-70", $offsetX="-150")
    UpdateRelStyle(business, admin_pannel, $offsetY="-70", $offsetX="40")
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
    
    Deployment_Node(aws, "AWS", "Amazon Web Services"){
        Deployment_Node(a_gateway, "API Gateway", "API") {
            ContainerDb(gateway, "Шлюз", "Шлюз для интеграции <br>с сервисами Amazon")
        }
        Deployment_Node(cluster, "Percona XtraDB Cluster MySQL", "master master") {
            Deployment_Node(mysql_a, "MySQL", "DB") {
                ContainerDb(db, "База данных", "Реляционная БД", "Хранение заказов, договоров, <br> объектов, счетов и пр.")
                ContainerDb(db_1, "База данных")
                ContainerDb(db_2, "База данных")
            }
        }
    }

    Rel(client, api, "Делает запросы в API", "TCP/IP")
    Rel(api, connector, "Отправляет запрос в шлюз Amazon", "")
    Rel(connector, gateway, "Пробрасывает запрос к сервисам Amazon", "")
    Rel(gateway, db, "Читает, пишет, удаляет <br> и обновляет строки", "")
    Rel(db, db_1, "Репликация данных", "")
    Rel(db_1, db_2, "Репликация данных", "")
    
    UpdateRelStyle(connector, gateway, $offsetY="-50")
```

## Структура хранилища данных
![db.png](sources%2Fimages%2Fdb.png)
