---
title: Podrobné pokyny pro službu Azure Application Insights Agent | Microsoft Docs
description: Podrobné pokyny pro zahájení práce s agentem Application Insights. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 29922f088a51e4876e5e2ec8fe87c3bbce4482f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521675"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Agent Application Insights (dřív pojmenovaný Monitorování stavu v2): podrobné pokyny

Tento článek popisuje, jak se připojit k Galerie prostředí PowerShell a stáhnout modul ApplicationMonitor.
K dispozici jsou nejběžnější parametry, které budete muset začít.
Pro případ, že nemáte přístup k Internetu, jsme také uvedli ruční pokyny ke stažení.

## <a name="get-an-instrumentation-key"></a>Získat klíč instrumentace

Abyste mohli začít, budete potřebovat klíč instrumentace. Další informace najdete v tématu [vytvoření prostředku Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Spusťte PowerShell jako správce se zvýšenými zásadami spouštění.

### <a name="run-as-admin"></a>Spustit jako správce

K provedení změn v počítači vyžaduje prostředí PowerShell oprávnění na úrovni správce.
### <a name="execution-policy"></a>Zásady spouštění
- Popis: ve výchozím nastavení jsou spuštěné skripty PowerShellu zakázané. Doporučujeme povolit skripty RemoteSigned jenom pro aktuální obor.
- Referenční informace: [o zásadách spouštění](/powershell/module/microsoft.powershell.core/about/about_execution_policies) a [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy).
- Příkaz: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process` .
- Volitelný parametr:
    - `-Force`. Obchází výzvu k potvrzení.

**Příklady chyb**

```output
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.

Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Předpoklady pro PowerShell

Pomocí příkazu proveďte audit své instance prostředí PowerShell `$PSVersionTable` .
Tento příkaz vytvoří následující výstup:

```output
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

## <a name="prerequisites-for-powershell-gallery"></a>Předpoklady pro Galerie prostředí PowerShell

Pomocí těchto kroků připravíte server tak, aby stahoval moduly z Galerie prostředí PowerShell.

> [!NOTE] 
> Galerie prostředí PowerShell podporuje Windows 10, Windows Server 2016 a PowerShell 6 +.
> Informace o dřívějších verzích najdete v tématu [instalace PowerShellGet](/powershell/scripting/gallery/installing-psget).


1. Spusťte PowerShell jako správce se zvýšenými zásadami spouštění.
2. Nainstalujte zprostředkovatele balíčku NuGet.
    - Popis: tohoto poskytovatele budete potřebovat k interakci s úložištěmi založenými na NuGet, jako je Galerie prostředí PowerShell.
    - Referenční informace: [install-PackageProvider](/powershell/module/packagemanagement/install-packageprovider).
    - Příkaz: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201` .
    - Volitelné parametry:
        - `-Proxy`. Určuje proxy server pro požadavek.
        - `-Force`. Obchází výzvu k potvrzení.
    
    Tato výzva se zobrazí, pokud není nastavená sada NuGet:

    ```output
    NuGet provider is required to continue
    PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. 
    The NuGet provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
    'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
    'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
    the NuGet provider now?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```    

3. Nakonfigurujte Galerie prostředí PowerShell jako důvěryhodné úložiště.
    - Popis: ve výchozím nastavení je Galerie prostředí PowerShell nedůvěryhodné úložiště.
    - Reference: [set-PSRepository](/powershell/module/powershellget/set-psrepository).
    - Příkaz: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted` .
    - Volitelný parametr:
        - `-Proxy`. Určuje proxy server pro požadavek.

    Tato výzva se zobrazí, pokud Galerie prostředí PowerShell není důvěryhodná:

    ```output
    Untrusted repository
    You are installing the modules from an untrusted repository. 
    If you trust this repository, change its InstallationPolicy value 
    by running the Set-PSRepository cmdlet. Are you sure you want to 
    install the modules from 'PSGallery'?
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
    ```

    Tuto změnu můžete potvrdit a auditovat všechny PSRepositories spuštěním `Get-PSRepository` příkazu.

4. Nainstalujte nejnovější verzi PowerShellGet.
    - Popis: Tento modul obsahuje nástroje, které slouží k získání dalších modulů z Galerie prostředí PowerShell. Verze 1.0.0.1 je dodávána se systémy Windows 10 a Windows Server. Je vyžadována verze 1.6.0 nebo vyšší. Pokud chcete zjistit, která verze je nainstalovaná, spusťte `Get-Command -Module PowerShellGet` příkaz.
    - Referenční informace: [instalace PowerShellGet](/powershell/scripting/gallery/installing-psget).
    - Příkaz: `Install-Module -Name PowerShellGet` .
    - Volitelné parametry:
        - `-Proxy`. Určuje proxy server pro požadavek.
        - `-Force`. Obchází upozornění "už nainstalovaného" a nainstaluje nejnovější verzi.

    Tato chyba se zobrazí, pokud nepoužíváte nejnovější verzi PowerShellGet:

    ```output
    Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
    At line:1 char:20
    Install-Module abc -AllowPrerelease
                   ~~~~~~~~~~~~~~~~
    CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
    FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    ```

5. Restartujte PowerShell. V aktuální relaci nelze načíst novou verzi. Nové relace PowerShellu budou načítat nejnovější verzi PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Stáhněte a nainstalujte modul pomocí Galerie prostředí PowerShell

Pomocí těchto kroků se stáhne modul AZ. ApplicationMonitor z Galerie prostředí PowerShell.

1. Ujistěte se, že jsou splněné všechny požadavky pro Galerie prostředí PowerShell.
2. Spusťte PowerShell jako správce se zvýšenými zásadami spouštění.
3. Nainstalujte modul AZ. ApplicationMonitor.
    - Referenční informace: [install-Module](/powershell/module/powershellget/install-module)
    - Příkaz: `Install-Module -Name Az.ApplicationMonitor` .
    - Volitelné parametry:
        - `-Proxy`. Určuje proxy server pro požadavek.
        - `-AllowPrerelease`. Umožňuje instalaci verzí Alpha a beta.
        - `-AcceptLicense`. Obchází výzvu "Accept License".
        - `-Force`. Obchází upozornění "nedůvěryhodné úložiště".

## <a name="download-and-install-the-module-manually-offline-option"></a>Ruční stažení a instalace modulu (možnost offline)

Pokud z nějakého důvodu nemůžete připojit modul PowerShellu, můžete ručně stáhnout a nainstalovat modul AZ. ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Ruční stažení nejnovějšího souboru nupkg

1. Přejděte na https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. V tabulce **Historie verzí** vyberte nejnovější verzi souboru.
3. V části **Možnosti instalace** vyberte **Ruční stažení**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Možnost 1: instalace do adresáře modulů PowerShellu
Nainstalujte ručně stažený modul PowerShellu do adresáře PowerShellu, aby byl zjistitelný pomocí relací PowerShellu.
Další informace najdete v tématu [Instalace modulu PowerShellu](/powershell/scripting/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Extrahování nupkg jako souboru ZIP pomocí Expand-Archive (v 1.0.1.0)

- Popis: základní verze souboru Microsoft. PowerShell. Archive (v 1.0.1.0) nemůže dekomprimovat soubory nupkg. Přejmenujte soubor s příponou. zip.
- Referenční informace: [expand-Archive](/powershell/module/microsoft.powershell.archive/expand-archive).
- Systému

    ```console
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Rozbalení nupkg pomocí Expand-Archive (v 1.1.0.0)

- Popis: pomocí aktuální verze Expand-Archive dekomprimovat soubory nupkg beze změny rozšíření.
- Referenční informace: [expand-Archive](/powershell/module/microsoft.powershell.archive/expand-archive) a [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Systému

    ```console
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Možnost 2: rozbalení a importování nupkg ručně
Nainstalujte ručně stažený modul PowerShellu do adresáře PowerShellu, aby byl zjistitelný pomocí relací PowerShellu.
Další informace najdete v tématu [Instalace modulu PowerShellu](/powershell/scripting/developer/module/installing-a-powershell-module).

Pokud instalujete modul do libovolného jiného adresáře, importujte modul ručně pomocí [Import-Module](/powershell/module/microsoft.powershell.core/import-module).

> [!IMPORTANT] 
> Knihovny DLL se budou instalovat prostřednictvím relativních cest.
> Uložte obsah balíčku do svého zamýšleného běhového adresáře a potvrďte, že přístupová oprávnění povolují čtení, ale nikoli zápis.

1. Změňte rozšíření na. zip a extrahujte obsah balíčku do svého zamýšleného instalačního adresáře.
2. Vyhledejte cestu k souboru Az.ApplicationMonitor.psd1.
3. Spusťte PowerShell jako správce se zvýšenými zásadami spouštění.
4. Načtěte modul pomocí `Import-Module Az.ApplicationMonitor.psd1` příkazu.
    

## <a name="route-traffic-through-a-proxy"></a>Směrování provozu prostřednictvím proxy serveru

Když monitorete počítač v privátním intranetu, budete muset směrovat přenos HTTP prostřednictvím proxy serveru.

Příkazy prostředí PowerShell ke stažení a instalaci AZ. ApplicationMonitor z Galerie prostředí PowerShell podporují `-Proxy` parametr.
Při psaní instalačních skriptů si Projděte předchozí pokyny.

Sada Application Insights SDK bude muset poslat telemetrii vaší aplikace Microsoftu. Doporučujeme nakonfigurovat nastavení proxy serveru pro vaši aplikaci v souboru web.config. Další informace najdete v tématu [Application Insights Nejčastější dotazy: průchozí proxy server](../faq.md#proxy-passthrough).


## <a name="enable-monitoring"></a>Povolení monitorování

`Enable-ApplicationInsightsMonitoring`K povolení monitorování použijte příkaz.

Podrobný popis způsobu použití této rutiny najdete v [referenčních informacích k rozhraní API](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring) .



## <a name="next-steps"></a>Další kroky

 Zobrazení telemetrických dat:

- [Prozkoumejte metriky](../essentials/metrics-charts.md) pro monitorování výkonu a využití.
- [Prohledejte události a protokoly](./diagnostic-search.md) a Diagnostikujte problémy.
- K pokročilejším dotazům [použijte Analytics](../logs/log-query-overview.md) .
- [Vytváření řídicích panelů](./overview-dashboard.md).

 Přidání další telemetrie:

- [Vytvoření webových testů](monitor-web-app-availability.md) a ověření, jestli web zůstává živý.
- [Přidejte telemetrii webového klienta](./javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění volání trasování.
- [Přidejte sadu Application Insights SDK do kódu](./asp-net.md) , abyste mohli vložit trasování a protokolování volání.

Další Application Insights agenta:

- Pomocí naší příručky můžete [řešit potíže s](status-monitor-v2-troubleshoot.md) agentem Application Insights.

