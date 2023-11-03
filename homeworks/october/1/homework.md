# [Hot Diggety Dog](https://nealford.com/katas/kata?id=HotDiggetyDog)

## Бизнес контекст

Владелец местных ларьков с хот-догами хочет обзавестить системой для управления торговыми точками.

**Пользователи**: ~50 продавцов

**Требования**:

- система должна работать на небольших устройствах (меньше ноутбука), чтобы его можно было эффективно использовать на
  мобильных точках продаж
- в системе можно делать скидки
- сохраняет время и место продажи каждого хот-дога
- отправляет уведомления сотрудникам, отвечающим за пополнение запасов точек продаж
- интегрировано с соц. сетями, чтобы можно было уведомлять клиентов когда они находятся вблизи точек продаж
- может выгружать отчеты в формате, который умеет читать бухгалтерское ПО

**Дополнительный контекст**:

- вынуждены заняться разработкой, потому что текущие способы отслеживания продаж требуют слишком много ручного труда
- нужно разработать как можно быстрее
- однако, важнее будет разработать систему так, чтобы ее не надо было переписывать в течение 3-х лет
- ограничений по бюджету нет

## Внимание

Данный кейс уже был рассмотрен ранее ([тут](otus-arch/homeworks/july/1/homework.md),
и [тут](otus-arch/homeworks/august/1/homework.md), и [тут](otus-arch/homeworks/september/1/homework.md)).\
Было найдено два архитектурных решения, одно из которых предполагает разработку системы с нуля.\
При разработке системы с нуля, были выбраны следующие контексты:

- контекст продавца
- контекст снабженца
- контекст администратора

Каждый контекст предполагает разработку отдельного сервиса.
На диаграмме контейнеров приложения, структур хранения данных в бд и деплоймент диаграмме и будет сконцентрирована
данная ADR.

## Диаграмма контейнеров приложения на основе выбранной модели функциональной декомпозиции

```mermaid
C4Context
    Container_Boundary(pos_app_b, "POS Mobile App 'Hot Diggety Dog'") {
        Person(seller, "Оператор точки продаж", "Продает хотдоги, ведет учет")
        Container(pos_app, "Точка продаж", "Приложение точки продаж <br> на мобильном устройстве.")
        ContainerDb(pos_db, "Хранение данных о продажах <br> до момента отправки + кеш")
    }

    Rel(seller, pos_app, "")
    Rel(pos_app, pos_db, "")

    Container_Boundary(supply_app_b, "Supply Mobile App 'Hot Diggety Dog'") {
        Person(supplyer, "Снабженец", "Пополняет запасы точки продаж")
        Container(supplyer_app, "Приложение снабженца", "Приложение для снабженца <br> на мобильном устройстве.")
    }

    Rel(supplyer, supplyer_app, "")

    Container_Boundary(cms_b, "CMS 'Hot Diggety Dog'") {
        Container(admin_api, "API", "Шлюз для интеграции <br> с приложениями")
        Person(admin, "Администратор", "Анализирует отчеты, <br> настраивает работу точек продаж, <br> координирует снабженцев")
        Container(admin_pannel, "CMS", "Административная панель")
        ContainerDb(admin_db, "Хранение всех отчетов")
    }

    Rel(admin_api, admin_db, "")
    Rel(admin_pannel, admin_db, "")
    Rel(pos_app, admin_api, "Синхронизация")
    Rel(admin_api, supplyer_app, "Уведомления")

    UpdateElementStyle(pos_app_b, $borderColor="white", $fontColor="white")
    UpdateElementStyle(supply_app_b, $borderColor="white", $fontColor="white")
    UpdateElementStyle(cms_b, $borderColor="white", $fontColor="white")

    UpdateRelStyle(seller, pos_app, $textColor="white", $lineColor="white")
    UpdateRelStyle(pos_app, pos_db, $textColor="white", $lineColor="white")
    UpdateRelStyle(supplyer, supplyer_app, $textColor="white", $lineColor="white")
    UpdateRelStyle(admin_api, admin_db, $textColor="white", $lineColor="white")
    UpdateRelStyle(admin_pannel, admin_db, $textColor="white", $lineColor="white")
    UpdateRelStyle(pos_app, admin_api, $textColor="white", $lineColor="white")
    UpdateRelStyle(admin_api, supplyer_app, $textColor="white", $lineColor="white")
```

## Декомпозиция слоя данных: какие данные в каких БД хранятся
```mermaid
---
title: Слой данных
---
classDiagram
    APP_DB -- Products
    APP_DB -- Transactions
    Ingredients <--> IngredientsToRecipeM2M
    Recipe <-- IngredientsToRecipeM2M
    Products <-- Recipe
    Reports <-- Transactions
    
    CMS_DB -- POS
    CMS_DB -- Tasks
    CMS_DB -- Settings
    CMS_DB -- CMSReports
    CMSReports <-- CMSTransactions
    POS <-- Employee
    
    class APP_DB{
    }

    class Ingredients{
        +int id
        +string name
        +string units
        +int counter
        +int capacity
    }
    
    class IngredientsToRecipeM2M{
        +int ingredient_id
        +int recipe_id
    }
    
    class Recipe{
        +int id
        +string name
    }

    class Products{
        +int id
        +string name
        +bool available
        +int recipe_id
    }

    class Transactions{
        +int id
        +int product_id
        +string status
        +string email
        +string phone_number
        +int report_id
        +time created_at
        +time updated_at
    }

    class Reports{
        +int id
        +string name
        +time created_at
    }

    class CMS_DB{
    }

    class CMSTransactions{
        +int id
        +int product_id
        +string status
        +string email
        +string phone_number
        +int report_id
        +time created_at
        +time updated_at
    }

    class CMSReports{
        +int id
        +string name
        +time created_at
    }
    
    class POS{
        +int id
        +string name
    }
    
    class Employee{
        +int id
        +string name
        +string surname
        +int age
        +int pos_id
    }

    class Tasks{
        +int id
        +json meta
        +string status
    }
    
    class Settings{
        +int id
        +string type
        +string key
        +string value
        +bool enabled
    }
```

## Деплоймент диаграмма
```mermaid
    C4Deployment
    title Deployment Diagram for Internet Banking System - Live

    Deployment_Node(mob_pos, "Мобильное приложение точки продаж", "Apple IOS / Android"){
        Container(mobile_pos, "Мобильное приложение", "Flutter", "Дает оператору возможность вести учет <br> проданных хотдогов и формировать отчеты")
    }

    Deployment_Node(mob_supply, "Мобильное приложение снабженца", "Apple IOS / Android"){
        Container(mobile_supply, "Мобильное приложение", "Flutter", "Оповещает снабженца о том, что нужно <br> пополнить запасы на указанной точке продаж")
    }

    Deployment_Node(comp, "Компьютер администратора", "Любая ОС, на которой запускается браузер"){
        Deployment_Node(browser, "Web Browser", "Google Chrome, Mozilla Firefox,<br/> Apple Safari or Microsoft Edge"){
            Container(spa, "Single Page Application", "JavaScript + React", "Предоставляет доступ к базе данных, <br> в которой хранятся отчеты о проданных хотдогах, <br> настройки и задачи для приложений")
        }
    }

    Deployment_Node(plc, "Hot Diggety Dog", "датацентр"){
        Deployment_Node(dn, "API", "CentOS 8.5"){
            Deployment_Node(nginx, "NGNINX", "Nginx 1.25.3"){
                Deployment_Node(kubernetes, "Kubernetes"){
                    Deployment_Node(docker, "Docker"){
                        Container(api, "REST API", "Golang, Echo", "CRUD к базе данных.")
                    }
                }
            }
        }
        Deployment_Node(db0, "db-0", "Ubuntu 22.04 LTS"){
            Deployment_Node(PostgreSQL, "PostgreSQL"){
                ContainerDb(db, "База данных", "Реляционная БД", "Хранит отчеты, транзакции, настройки приложений и т.д.")
            }
        }
        Deployment_Node(db1, "db-1", "Ubuntu 22.04 LTS") {
            Deployment_Node(PostgreSQL-1, "PostgreSQL") {
                ContainerDb(db2, "База данных", Реляционная БД", "Хранит отчеты, транзакции, настройки приложений и т.д.")
            }
        }
    }

    Rel(mobile_pos, api, "Делает запросы в API", "json/HTTPS")
    Rel(mobile_supply, api, "Делает запросы в API", "json/HTTPS")
    Rel(spa, api, "Делает запросы в API", "json/HTTPS")
    Rel(api, db, "Читает, пишет, удаляет <br> и обновляет строки", "gorm")
    Rel(api, db2, "Читает, пишет, удаляет <br> и обновляет строки", "gorm")
    Rel_R(db, db2, "Реплицирует данные")

    UpdateRelStyle(spa, api, $offsetY="-40", $offsetX="-100")
    UpdateRelStyle(mobile_pos, api, $offsetY="-130", $offsetX="-130")
    UpdateRelStyle(mobile_supply, api, $offsetY="-130", $offsetX="-60")
    UpdateRelStyle(api, db, $offsetY="-50", $offsetX="-40")
    UpdateRelStyle(api, db2, $offsetX="-40", $offsetY="-20")
    UpdateRelStyle(db, db2, $offsetY="-70")
```