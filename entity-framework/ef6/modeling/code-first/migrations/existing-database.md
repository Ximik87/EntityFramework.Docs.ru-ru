---
title: Code First Migrations с существующей базы данных - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: f0cc4f93-67dd-4664-9753-0a9f913814db
caps.latest.revision: 3
ms.openlocfilehash: 77e139a29bb4708b00fc6198a57780ce75197252
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "39122774"
---
# <a name="code-first-migrations-with-an-existing-database"></a>Code First Migrations с существующей базы данных
> [!NOTE]
> **EF4.3 и более поздних версий только** -функции, интерфейсы API, и т.д., описанных на этой странице появились в версии 4.1 платформы Entity Framework. При использовании более ранней версии могут быть неприменимы некоторые или все сведения.

В этой статье рассматривается использование с существующей базы данных, которая не была создана с Entity Framework Code First Migrations.

> [!NOTE]
> В этой статье предполагается, что вы знаете, как использовать Code First Migrations в основных сценариях. Если этого не сделать, то нужно будет прочесть [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md) перед продолжением.

## <a name="screencasts"></a>Демонстрационные ролики

Если вместо этого будет смотреть презентация, чем, в этой статье, следующие два видео рассматриваются то же содержимое, что в этой статье.

### <a name="video-one-migrations---under-the-hood"></a>Один видео: «Миграции — взгляд изнутри»

[Этот демонстрационный ролик](http://channel9.msdn.com/blogs/ef/migrations-under-the-hood) рассматривается как отслеживает миграций и использует сведения о модели для обнаружения изменений в модели.

### <a name="video-two-migrations---existing-databases"></a>Два видео: «Миграции — существующих баз данных»

Основываясь на концепциях из предыдущих видео, [этой трансляции](http://channel9.msdn.com/blogs/ef/migrations-existing-databases) описывается, как включить и использовать миграции с существующей базы данных.

## <a name="step-1-create-a-model"></a>Шаг 1: Создание модели

Первым шагом является создание модели Code First, предназначенного для существующей базы данных. [Code First для существующей базы данных](~/ef6/modeling/code-first/workflows/existing-database.md) разделе содержатся подробные указания о том, как это сделать.

>[!NOTE]
> Очень важно выполните остальные действия, описанные в этом разделе перед внесением любых изменений в модель, могут потребовать изменений в схему базы данных. Для следующих действий требуется модель должна быть синхронизированной со схемой базы данных.

## <a name="step-2-enable-migrations"></a>Шаг 2: Включение миграции

Следующим шагом является включение migrations. Это можно сделать, выполнив **Enable-Migrations** команду в консоли диспетчера пакетов.

Эта команда будет создайте папку с названием миграций и помещать один класс в его конфигурацию. Класс конфигурации здесь настраивается миграции для вашего приложения, можно найти дополнительные сведения см. в [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md) раздела.

## <a name="step-3-add-an-initial-migration"></a>Шаг 3: Добавление начального переноса

После создания и применения к миграции локальной базы данных, можно также применить эти изменения к другим базам данных. Например локальную базу данных могут быть тестовую базу данных и в конечном итоге можно также применить изменения к рабочей базе данных или другим разработчикам тестирования базы данных. Существует два варианта для этого шага, а также то, что следует выбрать от ли схемы из других баз данных пуст, или в настоящее время соответствуют схеме локальной базы данных.

-   **Вариант один: Использование существующей схемы как начальная точка.** Этот подход следует использовать при других баз данных, которые миграции будет применяться к в будущем будет иметь ту же схему, как в настоящее время имеет локальную базу данных. Например можно использовать это если локальной тестовой базе данных в настоящее время соответствует версии 1 из рабочей базы данных и будет применен эти миграции для обновления рабочей базы данных до версии 2.
-   **Второй вариант: Используйте пустую базу данных как начальная точка.** Этот подход следует использовать при других баз данных, которые миграции будет применяться к будущему являются пустыми (или еще не существуют). Например это может использовать, если к разработке приложения с помощью тестовой базы данных, но без использования миграции и вы позднее необходимо будет создавать рабочей базы данных с нуля.

### <a name="option-one-use-existing-schema-as-a-starting-point"></a>Вариант один: Используйте существующую схему в качестве отправной точки

Code First Migrations использует моментальный снимок модели, сохраненной в самой последней миграции для обнаружения изменений в модель (можно найти подробные сведения об этом в [Code First Migrations в командных средах](~/ef6/modeling/code-first/migrations/teams.md)). Так как мы собираемся предполагается, что базы данных уже имеют схему текущей модели, будет создан пустой миграции (нет-op), с текущей модели как моментальный снимок.

1.  Запустите **это добавить миграцию InitialCreate — IgnoreChanges** команду в консоли диспетчера пакетов. Будет создана пустая Миграция с текущей моделью, как моментальный снимок.
2.  Запустите **Update-Database** команду в консоли диспетчера пакетов. Это InitialCreate миграции будут применены к базе данных. Так как фактический перенос не содержит никаких изменений, он просто добавлять строки \_ \_MigrationsHistory таблицы, указывающее, что уже был применен такой миграции.

### <a name="option-two-use-empty-database-as-a-starting-point"></a>Второй вариант: Используйте пустую базу данных в качестве отправной точки

В этом сценарии мы должны миграций, чтобы иметь возможность создать всю базу данных с нуля — включая таблицы, которые уже присутствуют в локальной базе данных. Мы собираемся создать это InitialCreate миграции, включающий логику для создания существующей схемы. Затем мы сделаем нашу существующую базу данных похож на уже был применен такой миграции.

1.  Запустите **это добавить миграцию InitialCreate** команду в консоли диспетчера пакетов. При этом создается миграции для создания существующей схемы.
2.  Закомментируйте весь код в методе Up только что созданный миграции. Это позволит нам «apply» миграции в локальную базу данных без попытки повторного создания всех таблиц и т.д., которые уже существуют.
3.  Запустите **Update-Database** команду в консоли диспетчера пакетов. Это InitialCreate миграции будут применены к базе данных. Так как не содержит фактического переноса всех изменений (так как мы временно комментариями их), он будет просто добавлять строки \_ \_MigrationsHistory таблицы, указывающее, что уже был применен такой миграции.
4.  Отменить преобразование в комментарий код в метод. Это означает, что при применении этой миграции в будущих базах данных, схемы, которая уже существует в локальной базе данных будут создаваться с помощью миграций.

## <a name="things-to-be-aware-of"></a>Моментов, которые следует учитывать

Существует несколько вещей, которые необходимо учитывать при использовании миграции для существующей базы данных.

### <a name="defaultcalculated-names-may-not-match-existing-schema"></a>Имена по умолчанию/вычисляться может не соответствовать существующей схемы

Миграции явно задает имена столбцов и таблиц, когда он формирует шаблоны миграций. Однако существуют другие объекты базы данных, миграция вычисляет имя по умолчанию при применении для миграции. Сюда входят индексы и ограничения внешнего ключа. При нацеливании на существующую схему, эти имена вычисляемых может не совпадать, что действительно существует в базе данных.

Ниже приведены некоторые примеры, при необходимости учитывать это.

**Если вы использовали "один параметр: использовать существующую схему в качестве отправной точки" на шаге 3:**

-   Если будущие изменения в модели требуется изменение или удаление одного из объектов базы данных, называется по-разному, необходимо будет изменить сформированный миграции, чтобы указать правильное имя. Миграция API-интерфейсы имеют необязательный параметр имени, который позволяет это сделать.
    Например, существующей схемы могут иметь таблицу Post с BlogId внешний ключевой столбец, имеющий индекс с именем IndexFk\_BlogId. Однако по умолчанию миграции будет ожидать, что этот индекс, чтобы называться IX\_BlogId. При внесении изменений в модель, которая происходит удаление этого индекса, необходимо будет изменить сформированный DropIndex вызов для указания IndexFk\_BlogId имя.

**Если вы использовали "два параметра: пустую базу данных в качестве отправной точки используйте" на шаге 3:**

-   При попытке запустить метод вниз первоначальной миграции (, восстановление пустую базу данных) в локальной базе данных может завершиться ошибкой, так как миграция будет пытаться удалить индексы и ограничения внешнего ключа, с использованием неверные имена. Это только повлияет на локальную базу данных так, как другие базы данных будет создан с нуля, используя метод вверх первоначальной миграции.
    Если понизить существующей локальной базы данных пустым, проще всего сделать это вручную, либо путем удаления базы данных или удаления всех таблиц. После этого начального перехода на предыдущий, все объекты базы данных будут созданы заново с именами по умолчанию поэтому эта проблема не представляют собой еще раз.
-   Если будущие изменения в модели требуется изменение или удаление одного из объектов базы данных, называется по-разному, это не будет работать для существующей локальной базы данных — так как имена не совпадают значения по умолчанию. Тем не менее он будет работать для баз данных, которые были созданы «с нуля», так как они будут использоваться имена по умолчанию, выбранный с помощью миграций.
    Можно либо вручную внести эти изменения на локальную базу данных существующего или рассмотрите возможность установки миграций повторного создания базы данных с нуля — как будет происходить на других компьютерах.
-   Баз данных, созданных с помощью метода вверх первоначальной миграции может немного отличаться от локальной базы данных, так как имена вычисленное значение по умолчанию для индексов и ограничений внешнего ключа будет использоваться. Может также оказаться с дополнительные индексы как миграции создаст индексы во внешних ключевых столбцах по умолчанию — это не так в локальной исходной базы данных.

### <a name="not-all-database-objects-are-represented-in-the-model"></a>Не все объекты базы данных представлены в модели

Объекты базы данных, которые не являются частью модели не будет обрабатываться с помощью миграций. Они могут включать представления, хранимые процедуры, разрешения, таблицы, которые не являются частью вашей модели, дополнительные индексы, и т.д.

Ниже приведены некоторые примеры, при необходимости учитывать это.

-   Независимо от параметра выбранное на шаге 3, если требуется будущие изменения в модели, изменение или удаление эти дополнительные объекты, миграция не будут знать для внесения этих изменений. Например если удалить столбец, содержащий указатель на нем, миграция не будет удалить индекс. Необходимо вручную добавить код для формирования шаблонов миграции.
-   Если вы использовали "два параметра: используйте пустую базу данных в качестве отправной точки", эти дополнительные объекты не будут создаваться по метод первоначальной миграции.
    Вы можете изменить вверх и вниз методы для обеспечения проверки этих дополнительных объектов, если вы хотите. Для объектов, которые изначально не поддерживаются в API миграции — например представления — можно использовать [Sql](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.sql.aspx) необработанных SQL на создание или удаление их выполнение метода.