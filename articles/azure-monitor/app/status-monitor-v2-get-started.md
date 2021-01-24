---
title: Agent Azure Application Insights – Začínáme | Microsoft Docs
description: Průvodce rychlým startem pro Application Insights agenta. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 01/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f340b4fdf7fef6daecf8801d16d19aee9b89e4d5
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746454"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Začínáme s agentem Azure Monitor Application Insights pro místní servery

Tento článek obsahuje příkazy rychlého startu očekávané pro fungování většiny prostředí.
Pokyny závisí na Galerie prostředí PowerShell k distribuci aktualizací.
Tyto příkazy podporují parametr prostředí PowerShell `-Proxy` .

Vysvětlení těchto příkazů, pokyny k přizpůsobení a informace o řešení potíží najdete v [podrobných pokynech](status-monitor-v2-detailed-instructions.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="download-and-install-via-powershell-gallery"></a>Stažení a instalace prostřednictvím Galerie prostředí PowerShell

### <a name="install-prerequisites"></a>Požadavky na instalaci

> [!NOTE]
> Od dubna 2020 Galerie prostředí PowerShell zastaralou TLS 1,1 a 1,0.
>
> Požadavky na additionnal, které byste mohli potřebovat, najdete v tématu [podpora Galerie prostředí POWERSHELL TLS](https://devblogs.microsoft.com/powershell/powershell-gallery-tls-support).
>

Spusťte PowerShell jako správce.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Zavřete PowerShell.

### <a name="install-application-insights-agent"></a>Nainstalovat agenta Application Insights
Spusťte PowerShell jako správce.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Povolení monitorování
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -ConnectionString xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Ruční stažení a instalace (možnost offline)
### <a name="download-the-module"></a>Stáhnout modul
Ručně stáhněte nejnovější verzi modulu z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-application-insights-agent"></a>Extrahování a instalace agenta Application Insights
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Povolení monitorování
```powershell
Enable-ApplicationInsightsMonitoring -ConnectionString xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Další kroky

 Zobrazení telemetrických dat:

- [Prozkoumejte metriky](../platform/metrics-charts.md) pro monitorování výkonu a využití.
- [Prohledejte události a protokoly](./diagnostic-search.md) a Diagnostikujte problémy.
- K pokročilejším dotazům [použijte Analytics](../log-query/log-query-overview.md) .
- [Vytváření řídicích panelů](./overview-dashboard.md).

 Přidání další telemetrie:

- [Vytvoření webových testů](monitor-web-app-availability.md) a ověření, jestli web zůstává živý.
- [Přidejte telemetrii webového klienta](./javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění volání trasování.
- [Přidejte sadu Application Insights SDK do kódu](./asp-net.md) , abyste mohli vložit trasování a protokolování volání.

Další Application Insights agenta:

- Přečtěte si [podrobné pokyny](status-monitor-v2-detailed-instructions.md) pro vysvětlení příkazů, které najdete tady.
- Pomocí naší příručky můžete [řešit potíže s](status-monitor-v2-troubleshoot.md) agentem Application Insights.

