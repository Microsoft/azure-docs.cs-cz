---
title: Podrobné pokyny agenta Azure Application Insights Agent | Dokumenty společnosti Microsoft
description: Podrobné pokyny pro začínáme s Agent em insights aplikace. Sledujte výkon webových stránek bez opětovného nasazení webu. Funguje s ASP.NET webových aplikací hostovaných místně, ve virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8f6134e8f8fdb9af3f578afaf0670c32a3896e01
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766860"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Agent přehledů aplikací (dříve pojmenovaný Sledování stavu v2): Podrobné pokyny

Tento článek popisuje, jak napalubě do Galerie prostředí PowerShell a stáhnout modul ApplicationMonitor.
Zahrnuty jsou nejběžnější parametry, které budete potřebovat, abyste mohli začít.
Také jsme poskytli pokyny pro ruční stažení v případě, že nemáte přístup k internetu.

## <a name="get-an-instrumentation-key"></a>Získejte klíč pro instrumentaci

Chcete-li začít, potřebujete klíč instrumentace. Další informace naleznete [v tématu Vytvoření prostředku Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Spuštění prostředí PowerShell jako správce se zvýšenými zásadami provádění

### <a name="run-as-admin"></a>Spustit jako správce

PowerShell potřebuje oprávnění na úrovni správce, aby mohl provádět změny v počítači.
### <a name="execution-policy"></a>Zásady provádění
- Popis: Ve výchozím nastavení je spouštění skriptů prostředí PowerShell zakázáno. Doporučujeme povolit skripty RemoteSigned pouze pro aktuální obor.
- Odkaz: [O zásadách provádění](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) a [set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Příkaz: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Volitelný parametr:
    - `-Force`. Obchází výzvu k potvrzení.

**Příkladchyby**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Požadavky pro Prostředí PowerShell

Auditujte svou instanci `$PSVersionTable` prostředí PowerShell spuštěním příkazu.
Tento příkaz vytvoří následující výstup:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Tyto pokyny byly napsány a testovány v počítači se systémem Windows 10 a výše uvedenými verzemi.

## <a name="prerequisites-for-powershell-gallery"></a>Požadavky pro Galerii prostředí PowerShell

Tyto kroky připraví server ke stažení modulů z Galerie prostředí PowerShell.

> [!NOTE] 
> Galerie PowerShellu je podporovaná ve Windows 10, Windows Server 2016 a PowerShellu 6.
> Informace o starších verzích naleznete [v tématu Instalace prostředí PowerShellGet](/powershell/scripting/gallery/installing-psget).


1. Spusťte Prostředí PowerShell jako správce se zvýšenými zásadami provádění.
2. Nainstalujte zprostředkovatele balíčku NuGet.
    - Popis: K interakci s úložišti založenými na NuGetu, jako je PowerShell Gallery, potřebujete tohoto zprostředkovatele.
    - Odkaz: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Příkaz: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Volitelné parametry:
        - `-Proxy`. Určuje proxy server pro požadavek.
        - `-Force`. Obchází výzvu k potvrzení.
    
    Tato výzva se zobrazí, pokud není nuget nastaven:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Nakonfigurujte Galerii prostředí PowerShell jako důvěryhodné úložiště.
    - Popis: Ve výchozím nastavení je PowerShell Gallery nedůvěryhodné úložiště.
    - Reference: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Příkaz: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Volitelný parametr:
        - `-Proxy`. Určuje proxy server pro požadavek.

    Tato výzva se zobrazí, pokud galerie PowerShellu není důvěryhodná:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Tuto změnu můžete potvrdit a auditovat `Get-PSRepository` všechny úložiště PSspuštěním příkazu.

4. Nainstalujte nejnovější verzi PowerShellGet.
    - Popis: Tento modul obsahuje nástroje používané k získání dalších modulů z Galerie prostředí PowerShell. Verze 1.0.0.1 je dodávána s Windows 10 a Windows Server. Je vyžadována verze 1.6.0 nebo vyšší. Chcete-li zjistit, která `Get-Command -Module PowerShellGet` verze je nainstalována, spusťte příkaz.
    - Odkaz: [Instalace powershellgetu](/powershell/scripting/gallery/installing-psget).
    - Příkaz: `Install-Module -Name PowerShellGet`.
    - Volitelné parametry:
        - `-Proxy`. Určuje proxy server pro požadavek.
        - `-Force`. Obchází upozornění "již nainstalováno" a nainstaluje nejnovější verzi.

    Tato chyba se zobrazí, pokud nepoužíváte nejnovější verzi PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Restartujte prostředí PowerShell. Novou verzi nelze načíst v aktuální relaci. Nové relace PowerShellu načtou nejnovější verzi PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Stažení a instalace modulu přes PowerShell Gallery

Tyto kroky stáhnou modul Az.ApplicationMonitor z Galerie prostředí PowerShell.

1. Ujistěte se, že jsou splněny všechny požadavky pro Galerii Prostředí PowerShell.
2. Spusťte Prostředí PowerShell jako správce se zvýšenými zásadami provádění.
3. Nainstalujte modul Az.ApplicationMonitor.
    - Odkaz: [Instalační modul](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Příkaz: `Install-Module -Name Az.ApplicationMonitor`.
    - Volitelné parametry:
        - `-Proxy`. Určuje proxy server pro požadavek.
        - `-AllowPrerelease`. Umožňuje instalaci alfa a beta verzí.
        - `-AcceptLicense`. Obchází výzvu "Přijmout licenci"
        - `-Force`. Obchází upozornění "Nedůvěryhodné úložiště".

## <a name="download-and-install-the-module-manually-offline-option"></a>Stažení a instalace modulu ručně (možnost offline)

Pokud se z nějakého důvodu nemůžete připojit k modulu PowerShell, můžete ručně stáhnout a nainstalovat modul Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Ručně stáhnout nejnovější soubor nupkg

1. Přejděte do části https://www.powershellgallery.com/packages/Az.ApplicationMonitor (Soubor > Nový > Jiné).
2. V tabulce **Historie verzí** vyberte nejnovější verzi souboru.
3. V části **Možnosti instalace**vyberte **možnost Ruční stažení**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Možnost 1: Instalace do adresáře modulů PowerShellu
Nainstalujte ručně stažený modul PowerShellu do adresáře PowerShellu, aby ho relací Prostředí PowerShell uznaly.
Další informace naleznete [v tématu Instalace modulu Prostředí PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Rozbalit nupkg jako zip soubor pomocí Rozbalit-Archiv (v1.0.1.0)

- Popis: Základní verze Microsoft.PowerShell.Archive (v1.0.1.0) nemůže rozbalit soubory nupkg. Přejmenujte soubor pomocí přípony ZIP.
- Odkaz: [Rozbalit-Archiv](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Příkaz:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Rozbalit nupkg pomocí Rozbalit-Archiv (v1.1.0.0)

- Popis: Pomocí aktuální verze rozbalit-archiv rozbalit nupkg soubory beze změny rozšíření.
- Odkaz: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) a [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Příkaz:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Možnost 2: Rozbalit a importovat nupkg ručně
Nainstalujte ručně stažený modul PowerShellu do adresáře PowerShellu, aby ho relací Prostředí PowerShell uznaly.
Další informace naleznete [v tématu Instalace modulu Prostředí PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).

Pokud modul instalujete do jiného adresáře, importujte modul ručně pomocí [funkce Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> Knihovny DLL se nainstalují prostřednictvím relativních cest.
> Uložte obsah balíčku do určeného adresáře runtime a potvrďte, že přístupová oprávnění umožňují čtení, ale ne zapisují.

1. Změňte rozšíření na ".zip" a extrahujte obsah balíčku do zamýšleného instalačního adresáře.
2. Najít cestu k souboru Az.ApplicationMonitor.psd1.
3. Spusťte Prostředí PowerShell jako správce se zvýšenými zásadami provádění.
4. Načtěte modul `Import-Module Az.ApplicationMonitor.psd1` pomocí příkazu.
    

## <a name="route-traffic-through-a-proxy"></a>Směrování provozu přes proxy server

Při sledování počítače v privátní síti intranet budete muset směrovat přenosy HTTP přes proxy server.

Příkazy prostředí PowerShell ke stažení a instalaci az.ApplicationMonitor `-Proxy` z Galerie prostředí PowerShell podporují parametr.
Při psaní instalačních skriptů si přečtěte předchozí pokyny.

Sada Application Insights SDK bude muset odeslat telemetrickou hodnotu vaší aplikace společnosti Microsoft. Doporučujeme nakonfigurovat nastavení proxy serveru pro vaši aplikaci v souboru web.config. Další informace naleznete v [tématu Nejčastější dotazy k přehledům aplikací: Průchod proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)serveru .


## <a name="enable-monitoring"></a>Povolení monitorování

Pomocí `Enable-ApplicationInsightsMonitoring` příkazu povolte monitorování.

Podrobný popis použití této rutiny naleznete v [odkazu rozhraní API.](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring)



## <a name="next-steps"></a>Další kroky

 Zobrazení telemetrických dat:

- [Prozkoumejte metriky](../../azure-monitor/platform/metrics-charts.md) a sledujte výkon a využití.
- [Hledat události a protokoly](../../azure-monitor/app/diagnostic-search.md) diagnostikovat problémy.
- Pro pokročilejší dotazy [použijte službu Analytics.](../../azure-monitor/app/analytics.md)
- [Vytvořte řídicí panely](../../azure-monitor/app/overview-dashboard.md).

 Přidání další telemetrie:

- [Vytvoření webových testů](monitor-web-app-availability.md) a ověření, jestli web zůstává živý.
- [Přidejte telemetrickou službu webového klienta,](../../azure-monitor/app/javascript.md) chcete-li zobrazit výjimky z kódu webové stránky a povolit volání trasování.
- [Přidejte do kódu sadou SDK application insights,](../../azure-monitor/app/asp-net.md) abyste mohli vkládat volání trasování a protokolování.

S agentem Application Insights toho zvládnete víc:

- Použijte náš průvodce [k řešení potíží s](status-monitor-v2-troubleshoot.md) agentem Application Insights.
