# 12.3 Развертывание кластера на собственных серверах, лекция 1 - Роман Поцелуев
Поработав с персональным кластером, можно заняться проектами. Вам пришла задача подготовить кластер под новый проект.

## Задание 1: Описать требования к кластеру
Сначала проекту необходимо определить требуемые ресурсы. Известно, что проекту нужны база данных, система кеширования, а само приложение состоит из бекенда и фронтенда. Опишите, какие ресурсы нужны, если известно:

* база данных должна быть отказоустойчивой (не менее трех копий, master-slave) и потребляет около 4 ГБ ОЗУ в работе;
* кэш должен быть аналогично отказоустойчивый, более трех копий, потребление: 4 ГБ ОЗУ, 1 ядро;
* фронтенд обрабатывает внешние запросы быстро, отдавая статику: не более 50 МБ ОЗУ на каждый экземпляр;
* бекенду требуется больше: порядка 600 МБ ОЗУ и по 1 ядру на копию.

Требования: опишите, сколько нод в кластере и сколько ресурсов (ядра, ОЗУ, диск) нужно для запуска приложения. Расчет вести из необходимости запуска 5 копий фронтенда и 10 копий бекенда, база и кеш.

**Ответ:**

* Расчет общей производственной нагрузки.

| Нагрузка           | ОЗУ  | ЦПУ | Копии | Сумма ОЗУ | Сумма ЦПУ |
| ------------------ | ---- | --- | ----- | --------- | --------- |
| БД                 | 4    | 1   | 3     | 12        | 3         |
| КЭШ                | 4    | 1   | 3     | 12        | 3         |
| Фронтенд           | 0,05 | 1   | 5     | 0,25      | 5         |
| Бекенд             | 0,6  | 1   | 10    | 6         | 10        |
| Общая нагрузка     |      |     |       | 30,25     | 21        |

* Суммарный объем нагрузки на один сервер, при использовании 3 серверов. При потере одной ноды, нагрузка распределяется на две оставшиеся.

| Нагрузка                                | ОЗУ   | ЦПУ |
| --------------------------------------- | ----- | --- |
| Контрол                                 | 2     | 2   |
| Воркер                                  | 1     | 1   |
| Нагрузка на сервер (1/3 общей нагрузки) | 10,08 | 7   |
| Резерв (нагрузка на сервер/2)           | 6,54  | 5   |
| Итого с округлением до двух             | 20    | 16  |

Для реализации целей проекта требуется три сервера с 20 ГБ ОЗУ и 16 ядрами.
