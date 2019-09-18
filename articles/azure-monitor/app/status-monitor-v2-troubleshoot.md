---
title: Řešení potíží se službou Azure Monitorování stavu v2 a známé problémy | Microsoft Docs
description: Známé problémy Monitorování stavu v2 a příklady řešení potíží. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: c3e9bffaf3b533ef8fbe3e32c1dca671fb67c911
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058296"
---
# <a name="troubleshooting-status-monitor-v2"></a>Řešení potíží s Monitorování stavu v2

Pokud povolíte monitorování, může docházet k problémům, které zabraňují shromažďování dat.
V tomto článku jsou uvedené všechny známé problémy, které obsahují příklady řešení potíží.
Pokud se jedná o problém, který zde není uveden, můžete nás kontaktovat na GitHubu [](https://github.com/Microsoft/ApplicationInsights-Home/issues).

## <a name="known-issues"></a>Známé problémy

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Konfliktní knihovny DLL v adresáři bin aplikace

Pokud je některá z těchto knihoven DLL přítomna v adresáři bin, může monitorování selhat:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Některé z těchto knihoven DLL jsou součástí šablon výchozích aplikací sady Visual Studio, a to i v případě, že je vaše aplikace nepoužívá.
Pomocí nástrojů pro řešení potíží se můžete podívat na příznaky chování:

- PerfView
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- Zaiisreset a zatížení aplikace (bez telemetrie). Prozkoumejte pomocí Sysinternals (Handle. exe a ListDLLs. exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Konflikt se sdílenou konfigurací služby IIS

Pokud máte cluster webových serverů, můžete použít [sdílenou konfiguraci](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Do této sdílené konfigurace nelze vložit modul HTTP.
Spusťte příkaz Enable na každém webovém serveru pro instalaci knihovny DLL do mezipaměti GAC každého serveru.

Po spuštění příkazu Enable proveďte tyto kroky:
1. Přejít do sdíleného konfiguračního adresáře a najít soubor applicationHost. config.
2. Přidejte tento řádek do části moduly v konfiguraci:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Vnořené aplikace IIS

Ve verzi 1,0 nemusíme ve službě IIS instrumentovat vnořené aplikace.
Tento problém sledujeme [tady](https://github.com/microsoft/ApplicationInsights-Home/issues/369).

### <a name="advanced-sdk-configuration-isnt-available"></a>Pokročilá konfigurace sady SDK není k dispozici.

Konfigurace sady SDK není pro koncového uživatele vystavena ve verzi 1,0.
Tento problém sledujeme [tady](https://github.com/microsoft/ApplicationInsights-Home/issues/375).

    
    
## <a name="troubleshooting"></a>Řešení potíží
    
### <a name="troubleshooting-powershell"></a>Řešení potíží s PowerShellem

#### <a name="determine-which-modules-are-available"></a>Určete, které moduly jsou k dispozici.
Pomocí `Get-Module -ListAvailable` příkazu můžete určit, které moduly jsou nainstalovány.

#### <a name="import-a-module-into-the-current-session"></a>Import modulu do aktuální relace
Pokud se modul nenačte do relace PowerShellu, můžete ho ručně načíst pomocí `Import-Module <path to psd1>` příkazu.


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Řešení potíží s modulem Monitorování stavu v2

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>Vypíše příkazy dostupné v modulu Monitorování stavu v2.
Spuštěním příkazu `Get-Command -Module Az.ApplicationMonitor` získáte dostupné příkazy:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>Určení aktuální verze modulu Monitorování stavu v2
`Get-ApplicationInsightsMonitoringStatus -PowerShellModule` Spuštěním příkazu zobrazíte následující informace o modulu:
   - Verze modulu PowerShellu
   - Verze sady Application Insights SDK
   - Cesty k souborům modulu PowerShellu
    
Podrobný popis způsobu použití této rutiny najdete v referenčních informacích k [rozhraní API](status-monitor-v2-api-get-status.md) .


### <a name="troubleshooting-running-processes"></a>Odstraňování potíží s běžícími procesy

Můžete zkontrolovat procesy v počítači instrumentace a zjistit, jestli jsou všechny knihovny DLL načtené.
Pokud monitorování funguje, měli byste načíst aspoň 12 knihoven DLL.

K zkontrolování knihoven DLL použijte příkaz.`Get-ApplicationInsightsMonitoringStatus -InspectProcess`

Podrobný popis způsobu použití této rutiny najdete v referenčních informacích k [rozhraní API](status-monitor-v2-api-get-status.md) .


### <a name="collect-etw-logs-by-using-perfview"></a>Shromažďování protokolů ETW pomocí PerfView

#### <a name="setup"></a>Instalace

1. Stáhněte si PerfView. exe a PerfView64. exe [](https://github.com/Microsoft/perfview/releases)z GitHubu.
2. Spusťte PerfView64. exe.
3. Rozbalte položku **Pokročilá nastavení**.
4. Zrušte zaškrtnutí políček:
    - **Věřitel**
    - **sloučení**
    - **Kolekce symbolů .NET**
5. Nastavit tyto **Další zprostředkovatele**:`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Shromažďují se protokoly.

1. V příkazové konzole s oprávněními správce spusťte `iisreset /stop` příkaz pro vypnutí služby IIS a všech webových aplikací.
2. V PerfView vyberte **Spustit shromažďování**.
3. V příkazové konzole s oprávněními `iisreset /start` správce spusťte příkaz a spusťte službu IIS.
4. Zkuste přejít do aplikace.
5. Po načtení vaší aplikace se vraťte na PerfView a vyberte **Zastavit shromažďování**.



## <a name="next-steps"></a>Další kroky

- Podívejte se na [Reference k rozhraní API](status-monitor-v2-overview.md#powershell-api-reference) , kde najdete informace o parametrech, které jste možná vynechali
- Pokud se jedná o problém, který zde není uveden, můžete nás kontaktovat na GitHubu [](https://github.com/Microsoft/ApplicationInsights-Home/issues).
