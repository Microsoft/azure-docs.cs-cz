---
title: 'Azure Monitor stavu v2 reference k rozhraní API: Povolit monitorování | Dokumentace Microsoftu'
description: Odkaz na stav monitorování v2 API. Enable-ApplicationInsightsMonitoring. Sledování výkonu webu bez opětovného nasazení webu. Funguje s webové aplikace ASP.NET hostované místně, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: e0d5363e253e89b32b5eca14366504f0ace39043
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479631"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v031-alpha"></a>Rozhraní API v2 monitorování stavu: Enable-ApplicationInsightsMonitoring (v0.3.1 alfa)

Tento článek popisuje rutiny, která je členem skupiny [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvu o úrovni služeb, a to nedoporučujeme pro produkční úlohy. Některé funkce nemusí být podporované a některé můžou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Popis

Umožňuje využívat připojení, monitorování aplikací služby IIS na cílovém počítači.

Tato rutina se upravit soubor applicationHost.config služby IIS a nastavení několik klíčů registru.
Také vytvoří soubor applicationinsights.ikey.config, který definuje Instrumentační klíč používaný každou aplikaci.
Služba IIS načte RedfieldModule při spuštění, který vloží Application Insights SDK do aplikace, jako je spuštění aplikace.
Restartujte službu IIS pro vaše změny se projeví.

Když povolíte monitorování, doporučujeme použít [Live Metrics](live-stream.md) můžete rychle zjistit, pokud vaše aplikace odesílá nám telemetrická data.


> [!NOTE] 
> - Abyste mohli začít, budete potřebovat Instrumentační klíč. Další informace najdete v tématu [vytvořit prostředek](create-new-resource.md#copy-the-instrumentation-key).
> - Tato rutina vyžaduje, přečtěte si a přijměte naše prohlášení licence a ochrana osobních údajů.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci Powershellu s oprávněními správce a zásady provádění se zvýšenými oprávněními. Další informace najdete v tématu [spustit PowerShell jako správce pomocí zásad provádění se zvýšenými oprávněními](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Příklady

### <a name="example-with-a-single-instrumentation-key"></a>Příklad s jeden Instrumentační klíč
V tomto příkladu jsou přiřazeny všechny aplikace v aktuálním počítači jeden Instrumentační klíč.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Příklad s mapou klíče instrumentace
V tomto příkladu:
- `MachineFilter` odpovídá aktuální počítač s použitím `'.*'` zástupný znak.
- `AppFilter='WebAppExclude'` poskytuje `null` Instrumentační klíč. Zadaná aplikace nebude instrumentován.
- `AppFilter='WebAppOne'` konkrétní aplikaci přiřadí jedinečný Instrumentační klíč.
- `AppFilter='WebAppTwo'` konkrétní aplikaci přiřadí jedinečný Instrumentační klíč.
- Nakonec `AppFilter` také používá `'.*'` zástupným znakem, který odpovídá všem webovým aplikacím, které nejsou spárované s starší pravidla a přiřadit výchozí Instrumentační klíč.
- Mezery jsou přidány pro čitelnost.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>Parametry

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Povinné.** Tento parametr slouží k poskytování jeden Instrumentační klíč pro použití všemi aplikacemi v cílovém počítači.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Povinné.** Tento parametr slouží k poskytování více klíčů instrumentace a mapování klíčů instrumentace používají každou aplikaci.
Můžete vytvořit jeden instalační skript pro několik počítačů tak, že nastavíte `MachineFilter`.

> [!IMPORTANT]
> Aplikace bude hledána shoda s pravidly v pořadí, ve kterém jsou k dispozici pravidla. Proto je třeba zadat nejspecifičtější pravidla první a poslední nejvíce obecná pravidla.

#### <a name="schema"></a>Schéma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** je požadované C# regulární výraz počítače nebo název virtuálního počítače.
    - ". *" bude odpovídat vše
    - "Název" bude odpovídat jenom počítače s přesným názvem zadán.
- **AppFilter** je požadované C# regulární výraz počítače nebo název virtuálního počítače.
    - ". *" bude odpovídat vše
    - "ApplicationName" bude odpovídat pouze aplikace služby IIS s přesným názvem zadán.
- **InstrumentationKey** je potřeba povolit monitorování aplikací, které odpovídají předchozí dva filtry.
    - Ponechte tuto hodnotu null, pokud chcete definovat pravidla pro vyloučení monitorování.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Volitelné.** Chcete-li povolit modul instrumentace ke shromažďování události a zprávy o tom, co se děje během spuštění spravovaného procesu použijte tento přepínač. Tyto události a zprávy zahrnují kódy výsledků závislostí, příkazy HTTP a text příkazu SQL.

Instrumentace stroj přidá režii a je vypnuto ve výchozím nastavení.

### <a name="-acceptlicense"></a>-AcceptLicense
**Volitelné.** Použijte tento přepínač tak, aby přijímal příkaz licence a ochrana osobních údajů v bezobslužné instalace.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Až budete mít cluster webové servery, je možné, že používáte [sdílenou konfiguraci](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
HttpModule nelze vloženy do této sdílené konfigurace.
Tento skript selže se zprávou, že se vyžadují další instalační kroky.
Použijte tento přepínač k této kontroly ignorovat a pokračovat v instalaci požadované součásti. Další informace najdete v tématu [známá konflikt s iis sdílené – konfigurace](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**Společný parametr.** Chcete-li zobrazit podrobné protokoly použijte tento přepínač.

### <a name="-whatif"></a>-WhatIf 
**Společný parametr.** Použijte tento přepínač k testování a ověřování vstupních parametrů bez skutečně povolení monitorování.

## <a name="output"></a>Výstup


#### <a name="example-output-from-a-successful-enablement"></a>Ukázkový výstup z úspěšného povolení

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="next-steps"></a>Další postup

  Zobrazení telemetrických dat:
 - [Zkoumání metrik](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití.
- [Prohledávejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) k diagnostice problémů.
- [Použití analýzy](../../azure-monitor/app/analytics.md) pro pokročilejší dotazy.
- [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md).
 
 Přidání další telemetrie:
 - [Vytvářejte webové testy](monitor-web-app-availability.md) k Ujistěte se, že web zůstává živý.
- [Přidání telemetrie webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a povolit trasování volání.
- [Přidejte Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) abyste mohli vložit trasování a protokolování volání.
 
 Lepší využití v2 monitorování stavu:
 - Pomocí naší příručce ke [Poradce při potížích s](status-monitor-v2-troubleshoot.md) v2 monitorování stavu.
 - [Získání konfigurace](status-monitor-v2-api-get-config.md) potvrďte, že nastavení byly zaznamenány správně.
 - [Načíst stav](status-monitor-v2-api-get-status.md) ke kontrole monitorování.
