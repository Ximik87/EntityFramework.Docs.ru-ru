---
title: Поддержка Enum - конструктор EF - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
caps.latest.revision: 3
ms.openlocfilehash: cbf9b01fcbe21274ff3644c6ae6bc8fdfd338e3b
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2018
ms.locfileid: "39122585"
---
# <a name="enum-support---ef-designer"></a>Поддержка перечислений — конструктор EF
> [!NOTE]
> **EF5 и более поздних версий только** -функции, интерфейсы API, и т.д., описанных на этой странице появились в Entity Framework 5. При использовании более ранней версии могут быть неприменимы некоторые или все сведения.

Это видео и пошаговые пошаговом руководстве показано, как использовать типы перечисления с Entity Framework Designer. Также демонстрируется использование перечислений в запросе LINQ.

В этом пошаговом руководстве будет использоваться для создания новой базы данных Model First, но конструктор EF может также использоваться с [Database First](~/ef6/modeling/designer/workflows/database-first.md) рабочий процесс для сопоставления существующей базы данных.

В Entity Framework 5 добавлена поддержка перечисления. Чтобы использовать новые функции, например перечисления, Пространственные типы данных и функции, возвращающие табличные значения, необходимо ориентироваться .NET Framework 4.5. Visual Studio 2012 предназначенного для .NET 4.5 по умолчанию.

В Entity Framework, что перечисление может иметь следующие базовые типы: **байтов**, **Int16**, **Int32**, **Int64** , или **SByte**.

## <a name="watch-the-video"></a>Просмотреть видео
В этом видео показано, как использовать типы перечисления с Entity Framework Designer. Также демонстрируется использование перечислений в запросе LINQ.

**Представленный**: Юлия Корнич

**Видео**: [WMV](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)

## <a name="pre-requisites"></a>Предварительные требования

Необходимо будет установлен выпуск Visual Studio 2012 Ultimate, Premium, Professional и Web Express для выполнения этого пошагового руководства.

## <a name="set-up-the-project"></a>Настройка проекта

1.  Откройте Visual Studio 2012
2.  На **файл** последовательно выберите пункты **New**, а затем нажмите кнопку **проекта**
3.  В левой области щелкните **Visual C\#**, а затем выберите **консоли** шаблона
4.  Введите **EnumEFDesigner** как имя проекта и нажмите кнопку **ОК**

## <a name="create-a-new-model-using-the-ef-designer"></a>Создание новой модели в конструкторе EF

1.  Щелкните правой кнопкой мыши имя проекта в обозревателе решений, выберите пункт **добавить**, а затем нажмите кнопку **новый элемент**
2.  Выберите **данных** меню слева, а затем выберите **ADO.NET Entity Data Model** в области шаблонов
3.  Введите **EnumTestModel.edmx** имя файла, а затем нажмите кнопку **добавить**
4.  На странице мастера моделей EDM, выберите **пустую модель** в диалоговом окне Выбор содержимого модели
5.  Нажмите кнопку **Готово**

Конструктор сущностей, который предоставляет область конструктора для изменения модели, отображается.

Мастер выполняет следующие действия.

-   Создает файл EnumTestModel.edmx, который определяет концептуальную модель, модель хранения и сопоставления между ними. Задает свойство обработка артефактов метаданных из EDMX-файл для внедрения в выходной сборки, поэтому созданные метаданные файлы внедряются в сборку.
-   Добавляет ссылку на следующие сборки: EntityFramework, System.ComponentModel.DataAnnotations и System.Data.Entity.
-   Создает файлы EnumTestModel.tt и EnumTestModel.Context.tt и добавляет их в EDMX-файл. Эти файлы шаблонов T4 создавать код, который определяет тип производным DbContext и типов POCO, которые сопоставляются с сущностями в модели .edmx.

## <a name="add-a-new-entity-type"></a>Добавить новый тип сущности

1.  Щелкните правой кнопкой мыши пустой участок области конструктора, выберите **Add -&gt; сущности**, появится диалоговое окно новой сущности
2.  Укажите **отдел** для типа имя и указать **DepartmentID** оставьте тот тип, что имя ключевого свойства **Int32**
3.  Нажмите кнопку **ОК**.
4.  Щелкните правой кнопкой мыши сущность и выберите **Add New -&gt; скалярное свойство**
5.  Переименовать новое свойство **имя**
6.  Измените тип нового свойства для **Int32** (по умолчанию свойство имеет строковый тип) чтобы изменить тип, откройте окно свойств и измените свойство Type для **Int32**
7.  Добавить еще одно скалярное свойство и переименуйте его в **бюджета**, измените тип на **Decimal**

## <a name="add-an-enum-type"></a>Добавьте тип перечисления

1.  В конструкторе Entity Framework, щелкните правой кнопкой мыши свойство Name, выберите **преобразовать перечисление**

    ![ConvertToEnum](~/ef6/media/converttoenum.png)

2.  В **перечислить** тип диалогового окна **DepartmentNames** для именем типа перечисления, изменить базовый тип для **Int32**, а затем добавьте следующие элементы в тип: английского языка, Математические и экономики

    ![AddEnumType](~/ef6/media/addenumtype.png)

3.  Нажмите клавишу **ОК**
4.  Сохранение модели и построение проекта
    > [!NOTE]
    > При создании, предупреждения о несопоставленных сущностях и ассоциациях, появляются в списке ошибок. Эти предупреждения можно игнорировать, поскольку после мы решили создать базу данных из модели, пропадут.

Если взглянуть на окне «Свойства», можно заметить, что тип свойства имя было изменено на **DepartmentNames** и новые перечисляемого типа была добавлена в список типов.

При переключении в окно браузера модели, вы увидите сообщение о том, что тип также был добавлен к узлу для перечислимых типов.

![ModelBrowser](~/ef6/media/modelbrowser.png)

>[!NOTE]
> Можно также добавить новые типы перечисления из этого окна, щелкнув правой кнопкой мыши и выбрав **добавить тип перечисления**. После создания типа, он будет отображаться в списке типов, и вы смогли бы связать со свойством

## <a name="generate-database-from-model"></a>Создание базы данных на основе модели

Теперь мы сможем создать базу данных, основанный на модели.

1.  Щелкните правой кнопкой мыши пустое место в области конструктора сущностей и выберите пункт **создать базу данных из модели**
2.  Диалогового окна соединения данных из мастера создания базы данных является выбор отображаемых щелкните **новое подключение** кнопку укажите **(localdb)\\mssqllocaldb** для имени сервера и  **EnumTest** базы данных и нажмите кнопку **ОК**
3.  Диалоговое окно с запросом, если вы хотите создать новую базу данных будет всплывающее окно, нажмите кнопку **Да**.
4.  Нажмите кнопку **Далее** и мастер создания базы данных приводит к возникновению ошибки языка описания данных (DDL) для создания базы данных, сформированный код DDL отображается сводка и параметры диалогового окна поле Обратите внимание, что, DDL не содержит определение для таблицу, которая сопоставляется с типом перечисления
5.  Нажмите кнопку **Готово** щелкнув Готово не выполняет скрипт DDL.
6.  Мастер создания базы данных выполняет следующие: открывает **EnumTest.edmx.sql** в редакторе T-SQL формирует разделы схемы и сопоставления хранилища EDMX-файла файл строку подключения Adds в файл App.config
7.  Щелкните правой кнопкой мыши в редакторе T-SQL и выберите **Execute** The Server диалоговое окно подключения к откроется, введите сведения о соединении из шага 2 и нажмите кнопку **Connect**
8.  Чтобы просмотреть созданную схему, щелкните правой кнопкой мыши имя базы данных в обозревателе объектов SQL Server и выберите **обновления**

## <a name="persist-and-retrieve-data"></a>Сохранения и извлечения данных

Откройте файл Program.cs, в котором определен метод Main. Добавьте следующий код в функцию Main. Код добавляет новый объект отдела в контекст. Затем она сохраняет данные. Код также выполняется запрос LINQ, возвращающий подразделение, где имя задается DepartmentNames.English.

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

Скомпилируйте и запустите приложение. Программа выдает следующие результаты.

```
DepartmentID: 1 Name: English
```

Чтобы просмотреть данные в базе данных, щелкните правой кнопкой мыши имя базы данных в обозревателе объектов SQL Server и выберите **обновить**. Щелкните правой кнопкой мыши таблицу и выберите **данные представления**.

## <a name="summary"></a>Сводка

В этом пошаговом руководстве мы рассмотрели сведения о сопоставлении типов перечисления, с помощью Entity Framework Designer и как использовать перечисления в коде. 