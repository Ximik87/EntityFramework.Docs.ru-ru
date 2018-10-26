---
title: Метод Load - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 03c5a069-b7b4-455f-a16f-ee3b96cc4e28
ms.openlocfilehash: bcea8ab2477f44281cd5de824457a72a84ccc766
ms.sourcegitcommit: 4a795285004612ac03ab26532ac09ca333cb4c8f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123821"
---
# <a name="the-load-method"></a><span data-ttu-id="79284-102">Метод Load</span><span class="sxs-lookup"><span data-stu-id="79284-102">The Load Method</span></span>
<span data-ttu-id="79284-103">Существует несколько сценариев, где может потребоваться загрузка сущностей из базы данных в контекст, не выполняя никаких действий с этими сущностями немедленно.</span><span class="sxs-lookup"><span data-stu-id="79284-103">There are several scenarios where you may want to load entities from the database into the context without immediately doing anything with those entities.</span></span> <span data-ttu-id="79284-104">Хорошим примером этого загрузка сущностей для привязки данных, как описано в разделе [локальных данных](~/ef6/querying/local-data.md).</span><span class="sxs-lookup"><span data-stu-id="79284-104">A good example of this is loading entities for data binding as described in [Local Data](~/ef6/querying/local-data.md).</span></span> <span data-ttu-id="79284-105">Один из способов сделать это — написать запрос LINQ, а затем вызывать ToList в его, только для немедленного удаления созданный список.</span><span class="sxs-lookup"><span data-stu-id="79284-105">One common way to do this is to write a LINQ query and then call ToList on it, only to immediately discard the created list.</span></span> <span data-ttu-id="79284-106">Метода расширения Load работает так же, как ToList, за исключением того, что позволяет избежать создания списка полностью.</span><span class="sxs-lookup"><span data-stu-id="79284-106">The Load extension method works just like ToList except that it avoids the creation of the list altogether.</span></span>  

<span data-ttu-id="79284-107">Методы, представленные в этом разделе, также применимы к моделям, созданным с помощью Code First и конструктора EF.</span><span class="sxs-lookup"><span data-stu-id="79284-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="79284-108">Ниже приведены два примера использования нагрузки.</span><span class="sxs-lookup"><span data-stu-id="79284-108">Here are two examples of using Load.</span></span> <span data-ttu-id="79284-109">Первый берется из приложения привязки данных Windows Forms, где нагрузки используется для запроса сущностей перед привязкой к локальной коллекции, как описано в разделе [локальных данных](~/ef6/querying/local-data.md):</span><span class="sxs-lookup"><span data-stu-id="79284-109">The first is taken from a Windows Forms data binding application where Load is used to query for entities before binding to the local collection, as described in [Local Data](~/ef6/querying/local-data.md):</span></span>  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

<span data-ttu-id="79284-110">Второй пример показывает использование загрузки для загрузки фильтрованную коллекцию связанных сущностей, как описано в [загрузка связанных сущностей](~/ef6/querying/related-data.md):</span><span class="sxs-lookup"><span data-stu-id="79284-110">The second example shows using Load to load a filtered collection of related entities, as described in [Loading Related Entities](~/ef6/querying/related-data.md):</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework"))
        .Load();
}
```  
