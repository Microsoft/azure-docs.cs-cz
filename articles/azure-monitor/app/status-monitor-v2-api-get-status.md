---
title: 'Azure Monitor stavu v2 reference k rozhraní API: Získat stav | Dokumentace Microsoftu'
description: Odkaz na stav monitorování v2 API. Get-ApplicationInsightsMonitoringStatus. Sledování výkonu webu bez opětovného nasazení webu. Funguje s místně hostovanými webovými aplikacemi v ASP.NET, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: e579db587d5f56aecd60f584ea4805dd4ac1bf98
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718353"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus-v040-alpha"></a>Rozhraní API v2 monitorování stavu: Get-ApplicationInsightsMonitoringStatus (v0.4.0-alpha)

Tento článek popisuje rutiny, která je členem skupiny [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvu o úrovni služeb, a to nedoporučujeme pro produkční úlohy. Některé funkce nemusí být podporované a některé můžou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Popis

Tato rutina poskytuje informace o monitorování stavu řešení potíží.
Tuto rutinu použijte k prozkoumání stav monitorování, verze modulu PowerShell a ke kontrole spuštěnému procesu.
Tato rutina oznámí informace o verzi a informace o klíčových soubory potřebné pro monitorování.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci Powershellu s oprávněními správce.

## <a name="examples"></a>Příklady

### <a name="example-application-status"></a>Příklad: Stav aplikace

Spusťte příkaz `Get-ApplicationInsightsMonitoringStatus` zobrazíte stav monitorování webových serverů.

```
Machine Identifier:
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
- **Počítač identifikátor** je anonymní ID sloužící k jednoznačné identifikaci serveru. Pokud vytvoříte žádost o podporu, budeme potřebovat toto ID najít protokoly pro váš server.
- **Výchozí webová stránka** se zastaví ve službě IIS
- **DemoWebApp111** byla spuštěna ve službě IIS, ale nedostal žádné požadavky. Tato sestava uvádí, že neexistuje žádná spuštěný proces (ProcessId: nebyl nalezen).
- **DemoWebApp222** běží a je monitorována (instrumentovaného: true). V závislosti na konfiguraci uživatele, instrumentační klíč xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 odpovídal pro tuto lokalitu.
- **DemoWebApp333** byl ručně instrumentován pomocí Application Insights SDK. Monitorování stavu zjistil sady SDK a tento server nebude monitorovat.


### <a name="example-powershell-module-information"></a>Příklad: Informace o modulu prostředí PowerShell

Spusťte příkaz `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` zobrazíte informace o aktuální modul:

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

Můžete si prohlédnout procesu instrumentované počítače a zjistěte, jestli jsou načteny všechny knihovny DLL. Pokud monitorování funguje, by měla být načtena alespoň 12 knihovny DLL.

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

### <a name="no-parameters"></a>(Bez parametrů)

Ve výchozím nastavení budou tato rutina hlásit stav monitorování webových aplikací.
Tuto možnost použijte, pokud vaše aplikace byla instrumentována úspěšně zkontrolovat.
Můžete také zkontrolovat, který byl odpovídají Instrumentační klíč vašeho webu.


### <a name="-powershellmodule"></a>-PowerShellModule
**Volitelné**. Použijte tento přepínač k hlášení čísla verzí a cesty knihoven DLL, které jsou nezbytné pro monitorování.
Tuto možnost použijte, pokud je potřeba identifikovat verzi libovolnou knihovnou DLL, včetně Application Insights SDK.

### <a name="-inspectprocess"></a>-InspectProcess

**Volitelné**. Oznamuje, zda je spuštěna služba IIS použijte tento přepínač.
Také je stažen externí nástroje k určení, zda potřebné knihovny DLL jsou zavedeny do modulu runtime služby IIS.


Pokud tento proces z nějakého důvodu selže, můžete ručně spustit tyto příkazy:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) w3wp -p | findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Volitelné**. Použít pouze s InspectProcess. Použijte tento přepínač pro přeskočení výzvy uživateli, který se zobrazí předtím, než se stáhnou další nástroje.


## <a name="next-steps"></a>Další postup

 Lepší využití v2 monitorování stavu:
 - Pomocí naší příručce ke [Poradce při potížích s](status-monitor-v2-troubleshoot.md) v2 monitorování stavu.
