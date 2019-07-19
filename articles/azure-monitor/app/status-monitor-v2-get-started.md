---
title: Azure Monitorování stavu v2 – Začínáme | Microsoft Docs
description: Průvodce rychlým startem pro Monitorování stavu v2. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: a0c836c8ef947e190a0090b3435eec1c53ded436
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326259"
---
# <a name="get-started-with-status-monitor-v2"></a>Začínáme s Monitorování stavu v2

Tento článek obsahuje příkazy rychlého startu očekávané pro fungování většiny prostředí.
Pokyny závisí na Galerie prostředí PowerShell k distribuci aktualizací.
Tyto příkazy podporují parametr prostředí `-Proxy` PowerShell.

Vysvětlení těchto příkazů, pokyny k přizpůsobení a informace o řešení potíží najdete v [podrobných pokynech](status-monitor-v2-detailed-instructions.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="download-and-install-via-powershell-gallery"></a>Stažení a instalace prostřednictvím Galerie prostředí PowerShell

### <a name="install-prerequisites"></a>Požadavky na instalaci
Spusťte PowerShell jako správce.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Zavřete PowerShell.

### <a name="install-status-monitor-v2"></a>Nainstalovat Monitorování stavu v2
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
    
        
## <a name="download-and-install-manually-offline-option"></a>Ruční stažení a instalace (možnost offline)
### <a name="download-the-module"></a>Stáhnout modul
Ručně stáhněte nejnovější verzi modulu z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-status-monitor-v2"></a>Extrahování a instalace Monitorování stavu v2
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



## <a name="next-steps"></a>Další postup

 Zobrazení telemetrických dat:

- [Prozkoumejte metriky](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití.
- [Prohledejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) a Diagnostikujte problémy.
- K pokročilejším dotazům [použijte Analytics](../../azure-monitor/app/analytics.md) .
- [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md).

 Přidání další telemetrie:

- [Vytvářejte webové testy](monitor-web-app-availability.md) , abyste se ujistili, že vaše lokalita zůstane v provozu.
- [Přidejte telemetrii webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění volání trasování.
- [Přidejte sadu Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) , abyste mohli vložit trasování a protokolování volání.

Další informace najdete v Monitorování stavu v2:

- Přečtěte si [podrobné pokyny](status-monitor-v2-detailed-instructions.md) pro vysvětlení příkazů, které najdete tady.
- Použijte náš průvodce k [řešení potíží s](status-monitor-v2-troubleshoot.md) monitorování stavu v2.
