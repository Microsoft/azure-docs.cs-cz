---
title: Řešení potíží s agentem Azure Application Insights a známými problémy | Dokumenty společnosti Microsoft
description: Známé problémy agenta Application Insights a příklady řešení potíží. Sledujte výkon webových stránek bez opětovného nasazení webu. Funguje s ASP.NET webových aplikací hostovaných místně, ve virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 217629ba5c386557455cc2d2b8bd47f85fa8f84e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671150"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Poradce při potížích s agentem Přehledy aplikací (dříve pojmenovaný Stavový monitor v2)

Při povolení monitorování může dojít k problémům, které brání shromažďování dat.
Tento článek obsahuje seznam všech známých problémů a příklady řešení potíží.
Pokud narazíte na problém, který zde není uveden, můžete nás kontaktovat na [GitHubu](https://github.com/Microsoft/ApplicationInsights-Home/issues).

## <a name="known-issues"></a>Známé problémy

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Konfliktní knihovny DLL v adresáři přihrádky aplikace

Pokud se v adresáři bin nacházejí některé z těchto knihoven DLL, monitorování může selhat:

- Soubor Microsoft.ApplicationInsights.dll
- Soubor Microsoft.AspNet.TelemetryCorrelation.dll
- Soubor System.Diagnostics.DiagnosticSource.dll

Některé z těchto knihoven DLL jsou součástí výchozích šablon aplikací Visual Studia, i když je vaše aplikace nepoužívá.
Pomocí nástrojů pro řešení potíží můžete zobrazit symptomatické chování:

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset a zatížení aplikace (bez telemetrie). Prozkoumat s Sysinternals (Handle.exe a ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Konflikt se sdílenou konfigurací služby IIS

Pokud máte cluster webových serverů, je možné, že používáte [sdílenou konfiguraci](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
HttpModule nelze vložit do této sdílené konfigurace.
Spusťte příkaz Povolit na každém webovém serveru a nainstalujte dll do gac každého serveru.

Po spuštění příkazu Povolit proveďte tyto kroky:
1. Přejděte do sdíleného konfiguračního adresáře a vyhledejte soubor applicationHost.config.
2. Přidejte tento řádek do sekce moduly konfigurace:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Vnořené aplikace iIS

Neprovádíme instrumentované vnořené aplikace ve službách IIS ve verzi 1.0.
Sledujeme tento problém [zde](https://github.com/microsoft/ApplicationInsights-Home/issues/369).

### <a name="advanced-sdk-configuration-isnt-available"></a>Pokročilá konfigurace sady SDK není k dispozici.

Konfigurace sady SDK není vystavena koncovému uživateli ve verzi 1.0.
Sledujeme tento problém [zde](https://github.com/microsoft/ApplicationInsights-Home/issues/375).

    
    
## <a name="troubleshooting"></a>Řešení potíží
    
### <a name="troubleshooting-powershell"></a>Poradce při potížích s prostředím PowerShell

#### <a name="determine-which-modules-are-available"></a>Určení modulů, které jsou k dispozici
Pomocí příkazu `Get-Module -ListAvailable` můžete určit, které moduly jsou nainstalovány.

#### <a name="import-a-module-into-the-current-session"></a>Import modulu do aktuální relace
Pokud modul nebyl načten do relace prostředí PowerShell, můžete jej ručně `Import-Module <path to psd1>` načíst pomocí příkazu.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Poradce při potížích s modulem Agent Application Insights

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Seznam příkazů dostupných v modulu Agent Application Insights
Chcete-li `Get-Command -Module Az.ApplicationMonitor` získat dostupné příkazy, spusťte příkaz:

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

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Určení aktuální verze modulu Agent Application Insights
Spuštěním `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` příkazu zobrazíte následující informace o modulu:
   - Verze modulu PowerShell
   - Verze sady SDK application insights
   - Cesty k souborům modulu PowerShell
    
Podrobný popis použití této rutiny naleznete v [odkazu rozhraní API.](status-monitor-v2-api-get-status.md)


### <a name="troubleshooting-running-processes"></a>Poradce při potížích se spuštěných procesů

Můžete zkontrolovat procesy v počítači s přístrojem a zjistit, zda jsou načteny všechny knihovny DLL.
Pokud monitorování funguje, by mělo být načteno alespoň 12 knihoven DLL.

Pomocí `Get-ApplicationInsightsMonitoringStatus -InspectProcess` příkazu zkontrolujte knihovny DLL.

Podrobný popis použití této rutiny naleznete v [odkazu rozhraní API.](status-monitor-v2-api-get-status.md)


### <a name="collect-etw-logs-by-using-perfview"></a>Sbírat protokoly ETW pomocí PerfView

#### <a name="setup"></a>Nastavení

1. Stáhnout PerfView.exe a PerfView64.exe z [GitHubu](https://github.com/Microsoft/perfview/releases).
2. Spusťte soubor PerfView64.exe.
3. Rozbalte **rozšířené možnosti**.
4. Zrušte zaškrtnutí těchto políček:
    - **Zip**
    - **Sloučit**
    - **Kolekce symbolů .NET**
5. Nastavte tyto **další zprostředkovatele**:`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Shromažďování protokolů

1. V příkazové konzole s oprávněními `iisreset /stop` správce vypněte službu IIS a všechny webové aplikace spuštěním příkazu.
2. V perfView vyberte **Spustit kolekci**.
3. V příkazové konzole s oprávněními `iisreset /start` správce spusťte příkaz ke spuštění služby IIS.
4. Zkuste přejít do aplikace.
5. Po načtení aplikace se vraťte na PerfView a vyberte **Zastavit kolekci**.



## <a name="next-steps"></a>Další kroky

- Projděte si [odkaz na rozhraní API,](status-monitor-v2-overview.md#powershell-api-reference) abyste se dozvěděli o parametrech, které jste možná vynechali.
- Pokud narazíte na problém, který zde není uveden, můžete nás kontaktovat na [GitHubu](https://github.com/Microsoft/ApplicationInsights-Home/issues).
