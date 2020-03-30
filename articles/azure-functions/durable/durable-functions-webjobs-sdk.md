---
title: Jak spustit trvanlivé funkce jako webjobs - Azure
description: Naučte se kódovat a konfigurovat trvalé funkce pro spuštění v webjobs pomocí sady WebJobs SDK.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232742"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Jak spustit trvanlivé funkce jako webjobs

Ve výchozím nastavení trvalé funkce používá runtime Funkce Azure k hostování orchestrations. Však může být určité scénáře, kde budete potřebovat větší kontrolu nad kód, který naslouchá událostem. Tento článek ukazuje, jak implementovat orchestraci pomocí sady WebJobs SDK. Podrobnější porovnání funkcí a úloh webových úloh naleznete v [tématu Porovnání funkcí a úloh webových úloh](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Funkce Azure](../functions-overview.md) a rozšíření [Durable Functions](durable-functions-overview.md) jsou postavené na [sada WebJobs SDK](../../app-service/webjobs-sdk-how-to.md). Hostitel úloh v sada WebJobs SDK je runtime v Azure Functions. Pokud potřebujete řídit chování způsoby, které nejsou možné v Azure Functions, můžete vyvíjet a spouštět trvalé funkce pomocí WebJobs SDK sami.

Ve verzi 3.x sady WebJobs SDK je `IHost`hostitelem implementace aplikace a ve `JobHost` verzi 2.x použijete objekt.

Ukázka zřetězení trvalých funkcí je k dispozici ve verzi WebJobs SDK 2.x: stáhnout nebo klonovat [úložiště durable functions](https://github.com/azure/azure-functions-durable-extension/)a přejít do ukázkové *\\složky webjobssdk\\řetězení.*

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste obeznámeni se základy WebJobs SDK, C# vývoj knihovny třídy pro funkce Azure a trvalé funkce. Pokud potřebujete úvod k těmto tématům, podívejte se na následující zdroje:

* [Začínáme se sadou WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Vytvoření první funkce pomocí sady Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Odolná služba Functions](durable-functions-sequence.md)

Postup v tomto článku:

* [Nainstalujte Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) s **úlohou vývoje Azure.**

  Pokud už visual studio máte, ale nemáte tuto úlohu, přidejte úlohu výběrem **nástroje** > **získat nástroje a funkce**.

  (Místo toho můžete použít [visual studio kód,](https://code.visualstudio.com/) ale některé pokyny jsou specifické pro Visual Studio.)

* Nainstalujte a spusťte [emulátor úložiště Azure](../../storage/common/storage-use-emulator.md) verze 5.2 nebo novější. Alternativou je aktualizace souboru *App.config* s připojovacířetězec Azure Storage.

## <a name="webjobs-sdk-versions"></a>Verze sady WebJobs SDK

Tento článek vysvětluje, jak vyvinout projekt WebJobs SDK 2.x (ekvivalentní azure functions verze 1.x). Informace o verzi 3.x naleznete v [tématu WebJobs SDK 3.x](#webjobs-sdk-3x) dále v tomto článku.

## <a name="create-a-console-app"></a>Vytvoření konzolové aplikace

Chcete-li spustit trvalé funkce jako webové úlohy, musíte nejprve vytvořit konzolovou aplikaci. Projekt sady WebJobs SDK je pouze projekt konzolové aplikace s nainstalovanými příslušnými balíčky NuGet.

V dialogovém okně **Nový projekt** sady Visual Studio vyberte **aplikaci Klasické konzoly pro stolní počítače** > **(.NET Framework) systému**Windows Classic . V souboru projektu `TargetFrameworkVersion` by `v4.6.1`měl být .

Visual Studio má také šablonu projektu WebJob, kterou můžete použít výběrem **Cloud** > **Azure WebJob (.NET Framework).** Tato šablona nainstaluje mnoho balíčků, z nichž některé možná nebudete potřebovat.

## <a name="install-nuget-packages"></a>Instalace balíčků NuGet

Potřebujete balíčky NuGet pro webjobs SDK, základní vazby, architekturu protokolování a rozšíření trvalé úlohy. Zde jsou **příkazy konzoly Správce balíčků** pro tyto balíčky s nejnovějšími stabilními čísly verzí k datu, kdy byl tento článek napsán:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Potřebujete také poskytovatele protokolování. Následující příkazy nainstalují zprostředkovatele Azure `ConfigurationManager`Application Insights a . Umožňuje `ConfigurationManager` získat klíč instrumentace Application Insights z nastavení aplikace.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Následující příkaz nainstaluje zprostředkovatele konzoly:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Kód JobHost

Po vytvoření konzolové aplikace a instalaci balíčků NuGet, které potřebujete, jste připraveni k použití trvanlivé funkce. To uděláte pomocí kódu JobHost.

Chcete-li použít rozšíření `UseDurableTask` Durable `JobHostConfiguration` Functions, `Main` volání objektu v metodě:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Seznam vlastností, které můžete nastavit `DurableTaskExtension` v objektu, naleznete v [tématu host.json](../functions-host-json.md#durabletask).

Metoda `Main` je také místem pro nastavení zprostředkovatelů protokolování. Následující příklad konfiguruje konzolu a zprostředkovatele Application Insights.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Funkce

Trvalé funkce v kontextu WebJobs se poněkud liší od trvalé funkce v kontextu Funkce Azure. Je důležité být si vědom rozdílů při psaní kódu.

Sada WebJobs SDK nepodporuje následující funkce Azure Functions:

* [FunctionName, atribut](#functionname-attribute)
* [Aktivační událost PROTOKOLU HTTP](#http-trigger)
* [Trvalé funkce ROZHRANÍ API pro správu HTTP](#http-management-api)

### <a name="functionname-attribute"></a>FunctionName, atribut

V projektu Sady SDK webjobs je název metody funkce název funkce. Atribut `FunctionName` se používá pouze v Azure Functions.

### <a name="http-trigger"></a>Trigger HTTP

Sada WebJobs SDK nemá aktivační událost HTTP. Ukázkový projekt orchestrace klienta používá aktivační událost časovače:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>Rozhraní API pro správu PROTOKOLU HTTP

Vzhledem k tomu, že nemá žádnou aktivační událost HTTP, sada WebJobs SDK nemá žádné [rozhraní API pro správu protokolu HTTP](durable-functions-http-api.md).

V projektu WebJobs SDK můžete volat metody na objektu klienta orchestrace, namísto odesílání požadavků HTTP. Následující metody odpovídají třem úlohám, které můžete provést s rozhraním API pro správu protokolu HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Funkce klienta orchestrace v ukázkovém projektu spustí funkci orchestrator `GetStatusAsync` a pak přejde do smyčky, která volá každé 2 sekundy:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Spuštění ukázky

Máte trvalé funkce nastavené pro spuštění jako webjob a teď máte pochopení toho, jak se to bude lišit od spuštění trvanlivé funkce jako samostatné funkce Azure. V tomto okamžiku může být užitečné vidět, jak funguje v ukázce.

Tato část obsahuje přehled o spuštění [ukázkového projektu](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Podrobné pokyny, které vysvětlují, jak spustit projekt WebJobs SDK místně a nasadit ho do Azure WebJob, najdete v článku [Začínáme s webjobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Spuštění v místním prostředí

1. Ujistěte se, že je spuštěn emulátor úložiště (viz [Požadavky](#prerequisites)).

1. Pokud chcete zobrazit protokoly v Application Insights při spuštění projektu místně:

    a. Vytvořte prostředek Application Insights a použijte pro něj typ **obecné** aplikace.

    b. Uložte klíč instrumentace do souboru *App.config.*

1. Spusťte projekt.

### <a name="run-in-azure"></a>Spuštění v Azure

1. Vytvořte webovou aplikaci a účet úložiště.

1. Ve webové aplikaci uložte připojovací `AzureWebJobsStorage`řetězec úložiště do nastavení aplikace s názvem .

1. Vytvořte prostředek Application Insights a použijte pro něj typ **obecné** aplikace.

1. Uložte klíč instrumentace v `APPINSIGHTS_INSTRUMENTATIONKEY`nastavení aplikace s názvem .

1. Nasazení jako webová úloha.

## <a name="webjobs-sdk-3x"></a>WebJobs Sada SDK 3.x

Tento článek vysvětluje, jak vytvořit projekt WebJobs SDK 2.x. Pokud vyvíjíte projekt [WebJobs SDK 3.x,](../../app-service/webjobs-sdk-get-started.md) tato část vám pomůže pochopit rozdíly.

Hlavní zavedenou změnou je použití rozhraní .NET Core namísto rozhraní .NET Framework. Chcete-li vytvořit projekt WebJobs SDK 3.x, pokyny jsou stejné, s těmito výjimkami:

1. Vytvořte konzolovou aplikaci .NET Core. V dialogovém okně **Nový projekt** sady Visual Studio vyberte **položku .NET Core** > **Console App (.NET Core).** Soubor projektu určuje, `TargetFramework` `netcoreapp2.x`že je .

1. Zvolte verzi webjobs SDK 3.x následujících balíčků:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Pomocí konfiguračního rozhraní .NET Core nastavte připojovací řetězec úložiště a klíč Instrumentace Application Insights v souboru *appsettings.json.* Tady je příklad:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Změňte `Main` kód metody, který to provést. Tady je příklad:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Další kroky

Další informace o sadě WebJobs SDK naleznete v [tématu Jak používat sadu WebJobs SDK](../../app-service/webjobs-sdk-how-to.md).
