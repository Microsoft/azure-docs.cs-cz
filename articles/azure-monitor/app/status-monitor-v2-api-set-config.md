---
title: 'Reference k rozhraní API služby Azure Monitorování stavu v2: Nastavení konfigurace | Microsoft Docs'
description: Reference k rozhraní API pro Monitorování stavu v2 Set-ApplicationInsightsMonitoringConfig. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: f3a55caba13b3b96884d446e0750d9fb67a343df
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326298"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig"></a>Rozhraní API pro Monitorování stavu v2: Set-ApplicationInsightsMonitoringConfig

Tento dokument popisuje rutinu, která je členem [modulu PowerShellu AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis

Nastaví konfigurační soubor bez provedení úplné přeinstalace.
Restartujte službu IIS, aby se provedené změny projevily.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci PowerShellu s oprávněními správce.


## <a name="examples"></a>Příklady

### <a name="example-with-a-single-instrumentation-key"></a>Příklad s jedním klíčem instrumentace
V tomto příkladu se všem aplikacím v aktuálním počítači přiřadí jeden klíč instrumentace.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Příklad s mapou klíče instrumentace
V tomto příkladu:
- `MachineFilter`odpovídá aktuálnímu počítači pomocí `'.*'` zástupného znaku.
- `AppFilter='WebAppExclude'`poskytuje klíč `null` instrumentace. Zadaná aplikace nebude instrumentovaná.
- `AppFilter='WebAppOne'`přiřadí zadané aplikaci jedinečný klíč instrumentace.
- `AppFilter='WebAppTwo'`přiřadí zadané aplikaci jedinečný klíč instrumentace.
- `AppFilter` Nakonec taky`'.*'` používá zástupný znak ke spárování se všemi webovými aplikacemi, které se neshodují s předchozími pravidly a přiřazují výchozí klíč instrumentace.
- Jsou přidány mezery pro čitelnost.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parametry

### <a name="-instrumentationkey"></a>– InstrumentationKey
**Požadovanou.** Pomocí tohoto parametru můžete zadat jeden klíč instrumentace pro použití všemi aplikacemi v cílovém počítači.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Požadovanou.** Tento parametr použijte k zadání více klíčů instrumentace a mapování klíčů instrumentace používaných jednotlivými aplikacemi.
Jeden instalační skript pro několik počítačů můžete vytvořit nastavením `MachineFilter`.

> [!IMPORTANT]
> Aplikace budou odpovídat pravidlům v pořadí, v jakém jsou tato pravidla k dispozici. Proto byste měli nejdřív zadat nejvíce specifická pravidla a nejobecnější pravidla.

#### <a name="schema"></a>Schéma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** je požadovaný C# regulární výraz počítače nebo názvu virtuálního počítače.
    - '. * ' bude odpovídat všem
    - Hodnota ComputerName bude odpovídat jenom počítačům se zadaným názvem.
- **AppFilter** je požadovaný C# regulární výraz počítače nebo názvu virtuálního počítače.
    - '. * ' bude odpovídat všem
    - ' ApplicationName ' bude odpovídat pouze aplikacím služby IIS se zadaným názvem.
- **InstrumentationKey** se vyžaduje k tomu, aby bylo možné monitorovat aplikace, které odpovídají předchozím dvěma filtrům.
    - Pokud chcete definovat pravidla pro vyloučení monitorování, ponechte tuto hodnotu null.


### <a name="-verbose"></a>– Verbose
**Společný parametr** Pomocí tohoto přepínače zobrazíte podrobné protokoly.


## <a name="output"></a>Výstup

Ve výchozím nastavení není k dispozici žádný výstup.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Příklad podrobného výstupu z nastavení konfiguračního souboru prostřednictvím-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Příklad podrobného výstupu z nastavení konfiguračního souboru prostřednictvím-InstrumentationKeyMap

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

## <a name="next-steps"></a>Další postup

  Zobrazení telemetrických dat:
 - [Prozkoumejte metriky](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití.
- [Prohledejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) a Diagnostikujte problémy.
- K pokročilejším dotazům [použijte Analytics](../../azure-monitor/app/analytics.md) .
- [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md).
 
 Přidání další telemetrie:
 - [Vytvářejte webové testy](monitor-web-app-availability.md) , abyste se ujistili, že vaše lokalita zůstane v provozu.
- [Přidejte telemetrii webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění volání trasování.
- [Přidejte sadu Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) , abyste mohli vložit trasování a protokolování volání.
 
 Další informace najdete v Monitorování stavu v2:
 - Použijte náš průvodce k [řešení potíží s](status-monitor-v2-troubleshoot.md) monitorování stavu v2.
 - [Získáním konfigurace](status-monitor-v2-api-get-config.md) potvrďte, že vaše nastavení se správně nahrálo.
 - [Získejte stav](status-monitor-v2-api-get-status.md) pro kontrolu monitorování.
