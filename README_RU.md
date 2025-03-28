# BrowserTools MCP

> Сделайте ваши AI-инструменты в 10 раз более осведомленными и способными взаимодействовать с вашим браузером

Это приложение представляет собой мощный инструмент мониторинга и взаимодействия с браузером, который позволяет AI-приложениям через Model Context Protocol (MCP) от Anthropic захватывать и анализировать данные браузера с помощью расширения Chrome.

## Архитектура проекта

Проект состоит из трех основных компонентов:

1. **Расширение Chrome** (`chrome-extension`): Браузерное расширение, которое захватывает скриншоты, логи консоли, сетевую активность и элементы DOM.
2. **Серверное приложение Node** (`browser-tools-server`): Промежуточный сервер, который обеспечивает связь между расширением Chrome и экземпляром MCP-сервера.
3. **MCP сервер** (`browser-tools-mcp`): Сервер Model Context Protocol, который предоставляет стандартизированные инструменты для AI-клиентов для взаимодействия с браузером.

```
┌─────────────┐     ┌──────────────┐     ┌───────────────┐     ┌─────────────┐
│  MCP Клиент │ ──► │  MCP Сервер  │ ──► │  Node Сервер  │ ──► │ Расширение  │
│  (например, │ ◄── │ (Обработчик  │ ◄── │ (Промежуточ-  │ ◄── │   Chrome    │
│   Cursor)   │     │  протокола)  │     │  ный слой)    │     │             │
└─────────────┘     └──────────────┘     └───────────────┘     └─────────────┘
```

### Основные возможности

Model Context Protocol (MCP) - это возможность, поддерживаемая моделями Anthropic AI, которая позволяет создавать пользовательские инструменты для любого совместимого клиента. MCP-клиенты, такие как Claude Desktop, Cursor, Cline или Zed, могут запускать MCP-сервер, который "обучает" этих клиентов использовать новые инструменты.

Важно отметить, что **все логи хранятся локально** на вашем компьютере и НИКОГДА не отправляются внешним сторонним сервисам или API. BrowserTools MCP запускает локальный экземпляр NodeJS API-сервера, который взаимодействует с расширением BrowserTools для Chrome.

### Детальное описание компонентов

#### Расширение Chrome

- Отслеживает XHR запросы/ответы и логи консоли
- Отслеживает выбранные DOM элементы
- Отправляет все логи и текущий элемент в BrowserTools Connector
- Подключается к Websocket серверу для захвата/отправки скриншотов
- Позволяет пользователю настраивать лимиты токенов/усечения и путь к папке скриншотов

#### Node Сервер

- Действует как промежуточное ПО между расширением Chrome и MCP сервером
- Получает логи и текущий выбранный элемент от расширения Chrome
- Обрабатывает запросы от MCP сервера для захвата логов, скриншотов или текущего элемента
- Отправляет Websocket команды расширению Chrome для захвата скриншота
- Интеллектуально сокращает строки и количество дублирующихся объектов в логах для соблюдения лимитов токенов
- Удаляет cookies и чувствительные заголовки, чтобы избежать их отправки в LLM в MCP клиентах

#### MCP Сервер

- Реализует протокол Model Context Protocol
- Предоставляет стандартизированные инструменты для AI-клиентов
- Совместим с различными MCP-клиентами (Cursor, Cline, Zed, Claude Desktop и т.д.)

## Использование

После установки и настройки система позволяет любому совместимому MCP-клиенту:

- Отслеживать вывод консоли браузера
- Захватывать сетевой трафик
- Делать скриншоты
- Анализировать выбранные элементы
- Очищать логи, хранящиеся на MCP-сервере

## Совместимость

- Работает с любым MCP-совместимым клиентом
- В основном разработан для интеграции с Cursor IDE
- Поддерживает другие AI-редакторы и MCP-клиенты

## Установка

Полные инструкции по установке, быстрому старту и руководство по вкладу можно найти в [документации](https://browsertools.agentdesk.ai/).

## Обновления

Последняя версия v1.1.0 включает несколько исправлений ошибок для логирования и скриншотов.

Для использования:
1. Установите последнюю версию в вашей среде IDE / MCP клиенте:
   `npx @agentdeskai/browser-tools-mcp@1.1.0`

2. Загрузите последнюю версию расширения Chrome:
   [v1.1.0 BrowserToolsMCP Chrome Extension](https://github.com/AgentDeskAI/browser-tools-mcp/releases/download/v1.1.0/chrome-extension-v1-1-0.zip)

3. Запустите локальный Node сервер:
   `npx @agentdeskai/browser-tools-server`

4. После открытия инструментов разработчика Chrome, логи должны отправляться на ваш сервер! 