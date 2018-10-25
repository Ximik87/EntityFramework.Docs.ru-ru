---
title: Ведение журнала — EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 65501b5ac03ae544c51b7fc1a07fa9eea849f1e3
ms.sourcegitcommit: 5e11125c9b838ce356d673ef5504aec477321724
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50022149"
---
# <a name="logging"></a><span data-ttu-id="7a986-102">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="7a986-102">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="7a986-103">Для этой статьи вы можете скачать [пример](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) из репозитория GitHub.</span><span class="sxs-lookup"><span data-stu-id="7a986-103">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="7a986-104">Приложения ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a986-104">ASP.NET Core applications</span></span>

<span data-ttu-id="7a986-105">EF Core автоматически интегрируется с механизмы ведения журнала ASP.NET Core всякий раз, когда `AddDbContext` или `AddDbContextPool` используется.</span><span class="sxs-lookup"><span data-stu-id="7a986-105">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="7a986-106">Таким образом, при использовании ASP.NET Core, ведение журнала следует настроить как описано в разделе [документации по ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="7a986-106">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="7a986-107">Другие приложения</span><span class="sxs-lookup"><span data-stu-id="7a986-107">Other applications</span></span>

<span data-ttu-id="7a986-108">Ведение журнала в данный момент EF Core требуется ILoggerFactory, который сам настроены ILoggerProvider один или несколько.</span><span class="sxs-lookup"><span data-stu-id="7a986-108">EF Core logging currently requires an ILoggerFactory which is itself configured with one or more ILoggerProvider.</span></span> <span data-ttu-id="7a986-109">Общих поставщиков поставляются в следующие пакеты:</span><span class="sxs-lookup"><span data-stu-id="7a986-109">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="7a986-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): простое консольное средство ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="7a986-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="7a986-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): службы приложений Azure поддерживает «Журналы диагностики» и «Вход потока» компоненты.</span><span class="sxs-lookup"><span data-stu-id="7a986-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="7a986-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): журналы, чтобы отладчик монитор, используя System.Diagnostics.Debug.WriteLine().</span><span class="sxs-lookup"><span data-stu-id="7a986-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="7a986-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): записывает журнал событий Windows.</span><span class="sxs-lookup"><span data-stu-id="7a986-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="7a986-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): поддерживает EventSource/EventListener.</span><span class="sxs-lookup"><span data-stu-id="7a986-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="7a986-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): журналы, чтобы прослушиватель трассировки с помощью System.Diagnostics.TraceSource.TraceEvent().</span><span class="sxs-lookup"><span data-stu-id="7a986-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using System.Diagnostics.TraceSource.TraceEvent().</span></span>

<span data-ttu-id="7a986-116">После установки соответствующих пакетов, в приложении необходимо создать одноэлементный/глобальный экземпляр LoggerFactory.</span><span class="sxs-lookup"><span data-stu-id="7a986-116">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="7a986-117">Например используя средство ведения журнала консоли:</span><span class="sxs-lookup"><span data-stu-id="7a986-117">For example, using the console logger:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

<span data-ttu-id="7a986-118">Этот одноэлементный или глобальный экземпляр затем должен быть зарегистрирован в EF Core на `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="7a986-118">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="7a986-119">Пример:</span><span class="sxs-lookup"><span data-stu-id="7a986-119">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="7a986-120">Очень важно, приложения не создают новый экземпляр ILoggerFactory для каждого экземпляра контекста.</span><span class="sxs-lookup"><span data-stu-id="7a986-120">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="7a986-121">Это приведет к утечке памяти и снижению производительности.</span><span class="sxs-lookup"><span data-stu-id="7a986-121">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="7a986-122">Фильтрация, регистрируемых</span><span class="sxs-lookup"><span data-stu-id="7a986-122">Filtering what is logged</span></span>

<span data-ttu-id="7a986-123">Для фильтрации, регистрируемых проще всего настроить его при регистрации ILoggerProvider.</span><span class="sxs-lookup"><span data-stu-id="7a986-123">The easiest way to filter what is logged is to configure it when registering the ILoggerProvider.</span></span> <span data-ttu-id="7a986-124">Пример:</span><span class="sxs-lookup"><span data-stu-id="7a986-124">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

<span data-ttu-id="7a986-125">В этом примере для возвращения только сообщений фильтруется журнала:</span><span class="sxs-lookup"><span data-stu-id="7a986-125">In this example, the log is filtered to return only messages:</span></span>
 * <span data-ttu-id="7a986-126">в категории «Microsoft.EntityFrameworkCore.Database.Command»</span><span class="sxs-lookup"><span data-stu-id="7a986-126">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
 * <span data-ttu-id="7a986-127">на уровне «Сведения»</span><span class="sxs-lookup"><span data-stu-id="7a986-127">at the 'Information' level</span></span>

<span data-ttu-id="7a986-128">Категории средства ведения журнала для EF Core, определенных в `DbLoggerCategory` класса, чтобы его легко найти в категории, но эти разрешения в простых строк.</span><span class="sxs-lookup"><span data-stu-id="7a986-128">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="7a986-129">Дополнительные сведения о базовой инфраструктуре ведения журнала можно найти в [документации ведения журнала ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="7a986-129">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
