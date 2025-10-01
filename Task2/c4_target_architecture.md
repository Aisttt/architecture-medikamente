# Диаграмма контекста C4 целевого состояния - "Медикаменте"

## Целевая архитектура системы с Privacy by Design

![Диаграмма контекста C4 целевого состояния](./diagrams/c4_context_target_state.svg)

<details>
<summary>Код PlantUML диаграммы</summary>

```plantuml
@startuml c4_context_target_state
!theme plain
skinparam backgroundColor white
skinparam defaultFontSize 10

' Внешние пользователи
rectangle "ПАЦИЕНТЫ\n(Внешние пользователи)" as patients #lightblue
rectangle "ВРАЧИ\n(Пользователи системы)" as doctors #lightblue  
rectangle "АДМИНИСТРАТИВНЫЙ\nПЕРСОНАЛ\n(Пользователи системы)" as admin_staff #lightblue
rectangle "ВНЕШНИЕ\nЛАБОРАТОРИИ\n(Внешние системы)" as labs #lightcoral

' Основная система Медикаменте
package "СИСТЕМА МЕДИКАМЕНТЕ" as medikamente_system #lightgreen {
    
    ' Пользовательские интерфейсы
    rectangle "PATIENT PORTAL\n(Веб-портал пациентов)" as patient_portal #lightyellow
    rectangle "STAFF PORTAL\n(Портал сотрудников)" as staff_portal #lightyellow
    rectangle "MOBILE APP\n(Мобильное приложение)" as mobile_app #lightyellow
    
    ' Слой безопасности и приватности  
    package "PRIVACY & SECURITY LAYER" as security_layer #lightcyan {
        rectangle "IAM SERVICE\n(Управление доступом)" as iam #lightcyan
        rectangle "DATA CLASSIFICATION\n(Классификация данных)" as classification #lightcyan
        rectangle "ENCRYPTION SERVICE\n(Шифрование)" as encryption #lightcyan
        rectangle "AUDIT & COMPLIANCE\n(Аудит и соответствие)" as audit #lightcyan
    }
    
    ' Доменные сервисы
    package "DOMAIN SERVICES" as domain_services #lightsteelblue {
        rectangle "PATIENT DOMAIN\n(Управление пациентами)" as patient_domain #lightsteelblue
        rectangle "MEDICAL RECORDS\n(Медицинские карты)" as medical_records #lightsteelblue
        rectangle "BILLING DOMAIN\n(Биллинг)" as billing #lightsteelblue
        rectangle "LAB INTEGRATION\n(Интеграция с лабораториями)" as lab_integration #lightsteelblue
    }
    
    ' Аналитический слой
    package "PRIVACY-PRESERVING ANALYTICS" as analytics_layer #lavender {
        rectangle "DATA ANONYMIZATION\n(Обезличивание данных)" as anonymization #lavender
        rectangle "ANALYTICS PLATFORM\n(Платформа аналитики)" as analytics_platform #lavender
        rectangle "DATA LAKE\n(Озеро данных)" as data_lake #lavender
        rectangle "PRIVACY IMPACT\nASSESSMENT\n(Оценка влияния на приватность)" as privacy_impact #lavender
    }
    
    ' API Gateway
    rectangle "API GATEWAY\n(Шлюз API)" as api_gateway #orange
}

' Внешние системы
rectangle "1С: БУХГАЛТЕРИЯ\n(Существующая система)" as onec_accounting #lightgray
rectangle "1С: ТОРГОВЛЯ И СКЛАД\n(Существующая система)" as onec_trade #lightgray
rectangle "РОСКОМНАДЗОР\n(Регулятор)" as regulator #lightcoral

' Взаимодействия пользователей с системой
patients --> patient_portal : "Запись на приём,\nпросмотр результатов"
patients --> mobile_app : "Мобильный доступ"
doctors --> staff_portal : "Ведение медкарт,\nпросмотр расписания"
admin_staff --> staff_portal : "Управление записями,\nобработка платежей"

' Интеграции с внешними системами
labs --> api_gateway : "Передача результатов\nанализов (защищённый API)"
api_gateway --> lab_integration : "Обработка данных\nлабораторий"

' Интеграция с существующими системами
onec_accounting --> billing : "Синхронизация\nфинансовых данных"
onec_trade --> billing : "Данные о товарах\nи услугах"

' Отчётность регулятору
audit --> regulator : "Отчёты о соблюдении\n152-ФЗ"

' Внутренние взаимодействия
patient_portal --> iam : "Аутентификация"
staff_portal --> iam : "Контроль доступа"
mobile_app --> iam : "Авторизация"

iam --> patient_domain : "Проверка прав\nдоступа"
iam --> medical_records : "Контроль доступа\nк медкартам"
iam --> billing : "Авторизация\nплатежей"

classification --> encryption : "Автоматическое\nшифрование"
classification --> audit : "Логирование\nклассификации"

' Аналитический поток
patient_domain --> anonymization : "Обезличивание\nданных пациентов"
medical_records --> anonymization : "Обезличивание\nмедкарт"
billing --> anonymization : "Обезличивание\nфинансовых данных"

anonymization --> data_lake : "Сохранение\nобезличенных данных"
data_lake --> analytics_platform : "Аналитическая\nобработка"
analytics_platform --> privacy_impact : "Оценка рисков\nреидентификации"

' Примечания о Privacy by Design
note top of security_layer : PRIVACY BY DESIGN:\n- Проактивная защита\n- Приватность по умолчанию\n- Встроенная безопасность
note top of analytics_layer : PRIVACY-PRESERVING:\n- Только обезличенные данные\n- k-анонимность\n- Контроль реидентификации
note bottom of iam : МИНИМАЛЬНЫЕ ПРАВА:\nДоступ только к необходимым\nданным по ролям

@enduml
```

</details>

## Описание архитектуры

### Ключевые принципы Privacy by Design в архитектуре:

**1. Проактивная защита**
- Слой безопасности (Privacy & Security Layer) встроен в архитектуру
- Автоматическая классификация и шифрование данных

**2. Приватность по умолчанию**  
- IAM Service обеспечивает минимальные права доступа
- Данные закрыты по умолчанию, открываются только по необходимости

**3. Встроенная приватность**
- Каждый доменный сервис интегрирован со слоем безопасности
- Обезличивание данных происходит автоматически при передаче в аналитику

**4. Полная функциональность**
- Система обеспечивает все бизнес-требования
- Удобство использования не страдает от мер безопасности

**5. Сквозная безопасность**
- Защита на всех уровнях: от пользовательского интерфейса до хранения данных
- Шифрование, аудит и контроль доступа на каждом этапе

**6. Видимость и прозрачность**
- Audit & Compliance Service обеспечивает полную прозрачность
- Пациенты могут видеть, кто обращался к их данным

**7. Уважение к приватности**
- Пациент контролирует свои данные через Patient Portal
- Возможность отзыва согласий и удаления данных

### Новые блоки для обеспечения Privacy by Design:

1. **Privacy & Security Layer** - централизованный слой безопасности
2. **Data Classification Service** - автоматическая классификация данных
3. **Privacy-Preserving Analytics** - аналитика с сохранением приватности
4. **API Gateway** - контролируемый доступ к данным
5. **Privacy Impact Assessment** - оценка влияния на приватность
