---
title: 'Azure Monitor stavu v2 reference k rozhraní API: Spustit sledování | Dokumentace Microsoftu'
description: Odkaz na stav monitorování v2 API. Start trasování. Shromažďování protokolů trasování událostí pro Windows z monitorování stavu a sadu SDK Application Insights.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: b6787134707273a76290adb723a9bc9012252ebd
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807058"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace-v040-alpha"></a>Rozhraní API v2 monitorování stavu: Start-ApplicationInsightsMonitoringTrace (v0.4.0-alpha)

Tento článek popisuje rutiny, která je členem skupiny [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvu o úrovni služeb, a to nedoporučujeme pro produkční úlohy. Některé funkce nemusí být podporované a některé můžou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Popis

Shromažďuje [události trasování událostí pro Windows](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) z bez použití kódu připojení modulu runtime. Tato rutina se o alternativu k spuštění [PerfView](https://github.com/microsoft/perfview).

Shromážděné události budou zobrazeny v konzole v reálném čase a uloží do souboru ETL. Výstupní soubor ETL může otevřít [PerfView](https://github.com/microsoft/perfview) o pomoc.

Tato rutina se spustí, dokud nedosáhne dobu trvání časového limitu (výchozí hodnota 5 minut) nebo ručně zastavená (`Ctrl + C`).

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci Powershellu s oprávněními správce.

## <a name="examples"></a>Příklady

### <a name="how-to-collect-events"></a>Shromažďování událostí

Obvykle by zeptáme shromažďování událostí k prozkoumání, proč není právě instrumentované aplikace.

Připojte bez použití kódu modulu runtime bude generovat události trasování událostí pro Windows, při spuštění služby IIS a při spuštění aplikace.

Shromažďování těchto událostí:
1. V konzole příkazového řádku s oprávněními správce spusťte `iisreset /stop` vypnutí služby IIS a všechny webové aplikace.
2. Spusťte tuto rutinu
3. V konzole příkazového řádku s oprávněními správce spusťte `iisreset /start` spuštění služby IIS.
4. Došlo k pokusu o přechod do vaší aplikace.
5. Po dokončení načítání aplikace, můžete ručně zastavit jeho (`Ctrl + C`), nebo počkejte vypršení časového limitu.

### <a name="what-events-to-collect"></a>Jaké události ke shromažďování

Při shromažďování událostí máte tři možnosti:
1. Použijte přepínač `-CollectSdkEvents` shromažďování událostí vyzařovaného z Application Insights SDK.
2. Použijte přepínač `-CollectRedfieldEvents` shromažďování událostí, protože ho vygeneroval monitorování stavu a Redfield modulu Runtime. Tyto protokoly jsou užitečné při diagnostice služby IIS a spuštění aplikace.
3. Oba přepínače slouží ke shromáždění oba typy událostí.
4. Ve výchozím nastavení, pokud není zadán žádný přepínač, že oba typy událostí se budou shromažďovat.


## <a name="parameters"></a>Parametry

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Volitelné.** Tento parametr použijte, chcete-li nastavit, jak dlouho by měl tento skript shromažďování událostí. Výchozí hodnota je 5 minut.

### <a name="-logdirectory"></a>-LogDirectory
**Volitelné.** Pomocí tohoto přepínače můžete nastavit výstupní adresář souboru ETL. Ve výchozím nastavení bude tento soubor vytvořen v adresáři modulů Powershellu. Při provádění skriptu se zobrazí úplnou cestu.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Volitelné.** Tento přepínač umožňuje shromažďování událostí v Application Insights SDK.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Volitelné.** Chcete-li shromažďovat události z monitorování stavu a modul runtime Redfield použijte tento přepínač.

### <a name="-verbose"></a>-Verbose
**Společný parametr.** Tento přepínač použijte na výstup podrobných protokolů.



## <a name="output"></a>Výstup


### <a name="example-of-application-startup-logs"></a>Příklad protokolů po spuštění aplikace
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

- Přečtěte si další řešení problémů s kroky: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Zkontrolujte [Reference k rozhraní API](status-monitor-v2-overview.md#powershell-api-reference) Další informace o parametrech nebyla provedena.
- Pokud potřebujete další pomoc, můžete nás kontaktovat na [Githubu](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Lepší využití v2 monitorování stavu:
 - Pomocí naší příručce ke [Poradce při potížích s](status-monitor-v2-troubleshoot.md) v2 monitorování stavu.
 - [Získání konfigurace](status-monitor-v2-api-get-config.md) potvrďte, že nastavení byly zaznamenány správně.
 - [Načíst stav](status-monitor-v2-api-get-status.md) ke kontrole monitorování.
