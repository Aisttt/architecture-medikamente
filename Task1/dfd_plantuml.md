# DFD диаграммы в формате PlantUML - "Медикаменте"

**Уровень DFD:** Level 1-2 (детализация основных процессов без углубления в подпроцессы)  
**Назначение:** Визуализация потоков конфиденциальных данных для выявления уязвимостей безопасности

Этот файл содержит диаграммы потоков данных в формате PlantUML для конвертации в SVG.

## 1. Процесс записи пациента на приём

```plantuml
@startuml patient_registration_dfd
!theme plain

rectangle "ПАЦИЕНТ\n(Внешняя сущность)" as patient
circle "ЗАПИСЬ\nПАЦИЕНТА\n(Процесс 1.1)" as process1
circle "СОЗДАНИЕ\nМЕДКАРТЫ\n(Процесс 1.3)" as process3

database "ЖУРНАЛ ЗАПИСЕЙ\n(Excel)\n🔴 РИСК" as store1
database "АНКЕТЫ ПАЦИЕНТОВ\n(Файлы)\n🔴 РИСК" as store2  
database "МЕДИЦИНСКИЕ КАРТЫ\n(PDF/JPG)\n🔴 КРИТИЧНО" as store3

rectangle "МЕДИЦИНСКИЙ\nСПЕЦИАЛИСТ\n(Процесс 1.2)" as doctor

patient --> process1 : "Данные пациента\n(ФИО, телефон, жалобы)"
process1 --> store1 : "Персональные данные"
process1 --> store2 : "Персональные данные"
process1 --> doctor : "Персональные данные"

store1 --> doctor
store2 --> doctor

doctor --> process3 : "Медицинские данные"
process3 --> store3 : "Медицинская карта"

note top of store1 : D1
note top of store2 : D2
note top of store3 : D3

@enduml
```

## 2. Процесс обработки результатов анализов

```plantuml
@startuml lab_results_dfd
!theme plain

rectangle "ВНЕШНЯЯ\nЛАБОРАТОРИЯ\n(Внешняя сущность)" as lab
circle "ПОЛУЧЕНИЕ\nАНАЛИЗОВ\n(Процесс 2.1)\n🔴 Email/Факс" as process1
circle "ОБРАБОТКА\nФАЙЛОВ\n(Процесс 2.2)" as process2
circle "АНАЛИЗ\nРЕЗУЛЬТАТОВ\n(Процесс 2.3)" as process3
circle "ВНЕСЕНИЕ\nВ КАРТУ\n(Процесс 2.4)" as process4

database "АНАЛИЗЫ\n(Файлы)\n🔴 РИСК" as store4
database "МЕДИЦИНСКИЕ КАРТЫ\n(Обновлено)\n🔴 КРИТИЧНО" as store3

rectangle "МЕДИЦИНСКИЙ\nСПЕЦИАЛИСТ" as doctor

lab --> process1 : "Результаты анализов\n(PDF, данные)"
process1 --> process2 : "Файлы анализов"
process2 --> store4 : "Цифровые копии"
store4 --> process3 : "Медицинские данные"
process3 --> process4 : "Заключение врача"
process4 --> store3 : "Обновленная медкарта"

note top of store4 : D4
note top of store3 : D3

@enduml
```

## 3. Процесс оплаты услуг

```plantuml
@startuml payment_process_dfd
!theme plain

rectangle "ПАЦИЕНТ\n(Плательщик)" as patient
circle "ПРИЕМ\nПЛАТЕЖА\n(Процесс 3.1)" as process1
circle "БУХГАЛТЕРСКИЙ\nУЧЕТ\n(Процесс 3.3)" as process3

rectangle "ККМ\n(Касса)\n✅ ОК" as kkm
rectangle "1С: БУХГАЛТЕРИЯ\n✅ ОК" as oneс
rectangle "НАЛОГОВАЯ\nОТЧЕТНОСТЬ\n(Внешняя сущность)" as tax

database "УЧЕТ ПЛАТЕЖЕЙ\n(Excel)\n🔴 РИСК" as store_excel
database "ОБЩИЙ ДИСК\n🔴 РИСК" as store_disk

rectangle "ФИСКАЛЬНЫЕ\nДОКУМЕНТЫ\n✅ ОК" as fiscal

patient --> process1 : "Запрос на оплату\n(услуги, сумма)"
process1 --> kkm : "Данные платежа"
process1 --> store_excel : "Данные платежа"  
process1 --> oneс : "Данные платежа"

kkm --> fiscal
store_excel --> store_disk
oneс --> process3
process3 --> tax

@enduml
```

## 4. Защищённый процесс записи пациента (улучшенная версия)

```plantuml
@startuml secure_patient_registration
!theme plain

rectangle "ПАЦИЕНТ\n+ Согласие ПД\n✅ ЗАЩИЩЕНО" as patient
circle "РЕСЕПШЕН\n+ MFA\n✅ ОК" as reception
circle "СОЗДАНИЕ\nМЕДКАРТЫ\n✅ ОК" as create_record

database "ЗАЩИЩЁННАЯ БД\nAES-256\n✅ ЗАЩИЩЕНО" as secure_db
database "СИСТЕМА ТЕГИРОВАНИЯ\nPII_BASIC\n✅ ЗАЩИЩЕНО" as tagging
database "ЗАЩИЩЁННОЕ ХРАНИЛИЩЕ\n+ Аудит + Версии\n✅ ЗАЩИЩЕНО" as secure_storage

rectangle "ВРАЧ + MFA\n✅ ОК" as doctor
rectangle "DLP СИСТЕМА\n✅ ЗАЩИТА" as dlp
rectangle "SIEM МОНИТОРИНГ\n✅ КОНТРОЛЬ" as siem

patient --> reception : "Согласие на обработку ПД"
dlp --> reception : "Контроль утечек"
dlp --> doctor : "Контроль утечек"

reception --> secure_db : "Шифрование AES-256"
reception --> tagging : "Тегирование: PII_BASIC"

secure_db --> doctor : "RBAC: Doctor_Only"
doctor --> create_record : "Цифровая подпись"
create_record --> secure_storage : "Зашифрованная медкарта"

siem --> secure_db : "Наблюдение"
siem --> secure_storage : "Наблюдение"

@enduml
```

## Инструкция по конвертации в SVG

Для конвертации PlantUML диаграмм в SVG используйте:

### Онлайн:
1. Откройте http://www.plantuml.com/plantuml/uml/
2. Вставьте код диаграммы
3. Получите SVG-файл

### Локально:
```bash
# Установка PlantUML
brew install plantuml  # macOS
sudo apt install plantuml  # Ubuntu

# Конвертация в SVG
plantuml -tsvg dfd_plantuml.md
```

### VS Code:
1. Установите расширение "PlantUML"
2. Откройте файл с диаграммами
3. Используйте Ctrl+Shift+P → "PlantUML: Preview Current Diagram"

## Преимущества PlantUML диаграмм:
- ✅ Векторная графика (SVG)
- ✅ Масштабируемость
- ✅ Профессиональный вид
- ✅ Автоматическая компоновка
- ✅ Интеграция с документацией
- ✅ Версионирование в Git

