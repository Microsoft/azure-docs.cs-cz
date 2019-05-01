---
title: 'Stav služby Azure Monitor v2 Reference k rozhraní API: Nastavení konfigurace | Dokumentace Microsoftu'
description: Stav monitorování v2 API odkaz Set-ApplicationInsightsMonitoringConfig. Sledování výkonu webu bez opětovného nasazení webu. Funguje s místně hostovanými webovými aplikacemi v ASP.NET, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: 2ca738d5d79fc73f892922825d4b731e8ee92b72
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870489"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>Rozhraní API v2 monitorování stavu: Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Tento dokument popisuje rutiny, který je dodáván jako člen [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Popis

Nastavení konfiguračního souboru bez nutnosti opakovat úplná přeinstalace. Restartujte službu IIS pro vaše změny se projeví.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci Powershellu s oprávněními správce.


## <a name="examples"></a>Příklady

### <a name="example-with-single-instrumentation-key"></a>Příklad s jeden Instrumentační klíč
V tomto příkladu se všechny aplikace na aktuálním počítači přiřadí jeden Instrumentační klíč.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>Příklad s mapování klíčů instrumentace
V tomto příkladu 
- `MachineFilter` aktuální pomocí počítače se bude shodovat `'.*'` zástupný znak.
- `AppFilter='WebAppExclude'` poskytuje `null` InstrumentationKey. Tato aplikace nebude instrumentován.
- `AppFilter='WebAppOne'` přiřadí tuto konkrétní aplikaci jedinečný Instrumentační klíč.
- `AppFilter='WebAppTwo'` také, přiřadí se tento konkrétní aplikaci jedinečný Instrumentační klíč.
- A konečně `AppFilter` také používá `'.*'` zástupným znakem, který odpovídá jiné webové aplikace není odpovídající starší pravidla a přiřadí výchozí Instrumentační klíč.
- Přidány pro čitelnost pouze mezery.

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
**Povinné.** Tento parametr slouží k poskytování více Instrumentační klíče a mapování aplikace, které chcete použít které Instrumentační klíč. Tím, že nastavíte MachineFilter můžete vytvořit jeden instalační skript pro několik počítačů. 

> [!IMPORTANT] 
> Aplikace bude hledána shoda s pravidly v pořadí, které jste zadali. Proto byste měli zadat nejspecifičtější pravidla první a poslední nejvíce obecná pravidla.

#### <a name="schema"></a>Schéma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** je požadovaná c# regulární výraz názvu počítače nebo virtuálního počítače.
    - ". *" bude odpovídat vše
    - "Název" bude odpovídat jenom počítače s tímto názvem přesné.
- **AppFilter** je požadovaná c# regulární výraz názvu počítače nebo virtuálního počítače.
    - ". *" bude odpovídat vše
    - "ApplicationName" bude odpovídat pouze aplikace služby IIS s tímto názvem přesné.
- **InstrumentationKey** je nutné k povolení monitorování aplikací, které odpovídají výše uvedené dva filtry.
    - Ponechte tuto hodnotu null, pokud chcete definovat pravidla pro vyloučení, monitorování


### <a name="-verbose"></a>-Verbose
**Společný parametr.** Tento přepínač použijte na výstup podrobných protokolů.


## <a name="output"></a>Výstup

Ve výchozím nastavení žádný výstup.

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
