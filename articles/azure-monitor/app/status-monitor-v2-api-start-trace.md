---
title: 'Reference k rozhraní API služby Azure Application Insights agent: spustit trasování | Microsoft Docs'
description: Reference k rozhraní API agenta Application Insights. Spustit – trasovat. Shromažďovat protokoly ETW z Monitorování stavu a Application Insights SDK
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: b1c5aa34c46a20631b328abfb061dc2477150c72
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389853"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Application Insights rozhraní API agenta: Start-ApplicationInsightsMonitoringTrace

Tento článek popisuje rutinu, která je členem [modulu PowerShellu AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis

Shromažďuje [události ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) z běhového modulu připojení s kódováním. Tato rutina je alternativou ke spuštění [PerfView](https://github.com/microsoft/perfview).

Shromážděné události budou v reálném čase vytištěny do konzoly a uloženy do souboru ETL. Výstupní soubor ETL může otevřít [PerfView](https://github.com/microsoft/perfview) k dalšímu prošetření.

Tato rutina se spustí, dokud nedosáhne časového limitu (výchozí hodnota je 5 minut) nebo se zastaví ručně (`Ctrl + C`).

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci PowerShellu s oprávněními správce.

## <a name="examples"></a>Příklady

### <a name="how-to-collect-events"></a>Jak shromažďovat události

Normálně bychom vás poznamenali, že shromáždíte události, abyste zjistili, proč se vaše aplikace neinstrumentuje.

Modul runtime pro nekódování kódu bude generovat události ETW při spuštění služby IIS a při spuštění aplikace.

Shromažďování těchto událostí:
1. V konzole cmd s oprávněními správce spusťte `iisreset /stop` pro vypnutí služby IIS a všech webových aplikací.
2. Spustit tuto rutinu
3. V konzole cmd s oprávněními správce spusťte `iisreset /start` a spusťte službu IIS.
4. Zkuste přejít do aplikace.
5. Až se vaše aplikace dokončí, můžete ji ručně zastavit (`Ctrl + C`) nebo počkat na vypršení časového limitu.

### <a name="what-events-to-collect"></a>Jaké události se mají shromáždit

Při shromažďování událostí máte k dispozici tři možnosti:
1. Pomocí přepínače `-CollectSdkEvents` Shromážděte události vydávané ze sady Application Insights SDK.
2. Pomocí přepínače `-CollectRedfieldEvents` Shromážděte události vyvolané Monitorování stavu a modulem runtime Redfield. Tyto protokoly jsou užitečné při diagnostikování služby IIS a spuštění aplikace.
3. Oba přepínače slouží ke shromáždění obou typů událostí.
4. Ve výchozím nastavení, pokud není zadán žádný přepínač, budou shromažďovány oba typy událostí.


## <a name="parameters"></a>Parametry

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Volitelné.** Pomocí tohoto parametru můžete nastavit, jak dlouho má tento skript shromažďovat události. Výchozí hodnota je 5 minut.

### <a name="-logdirectory"></a>-LogDirectory
**Volitelné.** Pomocí tohoto přepínače Nastavte výstupní adresář souboru ETL. Ve výchozím nastavení se tento soubor vytvoří v adresáři modulů PowerShellu. Úplná cesta se zobrazí během provádění skriptu.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Volitelné.** Pomocí tohoto přepínače můžete shromažďovat události Application Insights SDK.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Volitelné.** Pomocí tohoto přepínače můžete shromažďovat události z Monitorování stavu a modulu runtime Redfield.

### <a name="-verbose"></a>– Verbose
**Společný parametr** Pomocí tohoto přepínače můžete vyvýstupovat podrobné protokoly.



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

- Projděte si další kroky pro řešení potíží: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Podívejte se na [Reference k rozhraní API](status-monitor-v2-overview.md#powershell-api-reference) , kde najdete informace o parametrech, které jste možná vynechali
- Pokud potřebujete další informace, můžete nás kontaktovat na [GitHubu](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Další Application Insights agenta:
 - Pomocí naší příručky můžete [řešit potíže s](status-monitor-v2-troubleshoot.md) agentem Application Insights.
 - [Získáním konfigurace](status-monitor-v2-api-get-config.md) potvrďte, že vaše nastavení se správně nahrálo.
 - [Získejte stav](status-monitor-v2-api-get-status.md) pro kontrolu monitorování.
