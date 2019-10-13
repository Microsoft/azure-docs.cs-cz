---
title: Vytvoření a spuštění vlastních testů dostupnosti pomocí Azure Functions
description: Tento dokument popisuje, jak vytvořit funkci Azure pomocí TrackAvailability (), která se pravidelně spouští podle konfigurace zadané ve funkci TimerTrigger. Výsledky tohoto testu se odešlou do vašeho prostředku Application Insights, kde se budete moct dotazovat na data výsledků dostupnosti a upozornit na ně. Přizpůsobené testy vám umožní zapisovat složitější testy dostupnosti, než je možné pomocí uživatelského rozhraní portálu, monitorovat aplikaci v rámci virtuální sítě Azure, změnit adresu koncového bodu nebo vytvořit test dostupnosti, pokud není ve vaší oblasti dostupný.
services: application-insights
documentationcenter: ''
author: morgangrobin
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: mogrobin
ms.openlocfilehash: 38a83169a7d1ffa03416f5947ada703bcba5017a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301370"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Vytvoření a spuštění vlastních testů dostupnosti pomocí Azure Functions

Tento článek popisuje, jak vytvořit funkci Azure pomocí TrackAvailability (), která se pravidelně spouští podle konfigurace zadané ve funkci TimerTrigger. Výsledky tohoto testu se odešlou do vašeho prostředku Application Insights, kde se budete moct dotazovat na data výsledků dostupnosti a upozornit na ně. To vám umožní vytvořit vlastní testy podobné tomu, co můžete dělat prostřednictvím [monitorování dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) na portálu. Přizpůsobené testy vám umožní psát složitější testy dostupnosti, než je možné pomocí uživatelského rozhraní portálu, monitorovat aplikaci v rámci virtuální sítě Azure, změnit adresu koncového bodu nebo vytvořit test dostupnosti, i když tato funkce není ve vaší oblasti dostupná.


## <a name="create-timer-triggered-function"></a>Vytvoření funkce aktivované časovačem

- Pokud máte prostředek Application Insights:
    - Ve výchozím nastavení Azure Functions vytvoří prostředek Application Insights, ale pokud byste chtěli použít některý z již vytvořených prostředků, budete ho muset během vytváření zadat.
    - Postupujte podle pokynů v tématu Postup [Vytvoření funkce Azure Functions prostředku a časovače](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (zastavit před vyčištěním) s následujícími možnostmi.
        -  Před výběrem možnosti **vytvořit**klikněte na část Application Insights.

            ![ Vytvoření aplikace Azure Functions s vlastním prostředkem App Insights](media/availability-azure-functions/create-function-app.png)

        - Klikněte na **Vybrat existující prostředek** a zadejte název prostředku. Vybrat **použít**

            ![Výběr existujícího prostředku Application Insights](media/availability-azure-functions/app-insights-resource.png)

        - Vyberte **Vytvořit**.
- Pokud ještě nemáte vytvořený prostředek Application Insights pro funkci aktivovanou časovačem:
    - Když vytváříte aplikaci Azure Functions, vytvoří se ve výchozím nastavení pro vás prostředek Application Insights.
    - Postupujte podle pokynů v tématu Jak [vytvořit prostředek Azure functions a funkci aktivovanou časovačem](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (před čištěním zastavit).

## <a name="sample-code"></a>Ukázka kódu

Zkopírujte následující kód do souboru run. CSX (Tato akce nahradí již existující kód). Provedete to tak, že přejdete do aplikace Azure Functions a na levé straně vyberete funkci Trigger časovače.

![Běh. csx funkce Azure Functions v Azure Portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Pro adresu koncového bodu použijte: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. Pokud se prostředek nenachází v oblasti, jako je Azure Government nebo Azure Čína, najdete v tomto článku o [přepsání výchozích koncových bodů](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) a výběru příslušného koncového bodu kanálu telemetrie pro vaši oblast.

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
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

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
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
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![Na pravé straně vyberte Přidat. Pojmenujte soubor Function. proj](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>Ověřit dostupnost

Abyste se ujistili, že všechno funguje, můžete se podívat na graf na kartě Dostupnost prostředku Application Insights.

![Karta dostupnost s úspěšnými výsledky](media/availability-azure-functions/availtab.png)

Při nastavování testu pomocí Azure Functions všimnete si, že na rozdíl od použití možnosti **Přidat test** na kartě Dostupnost nebude název testu zobrazen a nebudete s ním moci pracovat. Výsledky jsou vizuálně vizuální, ale místo stejného podrobného zobrazení, které získáte při vytváření testu dostupnosti prostřednictvím portálu, získáte souhrnné zobrazení.

Chcete-li zobrazit podrobnosti o koncových transakcích, vyberte možnost **úspěšné** nebo **neúspěšné** v oblasti podrobností a pak vyberte ukázku. Můžete také získat informace o podrobnostech transakce, a to tak, že vyberete datový bod v grafu.

![Výběr testu dostupnosti vzorků](media/availability-azure-functions/sample.png)

![Podrobnosti transakce od začátku do konce](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>Dotaz v protokolech (analýza)

Pomocí protokolů (Analytics) můžete zobrazit výsledky, závislosti a další informace o dostupnosti. Další informace o protokolech najdete v článku [Přehled dotazů protokolu](../../azure-monitor/log-query/log-query-overview.md).

![Výsledky dostupnosti](media/availability-azure-functions/availabilityresults.png)

![Závislosti](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Další kroky

- [Mapa aplikace](../../azure-monitor/app/app-map.md)
- [Diagnostika transakcí](../../azure-monitor/app/transaction-diagnostics.md)
