---
title: Odkaz na rozhraní API agenta Azure Application Insights
description: Odkaz na rozhraní API agenta Application Insights. Povolit ApplicationInsightsMonitoring. Sledujte výkon webových stránek bez opětovného nasazení webu. Funguje s ASP.NET webových aplikací hostovaných místně, ve virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: f5d66c5c21f7491e3bdc6bb70f693c3b98bf62dd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536739"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Rozhraní API agenta Application Insights: Enable-ApplicationInsightsMonitoring

Tento článek popisuje rutinu, která je členem [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis

Umožňuje monitorování aplikací služby IIS v cílovém počítači bez kódů.

Tato rutina upraví iis applicationHost.config a nastaví některé klíče registru.
Vytvoří také soubor applicationinsights.ikey.config, který definuje klíč instrumentace používaný každou aplikací.
Služby IIS načtou modul RedfieldModule při spuštění, který při spuštění aplikace vloží sadu Application Insights SDK do aplikací.
Změny se projeví až po restartování služby IIS.

Po povolení monitorování doporučujeme pomocí [live metrik rychle](live-stream.md) zkontrolovat, jestli nám vaše aplikace posílá telemetrii.


> [!NOTE] 
> - Chcete-li začít, potřebujete klíč instrumentace. Další informace naleznete [v tématu Vytvoření prostředku](create-new-resource.md#copy-the-instrumentation-key).
> - Tato rutina vyžaduje, abyste si přečetli a přijali naše licenční a prohlášení o zásadách ochrany osobních údajů.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci prostředí PowerShell s oprávněními správce a zásady zvýšeného spuštění. Další informace naleznete v [tématu Spuštění prostředí PowerShell jako správce se zvýšenými zásadami provádění](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Příklady

### <a name="example-with-a-single-instrumentation-key"></a>Příklad s jedním klíčem instrumentace
V tomto příkladu jsou všem aplikacím v aktuálním počítači přiřazen jeden klíč instrumentace.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Příklad s mapou klíčů instrumentace
V tomto příkladu:
- `MachineFilter`odpovídá aktuálnímu počítači `'.*'` pomocí zástupných symbolů.
- `AppFilter='WebAppExclude'`poskytuje `null` klíč instrumentace. Zadaná aplikace nebude instrumentována.
- `AppFilter='WebAppOne'`přiřadí zadané aplikaci jedinečný klíč instrumentace.
- `AppFilter='WebAppTwo'`přiřadí zadané aplikaci jedinečný klíč instrumentace.
- Nakonec `AppFilter` také používá `'.*'` zástupný znak tak, aby odpovídal všem webovým aplikacím, které nejsou spárovány s dřívějšími pravidly, a přiřadí výchozí klíč instrumentace.
- Prostory jsou přidány pro čitelnost.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>Parametry

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Povinná hodnota.** Tento parametr slouží k zadání jediného klíče instrumentace pro všechny aplikace v cílovém počítači.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Povinná hodnota.** Tento parametr slouží k zadání více instrumentačních klíčů a mapování instrumentačních klíčů používaných každou aplikací.
Nastavením `MachineFilter`můžete vytvořit jeden instalační skript pro několik počítačů .

> [!IMPORTANT]
> Aplikace se budou shodovat s pravidly v pořadí, v jakém jsou pravidla k dispozici. Proto byste měli nejprve zadat nejkonkrétnější pravidla a nejobecnější pravidla poslední.

#### <a name="schema"></a>Schéma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** je povinný regulární výraz jazyka C# počítače nebo názvu virtuálního počítače.
    - '.*' bude odpovídat všem
    - Název_počítače bude shodovat pouze s počítači s přesným názvem.
- **AppFilter** je povinný regulární výraz jazyka C# názvu webu iis. Seznam webů na serveru můžete získat spuštěním příkazu [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '.*' bude odpovídat všem
    - Název_webu bude odpovídat pouze webu iis s přesným názvem.
- **InstrumentationKey** je nutné povolit monitorování aplikací, které odpovídají předchozí dva filtry.
    - Pokud chcete definovat pravidla pro vyloučení monitorování, ponechte tuto hodnotu nulovou.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Volitelné.** Pomocí tohoto přepínače povolte instrumentační modul shromažďovat události a zprávy o tom, co se děje během provádění spravovaného procesu. Tyto události a zprávy zahrnují kódy výsledků závislostí, slovesa HTTP a text příkazu SQL.

Přístrojové stroje přidává nad hlavou a je vypnutý ve výchozím nastavení.

### <a name="-acceptlicense"></a>-AcceptLicense
**Volitelné.** Pomocí tohoto přepínače můžete přijmout licenci a prohlášení o zásadách ochrany osobních údajů v bezhlavých instalacích.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Pokud máte cluster webových serverů, je možné, že používáte [sdílenou konfiguraci](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
HttpModule nelze vložit do této sdílené konfigurace.
Tento skript se nezdaří se zprávou, že jsou požadovány další kroky instalace.
Pomocí tohoto přepínače můžete tuto kontrolu ignorovat a pokračovat v instalaci požadavků. Další informace naleznete v [tématu známý konflikt se sdílenou konfigurací](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Podrobné
**Společný parametr.** Tento přepínač slouží k zobrazení podrobných protokolů.

### <a name="-whatif"></a>-Co když 
**Společný parametr.** Pomocí tohoto přepínače otestujte a ověřte vstupní parametry bez skutečného povolení monitorování.

## <a name="output"></a>Výstup


#### <a name="example-output-from-a-successful-enablement"></a>Příklad výstupu z úspěšného povolení

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

## <a name="next-steps"></a>Další kroky

  Zobrazení telemetrických dat:
 - [Prozkoumejte metriky](../../azure-monitor/platform/metrics-charts.md) a sledujte výkon a využití.
- [Hledat události a protokoly](../../azure-monitor/app/diagnostic-search.md) diagnostikovat problémy.
- Pro pokročilejší dotazy [použijte službu Analytics.](../../azure-monitor/app/analytics.md)
- [Vytvořte řídicí panely](../../azure-monitor/app/overview-dashboard.md).
 
 Přidání další telemetrie:
 - [Vytvoření webových testů](monitor-web-app-availability.md) a ověření, jestli web zůstává živý.
- [Přidejte telemetrickou službu webového klienta,](../../azure-monitor/app/javascript.md) chcete-li zobrazit výjimky z kódu webové stránky a povolit volání trasování.
- [Přidejte do kódu sadou SDK application insights,](../../azure-monitor/app/asp-net.md) abyste mohli vkládat volání trasování a protokolování.
 
 S agentem Application Insights toho zvládnete víc:
 - Použijte náš průvodce [k řešení potíží s](status-monitor-v2-troubleshoot.md) agentem Application Insights.
 - [Získejte config](status-monitor-v2-api-get-config.md) a ověřte, zda byla vaše nastavení zaznamenána správně.
 - [Získejte stav](status-monitor-v2-api-get-status.md) pro kontrolu monitorování.
