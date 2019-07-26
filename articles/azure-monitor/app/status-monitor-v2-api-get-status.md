---
title: 'Reference k rozhraní API služby Azure Monitorování stavu v2: Získat stav | Microsoft Docs'
description: Reference k rozhraní API pro Monitorování stavu v2 Get-ApplicationInsightsMonitoringStatus. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s místně hostovanými webovými aplikacemi v ASP.NET, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: 50703ede205b42032e9618c1b16b0de2bd7f3a23
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424142"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus"></a>Rozhraní API pro Monitorování stavu v2: Get-ApplicationInsightsMonitoringStatus

Tento článek popisuje rutinu, která je členem [modulu PowerShellu AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis

Tato rutina poskytuje informace o řešení potíží s Monitorování stavu.
Pomocí této rutiny můžete prozkoumat stav monitorování, verzi modulu PowerShellu a zkontrolovat běžící proces.
Tato rutina oznámí informace o verzi a informace o souborech klíčů potřebných pro monitorování.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci PowerShellu s oprávněními správce.

## <a name="examples"></a>Příklady

### <a name="example-application-status"></a>Příklad: Stav aplikace

Spuštěním příkazu `Get-ApplicationInsightsMonitoringStatus` zobrazte stav monitorování webů.

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
- **Identifikátor počítače** je anonymní ID, které slouží k jednoznačné identifikaci vašeho serveru. Pokud vytvoříte žádost o podporu, budeme toto ID potřebovat k nalezení protokolů pro váš server.
- **Výchozí web** je zastavený ve službě IIS.
- Služba **DemoWebApp111** byla spuštěna ve službě IIS, ale neobdržela žádné požadavky. Tato sestava obsahuje nespuštěný proces (ProcessId: Nenalezeno).
- Služba **DemoWebApp222** je spuštěná a monitorovaná (instrumentovaná: true). Na základě konfigurace uživatele instrumentace klíče xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 byla pro tento web shodná.
- **DemoWebApp333** se ručně instrumentoval pomocí sady Application Insights SDK. Monitorování stavu zjistila sadu SDK a nebude monitorovat tuto lokalitu.


### <a name="example-powershell-module-information"></a>Příklad: Informace o modulu PowerShellu

Spusťte příkaz `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` , který zobrazí informace o aktuálním modulu:

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

### <a name="example-runtime-status"></a>Příklad: Stav modulu runtime

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

## <a name="parameters"></a>Parametry

### <a name="no-parameters"></a>(Žádné parametry)

Ve výchozím nastavení tato rutina oznámí stav monitorování webových aplikací.
Tuto možnost použijte, pokud chcete zkontrolovat, jestli se aplikace úspěšně instrumentoval.
Můžete také zkontrolovat, který klíč instrumentace byl spárován s vaší lokalitou.


### <a name="-powershellmodule"></a>-PowerShellModule
**Volitelné**. Pomocí tohoto přepínače můžete nahlásit čísla verzí a cesty knihoven DLL, které jsou požadovány pro monitorování.
Tuto možnost použijte, pokud potřebujete určit verzi jakékoli knihovny DLL, včetně sady Application Insights SDK.

### <a name="-inspectprocess"></a>-InspectProcess

**Volitelné**. Pomocí tohoto přepínače můžete ohlásit, jestli je služba IIS spuštěná.
Stáhne také externí nástroje a určí, zda jsou potřebné knihovny DLL načteny do modulu runtime služby IIS.


Pokud se tento proces z jakéhokoli důvodu nepovede, můžete tyto příkazy spustit ručně:
- iisreset. exe/status
- [handle64. exe](https://docs.microsoft.com/sysinternals/downloads/handle) – p W3wp | Findstr/I "InstrumentationEngine AI". ApplicationInsights"
- [listdlls64. exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) W3wp | Findstr/I "InstrumentationEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Volitelné**. Používáno pouze s InspectProcess. Pomocí tohoto přepínače přeskočíte výzvu uživatele, která se zobrazí před stažením dalších nástrojů.


## <a name="next-steps"></a>Další kroky

 Další informace najdete v Monitorování stavu v2:
 - Použijte náš průvodce k [řešení potíží s](status-monitor-v2-troubleshoot.md) monitorování stavu v2.
