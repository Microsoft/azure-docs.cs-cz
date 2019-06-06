---
title: Azure Monitor stavu v2 – Začínáme | Dokumentace Microsoftu
description: Příručka rychlý start pro monitorování stavu v2. Sledování výkonu webu bez opětovného nasazení webu. Funguje s webové aplikace ASP.NET hostované místně, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: 4da9d8e8efd5f70718f18b2e8e35ea6b5adf6757
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734978"
---
# <a name="get-started-with-status-monitor-v2"></a>Začínáme s monitorování stavu v2

Tento článek obsahuje příkazy rychlého startu má fungovat pro většinu prostředí.
Podle pokynů, závisí na galerii prostředí PowerShell k distribuci aktualizací.
Tyto příkazy podporují PowerShell `-Proxy` parametru.

Vysvětlení těchto příkazů, přizpůsobení pokyny a informace o řešení problémů, najdete v článku [podrobné pokyny](status-monitor-v2-detailed-instructions.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvu o úrovni služeb, a to nedoporučujeme pro produkční úlohy. Některé funkce nemusí být podporované a některé můžou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="download-and-install-via-powershell-gallery"></a>Stáhněte a nainstalujte pomocí Galerie prostředí PowerShell

### <a name="install-prerequisites"></a>Požadavky na instalaci
Spusťte PowerShell jako správce.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Zavřete prostředí PowerShell.

### <a name="install-status-monitor-v2"></a>Stav instalace monitorování v2
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
    
        
## <a name="download-and-install-manually-offline-option"></a>Stáhnout a nainstalovat ručně (offline možnost)
### <a name="download-the-module"></a>Stáhnout modul
Ručně stáhněte si nejnovější verzi modulu z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-status-monitor-v2"></a>Rozbalte ho a nainstalujte monitorování stavu v2
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

- [Zkoumání metrik](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití.
- [Prohledávejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) k diagnostice problémů.
- [Použití analýzy](../../azure-monitor/app/analytics.md) pro pokročilejší dotazy.
- [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md).

 Přidání další telemetrie:

- [Vytvářejte webové testy](monitor-web-app-availability.md) k Ujistěte se, že web zůstává živý.
- [Přidání telemetrie webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a povolit trasování volání.
- [Přidejte Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) abyste mohli vložit trasování a protokolování volání.

Lepší využití v2 monitorování stavu:

- Zkontrolujte [podrobné pokyny](status-monitor-v2-detailed-instructions.md) vysvětlení příkazy najdete tady.
- Pomocí naší příručce ke [Poradce při potížích s](status-monitor-v2-troubleshoot.md) v2 monitorování stavu.
