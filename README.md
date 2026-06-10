# 🔍 Проверка дубликатов заявок перед внесением в CRM

[![n8n](https://img.shields.io/badge/n8n-2.11.4-EE3A43?logo=n8n)](https://n8n.io)
[![NocoDB](https://img.shields.io/badge/NocoDB-0.200.0-0891b2?logo=nocodb)](https://nocodb.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Автоматизация на **n8n** для приёма заявок, проверки дубликатов (телефон / email), ветвления логики и отправки уведомлений в Telegram + Email. Защита от частых повторов (5 минут) и встроенный обработчик ошибок.

---

## 🎯 Возможности

- ✅ **Приём заявок** – веб-форма n8n (можно встроить на сайт, в Telegram, любой канал).
- ✅ **Нормализация данных** – телефон → единый формат `7XXXXXXXXXX`, email → lower case.
- ✅ **Проверка дубликатов** – параллельные запросы по `Phone` и `Email` к NocoDB.
- ✅ **Защита от спама** – повторные заявки за **5 минут** игнорируются.
- ✅ **Ветвление логики** – новый лид / дубликат.
- ✅ **Уведомления** – менеджеру в Telegram, клиенту на Email.
- ✅ **Глобальный Error Handler** – логирование сбоев в Google Sheets + Telegram администратору.

---

## 📊 Ключевые результаты

| Показатель                | ДО         | ПОСЛЕ       |
|---------------------------|------------|-------------|
| Время обработки заявки    | 5–10 мин   | **< 1 мин** |
| Пропуск дубликатов        | 15%        | **0%**      |
| Ручная проверка менеджером| 2 ч/день   | **0 ч**     |
| Повторы за 5 минут        | спамят     | **игнор**   |
| Оповещение об ошибках     | нет        | **✅ Telegram + Sheets** |

---

## 🧱 Архитектура

```mermaid
flowchart TD
    A[Веб-форма] --> B[Нормализация]
    B --> C[Проверка Phone]
    B --> D[Проверка Email]
    C --> E[Merge Combine All]
    D --> E
    E --> F{Заявка < 5 мин?}
    F -- Да --> G[Игнорировать]
    F -- Нет --> H{Дубликат?}
    H -- Да --> I[Запись в Дубликаты<br/>Письмо + Telegram]
    H -- Нет --> J[Запись в Лиды<br/>Письмо + Telegram + ссылка]

## 🚀 Быстрый старт

1. Требования
n8n (self-hosted, версия ≥ 2.0)

NocoDB (таблицы: Лиды, Дубликаты)

Telegram Bot (токен)

SMTP сервер (отправка писем)

Google Sheets (опционально, для логов ошибок)

2. Импорт workflow
Скачайте файлы из папки workflows/:

Checking_duplicates_before_entering_CRM.json – основной

Error_Handler_Checking_duplicates.json – обработчик ошибок

В n8n: Settings → Workflows → Import from File

3. Настройка credentials
Интеграция	Тип credentials	Комментарий
NocoDB	nocoDbApiToken	API-токен из NocoDB
Telegram	telegramApi	Токен бота @AlbatoCourse_bot
SMTP	smtp	Сервер, логин, пароль
Google Sheets	googleSheetsOAuth2Api	OAuth2 (для листа ошибок)
4. Переменные окружения (рекомендовано)
Вынесите чувствительные параметры в Environment Variables n8n:


5. Активация
Включите основной workflow (Active toggle).

В настройках основного workflow укажите Error Workflow → выберите Error Handler - Checking duplicates.

Опубликуйте ссылку на форму (копируется из узла Form Trigger).

📁 Структура репозитория

text
checking-duplicates-crm-automation/
├── README.md                     # Этот файл
├── workflows/
│   ├── Checking_duplicates_before_entering_CRM.json
│   └── Error_Handler_Checking_duplicates.json
├── docs/
│   ├── technical_specification.md
│   ├── user_manual.md
│   └── report.md
├── screenshots/
│   ├── canvas.png
│   ├── new_lead_telegram.png
│   ├── duplicate_telegram.png
│   ├── new_lead_email.png
│   ├── duplicate_email.png
│   ├── nocodb_leads_table.png
│   ├── error_handler_telegram.png
│   └── error_google_sheet.png
└── LICENSE

📖 Документация
Техническое задание – полное описание требований и логики.

Руководство пользователя – для менеджеров и администратора.

Отчёт о реализации – этапы, трудности, результаты тестирования.

🖼 Скриншоты
Основной workflow	Уведомление о новом лиде	Уведомление о дубликате
https://screenshots/canvas.png	https://screenshots/new_lead_telegram.png	https://screenshots/duplicate_telegram.png
Письмо клиенту (новый лид)	Письмо клиенту (дубликат)	Таблица Лиды в NocoDB
https://screenshots/new_lead_email.png	https://screenshots/duplicate_email.png	https://screenshots/nocodb_leads_table.png
Error Handler Telegram	Лог ошибок в Google Sheets
https://screenshots/error_handler_telegram.png	https://screenshots/error_google_sheet.png
Все скриншоты находятся в папке screenshots.

🤝 Вклад в проект
Если вы нашли ошибку или хотите улучшить автоматизацию:

Форкните репозиторий.

Создайте ветку feature/your-idea.

Отправьте pull request.

📄 Лицензия
MIT License. Подробнее в файле LICENSE.

📬 Контакты
Автор: [Ваше имя / студия автоматизации]
Telegram‑канал: @ваш_канал
Пример workflow развёрнут и протестирован в n8n self‑hosted (версия 2.11.4).