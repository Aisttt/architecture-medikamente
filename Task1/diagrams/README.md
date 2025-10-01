# Диаграммы потоков данных - "Медикаменте"

## 📁 Содержимое директории

### SVG диаграммы (готовые к использованию):
- `patient_registration_dfd.svg` - Процесс записи пациента
- `lab_results_dfd.svg` - Обработка результатов анализов  
- `payment_process_dfd.svg` - Процесс оплаты услуг
- `medical_record_management_dfd.svg` - Ведение медкарт
- `inventory_management_dfd.svg` - Учёт ТМЦ и закупок
- `secure_patient_registration.svg` - Защищённая запись пациента
- `secure_lab_results_dfd.svg` - Защищённая обработка анализов

### PlantUML исходники (для редактирования):
- `patient_registration.puml`
- `lab_results.puml`
- `payment_process.puml`
- `medical_record_management.puml`
- `inventory_management.puml`
- `secure_patient_registration.puml`
- `secure_lab_results.puml`

## 🔄 Как обновить диаграммы

### 1. Редактирование
```bash
# Откройте нужный .puml файл в редакторе
code patient_registration.puml
```

### 2. Генерация SVG
```bash
# Из корня проекта
cd architecture-medikamente/Task1/diagrams
plantuml -tsvg *.puml
```

### 3. Проверка результата
SVG файлы автоматически обновятся и отобразятся в markdown файлах.

## 🎨 Цветовая схема

- `#lightblue` - Внешние сущности (пациенты, партнёры)
- `#lightgreen` - Безопасные процессы и системы
- `#orange` - Процессы с проблемами безопасности
- `#pink` - Уязвимые хранилища данных
- `#red` - Критически уязвимые хранилища
- `#yellow` - Аналитические системы

## 📋 Символы DFD

- `rectangle` - Внешние сущности
- `circle` - Процессы
- `database` - Хранилища данных
- `-->` - Потоки данных
- `note` - Комментарии и проблемы

## 🔧 Инструменты

- **PlantUML** - генерация диаграмм
- **Java** - требуется для PlantUML
- **VS Code** - расширение PlantUML для предпросмотра
- **Онлайн**: http://www.plantuml.com/plantuml/uml/
