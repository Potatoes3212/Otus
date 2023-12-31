# ADR-2 Использование API-Gateway

## Контекст
Сервисы реализующие бизнес логику продукта находятся в сетевой зоне без интернета (inside).
В зоне с интернетом (DMZ) развёрнут ревёрс-прокси.
Ожидается увеличение количества пользователей сервиса и новые внешние интеграции.
### Текущая архитектура
* Запросы просто проксируются в inside.
### Архитектурные драйверы
* В текущем ревёрс прокси не реализованы механизмы повышения отказоустойчивости
* Все запросы идут через один ревёрс прокси.
### Цели
* Повысить отказоустойчивость системы для т.к. ожидается повышение нагрузки.
## Критичные сценарии и критичные характеристики
**Критичные сценарии:**
* Пользовательское приложение обращается к backend продукта
* Внешние системы обращаются к backend продукта.

**Критерии оценки решений:**
* Время выполнения запроса 
* Отказоустойчивость в сценарии повышения времени ответа сервисов
* Возможность приоритизировать  запросы к Backend .


## Варианты решения 
### Решение 1 Оставить всё как есть
**Преимущества**
* Нет затрат ресурсов на доработку.

**Недостатки**
* Backend может упасть при повышении нагрузки с более высокой вероятностью т.к. не реализованы механизмы повышения отказоустойчивости.

### Решение 2 Развернуть общий API-Gateway 
**Преимущества**
* Можно настроить механизмы отказоустойчивости: 
  * Throttling
  * Circuit Breaker
  * Retry
  * TimeOut
  * и т.д.

**Недостатки**
* Доработки API-Gateway для внешних интеграций влияют на доступность продукта для пользователей

### Решение 3 Развернуть отдельные API-Gateway для пользовательских запросов и для интеграций
**Преимущества**
* Можно настроить механизмы отказоустойчивости: 
  * Throttling
  * Circuit Breaker
  * Retry
  * TimeOut
  * и т.д.
* Нет влияния на пользовательский API-Gateway при работах на интеграционном.

**Недостатки**
* Дополнительная инфраструктурная сложность и точка отказа
* Дополнительные затраты ресурсов на настойку интеграционного API-Gateway.

# Решение 
Выбрано решение 3 т.к. оно в наибольшей степени удовлетворяет критериям оценки решения.
API-Gateway - это базовый элемент системы, влияющий на отказоусточивость всей системы в критичных сценариях. В связи с этим можно выделить больше ресурсов на реализацию наилучшего решения.
>Развернуть отдельные API-Gateway для пользовательских 
## Последствия решения 
**Положительные**
* На запросы пользователей нет влияния от интеграционного трафика
* Механизмы отказоустойчивости настраиваются независимо для технических и пользовательских запросов.

**Отрицательные**
* Усложняется поиск проблемы в связи с разделением трафика
* Выделены дополнительные инфраструктурные и человеческие ресурсы для реализации решения.