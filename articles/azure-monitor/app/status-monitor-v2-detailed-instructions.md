---
title: Azure Monitor stavu v2 podrobné pokyny | Dokumentace Microsoftu
description: Podrobné pokyny pro zahájení práce s v2 monitorování stavu. Sledování výkonu webu bez opětovného nasazení webu. Funguje s místně hostovanými webovými aplikacemi v ASP.NET, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: 6eca2b47c2362f34415db8b4f335f3089babc58b
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255879"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Stav monitorování v2 podrobné pokyny

Tento dokument podrobnosti o tom, jak se zapojit do Galerie prostředí PowerShell a stáhnout modul ApplicationMonitor. Zdokumentovali jsme nejběžnější parametry vyžaduje, abyste mohli začít.
Obsahuje rovněž pokyny k ručnímu provedení v případě, že přístup k Internetu není k dispozici.

> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="instrumentation-key"></a>Instrumentační klíč

Abyste mohli začít, musíte mít Instrumentační klíč. Další informace najdete v článku [vytvořte prostředek Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-administrator-with-an-elevated-execution-policy"></a>Spusťte PowerShell jako správce pomocí zásad provádění se zvýšenými oprávněními

**Spustit jako správce**: 
- Popis: Prostředí PowerShell, bude nutné oprávnění na úrovni správce provést změny v počítači.

**Zásady spouštění**:
- Popis: Ve výchozím nastavení spouštění skriptů prostředí PowerShell se deaktivuje. Doporučujeme, abyste RemoteSigned skripty umožňující v aktuálním oboru.
- Další informace: [O zásady spouštění](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) a [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)
- Cmd: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`
- Volitelné parametry:
    - `-Force` To se přeskočí výzvu k potvrzení.

**Příklady chyb:**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Požadavky na prostředí PowerShell

Auditovat vaší aktuální verzí Powershellu pomocí příkazu: `$PSVersionTable`.
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

Tyto pokyny se zapisují a testovat na počítač s Windows 10 pomocí uvedených verzí.

## <a name="prerequisites-for-powershell-gallery"></a>Předpoklady pro galerii prostředí PowerShell

Tyto kroky připraví váš server můžete stáhnout moduly z Galerie prostředí PowerShell.

> [!NOTE] 
> Podpora pro galerii prostředí PowerShell je součástí systému Windows 10, Windows Server 2016 a Powershellu 6. Pro starší verze projděte si tento dokument: [Instalace modulu PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)


1. Spusťte PowerShell jako správce pomocí zásad provádění se zvýšenými oprávněními.
2. Poskytovatele balíčku NuGet 
    - Popis: Tento zprostředkovatel je potřeba pracovat s úložišť založená na Nugetu je PowerShellGallery
    - Další informace: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)
    - Cmd: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`
    - Volitelné parametry:
        - `-Proxy` Určuje proxy server pro požadavek.
        - `-Force` To se přeskočí výzvu k potvrzení. 
    
    Pokud není nastavený NuGet obdržíte této výzvy:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Důvěryhodné úložiště
    - Popis: Ve výchozím nastavení je PowerShellGallery nedůvěryhodného úložiště.
    - Další informace: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)
    - Cmd: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`
    - Volitelné parametry:
        - `-Proxy` Určuje proxy server pro požadavek.

    Obdržíte tuto výzvu Galerie prostředí PowerShell není důvěryhodné:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    - Můžete tuto změnu potvrdit a auditovat všechny PSRepositories spuštěním cmd: `Get-PSRepository`

4. Verzi modulu PowerShellGet 
    - Popis: Tento modul obsahuje nástroje použít k získání ostatní moduly z Galerie prostředí PowerShell. V1.0.0.1 se dodává s Windows 10 a Windows Server. Minimální požadovaná verze je v1.6.0. Auditovat, která verze je nainstalovaná, spusťte příkaz: `Get-Command -Module PowerShellGet`
    - Další informace: [Instalace modulu PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)
    - Cmd: `Install-Module -Name PowerShellGet`
    - Volitelné parametry:
        - `-Proxy` Určuje proxy server pro požadavek.
        - `-Force` To bude ignorovat upozornění "již nainstalováno" a nainstalujte nejnovější verzi.

    Pokud nepoužíváte nejnovější verzi modulu PowerShellGet, zobrazí se tato chyba:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Restartujte prostředí PowerShell. Není možné načíst nové verze v aktuální relaci. Všechny nové relace prostředí Powershell budou mít nejnovější modul PowerShellGet načíst.

## <a name="download--install-via-powershell-gallery"></a>Stažení a instalace pomocí Galerie prostředí PowerShell

Tyto kroky budou stahovat modulu Az.ApplicationMonitor z Galerie prostředí PowerShell.

1. Předpoklady pro galerii prostředí PowerShell je povinný.
2. Spusťte PowerShell jako správce pomocí zásad provádění se zvýšenými oprávněními.
3. Instalace modulu Az.ApplicationMonitor
    - Další informace: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)
    - Cmd: `Install-Module -Name Az.ApplicationMonitor`
    - Volitelné parametry:
        - `-Proxy` Určuje proxy server pro požadavek.
        - `-AllowPrerelease` To vám umožní instalaci alfa a beta verze.
        - `-AcceptLicense` To se přeskočí řádku "Přijměte licenci"
        - `-Force` Tím se budou ignorovat upozornění "Nedůvěryhodného úložiště"

## <a name="download--install-manually-offline-option"></a>Stáhnout a nainstalovat ručně (offline možnost)

Pokud z nějakého důvodu se nelze připojit k modulu prostředí PowerShell, může ručně stáhněte a nainstalujte modul Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg"></a>Ručně stáhněte si nejnovější nupkg

1. Přejděte do: https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. Vyberte nejnovější verzi z historie verzí.
3. Vyhledejte "Možnosti instalace" a vyberte "Ruční stažení".

### <a name="option-1-install-into-powershell-modules-directory"></a>Možnost 1: instalace do adresáře modulů Powershellu
Nainstalujte ručně stažené modul prostředí PowerShell do adresáře, prostředí PowerShell, je možné najít relacemi Powershellu.
Další informace naleznete v tématu: [Instalace modulu prostředí PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-zip-using-expand-archive-v1010"></a>Rozbalte nupkg jako soubor zip pomocí Expand-archivu (v1.0.1.0)

- Popis: Základní verze Microsoft.PowerShell.Archive (v1.0.1.0) nelze rozbalit nupkg soubory. Přejmenujte soubor s příponou ".zip".
- Další informace: [Rozbalte archiv](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)
- Cmd: 

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-using-expand-archive-v1100"></a>Rozbalte nupkg pomocí Expand-archivu (v1.1.0.0).

- Popis: Použijte aktuální verzi rozbalte archiv k rozbalení nupkgs bez přejmenování rozšíření. 
- Další informace: [Rozbalte archiv](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) a [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)
- Cmd:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-manually"></a>Možnost 2: rozbalení a importovat ručně
Nainstalujte ručně stažené modul prostředí PowerShell do adresáře, prostředí PowerShell, je možné najít relacemi Powershellu.
Další informace naleznete v tématu: [Instalace modulu prostředí PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)

Pokud instalujete do žádného jiného adresáře, je nutné ručně naimportovat modul pomocí [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)

> [!IMPORTANT] 
> Instalace zabere knihovny DLL pomocí relativní cesty. Store obsah tohoto balíčku do adresáře určený modul runtime a potvrďte, že umožňují přístupová oprávnění pro čtení, ale ne zápisu.

1. Změňte příponu na ".zip" a extrahovat obsah balíčku do určené instalační adresář.
2. Najdete cestu k "Az.ApplicationMonitor.psd1".
3. Spusťte PowerShell jako správce pomocí zásad provádění se zvýšenými oprávněními. 
4. Načtení modulu pomocí příkazu: `Import-Module Az.ApplicationMonitor.psd1`.
    

## <a name="proxy"></a>Proxy server

Při monitorování počítačů v privátních intranetech, bude potřeba směrovat provoz protokolu http prostřednictvím proxy serveru.

Podpora Powershellové příkazy, které stáhne a nainstaluje Az.ApplicationMonitor z Galerie prostředí PowerShell `-Proxy` parametru.
Projděte si výše uvedené pokyny při psaní skriptů instalace.

Application Insights SDK potřebovat k odesílání telemetrie vaší aplikace společnosti Microsoft. Doporučujeme vám, konfigurace nastavení proxy serveru pro vaši aplikaci v souboru web.config. Zobrazit [Application Insights – nejčastější dotazy: Průchod proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough) Další informace.


## <a name="enable-monitoring"></a>Povolení monitorování 

Cmd: `Enable-ApplicationInsightsMonitoring`

Kontrola naše [Reference k rozhraní API](status-monitor-v2-api-enable-monitoring.md) podrobný popis toho, jak použít tuto rutinu. 



## <a name="next-steps"></a>Další postup

 Zobrazení telemetrických dat:

- [Zkoumání metrik](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití
- [Prohledávejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) k diagnostice problémů
- [Analýzy](../../azure-monitor/app/analytics.md) pro pokročilejší dotazy
- [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md)

 Přidání další telemetrie:

- [Vytvářejte webové testy](monitor-web-app-availability.md) k Ujistěte se, že web zůstává živý.
- [Přidání telemetrie webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění vložení trasovacích volání.
- [Přidejte sadu Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) , mohli vložit trasování a protokolování volání

Lepší využití v2 monitorování stavu:

- Pomocí naší příručce ke [Poradce při potížích](status-monitor-v2-troubleshoot.md) v2 monitorování stavu.
