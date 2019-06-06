---
title: 'Azure Monitor stavu v2 reference k rozhraní API: Nastavení konfigurace | Dokumentace Microsoftu'
description: Odkaz na stav monitorování v2 API. Set-ApplicationInsightsMonitoringConfig. Sledování výkonu webu bez opětovného nasazení webu. Funguje s webové aplikace ASP.NET hostované místně, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: 562ce8a4267370be9b049e3b56f213f82deb89c0
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734993"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>Rozhraní API v2 monitorování stavu: Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Tento dokument popisuje rutiny, která je členem skupiny [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvu o úrovni služeb, a to nedoporučujeme pro produkční úlohy. Některé funkce nemusí být podporované a některé můžou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Popis

Nastaví konfigurační soubor bez způsobem úplná přeinstalace.
Restartujte službu IIS pro vaše změny se projeví.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci Powershellu s oprávněními správce.


## <a name="examples"></a>Příklady

### <a name="example-with-a-single-instrumentation-key"></a>Příklad s jeden Instrumentační klíč
V tomto příkladu se všechny aplikace v aktuálním počítači přiřadí jeden Instrumentační klíč.

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
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

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
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** je požadované C# regulární výraz počítače nebo název virtuálního počítače.
    - ". *" bude odpovídat vše
    - "Název" bude odpovídat jen počítače se zadaným názvem.
- **AppFilter** je požadované C# regulární výraz počítače nebo název virtuálního počítače.
    - ". *" bude odpovídat vše
    - "ApplicationName" bude odpovídat pouze aplikace služby IIS se zadaným názvem.
- **InstrumentationKey** je nutné k povolení monitorování aplikací, které odpovídají předchozí dva filtry.
    - Ponechte tuto hodnotu null, pokud chcete definovat pravidla pro vyloučení monitorování.


### <a name="-verbose"></a>-Verbose
**Společný parametr.** Chcete-li zobrazit podrobné protokoly použijte tento přepínač.


## <a name="output"></a>Výstup

Ve výchozím nastavení, žádný výstup.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Podrobný příklad výstupu z nastavení souboru config prostřednictvím - InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Podrobný příklad výstupu z nastavení souboru config prostřednictvím - InstrumentationKeyMap

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
 - [Zkoumání metrik](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití.
- [Prohledávejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) k diagnostice problémů.
- [Použití analýzy](../../azure-monitor/app/analytics.md) pro pokročilejší dotazy.
- [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md).
 
 Přidání další telemetrie:
 - [Vytvářejte webové testy](monitor-web-app-availability.md) k Ujistěte se, že web zůstává živý.
- [Přidání telemetrie webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a povolit trasování volání.
- [Přidejte Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) abyste mohli vložit trasování a protokolování volání
 
 Lepší využití v2 monitorování stavu:
 - Pomocí naší příručce ke [Poradce při potížích s](status-monitor-v2-troubleshoot.md) v2 monitorování stavu.
 - [Získání konfigurace](status-monitor-v2-api-get-config.md) potvrďte, že nastavení byly zaznamenány správně.
 - [Načíst stav](status-monitor-v2-api-get-status.md) ke kontrole monitorování.
