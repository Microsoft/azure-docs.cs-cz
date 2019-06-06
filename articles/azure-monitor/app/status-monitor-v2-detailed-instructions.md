---
title: Azure Monitor stavu v2 podrobné pokyny | Dokumentace Microsoftu
description: Podrobné pokyny pro zahájení práce s v2 monitorování stavu. Sledování výkonu webu bez opětovného nasazení webu. Funguje s webové aplikace ASP.NET hostované místně, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: d0960c749d74903acc778c0f21d5c49f380195ae
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734184"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Stav monitorování v2: Podrobné pokyny

Tento článek popisuje, jak se zapojit do Galerie prostředí PowerShell a stáhnout modul ApplicationMonitor.
Popisuje nejběžnější parametry, které je potřeba, abyste mohli začít.
Zahrnuje také pokyny k ručnímu provedení v případě, že nemáte přístup k Internetu.

> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvu o úrovni služeb, a to nedoporučujeme pro produkční úlohy. Některé funkce nemusí být podporované a některé můžou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-an-instrumentation-key"></a>Získání klíče instrumentace

Abyste mohli začít, budete potřebovat Instrumentační klíč. Další informace najdete v tématu [vytvořte prostředek Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Spusťte PowerShell jako správce pomocí zásad provádění se zvýšenými oprávněními

**Spustit jako správce.**

Prostředí PowerShell vyžaduje oprávnění na úrovni správce provést změny v počítači.

**Zásady spouštění aplikace**
- Popis: Spouštění skriptů prostředí PowerShell je ve výchozím nastavení zakázána. Doporučujeme vám umožní RemoteSigned skripty jenom v aktuálním oboru.
- Další informace: [O zásady spouštění](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) a [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Příkaz: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Volitelný parametr:
    - `-Force`. Obchází výzvu k potvrzení.

**Příklady chyb**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Požadavky na prostředí PowerShell

Auditovat powershellu spuštěním `$PSVersionTable` příkazu.
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

Tyto pokyny se vytvoření a otestování v počítači se systémem Windows 10 a verze uvedené výše.

## <a name="prerequisites-for-powershell-gallery"></a>Předpoklady pro galerii prostředí PowerShell

Tyto kroky připraví váš server můžete stáhnout moduly z Galerie prostředí PowerShell.

> [!NOTE] 
> Galerie prostředí PowerShell se podporuje na Windows 10, Windows Server 2016 a Powershellu 6.
> Informace o předchozích verzích najdete v tématu [instalace Správce balíčků PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. Spusťte PowerShell jako správce pomocí zásad provádění se zvýšenými oprávněními.
2. Nainstalujte poskytovatele balíčku NuGet.
    - Popis: Je třeba tohoto zprostředkovatele k interakci s úložišť založená na Nugetu jako galerie prostředí PowerShell.
    - Další informace: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Příkaz: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Volitelné parametry:
        - `-Proxy`. Určuje proxy server pro požadavek.
        - `-Force`. Obchází výzvu k potvrzení.
    
    Pokud není nastavený NuGet obdržíte této výzvy:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Nakonfigurujte galerii prostředí PowerShell jako důvěryhodné úložiště.
    - Popis: Ve výchozím nastavení je Galerie prostředí PowerShell nedůvěryhodného úložiště.
    - Další informace: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Příkaz: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Volitelný parametr:
        - `-Proxy`. Určuje proxy server pro požadavek.

    Obdržíte tuto výzvu Galerie prostředí PowerShell není důvěryhodné:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Můžete tuto změnu potvrdit a auditovat všechny PSRepositories spuštěním `Get-PSRepository` příkazu.

4. Nainstalujte nejnovější verzi správce balíčků PowerShellGet.
    - Popis: Tento modul obsahuje nástroje použít k získání ostatní moduly z Galerie prostředí PowerShell. Verze 1.0.0.1 se dodává s Windows 10 a Windows Server. Verze 1.6.0 se vyžaduje nebo vyšší. Chcete-li zjistit, která verze je nainstalovaná, spusťte `Get-Command -Module PowerShellGet` příkazu.
    - Další informace: [Instalace Správce balíčků PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - Příkaz: `Install-Module -Name PowerShellGet`.
    - Volitelné parametry:
        - `-Proxy`. Určuje proxy server pro požadavek.
        - `-Force`. Obchází upozornění "již nainstalováno" a nainstaluje nejnovější verzi.

    Pokud nepoužíváte nejnovější verzi modulu PowerShellGet, zobrazí se tato chyba:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Restartujte prostředí PowerShell. Nová verze nelze načíst v aktuální relaci. Nová relace Powershellu se načte nejnovější verzi modulu PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Stáhněte a nainstalujte modul prostřednictvím Galerie prostředí PowerShell

Tyto kroky budou stahovat modulu Az.ApplicationMonitor z Galerie prostředí PowerShell.

1. Ujistěte se, že jsou splněné všechny požadavky pro galerii prostředí PowerShell.
2. Spusťte PowerShell jako správce pomocí zásad provádění se zvýšenými oprávněními.
3. Instalace modulu Az.ApplicationMonitor.
    - Další informace: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Příkaz: `Install-Module -Name Az.ApplicationMonitor`.
    - Volitelné parametry:
        - `-Proxy`. Určuje proxy server pro požadavek.
        - `-AllowPrerelease`. Umožňuje instalaci alfa a beta verze.
        - `-AcceptLicense`. Obchází řádku "Přijměte licenci"
        - `-Force`. Obchází upozornění "Nedůvěryhodného úložiště".

## <a name="download-and-install-the-module-manually-offline-option"></a>Stáhněte si a nainstalujte modul ručně (offline možnost)

Pokud z nějakého důvodu se nelze připojit k modulu prostředí PowerShell, můžete ručně stáhnout a nainstalovat modul Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Ručně stáhněte si nejnovější soubor nupkg

1. Přejděte do části https://www.powershellgallery.com/packages/Az.ApplicationMonitor (Soubor > Nový > Jiné).
2. Vyberte nejnovější verzi souboru v **historie verzí** tabulky.
3. V části **možnosti instalace**vyberte **ruční stažení**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Option 1: Instalace do adresáře modulů Powershellu
Nainstalujte ručně stažené modul prostředí PowerShell do adresáře PowerShell tak bude zjistitelné relacemi Powershellu.
Další informace najdete v tématu [instaluje se modul PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Rozbalte nupkg jako soubor zip s použitím Expand-archivu (v1.0.1.0)

- Popis: Základní verze Microsoft.PowerShell.Archive (v1.0.1.0) nelze rozbalit nupkg soubory. Přejmenujte soubor s příponou .zip.
- Další informace: [Rozbalte archiv](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Příkaz:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Rozbalte nupkg pomocí Expand-archivu (v1.1.0.0)

- Popis: Použijte aktuální verzi rozbalte archiv k rozbalení nupkg soubory beze změny rozšíření.
- Další informace: [Rozbalte archiv](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) a [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Příkaz:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Option 2: Rozbalte a importovat nupkg ručně
Nainstalujte ručně stažené modul prostředí PowerShell do adresáře PowerShell tak bude zjistitelné relacemi Powershellu.
Další informace najdete v tématu [instaluje se modul PowerShell](https://docs.mircrosoft.com/powershell/developer/module/installing-a-powershell-module).

Pokud instalujete modulu do žádného jiného adresáře, modul naimportovat ručně pomocí [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> Knihovny DLL se nainstaluje prostřednictvím relativní cesty.
> Store obsah balíčku v adresáři odpovídající modul runtime a potvrďte, že umožňují přístupová oprávnění pro čtení, ale ne zápisu.

1. Změňte příponu na ".zip" a extrahovat obsah balíčku do určené instalační adresář.
2. Najdete cestu k souboru Az.ApplicationMonitor.psd1.
3. Spusťte PowerShell jako správce pomocí zásad provádění se zvýšenými oprávněními.
4. Načtení modulu s použitím `Import-Module Az.ApplicationMonitor.psd1` příkazu.
    

## <a name="route-traffic-through-a-proxy"></a>Směrování provozu přes proxy server

Monitorovat počítač v privátním intranetu, bude nutné směrovat provoz protokolu HTTP prostřednictvím proxy serveru.

Podpora Powershellové příkazy, které stáhne a nainstaluje Az.ApplicationMonitor z Galerie prostředí PowerShell `-Proxy` parametru.
Při psaní skriptů instalace, projděte si podle předchozích pokynů.

Application Insights SDK potřebovat k odesílání telemetrie vaší aplikace společnosti Microsoft. Doporučujeme nakonfigurovat nastavení proxy serveru pro vaši aplikaci ve vašem souboru web.config. Další informace najdete v tématu [nejčastější dotazy k Application Insights: Průchod proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Povolení monitorování

Použití `Enable-ApplicationInsightsMonitoring` příkaz, který povolí monitorování.

Zobrazit [reference k rozhraní API](status-monitor-v2-api-enable-monitoring.md) podrobný popis toho, jak použít tuto rutinu.



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

- Pomocí naší příručce ke [Poradce při potížích s](status-monitor-v2-troubleshoot.md) v2 monitorování stavu.
