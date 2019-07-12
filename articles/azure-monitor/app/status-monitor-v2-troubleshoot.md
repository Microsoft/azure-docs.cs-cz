---
title: Azure Monitor stavu v2 odstraňování a známé problémy | Dokumentace Microsoftu
description: Známé problémy v2 monitorování stavu a řešení potíží s příklady. Sledování výkonu webu bez opětovného nasazení webu. Funguje s webové aplikace ASP.NET hostované místně, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: df59766ce38ac81568570cd6544ee28808ff8249
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807013"
---
# <a name="troubleshooting-status-monitor-v2"></a>Řešení potíží s stav monitorování v2

Když povolíte monitorování, se mohou vyskytnout problémy, které zabrání shromažďování data.
V tomto článku jsou uvedené všechny známé problémy a řešení problémů s příklady.
Pokud narazíte na problém, který tu není uvedený, můžete nás kontaktovat na [Githubu](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvu o úrovni služeb, a to nedoporučujeme pro produkční úlohy. Některé funkce nemusí být podporované a některé můžou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="known-issues"></a>Známé problémy

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Konfliktní knihovny DLL v adresáři bin aplikace

Pokud některý z těchto knihoven DLL jsou k dispozici v adresáři bin, nemusí podařit monitorování:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Některé z těchto knihoven DLL jsou součástí šablony aplikace výchozí sady Visual Studio, i v případě, že vaše aplikace nepoužívá.
Můžete použít nástroje pro odstraňování potíží zobrazíte napraveny chování:

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

- Příkaz IISReset a aplikace načíst (bez telemetrie). Prozkoumejte s Sysinternals (Handle.exe a ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Je v konfliktu s sdílenou konfiguraci služby IIS

Pokud máte cluster webové servery, je možné, že používáte [sdílenou konfiguraci](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
HttpModule nelze vloženy do této sdílené konfigurace.
Spusťte příkaz povolit na všech webových serverech, nainstalovat knihovny DLL do globální mezipaměti každý server.

Po spuštění příkazu Enable, proveďte tyto kroky:
1. Přejděte do adresáře, sdílenou konfiguraci a najít soubor applicationHost.config.
2. Přidejte následující řádek do části moduly konfigurace:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
## <a name="troubleshooting"></a>Řešení potíží
    
### <a name="troubleshooting-powershell"></a>Řešení potíží s Powershellu

#### <a name="determine-which-modules-are-available"></a>Určit, které moduly jsou k dispozici
Můžete použít `Get-Module -ListAvailable` příkaz k určení, které moduly jsou nainstalovány.

#### <a name="import-a-module-into-the-current-session"></a>Importování modulu do aktuální relace
Pokud modul se nenačetl do relace prostředí PowerShell, můžete ručně načíst ji pomocí `Import-Module <path to psd1>` příkazu.


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Řešení potíží s modulu v2 monitorování stavu

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>Seznam příkazů, které jsou k dispozici v modulu Sledování stavu v2
Spusťte příkaz `Get-Command -Module Az.ApplicationMonitor` zobrazíte dostupné příkazy:

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

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>Zjistit aktuální verzi modulu v2 monitorování stavu
Spustit `Get-ApplicationInsightsMonitoringStatus` příkazu můžete zobrazit následující informace o modulu:
   - Verze modulu prostředí PowerShell
   - Application Insights SDK verze
   - Cesty k souborům modulu prostředí PowerShell
    
Zkontrolujte [reference k rozhraní API](status-monitor-v2-api-get-status.md) podrobný popis toho, jak použít tuto rutinu.


### <a name="troubleshooting-running-processes"></a>Řešení potíží s spuštěné procesy

Můžete si prohlédnout procesy v instrumentovaném počítače k určení, zda jsou načteny všechny knihovny DLL.
Pokud monitorování funguje, by měla být načtena alespoň 12 knihovny DLL.

Použití `Get-ApplicationInsightsMonitoringStatus -InspectProcess` příkaz a zkontrolujte knihovny DLL.

Zkontrolujte [reference k rozhraní API](status-monitor-v2-api-get-status.md) podrobný popis toho, jak použít tuto rutinu.


### <a name="collect-etw-logs-by-using-perfview"></a>Shromažďování protokolů trasování událostí pro Windows pomocí nástroje PerfView

#### <a name="setup"></a>Instalace

1. Stáhněte si PerfView.exe a PerfView64.exe z [Githubu](https://github.com/Microsoft/perfview/releases).
2. Začněte PerfView64.exe.
3. Rozbalte **pokročilé možnosti**.
4. Zrušte zaškrtnutí těchto políček:
    - **PSČ**
    - **sloučení**
    - **Symbol kolekci .NET**
5. Toto nastavení **dalších poskytovatelů**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Shromažďování protokolů

1. V příkazové konzole s oprávněními správce, spusťte `iisreset /stop` příkaz k vypnutí možnosti služby IIS a všech webových aplikací.
2. V PerfView, vyberte **spustit shromažďování**.
3. V příkazové konzole s oprávněními správce, spusťte `iisreset /start` příkaz ke spuštění služby IIS.
4. Došlo k pokusu o přechod do vaší aplikace.
5. Jakmile vaše aplikace je načtena, vraťte se na PerfView a vyberte **zastavit shromažďování**.



## <a name="next-steps"></a>Další kroky

- Zkontrolujte [reference k rozhraní API](status-monitor-v2-overview.md#powershell-api-reference) Další informace o parametrech nebyla provedena.
- Pokud narazíte na problém, který tu není uvedený, můžete nás kontaktovat na [Githubu](https://github.com/Microsoft/ApplicationInsights-Home/issues).
