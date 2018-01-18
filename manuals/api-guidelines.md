# Руководство по REST API HB

<div style="font-size:150%">
Редакторы документа: Калтаев Азимхан, Туркин Артем<br/>
</div>

## Содержание
<!-- TOC depthFrom:1 depthTo:2 withLinks:1 updateOnSave:1 orderedList:0 -->

- [1. Введение](#1-Введение)
	- [Соглашения, используемые в документе](#Соглашения-используемые-в-документе)
- [2. Общие рекомендации](#2-Общие-рекомендации)
	- [![must](img/must.png) Придерживайтесь принципа API first](#-Придерживайтесь-принципа-api-first)
		- [4.3    Requirements language](#43-requirements-language)

<!-- /TOC -->

## 1. Введение
Данное руководство содержит рекомендации по проектированию REST API для разработчиков ИТ Блока ХБ.

Нашими основными целями при построении API является соблюдение последовательности и концентрация на реализации бизнес-логики. Мы ищем различные, не обязательно самые лучшие, но хорошо документируемые способы разработки API.

При прочтении данной статьи подразумевается, что вы знакомы с основными принципами HTTP и JSON.

Developers access most Microsoft Cloud Platform resources via HTTP interfaces.
Although each service typically provides language-specific frameworks to wrap their APIs, all of their operations eventually boil down to HTTP requests.
Microsoft must support a wide range of clients and services and cannot rely on rich frameworks being available for every development environment.
Thus a goal of these guidelines is to ensure Microsoft REST APIs can be easily and consistently consumed by any client with basic HTTP support.

To provide the smoothest possible experience for developers, it's important to have these APIs follow consistent design guidelines, thus making using them easy and intuitive.
This document establishes the guidelines to be followed by Microsoft REST API developers for developing such APIs consistently.

The benefits of consistency accrue in aggregate as well; consistency allows teams to leverage common code, patterns, documentation and design decisions.

These guidelines aim to achieve the following:
- Define consistent practices and patterns for all API endpoints across Microsoft.
- Adhere as closely as possible to accepted REST/HTTP best practices in the industry at-large.*
- Make accessing Microsoft Services via REST interfaces easy for all application developers.
- Allow service developers to leverage the prior work of other services to implement, test and document REST endpoints defined consistently.
- Allow for partners (e.g., non-Microsoft entities) to use these guidelines for their own REST endpoint design.

*Note: The guidelines are designed to align with building services which comply with the REST architectural style, though they do not address or require building services that follow the REST constraints.
The term "REST" is used throughout this document to mean services that are in the spirit of REST rather than adhering to REST by the book.*

В идеале все API должны выглядеть так, как будто их создал один и тот же автор.

При создании документа были использованы следующие материалы:
- [Zalando RESTful API and Event Scheme Guidelines](https://github.com/zalando/restful-api-guidelines)
- [Microsoft REST API Guidelines](https://github.com/Microsoft/api-guidelines)
- [Центр архитектуры Azure](https://docs.microsoft.com/ru-ru/azure/architecture/)

### Соглашения, используемые в документе
Ключевые слова "НЕОБХОДИМО", "НЕДОПУСТИМО", "ТРЕБУЕТСЯ", "НУЖНО", "НЕ ПОЗВОЛЯЕТСЯ", "СЛЕДУЕТ", "НЕ СЛЕДУЕТ", "РЕКОМЕНДУЕТСЯ", "НЕ РЕКОМЕНДУЕТСЯ", "ВОЗМОЖНО" и "НЕОБЯЗАТЕЛЬНО" в данном документе (без учета регистра) интерпретируются в соответствии с [RFC 2119][rfc-2119].

## 2. Общие рекомендации
Заголовки отмечены соответствующими метками: ![НЕОБХОДИМО](img/must.png), ![СЛЕДУЕТ](img/should.png), ![ВОЗМОЖНО](img/may.png).

### ![must](img/must.png) Придерживайтесь принципа API first
Как уже упоминалось во введении, API First является одним из основных принципов проектирования и архитектуры. В двух словах API First требует два аспекта:
- в первую очередь определение API за пределами кода, используя стандартный язык спецификации
- получение ранней обратной связи от экспертов и клиентов-разработчиков

Определяя API за пределами кода, мы хотим облегчить раннюю обратную связь, а также дисциплину разработки, которая фокусирует дизайн интерфейса сервиса на ...
- глубоком понимании домена и требуемых функциональных возможностей
- обобщении бизнес сущностей/ресурсов, т.е. избегание использования специфичных API (для конкретного случая)
- четком разделении вопросов ЧТО и КАК, т.е. абстрагирование от аспектов реализации. API должны быть стабильными, даже если мы полностью заменим реализацию сервиса, включая ее базовый стек технологий

Кроме того, описания API со стандартизованным форматом спецификации также способствует обеспечению ...
- единственного источника истины для спецификации API; это ключевая часть контракта между поставщиком услуг и клиентами
- инфраструктурного инструментария для обнаружения API, API GUI, документирования API, автоматической проверки качества

Важно понять, что API First не противоречит гибким принципам разработки. Сервисные приложения должны развиваться постепенно - и, следовательно, их API тоже. Конечно, спецификация API будет и должна развиваться итеративно в разные циклы; однако каждый из них начинается с загатовки (драфта) и ранней обратной связи команды и экспертов. API может измениться и улучшиться посредством замечаний, возникших в процессе реализации и результатов автоматического тестирования. Развитие API в течение жизненного цикла разработки может включать в себя изменения ломающие обратную совместимость для еще не выведенных в продуктив функций только при согласовании изменений с клиентами. Следовательно, API First не означает, что вы должны иметь 100%-ное понимание домена и требований, и никогда не сможете создавать код до того, как вы определите полный API и подтвердите его экспертным review. С другой стороны, API First, очевидно, противоречит плохой практике публикации API и запроса экспертной оценки после интеграции сервиса или даже после вывода сервиса в продуктив. Крайне важно запросить и получить раннюю обратную связь - как можно раньше, но не до того, как изменения API будут всесторонними с уделением особого внимания следующему шагу эволюции и будут иметь определенное качество (включая соответствие требованиям API), уже подтвержденное внутренними review команды.

### ![must](img/must.png) Предоставляйте спецификацию API с помощью OpenAPI
Используйте [спецификацию OpenAPI](https://swagger.io/specification) (также известное как Swagger) в качестве стандарта для определения файлов спецификаций API. Разработчики API должны предоставлять файлы спецификаций API с помощью YAML или JSON для повышения удобочитаемости.

The API specification files should be subject to version control using a source code management system - best together with the implementing sources. The API specifications of component external APIs must be published and marked with the intended target audience.

### ![should](img/should.png) Предоставляйте руководство пользователя API

The keywords "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).


[rfc-2119]: https://github.com/zzzik23/main/blob/master/manuals/RFC2119.md
