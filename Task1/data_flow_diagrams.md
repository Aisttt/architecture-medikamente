# Диаграммы потоков данных (DFD) - компания "Медикаменте"

## 1. Процесс записи пациента на приём

![Процесс записи пациента на приём](./diagrams/patient_registration_dfd.svg)

<details>
<summary>Код PlantUML диаграммы</summary>

```plantuml
@startuml patient_registration_dfd
!theme plain
skinparam backgroundColor white
skinparam defaultFontSize 10

' Внешние сущности
rectangle "ПАЦИЕНТ\n(Внешняя сущность)" as patient #lightblue

' Процессы  
circle "ЗАПИСЬ\nПАЦИЕНТА\n(Процесс 1.1)" as process1 #lightgreen
rectangle "МЕДИЦИНСКИЙ\nСПЕЦИАЛИСТ\n(Процесс 1.2)" as doctor #lightgreen
circle "СОЗДАНИЕ\nМЕДКАРТЫ\n(Процесс 1.3)" as process3 #lightgreen

' Хранилища данных (уязвимые)
database "ЖУРНАЛ ЗАПИСЕЙ\n(Excel)\n🔴 РИСК" as store1 #pink
database "АНКЕТЫ ПАЦИЕНТОВ\n(Файлы)\n🔴 РИСК" as store2 #pink
database "МЕДИЦИНСКИЕ КАРТЫ\n(PDF/JPG)\n🔴 КРИТИЧНО" as store3 #red

' Потоки данных
patient --> process1 : "Данные пациента\n(ФИО, телефон, жалобы)"
process1 --> store1 : "Персональные\nданные"
process1 --> store2 : "Персональные\nданные"  
process1 --> doctor : "Персональные\nданные"

store1 --> doctor : "Данные записи"
store2 --> doctor : "Анкетные данные"

doctor --> process3 : "Медицинские\nданные"
process3 --> store3 : "Медицинская\nкарта"

' Нумерация хранилищ
note top of store1 : D1
note top of store2 : D2  
note top of store3 : D3

@enduml
```

</details>

**Проблемы безопасности:**
- Отсутствие шифрования данных на общем диске
- Нет контроля доступа к файлам
- Отсутствует аудит действий

---

## 2. Процесс обработки результатов анализов

![Процесс обработки результатов анализов](./diagrams/lab_results_dfd.svg)

<details>
<summary>Код PlantUML диаграммы</summary>

```plantuml
@startuml lab_results_dfd
!theme plain
skinparam backgroundColor white
skinparam defaultFontSize 10

' Внешние сущности
rectangle "ВНЕШНЯЯ\nЛАБОРАТОРИЯ\n(Внешняя сущность)" as lab #lightblue

' Процессы
circle "ПОЛУЧЕНИЕ\nАНАЛИЗОВ\n(Процесс 2.1)\n🔴 Email/Факс" as process1 #orange
circle "ОБРАБОТКА\nФАЙЛОВ\n(Процесс 2.2)" as process2 #lightgreen
circle "АНАЛИЗ\nРЕЗУЛЬТАТОВ\n(Процесс 2.3)" as process3 #lightgreen
circle "ВНЕСЕНИЕ\nВ КАРТУ\n(Процесс 2.4)" as process4 #lightgreen

' Хранилища данных
database "АНАЛИЗЫ\n(Файлы)\n🔴 РИСК" as store4 #pink
database "МЕДИЦИНСКИЕ КАРТЫ\n(Обновлено)\n🔴 КРИТИЧНО" as store3 #red

' Потоки данных
lab --> process1 : "Результаты анализов\n(PDF, данные)"
process1 --> process2 : "Файлы анализов"
process2 --> store4 : "Цифровые копии"
store4 --> process3 : "Медицинские данные"
process3 --> process4 : "Заключение врача"
process4 --> store3 : "Обновленная\nмедкарта"

' Нумерация хранилищ
note top of store4 : D4
note top of store3 : D3

' Проблемы безопасности
note bottom of process1 : ПРОБЛЕМА:\nНебезопасная передача\nпо email/факс
note bottom of store4 : ПРОБЛЕМА:\nОтсутствие шифрования\nфайлов

@enduml
```

</details>

**Проблемы безопасности:**
- Небезопасная передача по email
- Результаты анализов доступны всем сотрудникам
- Нет шифрования медицинских данных

---

## 3. Процесс оплаты услуг

![Процесс оплаты услуг](./diagrams/payment_process_dfd.svg)

<details>
<summary>Код PlantUML диаграммы</summary>

```plantuml
@startuml payment_process_dfd
!theme plain
skinparam backgroundColor white
skinparam defaultFontSize 10

' Внешние сущности
rectangle "ПАЦИЕНТ\n(Плательщик)" as patient #lightblue
rectangle "НАЛОГОВАЯ\nОТЧЕТНОСТЬ\n(Внешняя сущность)" as tax #lightblue

' Процессы
circle "ПРИЕМ\nПЛАТЕЖА\n(Процесс 3.1)" as process1 #lightgreen
circle "БУХГАЛТЕРСКИЙ\nУЧЕТ\n(Процесс 3.3)" as process3 #lightgreen

' Системы (защищённые)
rectangle "ККМ\n(Касса)\n✅ ОК" as kkm #lightgreen
rectangle "1С: БУХГАЛТЕРИЯ\n✅ ОК" as oneс #lightgreen
rectangle "ФИСКАЛЬНЫЕ\nДОКУМЕНТЫ\n✅ ОК" as fiscal #lightgreen

' Хранилища данных (уязвимые)
database "УЧЕТ ПЛАТЕЖЕЙ\n(Excel)\n🔴 РИСК" as store_excel #pink
database "ОБЩИЙ ДИСК\n🔴 РИСК" as store_disk #pink

' Потоки данных
patient --> process1 : "Запрос на оплату\n(услуги, сумма)"
process1 --> kkm : "Данные платежа"
process1 --> store_excel : "Данные платежа"
process1 --> oneс : "Данные платежа"

kkm --> fiscal : "Фискальные\nданные"
store_excel --> store_disk : "Дублирование\nданных"
oneс --> process3 : "Финансовые\nданные"
process3 --> tax : "Налоговая\nотчётность"

' Проблемы безопасности
note bottom of store_excel : ПРОБЛЕМА:\nДублирование данных\nв Excel и 1С
note bottom of store_disk : ПРОБЛЕМА:\nФинансовые данные\nна общем диске

@enduml
```

</details>

**Проблемы безопасности:**
- Дублирование финансовых данных в Excel и 1С
- Отсутствие шифрования платёжной информации
- Нет контроля целостности данных

---

## 4. Процесс ведения медицинской карты

![Процесс ведения медицинской карты](./diagrams/medical_record_management_dfd.svg)

<details>
<summary>Код PlantUML диаграммы</summary>

```plantuml
@startuml medical_record_management_dfd
!theme plain
skinparam backgroundColor white
skinparam defaultFontSize 10

' Внешние сущности
rectangle "МЕДИЦИНСКИЙ\nСПЕЦИАЛИСТ\n(Врач)" as doctor #lightblue

' Процессы
circle "ОСМОТР\nПАЦИЕНТА\n(Процесс 4.1)" as process1 #lightgreen
circle "СОЗДАНИЕ\nМЕДКАРТЫ\n(Процесс 4.2)" as process2 #lightgreen
circle "НАЗНАЧЕНИЯ\n(Процесс 4.3)" as process3 #lightgreen
circle "СОХРАНЕНИЕ\nНА ДИСК\n(Процесс 4.4)" as process4 #orange

' Хранилища данных
database "НАЗНАЧЕНИЯ\n(Excel)\n🔴 РИСК" as store5 #pink
database "МЕДИЦИНСКИЕ КАРТЫ\n(Папки)\n🔴 КРИТИЧНО" as store3 #red

' Критическая проблема
rectangle "ВСЕ СОТРУДНИКИ\n(Доступ к данным)\n🔴 КРИТИЧЕСКАЯ ПРОБЛЕМА" as all_staff #red

' Потоки данных
doctor --> process1 : "Медицинские\nданные"
doctor --> process2 : "Медицинские\nданные"  
doctor --> process3 : "Медицинские\nданные"

process1 --> process4 : "Данные осмотра"
process2 --> process4 : "Медицинская карта"
process3 --> store5 : "Назначения"

store5 --> process4 : "Данные назначений"
process4 --> store3 : "Сохранённые\nдокументы"

store3 --> all_staff : "НЕКОНТРОЛИРУЕМЫЙ\nДОСТУП"

' Нумерация хранилищ
note top of store5 : D5
note top of store3 : D3

' Критические проблемы
note bottom of all_staff : КРИТИЧЕСКАЯ ПРОБЛЕМА:\nВсе сотрудники имеют доступ\nко всем медицинским данным
note bottom of store3 : ПРОБЛЕМЫ:\n- Нет версионности\n- Нет аудита изменений
note bottom of store5 : ПРОБЛЕМА:\nНазначения дублируются\nв Excel

@enduml
```

</details>

**Проблемы безопасности:**
- Медицинские данные доступны всем сотрудникам
- Отсутствует версионность документов
- Нет аудита изменений медкарт

---

## 5. Процесс учёта ТМЦ и закупок

![Процесс учёта ТМЦ и закупок](./diagrams/inventory_management_dfd.svg)

<details>
<summary>Код PlantUML диаграммы</summary>

```plantuml
@startuml inventory_management_dfd
!theme plain
skinparam backgroundColor white
skinparam defaultFontSize 10

' Внешние сущности
rectangle "ПОСТАВЩИК\n(Контрагент)" as supplier #lightblue

' Процессы
circle "ПРИЕМ\nТОВАРА\n(Процесс 5.1)" as process1 #lightgreen
circle "ИНТЕГРАЦИЯ\nС 1С\n(Процесс 5.2)" as process2 #lightgreen

' Системы (защищённые)
database "1С: ТОРГОВЛЯ\nИ СКЛАД\n✅ ОК" as store1c_trade #lightgreen
rectangle "1С: БУХГАЛТЕРИЯ\n✅ ОК" as system1c_acc #lightgreen

' Хранилища данных (уязвимые)
database "ДУБЛИРОВАНИЕ ДАННЫХ\n(Excel)\n🔴 РИСК" as store_excel #pink
database "ОБЩИЙ ДИСК\n🔴 РИСК" as store_disk #pink

' Потоки данных
supplier --> process1 : "Товары и документы\n(накладные, счета)"
process1 --> store1c_trade : "Документы поставки"
process1 --> store_excel : "Дублирование\nв Excel"

store1c_trade --> process2 : "Данные ТМЦ"
store_excel --> store_disk : "Коммерческие\nданные"

process2 --> system1c_acc : "Интеграция\nданных"

' Нумерация хранилищ
note top of store1c_trade : D7
note top of store_excel : D6

' Проблемы безопасности
note bottom of store_excel : ПРОБЛЕМА:\nКоммерческие данные\nдублируются в Excel
note bottom of store_disk : ПРОБЛЕМА:\nИнформация о поставщиках\nне защищена

@enduml
```

</details>

**Проблемы безопасности:**
- Коммерческая информация дублируется в Excel
- Отсутствует защита данных о поставщиках
- Нет контроля доступа к финансовой информации

---

## Общие выводы по DFD:

### Критические проблемы:
1. **Общий диск как единое хранилище** - все конфиденциальные данные доступны всем
2. **Отсутствие шифрования** - данные хранятся в открытом виде
3. **Дублирование данных** - в Excel и специализированных системах
4. **Отсутствие аудита** - невозможно отследить, кто и когда обращался к данным
5. **Небезопасная передача** - email, факс без шифрования
