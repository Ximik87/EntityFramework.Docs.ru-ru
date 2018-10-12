---
title: Создание модели — EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 88253ff3-174e-485c-b3f8-768243d01ee1
uid: core/modeling/index
ms.openlocfilehash: 67012d0f52cc77ce872fc428fccc20526f3fefad
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489275"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="1d33a-102">Создание и настройка модели</span><span class="sxs-lookup"><span data-stu-id="1d33a-102">Creating and configuring a Model</span></span>

<span data-ttu-id="1d33a-103">Entity Framework использует набор соглашений для создания модели на основе формы классов сущностей.</span><span class="sxs-lookup"><span data-stu-id="1d33a-103">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="1d33a-104">Вы можете указать дополнительную конфигурацию, чтобы дополнить и (или) переопределить данные, обнаруженные соглашением.</span><span class="sxs-lookup"><span data-stu-id="1d33a-104">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="1d33a-105">Эта статья описывает конфигурацию, которую можно применить к модели, ориентированной на любое хранилище данных, а также при ориентации на любую реляционную базу данных.</span><span class="sxs-lookup"><span data-stu-id="1d33a-105">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="1d33a-106">Поставщики также могут включить конфигурацию, предназначенную для конкретного хранилища данных.</span><span class="sxs-lookup"><span data-stu-id="1d33a-106">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="1d33a-107">Документацию по конфигурации для конкретного поставщика см. в разделе [Поставщики баз данных](../providers/index.md).</span><span class="sxs-lookup"><span data-stu-id="1d33a-107">For documentation on provider specific configuration see the [Database Providers](../providers/index.md) section.</span></span>

> [!TIP]  
> <span data-ttu-id="1d33a-108">Для этой статьи вы можете просмотреть [пример](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples) в репозитории GitHub.</span><span class="sxs-lookup"><span data-stu-id="1d33a-108">You can view this article’s [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="1d33a-109">Использование текучего API для настройки модели</span><span class="sxs-lookup"><span data-stu-id="1d33a-109">Use fluent API to configure a model</span></span>

<span data-ttu-id="1d33a-110">Вы можете переопределить метод `OnModelCreating` в производном контексте и использовать `ModelBuilder API` для настройки модели.</span><span class="sxs-lookup"><span data-stu-id="1d33a-110">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="1d33a-111">Это наиболее эффективный метод настройки, который позволяет задать конфигурацию без изменения ваших классов сущностей.</span><span class="sxs-lookup"><span data-stu-id="1d33a-111">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="1d33a-112">Конфигурация текучего API имеет самый высокий приоритет и переопределяет соглашения и заметки к данным.</span><span class="sxs-lookup"><span data-stu-id="1d33a-112">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Required.cs?range=5-15&highlight=5-10)] -->

``` csharp
    class MyContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Blog>()
                .Property(b => b.Url)
                .IsRequired();
        }
    }
```

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="1d33a-113">Использование заметок к данным для настройки модели</span><span class="sxs-lookup"><span data-stu-id="1d33a-113">Use data annotations to configure a model</span></span>

<span data-ttu-id="1d33a-114">Вы также можете применять для классов и свойств атрибуты (называемые заметками к данным).</span><span class="sxs-lookup"><span data-stu-id="1d33a-114">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="1d33a-115">Заметки к данным переопределяют соглашения, но они будут перезаписаны конфигурацией текучего API.</span><span class="sxs-lookup"><span data-stu-id="1d33a-115">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/Required.cs?range=11-16&highlight=4)] -->
``` csharp
    public class Blog
    {
        public int BlogId { get; set; }
        [Required]
        public string Url { get; set; }
    }
```
