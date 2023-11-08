## Контекстная диаграма

![context.png](https://github.com/ContinentalGuy/otus-arch/blob/master/project/sources/images/context.png)

## Диаграмма контейнеров

```mermaid
C4Container
    Container_Boundary(erp, "ERP") {
        Container(admin_pannel, "ERP", "Административная панель")
        ContainerDb(admin_db, "База Данных", "Хранение заказов, договоров, <br> объектов, счетов и пр.")
    }

    Person(admin, "Администратор", "")
    Person(business, "Владелец бизнеса", "")
    Rel(admin_pannel, admin_db, "")
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

    Deployment_Node(plc, "ЦОД", "датацентр") {
        Deployment_Node(dn, "ERP", "Ubuntu 22.04 LTS") {
            Deployment_Node(nginx, "NGNINX", "Nginx 1.25.3") {
                Container(api, "ERP", "ERP на Django", "Python 3.11")
            }
            Deployment_Node(PostgreSQL, "PostgreSQL") {
                ContainerDb(db, "База данных", "Реляционная БД", "Хранение заказов, договоров, <br> объектов, счетов и пр.")
            }
        }
    }

    Rel(client, api, "Делает запросы в API", "TCP/IP")
    Rel(api, db, "Читает, пишет, удаляет <br> и обновляет строки", "")
    UpdateRelStyle(client, api, $offsetY="-40", $offsetX="-100")
    UpdateRelStyle(api, db, $offsetY="30", $offsetX="-60")
```

## Диаграммы последовательности для пользовательских сценариев

### Отслеживание движения денежных средств

Сохранение данных о согласованном договоре
```mermaid
sequenceDiagram
    Admin ->> ERP: POST /customer
    activate ERP
    Note over Admin,ERP: {"name": "John Doe", <br>"comment": "Awesome customer"}
    ERP ->> DB: INSERT
    ERP -->> Admin: 200 OK
    deactivate ERP
    Admin ->> ERP: POST /object
    activate ERP
    Note over Admin,ERP: {"name": "House", <br>"address": "221B Baker Street", <br>"customer": **CUSTOMER**}
    ERP ->> DB: INSERT
    ERP -->> Admin: 200 OK
    deactivate ERP
    Admin ->> ERP: POST /contract
    activate ERP
    Note over Admin,ERP: {"name": "bureau", <br>"object": **OBJECT**, <br>"deadline":"01.01.2023"}
    ERP ->> DB: INSERT
    ERP -->> Admin: 200 OK
    deactivate ERP
```

Сохранение данных о приходе/расходе
```mermaid
sequenceDiagram
    Admin ->> ERP: POST /transaction
    activate ERP
    Note over Admin,ERP: {"name": "purchasing wood", <br>"amount": "100", <br>"currency": "RUB", <br>"type": "expense"}
    ERP ->> DB: INSERT
    ERP -->> Admin: 200 OK
    deactivate ERP

    Admin ->> ERP: POST /transaction
    activate ERP
    Note over Admin,ERP: {"name": "selling bureau", <br>"amount": "500", <br>"currency": "RUB", <br>"type": "income"}
    ERP ->> DB: INSERT
    ERP -->> Admin: 200 OK
    deactivate ERP
```

## Логгирование загруженности персонала

```mermaid
sequenceDiagram
    Admin ->> ERP: POST /employee_log
    activate ERP
    Note over Admin,ERP: {"employee": "John Doe", <br>"active": false, <br>"comment": "absent for unexcused <br>reasons", <br>"curent_date":"01.01.2023"}
    ERP ->> DB: INSERT
    ERP -->> Admin: 200 OK
    deactivate ERP
```

## Учет инвентаря
```mermaid
sequenceDiagram
    Admin ->> ERP: POST /inventory
    activate ERP
    Note over Admin,ERP: {"name": "milling cutter", <br>"state": "ready", <br>"location": "in the workshop"}
    ERP ->> DB: INSERT
    ERP -->> Admin: 200 OK
    deactivate ERP

    Admin ->> ERP: PATCH /inventory/1
    activate ERP
    Note over Admin,ERP: {"name": "milling cutter", <br>"state": "broken", <br>"location": "in the workshop"}
    ERP ->> DB: UPDATE
    ERP -->> Admin: 200 OK
    deactivate ERP
```