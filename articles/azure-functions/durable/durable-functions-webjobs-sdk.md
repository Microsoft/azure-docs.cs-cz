---
title: Jak spustit Durable Functions jako WebJobs – Azure
description: Naučte se, jak kódovat a nakonfigurovat Durable Functions pro spouštění ve službě WebJobs pomocí sady WebJobs SDK.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 826e475eb71563b52d687903aeac4ec936e267f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009496"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Jak spustit Durable Functions jako WebJobs

Ve výchozím nastavení používá Durable Functions k hostování orchestrací modul Azure Functions runtime. Nicméně mohou nastat určité scénáře, kdy potřebujete větší kontrolu nad kódem, který naslouchá událostem. V tomto článku se dozvíte, jak implementovat orchestraci pomocí sady WebJobs SDK. Podrobnější porovnání mezi funkcemi a úlohami WebJobs najdete v tématu [porovnání funkcí a WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Azure Functions](../functions-overview.md) a rozšíření [Durable Functions](durable-functions-overview.md) jsou postavená na sadě [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md). Hostitel úlohy v sadě WebJobs SDK je modul runtime v Azure Functions. Pokud potřebujete řídit chování způsobem, který není možné v Azure Functions, můžete vyvíjet a spouštět Durable Functions pomocí sady WebJobs SDK sami.

Ve verzi 3. x sady WebJobs SDK je hostitel implementací `IHost` a ve verzi 2. x použijete `JobHost` objekt.

Ukázka řetězení Durable Functions je k dispozici v sadě WebJobs SDK 2. x: Stáhněte nebo naklonujte [úložiště Durable Functions](https://github.com/azure/azure-functions-durable-extension/)a zarezervujte větev *v1* a pokračujte na složku *Samples \\ webjobssdk \\ Chaining* .

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že jste obeznámeni se základy sady WebJobs SDK, vývojem knihovny tříd C# pro Azure Functions a Durable Functions. Pokud potřebujete Úvod do těchto témat, přečtěte si následující zdroje informací:

* [Začínáme se sadou WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Vytvoření první funkce pomocí sady Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

Postup dokončení kroků v tomto článku:

* [Nainstalujte Visual Studio 2019](/visualstudio/install/) s úlohou **vývoj pro Azure** .

  Pokud již máte aplikaci Visual Studio, ale nemáte tuto úlohu, přidejte úlohu výběrem **nástrojů**  >  **získat nástroje a funkce**.

  (Místo toho můžete použít [Visual Studio Code](https://code.visualstudio.com/) , ale některé z pokynů jsou specifické pro sadu Visual Studio.)

* Nainstalujte a spusťte [emulátor Azure Storage](../../storage/common/storage-use-emulator.md) verze 5,2 nebo novější. Alternativou je aktualizace souboru *App.config* s připojovacím řetězcem Azure Storage.

## <a name="webjobs-sdk-versions"></a>Verze sady WebJobs SDK

Tento článek vysvětluje, jak vytvořit projekt WebJobs SDK 2. x (ekvivalent Azure Functions verze 1. x). Informace o verzi 3. x najdete v části [WebJobs SDK 3. x](#webjobs-sdk-3x) dále v tomto článku.

## <a name="create-a-console-app"></a>Vytvoření konzolové aplikace

Pokud chcete spustit Durable Functions jako WebJobs, musíte nejdřív vytvořit konzolovou aplikaci. Projekt sady WebJobs SDK je pouze projekt konzolové aplikace s nainstalovanými příslušnými balíčky NuGet.

V dialogovém okně **Nový projekt** sady Visual Studio vyberte možnost **Windows Classic Desktop**  >  **Konzolová aplikace (.NET Framework)**. V souboru projektu `TargetFrameworkVersion` by měl být `v4.6.1` .

Visual Studio má také šablonu projektu WebJob, kterou můžete použít výběrem **cloudová**  >  **Webová úloha Azure (.NET Framework)**. Tato šablona nainstaluje mnoho balíčků, z nichž některé možná nebudete potřebovat.

## <a name="install-nuget-packages"></a>Instalace balíčků NuGet

Balíčky NuGet budete potřebovat pro sadu WebJobs SDK, základní vazby, protokolovací rozhraní a rozšíření odolného úkolu. Tady jsou příkazy **konzoly Správce balíčků** pro tyto balíčky s nejnovějšími čísly stabilní verze ode dne, kdy byl tento článek napsán:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Také potřebujete poskytovatele protokolování. Následující příkazy instalují poskytovatele služby Azure Application Insights a `ConfigurationManager` . `ConfigurationManager`Umožňuje získat Application Insights klíč instrumentace z nastavení aplikace.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Následující příkaz nainstaluje poskytovatele konzoly:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost kód

Když jste vytvořili konzolovou aplikaci a nainstalovali balíčky NuGet, které potřebujete, budete připraveni použít Durable Functions. Uděláte to tak, že použijete JobHost kód.

Chcete-li použít rozšíření Durable Functions, zavolejte `UseDurableTask` na `JobHostConfiguration` objekt v `Main` metodě:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Seznam vlastností, které lze nastavit v `DurableTaskExtension` objektu, naleznete v tématu [host.json](../functions-host-json.md#durabletask).

`Main`Metoda je také místem pro nastavení zprostředkovatelů protokolování. Následující příklad konfiguruje konzolu a poskytovatele Application Insights.

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

## <a name="functions"></a>Functions

Durable Functions v kontextu WebJobs se trochu liší od Durable Functions v kontextu Azure Functions. Je důležité vědět o rozdílech při psaní kódu.

Sada WebJobs SDK nepodporuje následující funkce Azure Functions:

* [Atribut Function](#functionname-attribute)
* [Trigger HTTP](#http-trigger)
* [Rozhraní API pro správu Durable Functions HTTP](#http-management-api)

### <a name="functionname-attribute"></a>Atribut Function

V projektu sady WebJobs SDK je název metody funkce název funkce. `FunctionName`Atribut se používá pouze v Azure Functions.

### <a name="http-trigger"></a>HTTP trigger

Sada WebJobs SDK nemá Trigger HTTP. Klient orchestrace ukázkového projektu používá aktivační událost časovače:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>Rozhraní API pro správu HTTP

Protože nemá žádnou Trigger HTTP, Sada WebJobs SDK nemá žádné [rozhraní API pro správu http](durable-functions-http-api.md).

V projektu sady WebJobs SDK můžete volat metody v objektu klienta Orchestration místo odesláním požadavků HTTP. Následující metody odpovídají třem úlohám, které můžete provádět pomocí rozhraní API pro správu protokolu HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Funkce klienta Orchestration v ukázkovém projektu spustí funkci Orchestrator a pak přejde do smyčky, která `GetStatusAsync` každé 2 sekundy volá:

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

Máte Durable Functions nastavené tak, aby se spouštěla jako webová úloha, a teď už rozumíte tomu, jak se to bude lišit od spuštění Durable Functions jako samostatné Azure Functions. V tomto okamžiku může být užitečné, když vidíte, že funguje ve vzorku.

Tato část poskytuje přehled o tom, jak spustit [vzorový projekt](https://github.com/Azure/azure-functions-durable-extension/tree/v1/samples/webjobssdk/chaining). Podrobné pokyny, které vysvětlují, jak spustit projekt sady WebJobs v místním počítači a jak ho nasadit do úlohy Azure WebJob, najdete v tématu Začínáme [se sadou WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Spuštění v místním prostředí

1. Ujistěte se, že emulátor úložiště běží (viz [požadavky](#prerequisites)).

1. Pokud chcete zobrazit protokoly v Application Insights při místním spuštění projektu:

    a. Vytvořte prostředek Application Insights a použijte pro něj **obecný** typ aplikace.

    b. Uložte klíč instrumentace do souboru *App.config* .

1. Spusťte projekt.

### <a name="run-in-azure"></a>Spuštění v Azure

1. Vytvořte webovou aplikaci a účet úložiště.

1. Ve webové aplikaci uložte připojovací řetězec úložiště do nastavení aplikace s názvem `AzureWebJobsStorage` .

1. Vytvořte prostředek Application Insights a použijte pro něj **obecný** typ aplikace.

1. Uložte klíč instrumentace do nastavení aplikace s názvem `APPINSIGHTS_INSTRUMENTATIONKEY` .

1. Nasaďte jako webovou úlohu.

## <a name="webjobs-sdk-3x"></a>Sada WebJobs SDK 3. x

Tento článek vysvětluje, jak vytvořit projekt WebJobs SDK 2. x. Pokud vyvíjíte projekt [WebJobs SDK 3. x](../../app-service/webjobs-sdk-get-started.md) , Tato část vám pomůže porozumět rozdílům.

Hlavní zavedená změna je použití .NET Core místo .NET Framework. Chcete-li vytvořit projekt WebJobs SDK 3. x, jsou pokyny stejné, s těmito výjimkami:

1. Vytvořte konzolovou aplikaci .NET Core. V dialogovém okně **Nový projekt** sady Visual Studio vyberte možnost aplikace konzoly **.NET Core**  >  **(.NET Core)**. Soubor projektu určuje, zda `TargetFramework` je `netcoreapp2.x` .

1. Vyberte prodejní verzi WebJobs SDK 3. x z následujících balíčků:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Pomocí konfiguračního rozhraní .NET Core nastavte připojovací řetězec úložiště a Application Insights klíč instrumentace v *appsettings.jsv* souboru. Tady je příklad:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Chcete `Main` -li provést tento postup, změňte kód metody. Tady je příklad:

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

Další informace o sadě WebJobs SDK najdete v článku [Jak používat sadu WebJobs SDK](../../app-service/webjobs-sdk-how-to.md).
