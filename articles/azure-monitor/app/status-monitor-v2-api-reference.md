---
title: Odkaz na rozhraní API rozhraní API azure application insights .Net agent
description: Odkaz na rozhraní API agenta Application Insights. Sledujte výkon webových stránek bez opětovného nasazení webu. Funguje s ASP.NET webových aplikací hostovaných místně, ve virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 02762c4b3af735eb0b4c19aaf450b2b3a416a2be
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733671"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Odkaz na rozhraní API rozhraní API azure monitoru pro přehledy aplikací

Tento článek popisuje rutinu, která je členem [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!NOTE] 
> - Chcete-li začít, potřebujete klíč instrumentace. Další informace naleznete [v tématu Vytvoření prostředku](create-new-resource.md#copy-the-instrumentation-key).
> - Tato rutina vyžaduje, abyste si přečetli a přijali naše licenční a prohlášení o zásadách ochrany osobních údajů.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci prostředí PowerShell s oprávněními správce a zásady zvýšeného spuštění. Další informace naleznete v [tématu Spuštění prostředí PowerShell jako správce se zvýšenými zásadami provádění](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).
> - Tato rutina vyžaduje, abyste si přečetli a přijali naše licenční a prohlášení o zásadách ochrany osobních údajů.
> - Přístrojový stroj přidává další režii a je ve výchozím nastavení vypnutý.


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngineEngine

Povolí instrumentační modul nastavením některých klíčů registru.
Změny se projeví až po restartování služby IIS.

Instrumentační modul může doplnit data shromážděná sadami .NET SDK.
Shromažďuje události a zprávy, které popisují spuštění spravovaného procesu. Tyto události a zprávy zahrnují kódy výsledků závislostí, slovesa HTTP a [text příkazu SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Přístrojový motor povolte, pokud:
- Monitorování jste již povolili pomocí rutiny Povolit, ale nepovolili jste modul instrumentace.
- Ručně jste instrumentovali aplikaci pomocí sad SDK .NET a chcete shromažďovat další telemetrii.

### <a name="examples"></a>Příklady

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parametry

#### <a name="-acceptlicense"></a>-AcceptLicense
**Volitelné.** Pomocí tohoto přepínače můžete přijmout licenci a prohlášení o zásadách ochrany osobních údajů v bezhlavých instalacích.

#### <a name="-verbose"></a>-Podrobné
**Společný parametr.** Tento přepínač použijte k výstupu podrobných protokolů.

### <a name="output"></a>Výstup


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Příklad výstupu z úspěšného povolení přístrojového stroje

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

Umožňuje monitorování aplikací služby IIS v cílovém počítači bez kódů.

Tato rutina upraví iis applicationHost.config a nastaví některé klíče registru.
Vytvoří také soubor applicationinsights.ikey.config, který definuje klíč instrumentace používaný každou aplikací.
Služby IIS načtou modul RedfieldModule při spuštění, který při spuštění aplikace vloží sadu Application Insights SDK do aplikací.
Změny se projeví až po restartování služby IIS.

Po povolení monitorování doporučujeme pomocí [live metrik rychle](live-stream.md) zkontrolovat, jestli nám vaše aplikace posílá telemetrii.

### <a name="examples"></a>Příklady

#### <a name="example-with-a-single-instrumentation-key"></a>Příklad s jedním klíčem instrumentace
V tomto příkladu jsou všem aplikacím v aktuálním počítači přiřazen jeden klíč instrumentace.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Příklad s mapou klíčů instrumentace
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


### <a name="parameters"></a>Parametry

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Povinná hodnota.** Tento parametr slouží k zadání jediného klíče instrumentace pro všechny aplikace v cílovém počítači.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Povinná hodnota.** Tento parametr slouží k zadání více instrumentačních klíčů a mapování instrumentačních klíčů používaných každou aplikací.
Nastavením `MachineFilter`můžete vytvořit jeden instalační skript pro několik počítačů .

> [!IMPORTANT]
> Aplikace se budou shodovat s pravidly v pořadí, v jakém jsou pravidla k dispozici. Proto byste měli nejprve zadat nejkonkrétnější pravidla a nejobecnější pravidla poslední.

##### <a name="schema"></a>Schéma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** je povinný regulární výraz jazyka C# počítače nebo názvu virtuálního počítače.
    - '.*' bude odpovídat všem
    - Název_počítače bude shodovat pouze s počítači s přesným názvem.
- **AppFilter** je povinný regulární výraz jazyka C# názvu webu iis. Seznam webů na serveru můžete získat spuštěním příkazu [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '.*' bude odpovídat všem
    - Název_webu bude odpovídat pouze webu iis s přesným názvem.
- **InstrumentationKey** je nutné povolit monitorování aplikací, které odpovídají předchozí dva filtry.
    - Pokud chcete definovat pravidla pro vyloučení monitorování, ponechte tuto hodnotu nulovou.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Volitelné.** Pomocí tohoto přepínače povolte instrumentační modul shromažďovat události a zprávy o tom, co se děje během provádění spravovaného procesu. Tyto události a zprávy zahrnují kódy výsledků závislostí, slovesa HTTP a text příkazu SQL.

Přístrojové stroje přidává nad hlavou a je vypnutý ve výchozím nastavení.

#### <a name="-acceptlicense"></a>-AcceptLicense
**Volitelné.** Pomocí tohoto přepínače můžete přijmout licenci a prohlášení o zásadách ochrany osobních údajů v bezhlavých instalacích.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Pokud máte cluster webových serverů, je možné, že používáte [sdílenou konfiguraci](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
HttpModule nelze vložit do této sdílené konfigurace.
Tento skript se nezdaří se zprávou, že jsou požadovány další kroky instalace.
Pomocí tohoto přepínače můžete tuto kontrolu ignorovat a pokračovat v instalaci požadavků. Další informace naleznete v [tématu známý konflikt se sdílenou konfigurací](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Podrobné
**Společný parametr.** Tento přepínač slouží k zobrazení podrobných protokolů.

#### <a name="-whatif"></a>-Co když 
**Společný parametr.** Pomocí tohoto přepínače otestujte a ověřte vstupní parametry bez skutečného povolení monitorování.

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

## <a name="disable-instrumentationengine"></a>Funkce Disable-InstrumentationEngine

Zakáže instrumentační modul odebráním některých klíčů registru.
Změny se projeví až po restartování služby IIS.

### <a name="examples"></a>Příklady

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parametry 

#### <a name="-verbose"></a>-Podrobné
**Společný parametr.** Tento přepínač použijte k výstupu podrobných protokolů.

### <a name="output"></a>Výstup


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Příklad výstupu z úspěšného vypnutí instrumentačního stroje

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Zakázat-ApplicationInsightsMonitoring

Zakáže monitorování v cílovém počítači.
Tato rutina odebere úpravy aplikace IISHost.config a odebere klíče registru.

### <a name="examples"></a>Příklady

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parametry 

#### <a name="-verbose"></a>-Podrobné
**Společný parametr.** Tento přepínač slouží k zobrazení podrobných protokolů.

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

Získá konfigurační soubor a vytiskne hodnoty do konzoly.

### <a name="examples"></a>Příklady

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Parametry

Nejsou vyžadovány žádné parametry.

### <a name="output"></a>Výstup


##### <a name="example-output-from-reading-the-config-file"></a>Příklad výstupu ze čtení konfiguračního souboru

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

Tato rutina poskytuje informace o řešení potíží s monitorováním stavu.
Tato rutina slouží k prozkoumání stavu monitorování, verze modulu prostředí PowerShell a ke kontrole spuštěného procesu.
Tato rutina bude hlásit informace o verzi a informace o klíčových souborů potřebných pro monitorování.

### <a name="examples"></a>Příklady

#### <a name="example-application-status"></a>Příklad: Stav aplikace

Spuštěním `Get-ApplicationInsightsMonitoringStatus` příkazu zobrazíte stav monitorování webových serverů.

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
- **Identifikátor počítače** je anonymní ID používané k jednoznačné identifikaci serveru. Pokud vytvoříte žádost o podporu, budeme potřebovat toto ID k vyhledání protokolů pro váš server.
- **Výchozí web** je ve službě IIS zastaven.
- **DemoWebApp111** byl spuštěn ve službě IIS, ale neobdržel žádné požadavky. Tato sestava ukazuje, že neexistuje žádný spuštěný proces (ProcessId: nebyl nalezen).
- **DemoWebApp222** je spuštěn a je monitorován (Instrumented: true). Na základě konfigurace uživatele, Instrumentation Key xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 byl uzavřeno pro tento web.
- **DemoWebApp333** byl ručně instrumentován pomocí sady Application Insights SDK. Sledování stavu zjistilo sadu SDK a nebude tento web monitorovat.


#### <a name="example-powershell-module-information"></a>Příklad: Informace o modulu PowerShellu

Spuštěním `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` příkazu zobrazíte informace o aktuálním modulu:

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

#### <a name="example-runtime-status"></a>Příklad: Stav runtime

Můžete zkontrolovat proces v počítači s přístrojem a zjistit, zda jsou načteny všechny knihovny DLL. Pokud monitorování funguje, by mělo být načteno alespoň 12 knihoven DLL.

Spusťte `Get-ApplicationInsightsMonitoringStatus -InspectProcess`příkaz :


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

Ve výchozím nastavení bude tato rutina hlásit stav monitorování webových aplikací.
Tuto možnost použijte ke kontrole, pokud byla vaše aplikace úspěšně instrumentována.
Můžete si také prohlédnout, který klíč instrumentace byl spárován s vaším webem.


#### <a name="-powershellmodule"></a>-PowerShellModul
**Nepovinné**. Tento přepínač slouží k vykazovat čísla verzí a cesty knihoven DLL potřebných pro monitorování.
Tuto možnost použijte, pokud potřebujete identifikovat verzi libovolné dll, včetně sady Application Insights SDK.

#### <a name="-inspectprocess"></a>-InspectProcess

**Nepovinné**. Pomocí tohoto přepínače můžete ohlásit, zda je spuštěna iis.
Bude také stahovat externí nástroje k určení, zda jsou potřebné knihovny DLL načteny do běhu iis.


Pokud se tento proces z nějakého důvodu nezdaří, můžete tyto příkazy spustit ručně:
- iisreset.exe /stav
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr /I "InstrumentationEngineEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngineEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Nepovinné**. Používá se pouze s InspectProcess. Pomocí tohoto přepínače můžete přeskočit výzvu uživatele, která se zobrazí před stažením dalších nástrojů.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Nastaví konfigurační soubor bez úplné přeinstalace.
Změny se projeví až po restartování služby IIS.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci prostředí PowerShell s oprávněními správce.


### <a name="examples"></a>Příklady

#### <a name="example-with-a-single-instrumentation-key"></a>Příklad s jedním klíčem instrumentace
V tomto příkladu bude všem aplikacím v aktuálním počítači přiřazen jeden klíč instrumentace.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Příklad s mapou klíčů instrumentace
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

### <a name="parameters"></a>Parametry

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Povinná hodnota.** Tento parametr slouží k zadání jediného klíče instrumentace pro všechny aplikace v cílovém počítači.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Povinná hodnota.** Tento parametr slouží k zadání více instrumentačních klíčů a mapování instrumentačních klíčů používaných každou aplikací.
Nastavením `MachineFilter`můžete vytvořit jeden instalační skript pro několik počítačů .

> [!IMPORTANT]
> Aplikace se budou shodovat s pravidly v pořadí, v jakém jsou pravidla k dispozici. Proto byste měli nejprve zadat nejkonkrétnější pravidla a nejobecnější pravidla poslední.

##### <a name="schema"></a>Schéma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** je povinný regulární výraz jazyka C# počítače nebo názvu virtuálního počítače.
    - '.*' bude odpovídat všem
    - Název_počítače bude odpovídat pouze počítačům se zadaným názvem.
- **AppFilter** je povinný regulární výraz jazyka C# názvu počítače nebo virtuálního počítače.
    - '.*' bude odpovídat všem
    - Název_aplikace bude shodovat pouze aplikace služby IIS se zadaným názvem.
- **InstrumentationKey** je nutné povolit monitorování aplikací, které odpovídají předchozí dva filtry.
    - Pokud chcete definovat pravidla pro vyloučení monitorování, ponechte tuto hodnotu nulovou.


#### <a name="-verbose"></a>-Podrobné
**Společný parametr.** Tento přepínač slouží k zobrazení podrobných protokolů.


### <a name="output"></a>Výstup

Ve výchozím nastavení žádný výstup.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Příklad podrobného výstupu z nastavení konfiguračního souboru přes -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Příklad podrobného výstupu z nastavení konfiguračního souboru přes -InstrumentationKeyMap

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

## <a name="start-applicationinsightsmonitoringtrace"></a>Spuštění aplikaceInsightsMonitoringTrace

Shromažďuje [Události ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) z bezkódového připojení runtime. Tato rutina je alternativou ke spuštění [PerfView](https://github.com/microsoft/perfview).

Shromážděné události budou vytištěny do konzoly v reálném čase a uloženy do souboru ETL. Výstupní soubor ETL lze otevřít [PerfView](https://github.com/microsoft/perfview) pro další šetření.

Tato rutina bude spuštěna, dokud nedosáhne doby trvání časového limitu`Ctrl + C`(výchozí 5 minut) nebo dokud není zastavena ručně ( ).

### <a name="examples"></a>Příklady

#### <a name="how-to-collect-events"></a>Jak shromažďovat události

Za normálních okolností bychom vás požádat, abyste shromažďovat události, aby prošetřila, proč vaše aplikace není instrumentované.

Bezkódový připojit runtime bude vyzařovat Události ETW při spuštění služby IIS a při spuštění aplikace.

Chcete-li shromáždit tyto události:
1. V konzoli cmd s oprávněními správce spouštějte `iisreset /stop` Vypnutí služby IIS a všech webových aplikací.
2. Spustit tuto rutinu
3. V konzoli cmd s oprávněními správce spusťte `iisreset /start` Spuštění služby IIS.
4. Zkuste přejít do aplikace.
5. Po dokončení načítání aplikace ji můžete ručně`Ctrl + C`zastavit ( ) nebo počkat na časový rámec.

#### <a name="what-events-to-collect"></a>Jaké události sbírat

Při shromažďování událostí máte tři možnosti:
1. Přepínač slouží `-CollectSdkEvents` ke shromažďování událostí vyzařovaných ze sady Application Insights SDK.
2. Přepínač slouží `-CollectRedfieldEvents` ke shromažďování událostí vyzařovaných sledováním stavu a redfieldovým runtime. Tyto protokoly jsou užitečné při diagnostice služby IIS a spuštění aplikace.
3. Pomocí obou přepínačů můžete shromažďovat oba typy událostí.
4. Ve výchozím nastavení, pokud není zadán žádný přepínač, budou shromažďovány oba typy událostí.


### <a name="parameters"></a>Parametry

#### <a name="-maxdurationinminutes"></a>-MaxdurationInMinutes
**Volitelné.** Tento parametr slouží k nastavení, jak dlouho by měl tento skript shromažďovat události. Výchozí hodnota je 5 minut.

#### <a name="-logdirectory"></a>-LogDirectory
**Volitelné.** Tento přepínač slouží k nastavení výstupního adresáře souboru ETL. Ve výchozím nastavení bude tento soubor vytvořen v adresáři Modules prostředí PowerShell. Úplná cesta se zobrazí během spuštění skriptu.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Volitelné.** Tento přepínač slouží ke shromažďování událostí sady Application Insights SDK.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldUdálosti
**Volitelné.** Tento přepínač slouží ke shromažďování událostí z monitoru stavu a runtime Redfield.

#### <a name="-verbose"></a>-Podrobné
**Společný parametr.** Tento přepínač použijte k výstupu podrobných protokolů.



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
 - [Prozkoumejte metriky](../../azure-monitor/app/metrics-explorer.md) a sledujte výkon a využití.
- [Hledat události a protokoly](../../azure-monitor/app/diagnostic-search.md) diagnostikovat problémy.
- Pro pokročilejší dotazy používejte [analýzy.](../../azure-monitor/app/analytics.md)
- [Vytvořte řídicí panely](../../azure-monitor/app/overview-dashboard.md).
 
 Přidání další telemetrie:
 - [Vytvoření webových testů](monitor-web-app-availability.md) a ověření, jestli web zůstává živý.
- [Přidejte telemetrickou službu webového klienta,](../../azure-monitor/app/javascript.md) chcete-li zobrazit výjimky z kódu webové stránky a povolit volání trasování.
- [Přidejte do kódu sadou SDK application insights,](../../azure-monitor/app/asp-net.md) abyste mohli vkládat volání trasování a protokolování.
 
 S agentem Application Insights toho zvládnete víc:
 - Použijte náš průvodce [k řešení potíží s](status-monitor-v2-troubleshoot.md) agentem Application Insights.






