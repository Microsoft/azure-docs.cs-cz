---
title: Agent Azure Application Insights – začínáme | Dokumenty společnosti Microsoft
description: Průvodce rychlým startem pro agenta Application Insights. Sledujte výkon webových stránek bez opětovného nasazení webu. Funguje s ASP.NET webových aplikací hostovaných místně, ve virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 7819de1f3dfab7f934421de86c0481d2e063f7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671184"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Začínáme s agentem Azure Monitor Application Insights pro místní servery

Tento článek obsahuje příkazy rychlého startu, u kterých se očekává, že budou fungovat ve většině prostředí.
Pokyny závisí na Galerii Prostředí PowerShell distribuovat aktualizace.
Tyto příkazy podporují `-Proxy` parametr Prostředí PowerShell.

Vysvětlení těchto příkazů, pokynů k vlastním uzajvám a informací o řešení potíží naleznete v [podrobných pokynech](status-monitor-v2-detailed-instructions.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="download-and-install-via-powershell-gallery"></a>Stažení a instalace přes PowerShell Gallery

### <a name="install-prerequisites"></a>Požadavky na instalaci
Spusťte PowerShell jako správce.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Zavřete prostředí PowerShell.

### <a name="install-application-insights-agent"></a>Instalace agenta Přehledů aplikací
Spusťte PowerShell jako správce.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Povolení monitorování
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Stažení a ruční instalace (možnost offline)
### <a name="download-the-module"></a>Stáhněte si modul
Ručně stáhněte nejnovější verzi modulu z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-application-insights-agent"></a>Rozbalit a nainstalovat agenta Application Insights
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Povolení monitorování
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Další kroky

 Zobrazení telemetrických dat:

- [Prozkoumejte metriky](../../azure-monitor/app/metrics-explorer.md) a sledujte výkon a využití.
- [Hledat události a protokoly](../../azure-monitor/app/diagnostic-search.md) diagnostikovat problémy.
- Pro pokročilejší dotazy [použijte službu Analytics.](../../azure-monitor/app/analytics.md)
- [Vytvořte řídicí panely](../../azure-monitor/app/overview-dashboard.md).

 Přidání další telemetrie:

- [Vytvoření webových testů](monitor-web-app-availability.md) a ověření, jestli web zůstává živý.
- [Přidejte telemetrickou službu webového klienta,](../../azure-monitor/app/javascript.md) chcete-li zobrazit výjimky z kódu webové stránky a povolit volání trasování.
- [Přidejte do kódu sadou SDK application insights,](../../azure-monitor/app/asp-net.md) abyste mohli vkládat volání trasování a protokolování.

S agentem Application Insights toho zvládnete víc:

- Podrobné pokyny k vysvětlení příkazů, které naleznete zde, naleznete v [podrobných pokynech.](status-monitor-v2-detailed-instructions.md)
- Použijte náš průvodce [k řešení potíží s](status-monitor-v2-troubleshoot.md) agentem Application Insights.
