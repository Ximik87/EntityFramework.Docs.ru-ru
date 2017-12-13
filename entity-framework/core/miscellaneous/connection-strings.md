---
title: "Строки подключения - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
ms.technology: entity-framework-core
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: b4ed01f0452d74ac49d3fde780caa5f1b25a6e97
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2017
---
# <a name="connection-strings"></a><span data-ttu-id="e6206-102">Строки подключения</span><span class="sxs-lookup"><span data-stu-id="e6206-102">Connection Strings</span></span>

<span data-ttu-id="e6206-103">Большинство поставщиков базы данных требуют отдельной строки подключения к базе данных.</span><span class="sxs-lookup"><span data-stu-id="e6206-103">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="e6206-104">Иногда эта строка подключения содержит конфиденциальные сведения, которые необходимо защитить.</span><span class="sxs-lookup"><span data-stu-id="e6206-104">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="e6206-105">Также может потребоваться изменить строку подключения при переходах средах, таких как разработка, тестирование и производственного приложения.</span><span class="sxs-lookup"><span data-stu-id="e6206-105">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="net-framework-applications"></a><span data-ttu-id="e6206-106">Приложения .NET framework</span><span class="sxs-lookup"><span data-stu-id="e6206-106">.NET Framework Applications</span></span>

<span data-ttu-id="e6206-107">Приложения .NET framework, такие как WinForms, WPF, консоли и ASP.NET 4, будет иметь подключения попытка и проверенный строки.</span><span class="sxs-lookup"><span data-stu-id="e6206-107">.NET Framework applications, such as WinForms, WPF, Console, and ASP.NET 4, have a tried and tested connection string pattern.</span></span> <span data-ttu-id="e6206-108">Строка подключения должна быть добавлена в файл App.config приложения (Web.config при использовании ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="e6206-108">The connection string should be added to your applications App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="e6206-109">Если строка подключения содержит конфиденциальные сведения, например имя пользователя и пароль, можно защитить содержимое файла конфигурации с помощью [защищенной конфигурации](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration).</span><span class="sxs-lookup"><span data-stu-id="e6206-109">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using [Protected Configuration](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration).</span></span>

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]  
> <span data-ttu-id="e6206-110">`providerName` Параметр не является обязательным в строках подключения EF Core хранятся в файле App.config, так как поставщик базы данных настраивается с помощью кода.</span><span class="sxs-lookup"><span data-stu-id="e6206-110">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="e6206-111">Затем можно прочитать строку соединения с помощью `ConfigurationManager` API в его контексте `OnConfiguring` метод.</span><span class="sxs-lookup"><span data-stu-id="e6206-111">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="e6206-112">Может потребоваться добавить ссылку на `System.Configuration` framework сборки, чтобы иметь возможность использовать этот API.</span><span class="sxs-lookup"><span data-stu-id="e6206-112">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="e6206-113">Универсальная платформа Windows (UWP)</span><span class="sxs-lookup"><span data-stu-id="e6206-113">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="e6206-114">Строки подключения в приложении UWP обычно являются SQLite соединение, просто задает имя локального файла.</span><span class="sxs-lookup"><span data-stu-id="e6206-114">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="e6206-115">Обычно они не содержат конфиденциальной информации и не нужно изменить, так как приложение развертывается.</span><span class="sxs-lookup"><span data-stu-id="e6206-115">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="e6206-116">Таким образом эти строки подключения обычно нормально, которое должно остаться в коде, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="e6206-116">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="e6206-117">Если вы хотите переместить их в коде UWP поддерживает концепцию параметров см. в разделе [разделе параметров приложения UWP](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data) подробные сведения.</span><span class="sxs-lookup"><span data-stu-id="e6206-117">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
            optionsBuilder.UseSqlite("Data Source=blogging.db");
    }
}
```

## <a name="aspnet-core"></a><span data-ttu-id="e6206-118">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6206-118">ASP.NET Core</span></span>

<span data-ttu-id="e6206-119">В ASP.NET Core системы конфигурации является более гибким и строки подключения могут храниться в `appsettings.json`, переменную среды, хранилище секрета пользователя или другой источник конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e6206-119">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="e6206-120">В разделе [раздела конфигурации ASP.NET Core документации](https://docs.asp.net/en/latest/fundamentals/configuration.html) для получения дополнительных сведений.</span><span class="sxs-lookup"><span data-stu-id="e6206-120">See the [Configuration section of the ASP.NET Core documentation](https://docs.asp.net/en/latest/fundamentals/configuration.html) for more details.</span></span> <span data-ttu-id="e6206-121">В следующем примере показано строку подключения, сохраненную в `appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="e6206-121">The following example shows the connection string stored in `appsettings.json`.</span></span>

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="e6206-122">Контекст обычно настраивается в `Startup.cs` со строкой соединения, считываемое из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e6206-122">The context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="e6206-123">Примечание `GetConnectionString()` метод ищет значение конфигурации, ключ которого является `ConnectionStrings:<connection string name>`.</span><span class="sxs-lookup"><span data-stu-id="e6206-123">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```