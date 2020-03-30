---
title: Odkaz na rozhraní API agenta Azure Application Insights
description: Odkaz na rozhraní API agenta Application Insights. Get-ApplicationInsightsMonitoringStatus. Sledujte výkon webových stránek bez opětovného nasazení webu. Funguje s místně hostovanými webovými aplikacemi v ASP.NET, na virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 159dab4a228c822ef62c45c9ccceff638a9bea45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671252"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringstatus"></a>Rozhraní API agenta Application Insights: Get-ApplicationInsightsMonitoringStatus

Tento článek popisuje rutinu, která je členem [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis

Tato rutina poskytuje informace o řešení potíží s monitorováním stavu.
Tato rutina slouží k prozkoumání stavu monitorování, verze modulu prostředí PowerShell a ke kontrole spuštěného procesu.
Tato rutina bude hlásit informace o verzi a informace o klíčových souborů potřebných pro monitorování.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci prostředí PowerShell s oprávněními správce.

## <a name="examples"></a>Příklady

### <a name="example-application-status"></a>Příklad: Stav aplikace

Spuštěním `Get-ApplicationInsightsMonitoringStatus` příkazu zobrazíte stav monitorování webových serverů.

```
PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus
Machine Identifier:
811D43F7EC807E389FEA2E732381288ACCD70AFFF9F569559AC3A75F023FA639

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


### <a name="example-powershell-module-information"></a>Příklad: Informace o modulu PowerShellu

Spuštěním `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` příkazu zobrazíte informace o aktuálním modulu:

```
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

### <a name="example-runtime-status"></a>Příklad: Stav runtime

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

## <a name="parameters"></a>Parametry

### <a name="no-parameters"></a>(Žádné parametry)

Ve výchozím nastavení bude tato rutina hlásit stav monitorování webových aplikací.
Tuto možnost použijte ke kontrole, pokud byla vaše aplikace úspěšně instrumentována.
Můžete si také prohlédnout, který klíč instrumentace byl spárován s vaším webem.


### <a name="-powershellmodule"></a>-PowerShellModul
**Nepovinné**. Tento přepínač slouží k vykazovat čísla verzí a cesty knihoven DLL potřebných pro monitorování.
Tuto možnost použijte, pokud potřebujete identifikovat verzi libovolné dll, včetně sady Application Insights SDK.

### <a name="-inspectprocess"></a>-InspectProcess

**Nepovinné**. Pomocí tohoto přepínače můžete ohlásit, zda je spuštěna iis.
Bude také stahovat externí nástroje k určení, zda jsou potřebné knihovny DLL načteny do běhu iis.


Pokud se tento proces z nějakého důvodu nezdaří, můžete tyto příkazy spustit ručně:
- iisreset.exe /stav
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr /I "InstrumentationEngineEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngineEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Nepovinné**. Používá se pouze s InspectProcess. Pomocí tohoto přepínače můžete přeskočit výzvu uživatele, která se zobrazí před stažením dalších nástrojů.


## <a name="next-steps"></a>Další kroky

 S agentem Application Insights toho zvládnete víc:
 - Použijte náš průvodce [k řešení potíží s](status-monitor-v2-troubleshoot.md) agentem Application Insights.
