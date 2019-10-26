---
title: Reference k rozhraní API agenta Azure Application Insights
description: Reference k rozhraní API agenta Application Insights. Enable-ApplicationInsightsMonitoring. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dccd7e617174bef4a85cb6293cbcc459542310f9
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899711"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Rozhraní API pro Application Insights agenta: Enable-ApplicationInsightsMonitoring

Tento článek popisuje rutinu, která je členem [modulu PowerShellu AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis

Povoluje nekódovatelné monitorování aplikací služby IIS na cílovém počítači.

Tato rutina změní soubor applicationHost. config služby IIS a nastavila některé klíče registru.
Vytvoří také soubor ApplicationInsights. ikey. config, který definuje klíč instrumentace používaný jednotlivými aplikacemi.
Služba IIS načte RedfieldModule při spuštění, čímž vloží sadu Application Insights SDK do aplikací jako spuštění aplikací.
Restartujte službu IIS, aby se provedené změny projevily.

Po povolení monitorování doporučujeme pomocí [živých metrik](live-stream.md) rychle zjistit, jestli vaše aplikace posílá telemetrii v USA.


> [!NOTE] 
> - Abyste mohli začít, budete potřebovat klíč instrumentace. Další informace najdete v tématu [vytvoření prostředku](create-new-resource.md#copy-the-instrumentation-key).
> - Tato rutina vyžaduje, abyste zkontrolovali a přijali naše licence a prohlášení o zásadách ochrany osobních údajů.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci PowerShellu s oprávněními správce a zásadou spuštění se zvýšenými oprávněními. Další informace najdete v tématu [spuštění PowerShellu jako správce se zvýšenými zásadami spouštění](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Příklady

### <a name="example-with-a-single-instrumentation-key"></a>Příklad s jedním klíčem instrumentace
V tomto příkladu jsou všem aplikacím v aktuálním počítači přiřazený jeden klíč instrumentace.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Příklad s mapou klíče instrumentace
V tomto příkladu:
- `MachineFilter` odpovídá aktuálnímu počítači pomocí zástupného znaku `'.*'`.
- `AppFilter='WebAppExclude'` poskytuje klíč instrumentace `null`. Zadaná aplikace nebude instrumentovaná.
- `AppFilter='WebAppOne'` přiřadí zadané aplikaci jedinečný klíč instrumentace.
- `AppFilter='WebAppTwo'` přiřadí zadané aplikaci jedinečný klíč instrumentace.
- Nakonec `AppFilter` také používá zástupný znak `'.*'` pro porovnání všech webových aplikací, které neodpovídají předchozím pravidlům a přiřadí výchozí klíč instrumentace.
- Jsou přidány mezery pro čitelnost.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

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
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** je požadovaný C# regulární výraz počítače nebo názvu virtuálního počítače.
    - '. * ' bude odpovídat všem
    - Hodnota ComputerName bude odpovídat jenom počítačům se zadaným přesným názvem.
- **AppFilter** je požadovaný C# regulární výraz názvu webu služby IIS. Seznam webů na serveru můžete získat spuštěním příkazu [Get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '. * ' bude odpovídat všem
    - Možnost ' název_webu ' bude odpovídat pouze stránce služby IIS se zadaným přesným názvem.
- **InstrumentationKey** se vyžaduje k tomu, aby bylo možné monitorovat aplikace, které odpovídají předchozím dvěma filtrům.
    - Pokud chcete definovat pravidla pro vyloučení monitorování, ponechte tuto hodnotu null.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Volitelné.** Tento přepínač použijte, pokud chcete, aby modul instrumentace mohl shromažďovat události a zprávy o tom, co se děje během provádění spravovaného procesu. Mezi tyto události a zprávy patří kódy výsledků závislosti, příkazy HTTP a text příkazu SQL.

Modul instrumentace zvyšuje režii a je ve výchozím nastavení vypnutý.

### <a name="-acceptlicense"></a>-AcceptLicense
**Volitelné.** Pomocí tohoto přepínače přijměte licence a prohlášení o zásadách ochrany osobních údajů v bezobslužné instalaci.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Pokud máte cluster webových serverů, můžete použít [sdílenou konfiguraci](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Do této sdílené konfigurace nelze vložit modul HTTP.
Tento skript selže a zobrazí se zpráva, že je nutné provést další kroky instalace.
Pomocí tohoto přepínače tuto kontrolu ignorujte a pokračujte v instalaci požadovaných součástí. Další informace najdete v tématu [známé konflikty – with-IIS-Shared-Configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration) .

### <a name="-verbose"></a>– Verbose
**Společný parametr** Pomocí tohoto přepínače zobrazíte podrobné protokoly.

### <a name="-whatif"></a>-WhatIf 
**Společný parametr** Tento přepínač použijte k otestování a ověření vstupních parametrů, aniž byste umožnili monitorování.

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
 - [Prozkoumejte metriky](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití.
- [Prohledejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) a Diagnostikujte problémy.
- K pokročilejším dotazům [použijte Analytics](../../azure-monitor/app/analytics.md) .
- [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md).
 
 Přidání další telemetrie:
 - [Vytvářejte webové testy](monitor-web-app-availability.md) , abyste se ujistili, že vaše lokalita zůstane v provozu.
- [Přidejte telemetrii webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění volání trasování.
- [Přidejte sadu Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) , abyste mohli vložit trasování a protokolování volání.
 
 Další Application Insights agenta:
 - Pomocí naší příručky můžete [řešit potíže s](status-monitor-v2-troubleshoot.md) agentem Application Insights.
 - [Získáním konfigurace](status-monitor-v2-api-get-config.md) potvrďte, že vaše nastavení se správně nahrálo.
 - [Získejte stav](status-monitor-v2-api-get-status.md) pro kontrolu monitorování.
