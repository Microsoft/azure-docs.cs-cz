---
title: Jak spustit trvanlivý funguje jako WebJobs – Azure
description: Zjistěte, jak kód a konfigurace funkce trvanlivé spouštění v webové úlohy pomocí WebJobs SDK.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 3fc84d1492d2855ffa3bb5538226da049a928339
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33767319"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Jak spustit trvanlivý funguje jako webové úlohy

[Azure Functions](functions-overview.md) a [trvanlivý funkce](durable-functions-overview.md) rozšíření jsou postaveny na [WebJobs SDK](../app-service/web-sites-create-web-jobs.md). `JobHost` V WebJobs SDK je modul runtime v Azure Functions. Pokud je potřeba řízení `JobHost` chování způsoby není možné v Azure Functions, můžete vyvíjet a spustit trvanlivý funkce pomocí WebJobs SDK sami. Potom můžete spustit trvanlivý funkcí v webové úlohy Azure nebo kdekoli konzolovou aplikaci spustí.

Řetězení trvanlivý funkce ukázka je dostupná ve verzi WebJobs SDK: stažení nebo klonovat [trvanlivý funkce úložiště](https://github.com/azure/azure-functions-durable-extension/) a přejděte do *ukázky\\webjobssdk\\řetězení* složky.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste obeznámení se základy WebJobs SDK, C# – třída knihovny vývoji funkce Azure a trvanlivé funkce. Pokud potřebujete úvodní informace o těchto tématech, najdete v následujících materiálech:

* [Začínáme s WebJobs SDK](../app-service/webjobs-sdk-get-started.md)
* [Vytvoření první funkce pomocí sady Visual Studio](functions-create-your-first-function-visual-studio.md)
* [Trvanlivý funkce](durable-functions-sequence.md)

Pokud chcete provést kroky v tomto článku:

* [Nainstalovat Visual Studio 2017 verze 15.6 nebo novější](https://docs.microsoft.com/visualstudio/install/) s **Azure development** zatížení.

  Pokud už máte Visual Studio, ale nemáte této úlohy, přidejte zatížení výběrem **nástroje > funkcí a nástrojů pro získání**. 

  (Můžete použít [Visual Studio Code](https://code.visualstudio.com/) místo, ale některé pokyny jsou specifické pro Visual Studio.)

* Nainstalujte a spusťte [emulátoru úložiště Azure](../storage/common/storage-use-emulator.md) verze 5.2 nebo novější. Alternativou je aktualizovat *App.config* soubor s řetězcem připojení Azure Storage.

## <a name="webjobs-sdk-versions"></a>Verze sady SDK webové úlohy

Tento článek vysvětluje, jak vyvíjet projekt 2.x WebJobs SDK (ekvivalentní na Azure Functions verzi 1.x). Informace o verzi 3.x, najdete v části [WebJobs SDK 3.x](#webjobs-sdk-3x) dále v tomto článku. 

## <a name="create-console-app"></a>Vytvoření konzolové aplikace

Projekt WebJobs SDK je právě projekt aplikace konzoly se příslušné nainstalované balíčky NuGet.

V sadě Visual Studio **nový projekt** dialogovém okně, vyberte **klasický desktopový Windows > konzolovou aplikaci (rozhraní .NET Framework)**. V souboru projektu `TargetFrameworkVersion` by měla být `v4.6.1`.

Visual Studio také obsahuje šablony projektu webové úlohy, které můžete použít tak, že vyberete **cloudu > webové úlohy Azure (rozhraní .NET Framework)**. Tato šablona nainstaluje mnoho balíčky, z nichž některé nemusí potřebovat.

## <a name="install-nuget-packages"></a>Instalace balíčků NuGet

Balíčky NuGet nutnost WebJobs SDK, základní vazby, rozhraní protokolování a rozšíření trvanlivý úloh. Tady jsou **Konzola správce balíčků** příkazy pro tyto balíčky s nejnovější stabilní verze čísla k datu byla zapsána v tomto článku:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Musíte také poskytovatelé protokolování. Následující příkazy nainstalovat poskytovatele služby Application Insights a `ConfigurationManager`. `ConfigurationManager` Umožňuje získat klíč instrumentace Application Insights z nastavení aplikace.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Tento příkaz nainstaluje poskytovatele konzoly:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost kódu

Použití rozšíření trvanlivý funkce, volání `UseDurableTask` na `JobHostConfiguration` objekt v vaší `Main` metoda:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Seznam vlastností, které můžete nastavit v `DurableTaskExtension` objektu, najdete v části [host.json](functions-host-json.md#durabletask).

`Main` Metoda je také místní nastavení protokolování zprostředkovatele. Následující příklad konfiguruje konzoly a poskytovatelé Application Insights.

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

Existuje několik rozdílů v kód napsaný pro porovnání funkcí WebJobs SDK k zápisu pro službu Azure Functions.

Sada WebJobs SDK nepodporuje následující funkce Azure Functions:

* [Atribut %{FunctionName/](#functionname-attribute)
* [Aktivace protokolu HTTP](#http-trigger)
* [Trvanlivý HTTP funkce rozhraní API pro správu](#http-management-api)

### <a name="functionname-attribute"></a>Atribut %{FunctionName/

Název metody funkce v projektu WebJobs SDK je název funkce. `FunctionName` Atribut se používá jenom v Azure Functions.

### <a name="http-trigger"></a>Trigger HTTP

Sada WebJobs SDK nemá aktivační procedury protokolu HTTP. Ukázkový projekt orchestration klient používá aktivaci časovačem:

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

Protože nemá žádné triggeru protokolu HTTP, WebJobs SDK nemá [rozhraní API pro správu HTTP](durable-functions-http-api.md).

V projektu WebJobs SDK můžete volat metody pro objekt klienta orchestration místo odesílání požadavků HTTP. Následující metody odpovídají tři úlohy, které můžete provést pomocí rozhraní API pro správu protokolu HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Funkce klienta orchestration v projektu vzorku spustí funkci orchestrator a pak přejde do smyčku, která volá `GetStatusAsync` každé 2 sekundy:

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

Tato část obsahuje přehled o tom, jak spustit [ukázkový projekt](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Podrobné pokyny, které popisují, jak spustit projekt WebJobs SDK místně a nasadíte ho do webové úlohy Azure najdete v tématu [začít pracovat s WebJobs SDK](../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Spuštění v místním prostředí

1. Zkontrolujte, že je spuštěný emulátor úložiště (viz [požadavky](#prerequisites)).

1. Pokud chcete zobrazit protokoly ve službě Application Insights při spuštění místně:

  a. Vytvořte prostředek Application Insights, typ aplikace **Obecné**.

  b. Uložit klíč instrumentace do *App.config* souboru.

1. Spusťte projekt.

### <a name="run-in-azure"></a>Spustit v Azure

1. Vytvoření webové aplikace a účet úložiště.

1. Ve webové aplikaci uložte připojovací řetězec úložiště v nastavení aplikace s názvem AzureWebJobsStorage.

1. Vytvořte prostředek Application Insights, typ aplikace **Obecné**.

1. Klíč instrumentace uložte v nastavení aplikace s názvem APPINSIGHTS_INSTRUMENTATIONKEY.

1. Nasaďte jako webovou úlohu.

## <a name="webjobs-sdk-3x"></a>Sada WebJobs SDK 3.x

Hlavní změna zaváděné 3.x je použití .NET Core místo rozhraní .NET Framework. Vytvoření projektu 3.x tyto pokyny jsou stejné, s těmito výjimkami:

1. Vytvoření aplikace konzoly .NET Core. V sadě Visual Studio **nový projekt** dialogovém okně, vyberte **.NET Core > konzolové aplikace (.NET Core)**. Soubor projektu určuje, že `TargetFramework` je `netcoreapp2.0`.

1. Vyberte tuto předprodejní verzi 3.x následujících balíčků:

  * `Microsoft.Azure.WebJobs.Extensions`
  * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

2. Změna `Main` metoda kód slouží k získání připojovacího řetězce úložiště a klíč instrumentace Application Insights z *appSettings.JSON určený* souboru, pomocí rozhraní .NET Core konfigurace.  Tady je příklad:

   ```cs
   static void Main(string[] args)
   {
       var builder = new ConfigurationBuilder()
           .SetBasePath(Directory.GetCurrentDirectory())
           .AddJsonFile("appsettings.json");

       var appSettingsConfig = builder.Build();

       using (var loggerFactory = new LoggerFactory())
       {
           var config = new JobHostConfiguration();

           config.DashboardConnectionString = "";
           config.StorageConnectionString = 
               appSettingsConfig.GetConnectionString("AzureWebJobsStorage");
           var instrumentationKey =
               appSettingsConfig["APPINSIGHTS_INSTRUMENTATIONKEY"];

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

## <a name="next-steps"></a>Další postup

Další informace o WebJobs SDK najdete v tématu [jak používat sadu WebJobs SDK](../app-service/webjobs-sdk-how-to.md).

