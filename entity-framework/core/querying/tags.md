---
title: Теги запросов — EF Core
author: divega
ms.date: 11/14/2018
ms.assetid: 73C7A627-C8E9-452D-9CD5-AFCC8FEFE395
uid: core/querying/tags
ms.openlocfilehash: 3a4d516cab5836c659e42d825c4f1bf89355d671
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688769"
---
# <a name="query-tags"></a>Теги запросов
> [!NOTE]
> Это новая функция в EF Core 2.2.

Эта функция помогает сопоставить запросы LINQ в коде со сгенерированными запросами SQL, зарегистрированными в журналах.
Мы можете пометить запрос LINQ с помощью нового метода `TagWith()`: 

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

Этот запрос LINQ преобразуется в следующую инструкцию SQL:

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

Метод `TagWith()` можно вызвать много раз для одного и того же запроса.
Теги запросов являются кумулятивными.
Например, при использовании следующих методов

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

этот запрос   

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

преобразуется в:

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

В качестве тегов запросов можно использовать многострочные литералы.
Пример:

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

Преобразуется в следующий запрос SQL:

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>Известные ограничения
**Теги запросов не поддерживают параметризацию** — EF Core всегда обрабатывает теги в запросе LINQ как строковые литералы, которые включаются в сгенерированный запрос SQL.
Скомпилированные запросы, принимающие теги запросов в качестве параметров, не допускаются.