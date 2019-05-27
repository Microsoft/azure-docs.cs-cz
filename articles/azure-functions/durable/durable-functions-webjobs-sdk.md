---
title: Jak spustit Durable Functions jako WebJobs – Azure
description: Zjistěte, jak kód a nakonfigurujte Durable Functions ke spuštění v WebJobs pomocí sady SDK pro WebJobs.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 39a757900b4307d702a0ce0ce1c20694418aa8dd
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872823"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Jak spustit Durable Functions jako WebJobs

Ve výchozím nastavení používá modul runtime Azure Functions na hostiteli Orchestrace Durable Functions. Může však být některé scénáře, kde je nutné mít lepší kontrolu nad kód, který naslouchá událostem. V tomto článku se dozvíte, jak implementovat vaše Orchestrace pomocí sady WebJobs SDK. Další podrobné porovnání mezi službami Functions a WebJobs najdete v tématu [porovnání Functions a WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Služba Azure Functions](../functions-overview.md) a [Durable Functions](durable-functions-overview.md) rozšíření jsou postavené na [sada WebJobs SDK](../../app-service/webjobs-sdk-how-to.md). Hostitel úlohy v sadě WebJobs SDK je modul runtime ve službě Azure Functions. Pokud je potřeba řídit chování způsoby není možné ve službě Azure Functions, můžete vyvíjet a spouštět Durable Functions pomocí sady SDK pro WebJobs sami.

Ve verzi 3.x sada WebJobs SDK, hostitel je implementace `IHost`a ve verzi 2.x použijete `JobHost` objektu.

Řetězení Durable Functions ukázka je k dispozici ve verzi 2.x sady SDK pro WebJobs: Stáhněte nebo naklonujte [Durable Functions úložiště](https://github.com/azure/azure-functions-durable-extension/)a přejděte na *ukázky\\webjobssdk\\řetězení* složky.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste obeznámení se základy sady WebJobs SDK, C# třídy pro vývoj knihoven pro Azure Functions a odolná služba Functions. Pokud potřebujete úvodní informace k těmto tématům, naleznete v následujících zdrojích:

* [Začínáme se sadou WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Vytvoření první funkce pomocí sady Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

K dokončení kroků v tomto článku:

* [Nainstalovat Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) s **vývoj pro Azure** pracovního vytížení.

  Pokud už máte sadu Visual Studio, ale nemají úlohy, přidejte úlohu tak, že vyberete **nástroje** > **stažení nástrojů a funkcí**.

  (Můžete použít [Visual Studio Code](https://code.visualstudio.com/) místo toho ale některé pokyny jsou specifické pro Visual Studio.)

* Nainstalujte a spusťte [emulátoru úložiště Azure](../../storage/common/storage-use-emulator.md) verze 5.2 nebo vyšší. Alternativou je aktualizovat *App.config* soubor pomocí připojovacího řetězce služby Azure Storage.

## <a name="webjobs-sdk-versions"></a>Verze sady WebJobs SDK

Tento článek vysvětluje, jak k vývoji projektu 2.x sady SDK pro WebJobs (ekvivalentní k verzi Azure Functions 1.x). Informace o verzi 3.x, naleznete v tématu [sada WebJobs SDK 3.x](#webjobs-sdk-3x) dále v tomto článku.

## <a name="create-a-console-app"></a>Vytvoření aplikace konzoly

Spustit Durable Functions jako WebJobs, musíte nejprve vytvořit konzolovou aplikaci. Sada WebJobs SDK projektu je právě projekt konzolové aplikace s odpovídající nainstalované balíčky NuGet.

V sadě Visual Studio **nový projekt** dialogu **klasická plocha Windows** > **Konzolová aplikace (.NET Framework)**. V souboru projektu `TargetFrameworkVersion` by měl být `v4.6.1`.

Visual Studio také poskytuje šablony projektu úlohy WebJob, který můžete použít tak, že vyberete **cloudu** > **webová úloha Azure (.NET Framework)**. Tato šablona nainstaluje počtem balíčků, z nichž některé pravděpodobně nebudete potřebovat.

## <a name="install-nuget-packages"></a>Instalace balíčků NuGet

Sada WebJobs SDK, základní vazeb, protokolovacího rozhraní a rozšíření trvalý úlohy musíte balíčky NuGet. Tady jsou **Konzola správce balíčků** příkazy pro tyto balíčky s čísly nejnovější stabilní verze k datu, byla zapsána v tomto článku:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Budete také potřebovat poskytovatelé protokolování. Následujících příkazů nainstalujte zprostředkovatele Azure Application Insights a `ConfigurationManager`. `ConfigurationManager` Umožňuje získat Instrumentační klíč Application Insights z aplikace nastavení.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Následující příkaz nainstaluje poskytovatele konzoly:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost kódu

Vytvoří konzolovou aplikaci a nainstalované balíčky NuGet potřebujete, jste připraveni používat Durable Functions. To provedete pomocí JobHost kódu.

Chcete-li použít rozšíření Durable Functions, zavolejte `UseDurableTask` na `JobHostConfiguration` objektu v vaše `Main` metoda:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Seznam vlastností, které můžete nastavit `DurableTaskExtension` objektu, najdete v článku [host.json](../functions-host-json.md#durabletask).

`Main` Metoda je také místem, kde můžete nastavit protokolování poskytovatelů. Následující příklad nastaví konzoly a poskytovatele Application Insights.

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

Odolná služba Functions v kontextu WebJobs se liší trochu z Durable Functions v rámci služby Azure Functions. Je důležité znát rozdíly při psaní kódu.

Sada WebJobs SDK nepodporuje následující funkce Azure Functions:

* [FunctionName atribut](#functionname-attribute)
* [HTTP trigger](#http-trigger)
* [Odolné funkce protokolu HTTP rozhraní API pro správu](#http-management-api)

### <a name="functionname-attribute"></a>FunctionName atribut

Název metody funkce v projektu sady WebJobs SDK je název funkce. `FunctionName` Atribut se používá jenom v Azure Functions.

### <a name="http-trigger"></a>Trigger HTTP

Sada WebJobs SDK nemá triggeru HTTP. Klient Orchestrace ukázkový projekt využívá trigger časovače:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>Rozhraní API pro správu protokolu HTTP

Protože nemá žádné aktivační události HTTP, sady SDK pro WebJobs nemá žádné [HTTP rozhraní API pro správu](durable-functions-http-api.md).

V projektu sady WebJobs SDK můžete volat metody u objektu klienta Orchestrace, namísto pomocí zasílání požadavků HTTP. Následující metody, které odpovídají na tři úkoly, které vám pomůžou s rozhraním API pro správu protokolu HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Orchestrace klientská funkce v projektu vzorku spuštění funkce orchestrátoru a pak přejde do smyčku, která volá `GetStatusAsync` každé 2 sekundy:

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

Máte Durable Functions nastavení pro spouštění jako webová úloha a nyní máte znalost jak to se liší od spuštění Durable Functions jako samostatné Azure Functions. Zobrazuje, jak v ukázce v tomto okamžiku může být užitečný.

Tato část obsahuje základní informace o tom, jak spustit [ukázkový projekt](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Podrobné pokyny, které vysvětlují, jak místní spuštění projektu sady WebJobs SDK a jak ji nasadit do Azure WebJob najdete v tématu [Začínáme se sadou WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Spuštění v místním prostředí

1. Ujistěte se, že je spuštěný emulátor úložiště (viz [požadavky](#prerequisites)).

1. Pokud chcete zobrazit protokoly ve službě Application Insights při spuštění projektu místně:

    a. Vytvořte prostředek Application Insights a použít **Obecné** typ aplikace pro něj.

    b. Uložte klíč instrumentace do *App.config* souboru.

1. Spusťte projekt.

### <a name="run-in-azure"></a>Spustit v Azure

1. Vytvoření webové aplikace a účet úložiště.

1. Ve webové aplikaci uložit připojovací řetězec úložiště v aplikaci nastavení s názvem `AzureWebJobsStorage`.

1. Vytvořte prostředek Application Insights a použít **Obecné** typ aplikace pro něj.

1. Uložte klíč instrumentace v aplikaci nastavení s názvem `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Nasaďte jako Webjobu.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

Tento článek vysvětluje, jak k vývoji projektu 2.x sady SDK pro WebJobs. Pokud vytváříte [sada WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md) projektu, tato část vám pomůže pochopit rozdíly.

Hlavní změny zavedené je použití .NET Core namísto rozhraní .NET Framework. Vytvoření projektu 3.x sada WebJobs SDK, pokyny jsou stejné, s následujícími výjimkami:

1. Vytvoření konzolové aplikace .NET Core. V sadě Visual Studio **nový projekt** dialogu **.NET Core** > **Konzolová aplikace (.NET Core)**. Soubor projektu určuje, že `TargetFramework` je `netcoreapp2.x`.

1. Vyberte verzi sady SDK pro WebJobs 3.x následujících balíčků:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Nastavte připojovací řetězec úložiště a klíč instrumentace Application Insights do *appsettings.json* soubor pomocí konfigurace architektury .NET Core. Tady je příklad:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Změnit `Main` kódu metody k tomu. Tady je příklad:

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

## <a name="next-steps"></a>Další postup

Další informace o sadě WebJobs SDK najdete v tématu [způsob použití sady WebJobs SDK](../../app-service/webjobs-sdk-how-to.md).
