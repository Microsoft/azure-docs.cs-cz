---
title: Odkaz na rozhraní API agenta Azure Application Insights
description: Odkaz na rozhraní API agenta Application Insights. Set-ApplicationInsightsMonitoringConfig. Sledujte výkon webových stránek bez opětovného nasazení webu. Funguje s ASP.NET webových aplikací hostovaných místně, ve virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 1226b3e10adf786ed3335844a5d3f4e530911705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671235"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Rozhraní API agenta application insights: Set-ApplicationInsightsMonitoringConfig

Tento dokument popisuje rutinu, která je členem [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis

Nastaví konfigurační soubor bez úplné přeinstalace.
Změny se projeví až po restartování služby IIS.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci prostředí PowerShell s oprávněními správce.


## <a name="examples"></a>Příklady

### <a name="example-with-a-single-instrumentation-key"></a>Příklad s jedním klíčem instrumentace
V tomto příkladu bude všem aplikacím v aktuálním počítači přiřazen jeden klíč instrumentace.

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
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

## <a name="parameters"></a>Parametry

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Požadované.** Tento parametr slouží k zadání jediného klíče instrumentace pro všechny aplikace v cílovém počítači.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Požadované.** Tento parametr slouží k zadání více instrumentačních klíčů a mapování instrumentačních klíčů používaných každou aplikací.
Nastavením `MachineFilter`můžete vytvořit jeden instalační skript pro několik počítačů .

> [!IMPORTANT]
> Aplikace se budou shodovat s pravidly v pořadí, v jakém jsou pravidla k dispozici. Proto byste měli nejprve zadat nejkonkrétnější pravidla a nejobecnější pravidla poslední.

#### <a name="schema"></a>Schéma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** je povinný regulární výraz jazyka C# počítače nebo názvu virtuálního počítače.
    - '.*' bude odpovídat všem
    - Název_počítače bude odpovídat pouze počítačům se zadaným názvem.
- **AppFilter** je povinný regulární výraz jazyka C# názvu počítače nebo virtuálního počítače.
    - '.*' bude odpovídat všem
    - Název_aplikace bude shodovat pouze aplikace služby IIS se zadaným názvem.
- **InstrumentationKey** je nutné povolit monitorování aplikací, které odpovídají předchozí dva filtry.
    - Pokud chcete definovat pravidla pro vyloučení monitorování, ponechte tuto hodnotu nulovou.


### <a name="-verbose"></a>-Podrobné
**Společný parametr.** Tento přepínač slouží k zobrazení podrobných protokolů.


## <a name="output"></a>Výstup

Ve výchozím nastavení žádný výstup.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Příklad podrobného výstupu z nastavení konfiguračního souboru přes -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Příklad podrobného výstupu z nastavení konfiguračního souboru přes -InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>Další kroky

  Zobrazení telemetrických dat:
 - [Prozkoumejte metriky](../../azure-monitor/app/metrics-explorer.md) a sledujte výkon a využití.
- [Hledat události a protokoly](../../azure-monitor/app/diagnostic-search.md) diagnostikovat problémy.
- Pro pokročilejší dotazy [použijte službu Analytics.](../../azure-monitor/app/analytics.md)
- [Vytvořte řídicí panely](../../azure-monitor/app/overview-dashboard.md).
 
 Přidání další telemetrie:
 - [Vytvoření webových testů](monitor-web-app-availability.md) a ověření, jestli web zůstává živý.
- [Přidejte telemetrickou službu webového klienta,](../../azure-monitor/app/javascript.md) chcete-li zobrazit výjimky z kódu webové stránky a povolit volání trasování.
- [Přidání sady Application Insights SDK do kódu,](../../azure-monitor/app/asp-net.md) abyste mohli vkládat trasování a protokolovat hovory
 
 S agentem Application Insights toho zvládnete víc:
 - Použijte náš průvodce [k řešení potíží s](status-monitor-v2-troubleshoot.md) agentem Application Insights.
 - [Získejte config](status-monitor-v2-api-get-config.md) a ověřte, zda byla vaše nastavení zaznamenána správně.
 - [Získejte stav](status-monitor-v2-api-get-status.md) pro kontrolu monitorování.
