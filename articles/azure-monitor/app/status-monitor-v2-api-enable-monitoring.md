---
title: 'Stav služby Azure Monitor v2 Reference k rozhraní API: Povolit monitorování | Dokumentace Microsoftu'
description: Stav monitorování v2 API odkaz Enable-ApplicationInsightsMonitoring. Sledování výkonu webu bez opětovného nasazení webu. Funguje s místně hostovanými webovými aplikacemi v ASP.NET, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: 0a443df0c55dc916ef6d12d53811e9d9932370e7
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255920"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>Rozhraní API v2 monitorování stavu: Enable-ApplicationInsightsMonitoring (v0.2.1 alfa)

Tento dokument popisuje rutiny, který je dodáván jako člen [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Popis

Povolit kód bez připojení, monitorování aplikací služby IIS na cílovém počítači.
Tato rutina se upravit soubor applicationHost.config služby IIS a nastavení několik klíčů registru.
Tato rutina vytvoří také applicationinsights.ikey.config, která definuje, která aplikace používá které Instrumentační klíč.
Služba IIS načte RedfieldModule při spuštění, který vloží Application Insights SDK do aplikace, jak tyto aplikace se spouštějí.
Restartujte službu IIS pro vaše změny se projeví.

Po povolení monitorování, doporučujeme používat [Live Metrics](live-stream.md) rychle sledovat, pokud vaše aplikace je odeslání telemetrie.


> [!NOTE] 
> Abyste mohli začít, musíte mít Instrumentační klíč. Další informace najdete v článku [vytvořit nový prostředek](create-new-resource.md#copy-the-instrumentation-key).


> [!IMPORTANT] 
> Tato rutina vyžaduje relaci Powershellu s oprávněními správce a zásady provádění se zvýšenými oprávněními. Čtení [tady](status-monitor-v2-detailed-instructions.md#run-powershell-as-administrator-with-an-elevated-execution-policy) Další informace.

> [!NOTE] 
> Tato rutina se vyžadují, abyste přečtěte si a přijměte naše prohlášení licence a ochrana osobních údajů.


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


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Volitelné.** Chcete-li povolit modul instrumentace ke shromažďování události a zprávy o co se děje během spuštění spravovaného procesu použijte tento přepínač. Mimo jiné včetně závislostí kódy výsledků, příkazy HTTP a Text příkazu SQL. Modul instrumentace se přidat další režii a je vypnuto ve výchozím nastavení.

### <a name="-acceptlicense"></a>-AcceptLicense
**Volitelné.** Použijte tento přepínač tak, aby přijímal příkaz licence a ochrana osobních údajů v bezobslužné instalace.

### <a name="-verbose"></a>-Verbose
**Společný parametr.** Tento přepínač použijte na výstup podrobných protokolů.

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
 - [Zkoumání metrik](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití
- [Prohledávejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) k diagnostice problémů
- [Analýzy](../../azure-monitor/app/analytics.md) pro pokročilejší dotazy
- [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md)
 
 Přidání další telemetrie:
 - [Vytvářejte webové testy](monitor-web-app-availability.md) k Ujistěte se, že web zůstává živý.
- [Přidání telemetrie webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění vložení trasovacích volání.
- [Přidejte sadu Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) , mohli vložit trasování a protokolování volání
 
 Lepší využití v2 monitorování stavu:
 - Pomocí naší příručce ke [Poradce při potížích](status-monitor-v2-troubleshoot.md) v2 monitorování stavu.
 - [Získání konfigurace](status-monitor-v2-api-get-config.md) potvrďte, že nastavení byly zaznamenány správně.
 - [Načíst stav](status-monitor-v2-api-get-status.md) ke kontrole monitorování.
