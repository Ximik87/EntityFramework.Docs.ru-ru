---
title: Устойчивость подключений — EF Core
author: rowanmiller
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: 729cf9b8c038ea2adba8c79c68d9f6fb1676fefa
ms.sourcegitcommit: 5e11125c9b838ce356d673ef5504aec477321724
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50022188"
---
# <a name="connection-resiliency"></a><span data-ttu-id="fdecd-102">Устойчивость подключений</span><span class="sxs-lookup"><span data-stu-id="fdecd-102">Connection Resiliency</span></span>

<span data-ttu-id="fdecd-103">Устойчивость подключения автоматически осуществляет новую попытку команды поврежденной базы данных.</span><span class="sxs-lookup"><span data-stu-id="fdecd-103">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="fdecd-104">Эта функция может использоваться с любой базой данных, указав «стратегия выполнения», который инкапсулирует логику, необходимую для обнаружения сбоев и повторите команды.</span><span class="sxs-lookup"><span data-stu-id="fdecd-104">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="fdecd-105">Поставщики EF Core может предоставлять адаптированные для их условия сбоя конкретной базы данных и политики повтора оптимальной стратегии выполнения.</span><span class="sxs-lookup"><span data-stu-id="fdecd-105">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="fdecd-106">Например поставщик SQL Server включает в себя стратегии выполнения, специально предназначенную для SQL Server (включая SQL Azure).</span><span class="sxs-lookup"><span data-stu-id="fdecd-106">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="fdecd-107">Он учитывает типы исключений, которые могут быть повторены и содержит допустимые значения по умолчанию максимальное число повторных попыток, задержку между повторными попытками и т. д.</span><span class="sxs-lookup"><span data-stu-id="fdecd-107">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="fdecd-108">Стратегия выполнения указывается при настройке параметров для текущего контекста.</span><span class="sxs-lookup"><span data-stu-id="fdecd-108">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="fdecd-109">Это обычно находится в `OnConfiguring` метода производного контекста, или в `Startup.cs` для приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fdecd-109">This is typically in the `OnConfiguring` method of your derived context, or in `Startup.cs` for an ASP.NET Core application.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

## <a name="custom-execution-strategy"></a><span data-ttu-id="fdecd-110">Стратегия выполнения пользовательских</span><span class="sxs-lookup"><span data-stu-id="fdecd-110">Custom execution strategy</span></span>

<span data-ttu-id="fdecd-111">Нет механизма регистрации стратегию выполнения пользовательских своим собственным в том случае, если вы хотите изменить любые параметры по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="fdecd-111">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="fdecd-112">Стратегии выполнения и транзакции</span><span class="sxs-lookup"><span data-stu-id="fdecd-112">Execution strategies and transactions</span></span>

<span data-ttu-id="fdecd-113">Стратегия выполнения, которая автоматически осуществляет новую попытку в случае сбоев необходимо иметь возможность воспроизвести каждой операции в блоке повторных попыток, которое не удается.</span><span class="sxs-lookup"><span data-stu-id="fdecd-113">An execution strategy that automatically retries on failures needs to be able to play back each operation in a retry block that fails.</span></span> <span data-ttu-id="fdecd-114">После включения повторных попыток, каждой операции, выполняемые с помощью EF Core становится отдельной повторяемые операции.</span><span class="sxs-lookup"><span data-stu-id="fdecd-114">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation.</span></span> <span data-ttu-id="fdecd-115">То есть каждый запрос и каждый вызов `SaveChanges()` будет повторена как единое целое, при возникновении временного сбоя.</span><span class="sxs-lookup"><span data-stu-id="fdecd-115">That is, each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="fdecd-116">Тем не менее если код запускает транзакцию с помощью `BeginTransaction()` вы определяете собственную группу операций, которые должны рассматриваться как единое целое, и все содержимое транзакции необходимо воспроизводить должен произойти сбой.</span><span class="sxs-lookup"><span data-stu-id="fdecd-116">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, and everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="fdecd-117">Если попытаться сделать это, при использовании стратегии выполнения, вы получите исключение, как показано ниже:</span><span class="sxs-lookup"><span data-stu-id="fdecd-117">You will receive an exception like the following if you attempt to do this when using an execution strategy:</span></span>

> <span data-ttu-id="fdecd-118">InvalidOperationException: Настроенная стратегия выполнения 'SqlServerRetryingExecutionStrategy' не поддерживает запуск транзакций пользователем.</span><span class="sxs-lookup"><span data-stu-id="fdecd-118">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="fdecd-119">Используйте стратегию выполнения, возвращенную 'DbContext.Database.CreateExecutionStrategy()', чтобы выполнить все операции в транзакции как повторяемую единицу.</span><span class="sxs-lookup"><span data-stu-id="fdecd-119">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="fdecd-120">Решение заключается в том, чтобы вызвать стратегию выполнения с помощью делегата, который представляет все, нужно выполнить вручную.</span><span class="sxs-lookup"><span data-stu-id="fdecd-120">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="fdecd-121">В случае временного сбоя стратегия выполнения будет снова вызывать делегат.</span><span class="sxs-lookup"><span data-stu-id="fdecd-121">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

<span data-ttu-id="fdecd-122">Этот подход можно также использовать с внешней транзакции.</span><span class="sxs-lookup"><span data-stu-id="fdecd-122">This approach can also be used with ambient transactions.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="fdecd-123">Ошибка фиксации транзакции и проблема идемпотентности</span><span class="sxs-lookup"><span data-stu-id="fdecd-123">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="fdecd-124">В общем случае при сбое подключения текущая транзакция откатывается.</span><span class="sxs-lookup"><span data-stu-id="fdecd-124">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="fdecd-125">Тем не менее, если соединение разорвано во время транзакции благосостояние зафиксирована полученный в результате состояние транзакции неизвестно.</span><span class="sxs-lookup"><span data-stu-id="fdecd-125">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span> <span data-ttu-id="fdecd-126">См. в разделе, это [блога](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) для получения дополнительных сведений.</span><span class="sxs-lookup"><span data-stu-id="fdecd-126">See this [blog post](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) for more details.</span></span>

<span data-ttu-id="fdecd-127">По умолчанию, стратегия выполнения будет повторять эту операцию, так как в том случае, если откат транзакции, но если это не так это приведет к исключение, если новое состояние базы данных несовместим или может привести к **повреждения данных** Если Операция не полагаться на определенном состоянии, например при вставке новой строки с помощью автоматически сформированных значений ключа.</span><span class="sxs-lookup"><span data-stu-id="fdecd-127">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="fdecd-128">Существует несколько способов решения этой проблемы.</span><span class="sxs-lookup"><span data-stu-id="fdecd-128">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="fdecd-129">Вариант 1. nothing (почти)</span><span class="sxs-lookup"><span data-stu-id="fdecd-129">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="fdecd-130">Вероятность сбоя подключения во время фиксации транзакции недостаточно, поэтому оно может быть приемлемым для вашего приложения, просто ошибкой, если данное состояние возникает, фактически.</span><span class="sxs-lookup"><span data-stu-id="fdecd-130">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="fdecd-131">Тем не менее необходимо избегать использования сформированные хранилищем ключи, чтобы гарантировать, что исключение вместо добавления повторяющуюся строку.</span><span class="sxs-lookup"><span data-stu-id="fdecd-131">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="fdecd-132">Рекомендуется использовать значение идентификатора GUID, сформированное клиентом или генератор значение на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="fdecd-132">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="fdecd-133">Вариант 2 - состояния приложения перестроения</span><span class="sxs-lookup"><span data-stu-id="fdecd-133">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="fdecd-134">Отменить текущий `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="fdecd-134">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="fdecd-135">Создайте новый `DbContext` и восстановить состояние приложения из базы данных.</span><span class="sxs-lookup"><span data-stu-id="fdecd-135">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="fdecd-136">Информировать пользователей о том, что последняя операция не выполнена успешно.</span><span class="sxs-lookup"><span data-stu-id="fdecd-136">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="fdecd-137">Вариант 3: Добавление проверки состояния</span><span class="sxs-lookup"><span data-stu-id="fdecd-137">Option 3 - Add state verification</span></span>

<span data-ttu-id="fdecd-138">Для большинства операций, которые изменяют состояние базы данных можно добавить код, который проверяет, успешно ли оно выполнено.</span><span class="sxs-lookup"><span data-stu-id="fdecd-138">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="fdecd-139">EF предоставляет метод расширения, чтобы облегчить эту задачу - `IExecutionStrategy.ExecuteInTransaction`.</span><span class="sxs-lookup"><span data-stu-id="fdecd-139">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="fdecd-140">Этот метод начинает и фиксирует транзакцию, а также принимает функцию в `verifySucceeded` параметр, который вызывается, когда возникает временная ошибка во время фиксации транзакции.</span><span class="sxs-lookup"><span data-stu-id="fdecd-140">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="fdecd-141">Здесь `SaveChanges` вызывается с `acceptAllChangesOnSuccess` присвоено `false` во избежание изменения состояния `Blog` сущность `Unchanged` Если `SaveChanges` завершается успешно.</span><span class="sxs-lookup"><span data-stu-id="fdecd-141">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="fdecd-142">Это позволяет повторите ту же операцию, если фиксация завершается сбоем и выполняется откат транзакции.</span><span class="sxs-lookup"><span data-stu-id="fdecd-142">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="fdecd-143">Вариант 4: вручную отслеживания транзакции</span><span class="sxs-lookup"><span data-stu-id="fdecd-143">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="fdecd-144">Если требуется использовать сформированные хранилищем ключи или вам требуется универсальный способ обработки сбоев, не зависящая от операции, выполняемой каждой транзакции можно было назначать идентификатор, который проверяется при фиксация завершается сбоем.</span><span class="sxs-lookup"><span data-stu-id="fdecd-144">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="fdecd-145">Добавление таблицы в базу данных, используемый для отслеживания состояния транзакций.</span><span class="sxs-lookup"><span data-stu-id="fdecd-145">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="fdecd-146">Вставьте строку в таблице в начале каждой транзакции.</span><span class="sxs-lookup"><span data-stu-id="fdecd-146">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="fdecd-147">В случае сбоя подключения во время фиксации, проверьте наличие соответствующей строки в базе данных.</span><span class="sxs-lookup"><span data-stu-id="fdecd-147">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="fdecd-148">Если фиксация выполнена успешно, удалите соответствующей строки во избежание увеличение размера таблицы.</span><span class="sxs-lookup"><span data-stu-id="fdecd-148">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="fdecd-149">Убедитесь, что контекст, используемый для проверки стратегии выполнения определяются как соединение может привести к отказу во время проверки, если не удалось выполнить во время фиксации транзакции.</span><span class="sxs-lookup"><span data-stu-id="fdecd-149">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>
