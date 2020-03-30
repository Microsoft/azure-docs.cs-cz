---
title: Vytváření a spouštění vlastních testů dostupnosti pomocí funkcí Azure
description: Tento dokument se bude týkat, jak vytvořit funkci Azure s TrackAvailability(), který bude pravidelně spouštět podle konfigurace uvedené ve funkci TimerTrigger. Výsledky tohoto testu budou odeslány do prostředku Application Insights, kde budete moci dotazovat a upozorňovat na data výsledků dostupnosti. Přizpůsobené testy vám umožní psát složitější testy dostupnosti, než je možné pomocí portálu uznaného, monitorování aplikace uvnitř virtuální sítě Azure, změna adresy koncového bodu nebo vytvoření testu dostupnosti, pokud není k dispozici ve vaší oblasti.
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665795"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Vytváření a spouštění vlastních testů dostupnosti pomocí funkcí Azure

Tento článek se bude zabývat tím, jak vytvořit funkci Azure s TrackAvailability(), která se bude pravidelně spouštět podle konfigurace uvedené ve funkci TimerTrigger s vlastní obchodní logikou. Výsledky tohoto testu budou odeslány do prostředku Application Insights, kde budete moci dotazovat a upozorňovat na data výsledků dostupnosti. To umožňuje vytvořit vlastní testy podobné tomu, co můžete udělat prostřednictvím [monitorování dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) na portálu. Přizpůsobené testy vám umožní psát složitější testy dostupnosti, než je možné pomocí portálu uznaného, monitorování aplikace uvnitř virtuální sítě Azure, změna adresy koncového bodu nebo vytvoření testu dostupnosti, i když tato funkce není k dispozici ve vaší oblasti.

> [!NOTE]
> Tento příklad je určen výhradně k zobrazení mechaniky, jak trackAvailability() volání rozhraní API funguje v rámci funkce Azure. Není, jak napsat základní http test kódu/obchodní logiku, která by byla požadována pro přeměnu na plně funkční test dostupnosti. Ve výchozím nastavení, pokud budete procházet tento příklad, budete vytvářet test dostupnosti, který bude vždy generovat selhání.

## <a name="create-timer-triggered-function"></a>Vytvořit funkci spouštění časovače

- Pokud máte prostředek Application Insights:
    - Ve výchozím nastavení Azure Functions vytvoří prostředek Application Insights, ale pokud chcete použít jeden z již vytvořených prostředků, budete muset zadat, že při vytváření.
    - Postupujte podle pokynů, jak [vytvořit prostředek Azure Functions a timer aktivovanou funkci](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (zastavit před vyčištěním) s následujícími možnostmi.
        -  V horní části vyberte kartu **Monitorování.**

            ![ Vytvoření aplikace Azure Functions s vlastním zdrojem Přehledů aplikací](media/availability-azure-functions/create-function-app.png)

        - Vyberte rozevírací pole Přehledy aplikací a zadejte nebo vyberte název svého prostředku.

            ![Výběr existujícího prostředku Application Insights](media/availability-azure-functions/app-insights-resource.png)

        - Vybrat **zkontrolovat + vytvořit**
- Pokud ještě nemáte pro funkci aktivovanou časovačem vytvořený prostředek Application Insights:
    - Ve výchozím nastavení při vytváření aplikace Azure Functions vytvoří prostředek Application Insights pro vás.
    - Postupujte podle pokynů, jak [vytvořit prostředek Azure Functions a timer aktivovanou funkci](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (zastavit před vyčištěním).

## <a name="sample-code"></a>Ukázka kódu

Zkopírujte níže uvedený kód do souboru run.csx (tím se nahradí již existující kód). Chcete-li to provést, přejděte do aplikace Azure Functions a vyberte funkci aktivační události časovače na levé straně.

>[!div class="mx-imgBorder"]
>![Run.csx funkce Azure na webu Azure Portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Pro adresu koncového bodu, `EndpointAddress= https://dc.services.visualstudio.com/v2/track`kterou byste použili: . Pokud váš prostředek se nachází v oblasti, jako je Azure Government nebo Azure China v takovém případě naleznete v tomto článku o [přepsání výchozí koncové body](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) a vyberte příslušný koncový bod kanálu telemetrie pro vaši oblast.

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

Vpravo v zobrazení souborů vyberte **Přidat**. Zavolejte nový soubor **function.proj** s následující konfigurací.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![Na pravé straně vyberte, přidejte. Pojmenování funkce souboru.proj](media/availability-azure-functions/addfile.png)

Vpravo v zobrazení souborů vyberte **Přidat**. Volání nového souboru **runAvailabilityTest.csx** s následující konfigurací.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Zkontrolovat dostupnost

Chcete-li se ujistit, že vše funguje, můžete se podívat na graf na kartě Dostupnost prostředku Application Insights.

> [!NOTE]
> Pokud jste implementovali vlastní obchodní logiku v runAvailabilityTest.csx pak uvidíte úspěšné výsledky, jako na níže uvedených snímcích obrazovky, pokud jste to neudělali, pak uvidíte neúspěšné výsledky.

>[!div class="mx-imgBorder"]
>![Karta Dostupnost s úspěšnými výsledky](media/availability-azure-functions/availtab.png)

Když nastavíte test pomocí funkce Azure, všimnete si, že na rozdíl od použití **add test** na kartě Dostupnost se název vašeho testu nezobrazí a nebudete s ním moci pracovat. Výsledky jsou vizualizovány, ale místo stejného podrobného zobrazení, které získáte při vytváření testu dostupnosti prostřednictvím portálu, získáte souhrnné zobrazení.

Chcete-li zobrazit podrobnosti o transakci od konce, vyberte **možnost Úspěšné** nebo **Neúspěšné** v části Přejít k podrobnostem a pak vyberte ukázku. Můžete také získat podrobnosti o transakci od konce kliknutím na výběr datového bodu v grafu.

>[!div class="mx-imgBorder"]
>![Výběr testu dostupnosti vzorku](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![Podrobnosti o transakcích od konce](media/availability-azure-functions/end-to-end.png)

Pokud jste spustili vše tak, jak je (bez přidání obchodní logiky), uvidíte, že test se nezdařil.

## <a name="query-in-logs-analytics"></a>Dotaz v protokolech (Analytics)

Protokoly(analýzy) můžete zobrazit výsledky dostupnosti, závislosti a další. Další informace o protokolech naleznete v části [Přehled dotazu protokolu](../../azure-monitor/log-query/log-query-overview.md).

>[!div class="mx-imgBorder"]
>![Výsledky dostupnosti](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Závislosti](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Další kroky

- [Mapa aplikace](../../azure-monitor/app/app-map.md)
- [Diagnostika transakcí](../../azure-monitor/app/transaction-diagnostics.md)
