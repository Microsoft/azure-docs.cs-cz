---
title: Vytvoření webové aplikace v ASP.NET Core s využitím Azure cache pro Redis
description: V tomto rychlém startu se dozvíte, jak vytvořit ASP.NET Core webovou aplikaci s využitím Azure cache pro Redis.
author: brendanzagaeski
ms.author: brzaga
ms.service: cache
ms.devlang: dotnet
ms.custom: devx-track-csharp, mvc
ms.topic: quickstart
ms.date: 03/31/2021
ms.openlocfilehash: 19c346bd3bdc0a5882244ff595bfeedbde8c06e4
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123607"
---
# <a name="quickstart-use-azure-cache-for-redis-with-an-aspnet-core-web-app"></a>Rychlý Start: použití mezipaměti Azure pro Redis s webovou aplikací ASP.NET Core 

V tomto rychlém startu zahrňte Azure cache pro Redis do webové aplikace ASP.NET Core, která se připojuje ke službě Azure cache pro Redis pro ukládání a načítání dat z mezipaměti.

## <a name="skip-to-the-code-on-github"></a>Přeskočit na kód na GitHubu

Pokud chcete přeskočit přímo na kód, přečtěte si téma [rychlý start ASP.NET Core](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/aspnet-core) na GitHubu.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Sada .NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-a-cache"></a>Vytvoření mezipaměti

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Poznamenejte si **NÁZEV HOSTITELE** a **Primární** přístupový klíč. Tyto hodnoty později použijete k vytvoření tajného kódu *CacheConnection*.

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

Otevřete nové příkazové okno a spuštěním následujícího příkazu vytvořte novou webovou aplikaci ASP.NET Core (Model-View-Controller):

```dotnetcli
dotnet new mvc -o ContosoTeamStats
```

V příkazovém okně přejděte do nového adresáře projektu *ContosoTeamStats* .

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

Spuštěním následujícího příkazu přidejte k projektu balíček *Microsoft.Extensions.Configuration.UserSecrets*:

```dotnetcli
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
```

Spuštěním následujícího příkazu balíčky obnovte:

```dotnetcli
dotnet restore
```

Po nahrazení zástupných znaků (včetně ostrých závorek) názvu mezipaměti a primárního přístupového klíče spusťte v okně příkazové řádku následující příkaz, který uloží nový tajný kód s názvem *CacheConnection*:

```dotnetcli
dotnet user-secrets set CacheConnection "<cache name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<primary-access-key>"
```

## <a name="configure-the-cache-client"></a>Konfigurace klienta mezipaměti

V této části nakonfigurujete aplikaci tak, aby používala klienta [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) pro .NET.

V příkazovém okně spusťte následující příkaz v adresáři projektu *ContosoTeamStats* :

```dotnetcli
dotnet add package StackExchange.Redis
```

Po dokončení instalace budete moct se svým projektem používat klienta mezipaměti *StackExchange.Redis*.

## <a name="update-the-homecontroller-and-layout"></a>Aktualizace souboru HomeController a rozložení

`using`Do *souboru controllers\homecontroller.cs* přidejte následující příkazy:

```csharp
using System.Net.Sockets;
using System.Text;
using System.Threading;

using Microsoft.Extensions.Configuration;

using StackExchange.Redis;
```

Nahraďte:

```csharp
private readonly ILogger<HomeController> _logger;

public HomeController(ILogger<HomeController> logger)
{
    _logger = logger;
}
```

textem:

```csharp
private readonly ILogger<HomeController> _logger;
private static IConfiguration Configuration { get; set; }

public HomeController(ILogger<HomeController> logger, IConfiguration configuration)
{
    _logger = logger;
    if (Configuration == null)
        Configuration = configuration;
}
```

Do třídy přidejte následující členy `HomeController` , aby podporovaly novou `RedisCache` akci, která spustí některé příkazy pro novou mezipaměť.

```csharp
public ActionResult RedisCache()
{
    ViewBag.Message = "A simple example with Azure Cache for Redis on ASP.NET Core.";

    IDatabase cache = GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    ViewBag.command1 = "PING";
    ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

    // Simple get and put of integral data types into the cache
    ViewBag.command2 = "GET Message";
    ViewBag.command2Result = cache.StringGet("Message").ToString();

    ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET Core!\"";
    ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET Core!").ToString();

    // Demonstrate "SET Message" executed as expected...
    ViewBag.command4 = "GET Message";
    ViewBag.command4Result = cache.StringGet("Message").ToString();

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires allowAdmin=true in the connection string
    ViewBag.command5 = "CLIENT LIST";
    StringBuilder sb = new StringBuilder();
    var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
    IServer server = GetServer(endpoint.Host, endpoint.Port);
    ClientInfo[] clients = server.ClientList();

    sb.AppendLine("Cache response :");
    foreach (ClientInfo client in clients)
    {
        sb.AppendLine(client.Raw);
    }

    ViewBag.command5Result = sb.ToString();

    return View();
}

private const string SecretName = "CacheConnection";

private static long lastReconnectTicks = DateTimeOffset.MinValue.UtcTicks;
private static DateTimeOffset firstErrorTime = DateTimeOffset.MinValue;
private static DateTimeOffset previousErrorTime = DateTimeOffset.MinValue;

private static readonly object reconnectLock = new object();

// In general, let StackExchange.Redis handle most reconnects,
// so limit the frequency of how often ForceReconnect() will
// actually reconnect.
public static TimeSpan ReconnectMinFrequency => TimeSpan.FromSeconds(60);

// If errors continue for longer than the below threshold, then the
// multiplexer seems to not be reconnecting, so ForceReconnect() will
// re-create the multiplexer.
public static TimeSpan ReconnectErrorThreshold => TimeSpan.FromSeconds(30);

public static int RetryMaxAttempts => 5;

private static Lazy<ConnectionMultiplexer> lazyConnection = CreateConnection();

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}

private static Lazy<ConnectionMultiplexer> CreateConnection()
{
    return new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = Configuration[SecretName];
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
}

private static void CloseConnection(Lazy<ConnectionMultiplexer> oldConnection)
{
    if (oldConnection == null)
        return;

    try
    {
        oldConnection.Value.Close();
    }
    catch (Exception)
    {
        // Example error condition: if accessing oldConnection.Value causes a connection attempt and that fails.
    }
}

/// <summary>
/// Force a new ConnectionMultiplexer to be created.
/// NOTES:
///     1. Users of the ConnectionMultiplexer MUST handle ObjectDisposedExceptions, which can now happen as a result of calling ForceReconnect().
///     2. Don't call ForceReconnect for Timeouts, just for RedisConnectionExceptions or SocketExceptions.
///     3. Call this method every time you see a connection exception. The code will:
///         a. wait to reconnect for at least the "ReconnectErrorThreshold" time of repeated errors before actually reconnecting
///         b. not reconnect more frequently than configured in "ReconnectMinFrequency"
/// </summary>
public static void ForceReconnect()
{
    var utcNow = DateTimeOffset.UtcNow;
    long previousTicks = Interlocked.Read(ref lastReconnectTicks);
    var previousReconnectTime = new DateTimeOffset(previousTicks, TimeSpan.Zero);
    TimeSpan elapsedSinceLastReconnect = utcNow - previousReconnectTime;

    // If multiple threads call ForceReconnect at the same time, we only want to honor one of them.
    if (elapsedSinceLastReconnect < ReconnectMinFrequency)
        return;

    lock (reconnectLock)
    {
        utcNow = DateTimeOffset.UtcNow;
        elapsedSinceLastReconnect = utcNow - previousReconnectTime;

        if (firstErrorTime == DateTimeOffset.MinValue)
        {
            // We haven't seen an error since last reconnect, so set initial values.
            firstErrorTime = utcNow;
            previousErrorTime = utcNow;
            return;
        }

        if (elapsedSinceLastReconnect < ReconnectMinFrequency)
            return; // Some other thread made it through the check and the lock, so nothing to do.

        TimeSpan elapsedSinceFirstError = utcNow - firstErrorTime;
        TimeSpan elapsedSinceMostRecentError = utcNow - previousErrorTime;

        bool shouldReconnect =
            elapsedSinceFirstError >= ReconnectErrorThreshold // Make sure we gave the multiplexer enough time to reconnect on its own if it could.
            && elapsedSinceMostRecentError <= ReconnectErrorThreshold; // Make sure we aren't working on stale data (e.g. if there was a gap in errors, don't reconnect yet).

        // Update the previousErrorTime timestamp to be now (e.g. this reconnect request).
        previousErrorTime = utcNow;

        if (!shouldReconnect)
            return;

        firstErrorTime = DateTimeOffset.MinValue;
        previousErrorTime = DateTimeOffset.MinValue;

        Lazy<ConnectionMultiplexer> oldConnection = lazyConnection;
        CloseConnection(oldConnection);
        lazyConnection = CreateConnection();
        Interlocked.Exchange(ref lastReconnectTicks, utcNow.UtcTicks);
    }
}

// In real applications, consider using a framework such as
// Polly to make it easier to customize the retry approach.
private static T BasicRetry<T>(Func<T> func)
{
    int reconnectRetry = 0;
    int disposedRetry = 0;

    while (true)
    {
        try
        {
            return func();
        }
        catch (Exception ex) when (ex is RedisConnectionException || ex is SocketException)
        {
            reconnectRetry++;
            if (reconnectRetry > RetryMaxAttempts)
                throw;
            ForceReconnect();
        }
        catch (ObjectDisposedException)
        {
            disposedRetry++;
            if (disposedRetry > RetryMaxAttempts)
                throw;
        }
    }
}

public static IDatabase GetDatabase()
{
    return BasicRetry(() => Connection.GetDatabase());
}

public static System.Net.EndPoint[] GetEndPoints()
{
    return BasicRetry(() => Connection.GetEndPoints());
}

public static IServer GetServer(string host, int port)
{
    return BasicRetry(() => Connection.GetServer(host, port));
}
```

Otevřete *Views\Shared \\ _Layout. cshtml*.

Nahraďte:

```cshtml
<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">ContosoTeamStats</a>
```

textem:

```cshtml
<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="RedisCache">Azure Cache for Redis Test</a>
```

## <a name="add-a-new-rediscache-view-and-update-the-styles"></a>Přidat nové zobrazení RedisCache a aktualizovat styly

Vytvořte nový soubor *Views\Home\RedisCache.cshtml* s následujícím obsahem:

```cshtml
@{
    ViewBag.Title = "Azure Cache for Redis Test";
}

<h2>@ViewBag.Title.</h2>
<h3>@ViewBag.Message</h3>
<br /><br />
<table border="1" cellpadding="10" class="redis-results">
    <tr>
        <th>Command</th>
        <th>Result</th>
    </tr>
    <tr>
        <td>@ViewBag.command1</td>
        <td><pre>@ViewBag.command1Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command2</td>
        <td><pre>@ViewBag.command2Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command3</td>
        <td><pre>@ViewBag.command3Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command4</td>
        <td><pre>@ViewBag.command4Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command5</td>
        <td><pre>@ViewBag.command5Result</pre></td>
    </tr>
</table>
```

Přidejte následující řádky do *wwwroot\css\site.CSS*:

```css
.redis-results pre {
  white-space: pre-wrap;
}
```

## <a name="run-the-app-locally"></a>Místní spuštění aplikace

Vytvořte aplikaci spuštěním následujícího příkazu v okně příkazového řádku:

```dotnetcli
dotnet build
```

Potom aplikaci tímto příkazem spusťte:

```dotnetcli
dotnet run
```

`https://localhost:5001`Ve webovém prohlížeči přejděte na.

Vyberte možnost **Azure cache for Redis test** v navigačním panelu webové stránky a otestujte přístup k mezipaměti.

V níže uvedeném příkladu můžete vidět, že klíč `Message` měl předtím hodnotu z mezipaměti, která byla nastavena pomocí konzoly Redis na webu Azure Portal. Aplikace tuto hodnotu z mezipaměti aktualizovala. Aplikace rovněž spustila příkazy `PING` a `CLIENT LIST`.

![Jednoduchý test dokončený místně](./media/cache-web-app-aspnet-core-howto/cache-simple-test-complete-local.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete prostředky vytvořené v tomto rychlém startu zachovat a znovu je použít.

V opačném případě, pokud jste už s ukázkovou aplikací v tomto rychlém startu skončili, můžete prostředky Azure vytvořené v tomto rychlém startu odstranit, abyste se vyhnuli poplatkům.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Při odstranění skupiny prostředků se všechny prostředky, které obsahuje, trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.

### <a name="to-delete-a-resource-group"></a>Odstranění skupiny prostředků

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

2. Do pole **Filtrovat podle názvu** zadejte název vaší skupiny prostředků. V pokynech v tomto článku se používala skupina prostředků *TestResources*. U skupiny prostředků ve výsledcích hledání vyberte **...** a pak vyberte **Odstranit skupinu prostředků**.

    ![Odstranit](./media/cache-web-app-howto/cache-delete-resource-group.png)

Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění zadáním názvu vaší skupiny prostředků a vyberte **Odstranit**.

Po chvíli se skupina prostředků včetně všech prostředků, které obsahuje, odstraní.

## <a name="next-steps"></a>Další kroky

Informace o nasazení do Azure najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření aplikace ASP.NET Core a SQL Database v Azure App Service](/azure/app-service/tutorial-dotnetcore-sqldb-app)

Informace o ukládání tajného klíče připojení k mezipaměti v Azure Key Vault najdete v tématech:

> [!div class="nextstepaction"]
> [Poskytovatel konfigurace Azure Key Vault v ASP.NET Core](/aspnet/core/security/key-vault-configuration)

Chcete škálovat mezipaměť z nižší úrovně na vyšší úroveň?

> [!div class="nextstepaction"]
> [Jak škálovat Azure cache pro Redis](./cache-how-to-scale.md)

Chcete optimalizovat a uložit své útraty do cloudu?

> [!div class="nextstepaction"]
> [Zahájení analýzy nákladů pomocí Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
