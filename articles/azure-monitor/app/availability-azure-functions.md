---
title: Vytvoření a spuštění vlastních testů dostupnosti pomocí Azure Functions
description: Tento dokument popisuje, jak vytvořit funkci Azure pomocí TrackAvailability (), která se pravidelně spouští podle konfigurace zadané ve funkci TimerTrigger. Výsledky tohoto testu se odešlou do vašeho prostředku Application Insights, kde se budete moct dotazovat na data výsledků dostupnosti a upozornit na ně. Přizpůsobené testy vám umožní zapisovat složitější testy dostupnosti, než je možné pomocí uživatelského rozhraní portálu, monitorovat aplikaci v rámci virtuální sítě Azure, změnit adresu koncového bodu nebo vytvořit test dostupnosti, pokud není ve vaší oblasti dostupný.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 98d9eaadb31ffdeabe85752f7c76bdd4f7c0d4f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589945"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Vytvoření a spuštění vlastních testů dostupnosti pomocí Azure Functions

Tento článek popisuje, jak vytvořit funkci Azure pomocí TrackAvailability (), která se pravidelně spouští podle konfigurace zadané ve funkci TimerTrigger s vlastní obchodní logikou. Výsledky tohoto testu se odešlou do vašeho prostředku Application Insights, kde se budete moct dotazovat na data výsledků dostupnosti a upozornit na ně. To vám umožní vytvořit vlastní testy podobné tomu, co můžete dělat prostřednictvím [monitorování dostupnosti](./monitor-web-app-availability.md) na portálu. Přizpůsobené testy vám umožní psát složitější testy dostupnosti, než je možné pomocí uživatelského rozhraní portálu, monitorovat aplikaci v rámci virtuální sítě Azure, změnit adresu koncového bodu nebo vytvořit test dostupnosti, i když tato funkce není ve vaší oblasti dostupná.

> [!NOTE]
> Tento příklad je určený výhradně k tomu, aby vám ukázal, jak volání rozhraní API TrackAvailability () funguje v rámci funkce Azure Function. Nemusíte psát podkladový kód testu HTTP nebo obchodní logiku, který by byl nutný k tomu, aby se tento test vypnul na plně funkční test dostupnosti. Ve výchozím nastavení se při procházení tohoto příkladu vytvoří test dostupnosti, který bude vždy generovat selhání.

## <a name="create-timer-triggered-function"></a>Vytvoření funkce aktivované časovačem

- Pokud máte prostředek Application Insights:
    - Ve výchozím nastavení Azure Functions vytvoří prostředek Application Insights, ale pokud byste chtěli použít některý z již vytvořených prostředků, budete ho muset během vytváření zadat.
    - Postupujte podle pokynů v tématu Postup [Vytvoření funkce Azure Functions prostředku a časovače](../../azure-functions/functions-create-scheduled-function.md) (zastavit před vyčištěním) s následujícími možnostmi.
        -  Vyberte kartu **monitorování** v pravém horním rohu.

            ![ Vytvoření aplikace Azure Functions s vlastním prostředkem App Insights](media/availability-azure-functions/create-function-app.png)

        - Vyberte rozevírací seznam Application Insights a zadejte nebo vyberte název prostředku.

            ![Výběr existujícího prostředku Application Insights](media/availability-azure-functions/app-insights-resource.png)

        - Vyberte **Zkontrolovat a vytvořit**.
- Pokud ještě nemáte vytvořený prostředek Application Insights pro funkci aktivovanou časovačem:
    - Když vytváříte aplikaci Azure Functions, vytvoří se ve výchozím nastavení pro vás prostředek Application Insights.
    - Postupujte podle pokynů v tématu Jak [vytvořit prostředek Azure functions a funkci aktivovanou časovačem](../../azure-functions/functions-create-scheduled-function.md) (před čištěním zastavit).

## <a name="sample-code"></a>Ukázka kódu

Zkopírujte následující kód do souboru run. CSX (Tato akce nahradí již existující kód). Provedete to tak, že přejdete do aplikace Azure Functions a na levé straně vyberete funkci Trigger časovače.

>[!div class="mx-imgBorder"]
>![Běh. csx funkce Azure Functions v Azure Portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Pro adresu koncového bodu použijte: `EndpointAddress= https://dc.services.visualstudio.com/v2/track` . Pokud se prostředek nenachází v oblasti, jako je Azure Government nebo Azure Čína, najdete v tomto článku o [přepsání výchozích koncových bodů](./custom-endpoints.md#regions-that-require-endpoint-modification) a výběru příslušného koncového bodu kanálu telemetrie pro vaši oblast.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");
 
    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }
 
    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";
 
    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

Na pravé straně v části Zobrazit soubory vyberte **Přidat**. Zavolejte nový soubor **Function. proj** s následující konfigurací.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights" Version="2.15.0" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![Na pravé straně vyberte Přidat. Pojmenujte soubor Function. proj](media/availability-azure-functions/addfile.png)

Na pravé straně v části Zobrazit soubory vyberte **Přidat**. Zavolejte nový soubor **runAvailabilityTest. csx** s následující konfigurací.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Ověřit dostupnost

Abyste se ujistili, že všechno funguje, můžete se podívat na graf na kartě Dostupnost prostředku Application Insights.

> [!NOTE]
> Pokud jste implementovali vlastní obchodní logiku v runAvailabilityTest. csx, zobrazí se úspěšné výsledky, jako na snímcích obrazovky níže, pokud jste tak nepracovali, zobrazí se neúspěšné výsledky. Testy vytvořené pomocí se `TrackAvailability()` zobrazí s **vlastním** názvem testu vedle názvu testu.

>[!div class="mx-imgBorder"]
>![Karta dostupnost s úspěšnými výsledky](media/availability-azure-functions/availability-custom.png)

Chcete-li zobrazit podrobnosti o koncových transakcích, vyberte možnost **úspěšné** nebo **neúspěšné** v oblasti podrobností a pak vyberte ukázku. Můžete také získat informace o podrobnostech transakce, a to tak, že vyberete datový bod v grafu.

>[!div class="mx-imgBorder"]
>![Výběr testu dostupnosti vzorků](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![Podrobnosti transakce od začátku do konce](media/availability-azure-functions/end-to-end.png)

Pokud jste spustili vše, co je (bez přidání obchodní logiky), pak se test nezdařil.

## <a name="query-in-logs-analytics"></a>Dotaz v protokolech (analýza)

Pomocí protokolů (Analytics) můžete zobrazit výsledky, závislosti a další informace o dostupnosti. Další informace o protokolech najdete v článku [Přehled dotazů protokolu](../logs/log-query-overview.md).

>[!div class="mx-imgBorder"]
>![Výsledky dostupnosti](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Snímek obrazovky se zobrazí nová karta dotaz se závislostmi omezenými na 50.](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Další kroky

- [Mapa aplikace](./app-map.md)
- [Diagnostika transakcí](./transaction-diagnostics.md)

