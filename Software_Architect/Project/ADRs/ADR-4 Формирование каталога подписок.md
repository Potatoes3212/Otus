# ADR-4 Формирование каталога подписок

## Контекст
* Пользователю предоставляется каталог доступных для подключения подписок и дополнительных опций.
### Текущая архитектура
* Старый сервис old-billing отдаёт подписки из справочника Subscription-Directory фильтрация подписок настроена в файле конфигурации. 
* APP отдельно получает список подписок, действующую подписку пользователя и объединяет их на своей стороне. 
### Архитектурные драйверы
* Убрать логику с FE:
  * Формировать каталог подписок с действующей подпиской пользователя на стороне BE
* Изменять каталог подписок с минимальными затратами ресурсов

### Цели
* Выбрать сервис, который будет формировать каталог подписок для FE. 
## Критичные сценарии и критичные характеристики
**Критичные сценарии:**
* Формирование каталога подписок 
* Добавление новой подписки в каталог 
* Изменение условий отображения подписок

**Критерии оценки решений:**
* Стоимость добавления новой подписки в каталог 
* Стоимость внесения изменений в механизм формирования каталога

## Варианты решения 
### Решение 1. Формировать каталог подписок в сервисе управления подписками subscription-manager. BFF только проксирует запрос от клиента в subscription-manager
**Преимущества**
* Меньше сетевых запросов между контекстами
* Внесение изменений в логику формирования каталога затронет только контекст подписок
* Упрощение кода BFF. Сервис представления данных изолирован от логики контекста подписок

**Недостатки**
* В случае отказа сервиса subscription-manager все функции контекста подписок будут недоступны пользователю
* Если появится новая витрина и новый BFF, то необходимо будет дорабатывать сервис subscription-manager

### Решение 2. Формировать каталог подписок в BFF. subscription-manager предоставляет отдельные интерфейсы для получения каталога и подписки пользователя
**Преимущества**
* Можно гибко формировать каталог подписок под новые витрины
* В случае отказа subscription-manager можно сохранить часть функций контекста подписок доступными для пользователя

**Недостатки**
* Дополнительные сетевые запросы между контекстами 
* Разделение одного бизнес-процесса на два контекста
* Внесение дополнительной логики в BFF
* Изменение условий отображения каталога подписок затронет все BFF


# Решение 
Выбран вариант решения 1
>Формировать каталог подписок в сервисе управления подписками subscription-manager. BFF только проксирует запрос от клиента в subscription-manager

## Последствия решения 
**Положительные**
* Изменения в контексте подписок не затрагивают BFF 

**Отрицательные**
* subscription-manager - single point of failure