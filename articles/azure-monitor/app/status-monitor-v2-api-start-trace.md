---
title: Odkaz na rozhraní API agenta Azure Application Insights
description: Odkaz na rozhraní API agenta Application Insights. Start-Trace. Shromážděte protokoly ETW z monitorování stavu a scharty aplikací SDK.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671218"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Rozhraní API agenta Application Insights: Start-ApplicationInsightsMonitoring

Tento článek popisuje rutinu, která je členem [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis

Shromažďuje [Události ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) z bezkódového připojení runtime. Tato rutina je alternativou ke spuštění [PerfView](https://github.com/microsoft/perfview).

Shromážděné události budou vytištěny do konzoly v reálném čase a uloženy do souboru ETL. Výstupní soubor ETL lze otevřít [PerfView](https://github.com/microsoft/perfview) pro další šetření.

Tato rutina bude spuštěna, dokud nedosáhne doby trvání časového limitu`Ctrl + C`(výchozí 5 minut) nebo dokud není zastavena ručně ( ).

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci prostředí PowerShell s oprávněními správce.

## <a name="examples"></a>Příklady

### <a name="how-to-collect-events"></a>Jak shromažďovat události

Za normálních okolností bychom vás požádat, abyste shromažďovat události, aby prošetřila, proč vaše aplikace není instrumentované.

Bezkódový připojit runtime bude vyzařovat Události ETW při spuštění služby IIS a při spuštění aplikace.

Chcete-li shromáždit tyto události:
1. V konzoli cmd s oprávněními správce spouštějte `iisreset /stop` Vypnutí služby IIS a všech webových aplikací.
2. Spustit tuto rutinu
3. V konzoli cmd s oprávněními správce spusťte `iisreset /start` Spuštění služby IIS.
4. Zkuste přejít do aplikace.
5. Po dokončení načítání aplikace ji můžete ručně`Ctrl + C`zastavit ( ) nebo počkat na časový rámec.

### <a name="what-events-to-collect"></a>Jaké události sbírat

Při shromažďování událostí máte tři možnosti:
1. Přepínač slouží `-CollectSdkEvents` ke shromažďování událostí vyzařovaných ze sady Application Insights SDK.
2. Přepínač slouží `-CollectRedfieldEvents` ke shromažďování událostí vyzařovaných sledováním stavu a redfieldovým runtime. Tyto protokoly jsou užitečné při diagnostice služby IIS a spuštění aplikace.
3. Pomocí obou přepínačů můžete shromažďovat oba typy událostí.
4. Ve výchozím nastavení, pokud není zadán žádný přepínač, budou shromažďovány oba typy událostí.


## <a name="parameters"></a>Parametry

### <a name="-maxdurationinminutes"></a>-MaxdurationInMinutes
**Volitelné.** Tento parametr slouží k nastavení, jak dlouho by měl tento skript shromažďovat události. Výchozí hodnota je 5 minut.

### <a name="-logdirectory"></a>-LogDirectory
**Volitelné.** Tento přepínač slouží k nastavení výstupního adresáře souboru ETL. Ve výchozím nastavení bude tento soubor vytvořen v adresáři Modules prostředí PowerShell. Úplná cesta se zobrazí během spuštění skriptu.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Volitelné.** Tento přepínač slouží ke shromažďování událostí sady Application Insights SDK.

### <a name="-collectredfieldevents"></a>-CollectRedfieldUdálosti
**Volitelné.** Tento přepínač slouží ke shromažďování událostí z monitoru stavu a runtime Redfield.

### <a name="-verbose"></a>-Podrobné
**Společný parametr.** Tento přepínač použijte k výstupu podrobných protokolů.



## <a name="output"></a>Výstup


### <a name="example-of-application-startup-logs"></a>Příklad protokolů spuštění aplikace
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>Další kroky

Další řešení potíží:

- Zde naleznete další kroky při řešení potíží:https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Projděte si [odkaz na rozhraní API,](status-monitor-v2-overview.md#powershell-api-reference) abyste se dozvěděli o parametrech, které jste možná vynechali.
- Pokud potřebujete další pomoc, můžete nás kontaktovat na [GitHubu](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 S agentem Application Insights toho zvládnete víc:
 - Použijte náš průvodce [k řešení potíží s](status-monitor-v2-troubleshoot.md) agentem Application Insights.
 - [Získejte config](status-monitor-v2-api-get-config.md) a ověřte, zda byla vaše nastavení zaznamenána správně.
 - [Získejte stav](status-monitor-v2-api-get-status.md) pro kontrolu monitorování.
