---
title: Jak spustit odolná služba functions jako WebJobs – Azure
description: Zjistěte, jak kód a nakonfigurujte Durable Functions ke spuštění v WebJobs pomocí sady SDK pro WebJobs.
services: functions
author: ggailey777
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
ms.openlocfilehash: 90100461d7db93ece23bf2420a1b7e8c43da915b
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343045"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Jak spustit odolná služba functions jako WebJobs

[Služba Azure Functions](functions-overview.md) a [Durable Functions](durable-functions-overview.md) rozšíření jsou postavené na [sada WebJobs SDK](../app-service/web-sites-create-web-jobs.md). `JobHost` v sadě WebJobs SDK je modul runtime ve službě Azure Functions. Pokud potřebujete mít kontrolu nad `JobHost` chování způsoby není možné ve službě Azure Functions, můžete vyvíjet a spouštět odolná služba functions pomocí sady SDK pro WebJobs sami. Pak můžete spustit váš odolná služba functions ve webové úloze Azure nebo kdekoli konzolovou aplikaci spustí.

Řetězení Durable Functions ukázka je k dispozici ve verzi sady SDK pro WebJobs: Stáhněte nebo naklonujte [Durable Functions úložiště](https://github.com/azure/azure-functions-durable-extension/) a přejděte *ukázky\\webjobssdk\\řetězení* složky.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste obeznámení se základy sady WebJobs SDK, C# třídy pro vývoj knihoven pro Azure Functions a odolná služba Functions. Pokud potřebujete úvodní informace k těmto tématům, naleznete v následujících zdrojích:

* [Začínáme se sadou WebJobs SDK](../app-service/webjobs-sdk-get-started.md)
* [Vytvoření první funkce pomocí sady Visual Studio](functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

K dokončení kroků v tomto článku:

* [Instalace sady Visual Studio 2017 verze 15.6 nebo novější](https://docs.microsoft.com/visualstudio/install/) s **vývoj pro Azure** pracovního vytížení.

  Pokud už máte sadu Visual Studio, ale nemají úlohy, přidejte úlohu tak, že vyberete **nástroje > Získejte nástroje a funkce**. 

  (Můžete použít [Visual Studio Code](https://code.visualstudio.com/) místo toho ale některé pokyny jsou specifické pro Visual Studio.)

* Nainstalujte a spusťte [emulátoru úložiště Azure](../storage/common/storage-use-emulator.md) verze 5.2 nebo vyšší. Alternativou je aktualizovat *App.config* soubor pomocí připojovacího řetězce služby Azure Storage.

## <a name="webjobs-sdk-versions"></a>Verze sady WebJobs SDK

Tento článek vysvětluje, jak k vývoji projektu 2.x sady SDK pro WebJobs (ekvivalentní k verzi Azure Functions 1.x). Informace o verzi 3.x, naleznete v tématu [sada WebJobs SDK 3.x](#webjobs-sdk-3x) dále v tomto článku. 

## <a name="create-console-app"></a>Vytvoření konzolové aplikace

Sada WebJobs SDK projektu je právě projekt konzolové aplikace s odpovídající nainstalované balíčky NuGet.

V sadě Visual Studio **nový projekt** dialogového okna, vyberte **klasická plocha Windows > Konzolová aplikace (.NET Framework)**. V souboru projektu `TargetFrameworkVersion` by měl být `v4.6.1`.

Visual Studio také poskytuje šablony projektu úlohy WebJob, který můžete použít tak, že vyberete **Cloud > Webová úloha Azure (.NET Framework)**. Tato šablona nainstaluje počtem balíčků, z nichž některé pravděpodobně nebudete potřebovat.

## <a name="install-nuget-packages"></a>Instalace balíčků NuGet

Sada WebJobs SDK, základní vazeb, protokolovacího rozhraní a rozšíření trvalý úlohy musíte balíčky NuGet. Tady jsou **Konzola správce balíčků** příkazy pro tyto balíčky s nejnovější stabilní verzi čísla k datu, byla zapsána v tomto článku:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Budete také potřebovat poskytovatelé protokolování. Následující příkazy nainstalují poskytovatele Application Insights a `ConfigurationManager`. `ConfigurationManager` Umožňuje získat Instrumentační klíč Application Insights z aplikace nastavení.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Následující příkaz nainstaluje poskytovatele konzoly:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost kódu

Chcete-li použít rozšíření Durable Functions, zavolejte `UseDurableTask` na `JobHostConfiguration` objektu v vaše `Main` metoda:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Seznam vlastností, které můžete nastavit `DurableTaskExtension` objektu, najdete v článku [host.json](functions-host-json.md#durabletask).

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

## <a name="functions"></a>Functions

Existuje několik rozdílů v kódu, který napíšete pro porovnání funkcí sady SDK pro WebJobs k zápisu pro službu Azure Functions.

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

V projektu sady WebJobs SDK můžete volat metody u objektu klienta Orchestrace neodesílá požadavky HTTP. Následující metody, které odpovídají na tři úkoly, které vám pomůžou s rozhraním API pro správu protokolu HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Spuštění funkce orchestrátoru funkce klienta Orchestrace v ukázkový projekt a pak přejde do smyčku, která volá `GetStatusAsync` každé 2 sekundy:

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

Tato část obsahuje základní informace o tom, jak spustit [ukázkový projekt](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Podrobné pokyny, které vysvětlují, jak místní spuštění projektu sady WebJobs SDK a jak ji nasadit do Azure WebJob najdete v tématu [Začínáme se sadou WebJobs SDK](../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Spuštění v místním prostředí

1. Ujistěte se, že je spuštěný emulátor úložiště (viz [požadavky](#prerequisites)).

1. Pokud chcete zobrazit protokoly ve službě Application Insights, když spouštíte místně:

  a. Vytvořte prostředek Application Insights typu app **Obecné**.

  b. Uložte klíč instrumentace do *App.config* souboru.

1. Spusťte projekt.

### <a name="run-in-azure"></a>Spustit v Azure

1. Vytvoření webové aplikace a účet úložiště.

1. Ve webové aplikaci uložte připojovací řetězec úložiště v nastavení aplikace s názvem AzureWebJobsStorage.

1. Vytvořte prostředek Application Insights typu app **Obecné**.

1. Uložte klíč instrumentace v nastavení aplikace s názvem APPINSIGHTS_INSTRUMENTATIONKEY.

1. Nasaďte jako Webjobu.

## <a name="webjobs-sdk-3x"></a>Sada WebJobs SDK 3.x

Hlavní změny zavedené 3.x je použití .NET Core namísto rozhraní .NET Framework. Vytvoření projektu 3.x pokynů jsou stejné, s následujícími výjimkami:

1. Vytvoření konzolové aplikace .NET Core. V sadě Visual Studio **nový projekt** dialogového okna, vyberte **.NET Core > Konzolová aplikace (.NET Core)**. Soubor projektu určuje, že `TargetFramework` je `netcoreapp2.0`.

1. Zvolte tuto předprodejní verzi 3.x následujících balíčků:

  * `Microsoft.Azure.WebJobs.Extensions`
  * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

2. Změna `Main` kódu metody k získání připojovacího řetězce úložiště a Instrumentační klíč Application Insights z *appsettings.json* soubor, pomocí rozhraní .NET Core konfigurace.  Tady je příklad:

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

Další informace o sadě WebJobs SDK najdete v tématu [způsob použití sady WebJobs SDK](../app-service/webjobs-sdk-how-to.md).

