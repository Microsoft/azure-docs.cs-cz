---
title: Reference k rozhraní API agenta Azure Application Insights .NET
description: Reference k rozhraní API agenta Application Insights. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 7c5c2f1d055ec6270892873548872b20b17b4158
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566893"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Reference k rozhraní API agenta Azure Monitor Application Insights

Tento článek popisuje rutinu, která je členem [modulu PowerShellu AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!NOTE] 
> - Abyste mohli začít, budete potřebovat klíč instrumentace. Další informace najdete v tématu [vytvoření prostředku](create-new-resource.md#copy-the-instrumentation-key).
> - Tato rutina vyžaduje, abyste zkontrolovali a přijali naše licence a prohlášení o zásadách ochrany osobních údajů.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci PowerShellu s oprávněními správce a zásadou spuštění se zvýšenými oprávněními. Další informace najdete v tématu [spuštění PowerShellu jako správce se zvýšenými zásadami spouštění](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).
> - Tato rutina vyžaduje, abyste zkontrolovali a přijali naše licence a prohlášení o zásadách ochrany osobních údajů.
> - Modul instrumentace přidává další režii a je ve výchozím nastavení vypnutý.


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine

Povolí modul instrumentace nastavením některých klíčů registru.
Restartujte službu IIS, aby se změny projevily.

Modul instrumentace může doplnit data shromažďovaná sadami .NET SDK.
Shromažďuje události a zprávy, které popisují spuštění spravovaného procesu. Mezi tyto události a zprávy patří kódy výsledků závislosti, příkazy HTTP a [text příkazu SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Modul instrumentace povolte, pokud:
- Již jste povolili monitorování pomocí rutiny Enable, ale nepovolili jste modul instrumentace.
- Aplikaci jste ručně nastavili pomocí sady .NET SDK a chtěli byste shromažďovat další telemetrii.

### <a name="examples"></a>Příklady

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parametry

#### <a name="-acceptlicense"></a>-AcceptLicense
**Volitelné.** Pomocí tohoto přepínače přijměte licence a prohlášení o zásadách ochrany osobních údajů v bezobslužné instalaci.

#### <a name="-verbose"></a>– Verbose
**Společný parametr** Pomocí tohoto přepínače můžete vyvýstupovat podrobné protokoly.

### <a name="output"></a>Výstup


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Příklad výstupu úspěšného povolení modulu instrumentace

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

Povoluje nekódovatelné monitorování aplikací služby IIS na cílovém počítači.

Tato rutina změní applicationHost.config služby IIS a nastavila některé klíče registru.
Vytvoří také soubor applicationinsights.ikey.config, který definuje klíč instrumentace používaný jednotlivými aplikacemi.
Služba IIS načte RedfieldModule při spuštění, čímž vloží sadu Application Insights SDK do aplikací jako spuštění aplikací.
Restartujte službu IIS, aby se provedené změny projevily.

Po povolení monitorování doporučujeme pomocí [živých metrik](live-stream.md) rychle zjistit, jestli vaše aplikace posílá telemetrii v USA.

### <a name="examples"></a>Příklady

#### <a name="example-with-a-single-instrumentation-key"></a>Příklad s jedním klíčem instrumentace
V tomto příkladu jsou všem aplikacím v aktuálním počítači přiřazený jeden klíč instrumentace.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Příklad s mapou klíče instrumentace
V tomto příkladu:
- `MachineFilter` odpovídá aktuálnímu počítači pomocí `'.*'` zástupného znaku.
- `AppFilter='WebAppExclude'` poskytuje `null` klíč instrumentace. Zadaná aplikace nebude instrumentovaná.
- `AppFilter='WebAppOne'` přiřadí zadané aplikaci jedinečný klíč instrumentace.
- `AppFilter='WebAppTwo'` přiřadí zadané aplikaci jedinečný klíč instrumentace.
- Nakonec `AppFilter` taky používá `'.*'` zástupný znak ke spárování se všemi webovými aplikacemi, které se neshodují s předchozími pravidly a přiřazují výchozí klíč instrumentace.
- Jsou přidány mezery pro čitelnost.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```

> [!NOTE]
> Pojmenování AppFilter v tomto kontextu může být matoucí, `AppFilter` v případě .NET ve službě IIS nastaví filtr regulárního výrazu názvu aplikace (třída HostingEnvironment. název_webu). `VirtualPathFilter` Nastaví filtr regulárních výrazu virtuální cesty (třída HostingEnvironment. ApplicationVirtualPath v případě .NET v IIS). K instrumentaci jedné aplikace byste měli použít VirtualPathFilter následujícím způsobem: `Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap @(@{VirtualPathFilter="^/MyAppName$"; InstrumentationSettings=@{InstrumentationKey='<your ikey>'}})`

### <a name="parameters"></a>Parametry

#### <a name="-instrumentationkey"></a>– InstrumentationKey
**Požadovanou.** Pomocí tohoto parametru můžete zadat jeden klíč instrumentace pro použití všemi aplikacemi v cílovém počítači.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Požadovanou.** Tento parametr použijte k zadání více klíčů instrumentace a mapování klíčů instrumentace používaných jednotlivými aplikacemi.
Jeden instalační skript pro několik počítačů můžete vytvořit nastavením `MachineFilter` .

> [!IMPORTANT]
> Aplikace budou odpovídat pravidlům v pořadí, v jakém jsou tato pravidla k dispozici. Proto byste měli nejdřív zadat nejvíce specifická pravidla a nejobecnější pravidla.

##### <a name="schema"></a>Schéma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** je povinný regulární výraz jazyka C# počítače nebo názvu virtuálního počítače.
    - '. * ' bude odpovídat všem
    - Hodnota ComputerName bude odpovídat jenom počítačům se zadaným přesným názvem.
- **AppFilter** je požadovaný regulární výraz jazyka C# názvu webu služby IIS. Seznam webů na serveru můžete získat spuštěním příkazu [Get-iissite](/powershell/module/iisadministration/get-iissite).
    - '. * ' bude odpovídat všem
    - Možnost ' název_webu ' bude odpovídat pouze stránce služby IIS se zadaným přesným názvem.
- **InstrumentationKey** se vyžaduje k tomu, aby bylo možné monitorovat aplikace, které odpovídají předchozím dvěma filtrům.
    - Pokud chcete definovat pravidla pro vyloučení monitorování, ponechte tuto hodnotu null.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Volitelné.** Tento přepínač použijte, pokud chcete, aby modul instrumentace mohl shromažďovat události a zprávy o tom, co se děje během provádění spravovaného procesu. Mezi tyto události a zprávy patří kódy výsledků závislosti, příkazy HTTP a text příkazu SQL.

Modul instrumentace zvyšuje režii a je ve výchozím nastavení vypnutý.

#### <a name="-acceptlicense"></a>-AcceptLicense
**Volitelné.** Pomocí tohoto přepínače přijměte licence a prohlášení o zásadách ochrany osobních údajů v bezobslužné instalaci.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Pokud máte cluster webových serverů, můžete použít [sdílenou konfiguraci](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Do této sdílené konfigurace nelze vložit modul HTTP.
Tento skript selže a zobrazí se zpráva, že je nutné provést další kroky instalace.
Pomocí tohoto přepínače tuto kontrolu ignorujte a pokračujte v instalaci požadovaných součástí. Další informace najdete v tématu [známé konflikty – with-IIS-Shared-Configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration) .

#### <a name="-verbose"></a>– Verbose
**Společný parametr** Pomocí tohoto přepínače zobrazíte podrobné protokoly.

#### <a name="-whatif"></a>-WhatIf 
**Společný parametr** Tento přepínač použijte k otestování a ověření vstupních parametrů, aniž byste umožnili monitorování.

### <a name="output"></a>Výstup

#### <a name="example-output-from-a-successful-enablement"></a>Příklad výstupu z úspěšného povolení

```powershell
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

## <a name="disable-instrumentationengine"></a>Disable-InstrumentationEngine

Zakáže modul instrumentace odebráním některých klíčů registru.
Restartujte službu IIS, aby se změny projevily.

### <a name="examples"></a>Příklady

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parametry 

#### <a name="-verbose"></a>– Verbose
**Společný parametr** Pomocí tohoto přepínače můžete vyvýstupovat podrobné protokoly.

### <a name="output"></a>Výstup


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Příklad výstupu z úspěšného zakázání modulu instrumentace

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

Zakáže monitorování v cílovém počítači.
Tato rutina odstraní úpravy applicationHost.config služby IIS a odebere klíče registru.

### <a name="examples"></a>Příklady

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parametry 

#### <a name="-verbose"></a>– Verbose
**Společný parametr** Pomocí tohoto přepínače zobrazíte podrobné protokoly.

### <a name="output"></a>Výstup


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Příklad výstupu z úspěšného zakázání monitorování

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
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
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

Načte konfigurační soubor a vytiskne hodnoty do konzoly.

### <a name="examples"></a>Příklady

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Parametry

Nejsou vyžadovány žádné parametry.

### <a name="output"></a>Výstup


##### <a name="example-output-from-reading-the-config-file"></a>Příklad výstupu z čtení konfiguračního souboru

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

Tato rutina poskytuje informace o řešení potíží s Monitorování stavu.
Pomocí této rutiny můžete prozkoumat stav monitorování, verzi modulu PowerShellu a zkontrolovat běžící proces.
Tato rutina oznámí informace o verzi a informace o souborech klíčů potřebných pro monitorování.

### <a name="examples"></a>Příklady

#### <a name="example-application-status"></a>Příklad: stav aplikace

Spuštěním příkazu `Get-ApplicationInsightsMonitoringStatus` Zobrazte stav monitorování webů.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

V tomto příkladu;
- **Identifikátor počítače** je anonymní ID, které slouží k jednoznačné identifikaci vašeho serveru. Pokud vytvoříte žádost o podporu, budeme toto ID potřebovat k nalezení protokolů pro váš server.
- **Výchozí web** je zastavený ve službě IIS.
- Služba **DemoWebApp111** byla spuštěna ve službě IIS, ale neobdržela žádné požadavky. Tato sestava obsahuje nespuštěný proces (ProcessId: Nenalezeno).
- Služba **DemoWebApp222** je spuštěná a monitorovaná (instrumentovaná: true). Na základě konfigurace uživatele instrumentace klíče xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 byla pro tento web shodná.
- **DemoWebApp333** se ručně instrumentoval pomocí sady Application Insights SDK. Monitorování stavu zjistila sadu SDK a nebude monitorovat tuto lokalitu.


#### <a name="example-powershell-module-information"></a>Příklad: informace o modulu PowerShellu

Spusťte příkaz `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` , který zobrazí informace o aktuálním modulu:

```powershell

PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

#### <a name="example-runtime-status"></a>Příklad: běhový stav

Můžete zkontrolovat proces v instrumentované počítači a zjistit, jestli jsou všechny knihovny DLL načtené. Pokud monitorování funguje, měli byste načíst aspoň 12 knihoven DLL.

Spusťte příkaz `Get-ApplicationInsightsMonitoringStatus -InspectProcess`:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

### <a name="parameters"></a>Parametry

#### <a name="no-parameters"></a>(Žádné parametry)

Ve výchozím nastavení tato rutina oznámí stav monitorování webových aplikací.
Tuto možnost použijte, pokud chcete zkontrolovat, jestli se aplikace úspěšně instrumentoval.
Můžete také zkontrolovat, který klíč instrumentace byl spárován s vaší lokalitou.


#### <a name="-powershellmodule"></a>-PowerShellModule
**Volitelné**. Pomocí tohoto přepínače můžete nahlásit čísla verzí a cesty knihoven DLL, které jsou požadovány pro monitorování.
Tuto možnost použijte, pokud potřebujete určit verzi jakékoli knihovny DLL, včetně sady Application Insights SDK.

#### <a name="-inspectprocess"></a>-InspectProcess

**Volitelné**. Pomocí tohoto přepínače můžete ohlásit, jestli je služba IIS spuštěná.
Stáhne také externí nástroje a určí, zda jsou potřebné knihovny DLL načteny do modulu runtime služby IIS.


Pokud se tento proces z jakéhokoli důvodu nepovede, můžete tyto příkazy spustit ručně:
- iisreset.exe/status
- [handle64.exe](/sysinternals/downloads/handle) -p W3wp | Findstr/I "InstrumentationEngine AI". ApplicationInsights
- [listdlls64.exe](/sysinternals/downloads/listdlls) W3wp | Findstr/I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Volitelné**. Používáno pouze s InspectProcess. Pomocí tohoto přepínače přeskočíte výzvu uživatele, která se zobrazí před stažením dalších nástrojů.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Nastaví konfigurační soubor bez provedení úplné přeinstalace.
Restartujte službu IIS, aby se provedené změny projevily.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci PowerShellu s oprávněními správce.


### <a name="examples"></a>Příklady

#### <a name="example-with-a-single-instrumentation-key"></a>Příklad s jedním klíčem instrumentace
V tomto příkladu se všem aplikacím v aktuálním počítači přiřadí jeden klíč instrumentace.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Příklad s mapou klíče instrumentace
V tomto příkladu:
- `MachineFilter` odpovídá aktuálnímu počítači pomocí `'.*'` zástupného znaku.
- `AppFilter='WebAppExclude'` poskytuje `null` klíč instrumentace. Zadaná aplikace nebude instrumentovaná.
- `AppFilter='WebAppOne'` přiřadí zadané aplikaci jedinečný klíč instrumentace.
- `AppFilter='WebAppTwo'` přiřadí zadané aplikaci jedinečný klíč instrumentace.
- Nakonec `AppFilter` taky používá `'.*'` zástupný znak ke spárování se všemi webovými aplikacemi, které se neshodují s předchozími pravidly a přiřazují výchozí klíč instrumentace.
- Jsou přidány mezery pro čitelnost.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>Parametry

#### <a name="-instrumentationkey"></a>– InstrumentationKey
**Požadovanou.** Pomocí tohoto parametru můžete zadat jeden klíč instrumentace pro použití všemi aplikacemi v cílovém počítači.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Požadovanou.** Tento parametr použijte k zadání více klíčů instrumentace a mapování klíčů instrumentace používaných jednotlivými aplikacemi.
Jeden instalační skript pro několik počítačů můžete vytvořit nastavením `MachineFilter` .

> [!IMPORTANT]
> Aplikace budou odpovídat pravidlům v pořadí, v jakém jsou tato pravidla k dispozici. Proto byste měli nejdřív zadat nejvíce specifická pravidla a nejobecnější pravidla.

##### <a name="schema"></a>Schéma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** je povinný regulární výraz jazyka C# počítače nebo názvu virtuálního počítače.
    - '. * ' bude odpovídat všem
    - Hodnota ComputerName bude odpovídat jenom počítačům se zadaným názvem.
- **AppFilter** je povinný regulární výraz jazyka C# počítače nebo názvu virtuálního počítače.
    - '. * ' bude odpovídat všem
    - ' ApplicationName ' bude odpovídat pouze aplikacím služby IIS se zadaným názvem.
- **InstrumentationKey** se vyžaduje k tomu, aby bylo možné monitorovat aplikace, které odpovídají předchozím dvěma filtrům.
    - Pokud chcete definovat pravidla pro vyloučení monitorování, ponechte tuto hodnotu null.


#### <a name="-verbose"></a>– Verbose
**Společný parametr** Pomocí tohoto přepínače zobrazíte podrobné protokoly.


### <a name="output"></a>Výstup

Ve výchozím nastavení není k dispozici žádný výstup.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Příklad podrobného výstupu z nastavení konfiguračního souboru prostřednictvím-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Příklad podrobného výstupu z nastavení konfiguračního souboru prostřednictvím-InstrumentationKeyMap

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

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

Shromažďuje [události ETW](/windows/desktop/etw/event-tracing-portal) z běhového modulu připojení s kódováním. Tato rutina je alternativou ke spuštění [PerfView](https://github.com/microsoft/perfview).

Shromážděné události budou v reálném čase vytištěny do konzoly a uloženy do souboru ETL. Výstupní soubor ETL může otevřít [PerfView](https://github.com/microsoft/perfview) k dalšímu prošetření.

Tato rutina se spustí, dokud nedosáhne časového limitu (výchozí hodnota je 5 minut) nebo se zastaví ručně ( `Ctrl + C` ).

### <a name="examples"></a>Příklady

#### <a name="how-to-collect-events"></a>Jak shromažďovat události

Normálně bychom vás poznamenali, že shromáždíte události, abyste zjistili, proč se vaše aplikace neinstrumentuje.

Modul runtime pro nekódování kódu bude generovat události ETW při spuštění služby IIS a při spuštění aplikace.

Shromažďování těchto událostí:
1. V konzole cmd s oprávněními správce spusťte `iisreset /stop` vypnutí služby IIS a všech webových aplikací.
2. Spustit tuto rutinu
3. V konzole cmd s oprávněními správce spusťte `iisreset /start` službu IIS.
4. Zkuste přejít do aplikace.
5. Po dokončení načítání aplikace ji můžete zastavit ručně ( `Ctrl + C` ) nebo počkat na časový limit.

#### <a name="what-events-to-collect"></a>Jaké události se mají shromáždit

Při shromažďování událostí máte k dispozici tři možnosti:
1. Pomocí přepínače `-CollectSdkEvents` Shromážděte události vydávané ze sady Application Insights SDK.
2. Pomocí přepínače `-CollectRedfieldEvents` Shromážděte události vydávané monitorování stavu a modulu runtime Redfield. Tyto protokoly jsou užitečné při diagnostikování služby IIS a spuštění aplikace.
3. Oba přepínače slouží ke shromáždění obou typů událostí.
4. Ve výchozím nastavení, pokud není zadán žádný přepínač, budou shromažďovány oba typy událostí.


### <a name="parameters"></a>Parametry

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Volitelné.** Pomocí tohoto parametru můžete nastavit, jak dlouho má tento skript shromažďovat události. Výchozí hodnota je 5 minut.

#### <a name="-logdirectory"></a>-LogDirectory
**Volitelné.** Pomocí tohoto přepínače Nastavte výstupní adresář souboru ETL. Ve výchozím nastavení se tento soubor vytvoří v adresáři modulů PowerShellu. Úplná cesta se zobrazí během provádění skriptu.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Volitelné.** Pomocí tohoto přepínače můžete shromažďovat události Application Insights SDK.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Volitelné.** Pomocí tohoto přepínače můžete shromažďovat události z Monitorování stavu a modulu runtime Redfield.

#### <a name="-verbose"></a>– Verbose
**Společný parametr** Pomocí tohoto přepínače můžete vyvýstupovat podrobné protokoly.



### <a name="output"></a>Výstup


#### <a name="example-of-application-startup-logs"></a>Příklad protokolů spuštění aplikace
```powershell
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

  Zobrazení telemetrických dat:
 - [Prozkoumejte metriky](../essentials/metrics-charts.md) pro monitorování výkonu a využití.
- [Prohledejte události a protokoly](./diagnostic-search.md) a Diagnostikujte problémy.
- K pokročilejším dotazům použijte [Analytics](../logs/log-query-overview.md) .
- [Vytváření řídicích panelů](./overview-dashboard.md).
 
 Přidání další telemetrie:
 - [Vytvoření webových testů](monitor-web-app-availability.md) a ověření, jestli web zůstává živý.
- [Přidejte telemetrii webového klienta](./javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění volání trasování.
- [Přidejte sadu Application Insights SDK do kódu](./asp-net.md) , abyste mohli vložit trasování a protokolování volání.
 
 Další Application Insights agenta:
 - Pomocí naší příručky můžete [řešit potíže s](status-monitor-v2-troubleshoot.md) agentem Application Insights.

