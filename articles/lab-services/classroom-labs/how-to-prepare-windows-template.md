---
title: Průvodce nastavením počítače se šablonami windows | Dokumenty společnosti Microsoft
description: Obecné kroky k přípravě počítače šablony systému Windows ve službách Lab Services.  Mezi tyto kroky patří nastavení plánu služby Windows Update, instalace OneDrivu a instalace Office.
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: c1aaf588f61b329fa3b838b8a92f3e287897315b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521186"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Průvodce nastavením počítače se šablonami Windows ve službě Azure Lab Services

Pokud nastavujete počítač se šablonami Windows 10 pro Azure Lab Services, tady je několik doporučených postupů a tipů, které je třeba zvážit. Níže uvedené kroky konfigurace jsou volitelné.  Tyto přípravné kroky by však mohly pomoci zvýšit produktivitu studentů, minimalizovat přerušení času ve třídě a zajistit, aby používali nejnovější technologie.

>[!IMPORTANT]
>Tento článek obsahuje fragmenty prostředí PowerShell pro zjednodušení procesu úpravy šablony počítače.  U všech zobrazených skriptů prostředí PowerShell je budete chtít spustit v prostředí Windows PowerShell s oprávněními správce. V systému Windows 10 je rychlý způsob, jak toho dosáhnout, klikněte pravým tlačítkem myši na nabídku Start a zvolte "Windows PowerShell (Admin)".

## <a name="install-and-configure-onedrive"></a>Instalace a konfigurace OneDrivu

Chcete-li chránit data studentů před ztrátou při resetování virtuálního počítače, doporučujeme studentům zálohovat jejich data do cloudu.  Microsoft OneDrive může studentům pomoci chránit jejich data.  

### <a name="install-onedrive"></a>Instalace OneDrivu

Pokud chcete OneDrive stáhnout a nainstalovat ručně, přečtěte si stránky pro stažení [OneDrivu](https://onedrive.live.com/about/download/) nebo [OneDrivu pro firmy.](https://onedrive.live.com/about/business/)

Můžete také použít následující skript Prostředí PowerShell.  Automaticky stáhne a nainstaluje nejnovější verzi OneDrivu.  Po instalaci klienta OneDrivu spusťte instalační program.  V našem příkladu `/allUsers` používáme přepínač k instalaci OneDrivu pro všechny uživatele na počítači. `/silent` Přepínač používáme také k tiché instalaci OneDrivu.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>Vlastní nastavení OneDrivu

Existuje mnoho [úprav, které lze provést na OneDrive](https://docs.microsoft.com/onedrive/use-group-policy). Podívejme se na některé z běžnějších úprav.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Tiché přesunutí známých složek Windows na OneDrive

Složky jako Dokumenty, Soubory ke stažení a Obrázky se často používají k ukládání souborů studentů. Pokud zajistíte, že se tyto složky zálohují na OneDrive, doporučujeme přesunout tyto složky na OneDrive.

Pokud používáte počítač, který nepoužívá službu Active Directory, mohou uživatelé tyto složky po ověření na OneDrive ručně přesunout na OneDrive.

1. Otevření Průzkumníka souborů
2. Klikněte pravým tlačítkem myši na složku Dokumenty, Soubory ke stažení nebo Obrázky.
3. Přejděte na vlastnosti > umístění.  Přesuňte složku do nové složky v adresáři OneDrivu.

Pokud je váš virtuální počítač připojený ke službě Active Directory, můžete nastavit, aby se počítač šablony automaticky vyzývavá studenty k přesunutí známých složek na OneDrive.  

Budete muset nejprve získat ID klienta office.  Další pokyny najdete v [tématu vyhledání ID klienta Office 365](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).  ID klienta Office 365 můžete získat také pomocí následujícího Prostředí PowerShell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Až budete mít ID klienta Office 365, nastavte OneDrive tak, aby se na OneDrive zobrazoval pomocí následujícího PowerShellu.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Použití souborů OneDrivu na vyžádání

Studenti mohou mít v rámci svých účtů OneDrive mnoho souborů. Chcete-li ušetřit místo na počítači a zkrátit dobu stahování, doporučujeme, aby všechny soubory uložené v účtu OneDrive studenta byly na vyžádání.  Soubory na vyžádání se stáhnou pouze po připojení uživatele k souboru.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Tiché přihlašování uživatelů na OneDrive

OneDrive se dá nastavit tak, aby se automaticky přihlašoval pomocí přihlašovacích údajů k Systému Windows přihlášeného uživatele.  Automatické přihlášení je užitečné pro třídy, kde se student přihlašuje pomocí svých školních přihlašovacích údajů k Office 365.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Zakázání kurzu, který se zobrazí na konci nastavení OneDrivu

Toto nastavení umožňuje zabránit spuštění kurzu ve webovém prohlížeči na konci instalace OneDrivu.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Nastavení maximální velikosti souboru, který se má stahovat automaticky

Toto nastavení se používá ve spojení s tichým přihlášením uživatelů ke klientovi synchronizace OneDrivu s jejich přihlašovacími údaji systému Windows na zařízeních, která nemají povolenou funkci Soubory OneDrivu na vyžádání. Každý uživatel, který má OneDrive, který je větší než zadaná prahová hodnota (v MB), bude vyzván k výběru složek, které chce synchronizovat, před tím, než klient synchronizace OneDrivu (OneDrive.exe) stáhne soubory.  V našem příkladu "1111-2222-3333-4444" je ID klienta Office 365 a 0005000 nastaví prahovou hodnotu 5 GB.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>Instalace a konfigurace Microsoft Office 365

### <a name="install-microsoft-office-365"></a>Instalace Microsoft Office 365

Pokud váš počítač se šablonami potřebuje Office, doporučujeme instalaci Office pomocí [Nástroje pro nasazení sady Office (ODT)](https://www.microsoft.com/download/details.aspx?id=49117 ). Budete muset vytvořit opakovaně použitelný konfigurační soubor pomocí [služby Konfigurace klienta Office 365,](https://config.office.com/) abyste si mohli vybrat, jakou architekturu, jaké funkce budete potřebovat z Office a jak často se aktualizuje.

1. Přejděte na [službu Konfigurace klienta Office 365](https://config.office.com/) a stáhněte si vlastní konfigurační soubor.
2. Stáhnout [Nástroj pro nasazení sady Office](https://www.microsoft.com/download/details.aspx?id=49117).  Stažený soubor `setup.exe`bude .
3. Spusťte `setup.exe /download configuration.xml` stažení součástí Office.
4. Spusťte `setup.exe /configure configuration.xml` instalaci součástí Office.

### <a name="change-the-microsoft-office-365-update-channel"></a>Změna kanálu aktualizace Microsoft Office 365

Pomocí Nástroje konfigurace Office můžete nastavit, jak často office přijímá aktualizace. Pokud však potřebujete upravit, jak často office po instalaci přijímá aktualizace, můžete změnit adresu URL aktualizačního kanálu. Update channel URL addresses can be found at [Change the Office 365 ProPlus update channel for devices in your organization](https://docs.microsoft.com/deployoffice/change-update-channels). Následující příklad ukazuje, jak nastavit Office 365 tak, aby používal kanál měsíční aktualizace.

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Instalace a konfigurace aktualizací

### <a name="install-the-latest-windows-updates"></a>Instalace nejnovějších aktualizací systému Windows

Doporučujeme nainstalovat nejnovější aktualizace společnosti Microsoft na počítači šablony pro účely zabezpečení před publikováním šablony virtuálního počítače.  Také potenciálně zabraňuje studentům, aby byli ve své práci rušně, když jsou aktualizace spuštěny v neočekávaných časech.

1. Spustit **nastavení** z nabídky Start
2. Klikněte na **Aktualizovat** & zabezpečení
3. Klikněte na **Vyhledat aktualizace.**
4. Aktualizace se stáhnou a nainstalují.

PowerShell můžete také použít k aktualizaci počítače šablony.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Některé aktualizace mohou vyžadovat restartování počítače.  Pokud je vyžadováno restartování, budete vyzváni.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Instalace nejnovějších aktualizací pro aplikace pro Microsoft Store

Doporučujeme, aby všechny aplikace pro Microsoft Store byly aktualizovány na nejnovější verze.  Tady jsou pokyny k ruční aktualizaci aplikací z Microsoft Storu.  

1. Spusťte aplikaci **Microsoft Store.**
2. Klikněte na elipsu (...) vedle fotografie uživatele v horním rohu aplikace.
3. V rozevírací nabídce vyberte **Stáhnout** a aktualizace.
4. Klikněte na tlačítko **Získat aktualizaci.**

Pomocí prostředí PowerShell můžete také aktualizovat aplikace microsoft store, které jsou již nainstalovány.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Zastavení automatických aktualizací systému Windows

Po aktualizaci systému Windows na nejnovější verzi můžete zvážit ukončení aktualizací systému Windows.  Automatické aktualizace mohou potenciálně narušit naplánovaný čas kurzu.  Pokud je kurz delší, zvažte možnost požádat studenty, aby ručně zkontrolovali aktualizace nebo nastavovali automatické aktualizace na dobu mimo plánované hodiny kurzu.  Další informace o možnostech vlastního nastavení pro službu Windows Update naleznete v [části Správa dalších nastavení služby Windows Update](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings).

Automatické aktualizace systému Windows mohou být zastaveny pomocí následujícího skriptu prostředí PowerShell.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Instalace cizojazyčných sad

Pokud potřebujete na virtuálním počítači nainstalované další jazyky, můžete je přidat přes Microsoft Store.

1. Spuštění Microsoft Storu
2. Vyhledat výraz "jazyková sada"
3. Výběr jazyka, který chcete nainstalovat

Pokud jste již přihlášeni k virtuálnímu počítači šablony, použijte [zástupce "Nainstalovat jazykovou sadu"](ms-settings:regionlanguage?activationSource=SMC-IA-4027670) a přejděte přímo na stránku příslušného nastavení.

## <a name="remove-unneeded-built-in-apps"></a>Odebrání nepotřebných vestavěných aplikací

Systém Windows 10 je dodáván s mnoha vestavěnými aplikacemi, které nemusí být potřebné pro vaši konkrétní třídu. Chcete-li zjednodušit bitovou kopii počítače pro studenty, můžete odinstalovat některé aplikace z počítače šablony.  Chcete-li zobrazit seznam nainstalovaných aplikací, použijte rutinu prostředí PowerShell. `Get-AppxPackage`  Následující příklad ukazuje všechny nainstalované aplikace, které lze odebrat.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Chcete-li odebrat aplikaci, použijte rutinu Odebrat appx.  Následující příklad ukazuje, jak odstranit vše, co xbox související.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Instalace běžných výukových aplikací

Nainstalujte si další aplikace, které se běžně používají pro výuku prostřednictvím aplikace pro Windows Store. Návrhy zahrnují aplikace, jako je [aplikace Microsoft Whiteboard](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)a Minecraft [Education Edition](https://education.minecraft.net/). Tyto aplikace musí být nainstalovány ručně prostřednictvím Windows Store nebo prostřednictvím příslušných webů na šabloně Virtuální počítač.

## <a name="conclusion"></a>Závěr

Tento článek vám ukázal volitelné kroky k přípravě virtuálního počítače šablony systému Windows pro efektivní třídu.  Mezi kroky patří instalace OneDrivu a instalace Office 365, instalace aktualizací pro Windows a instalace aktualizací pro aplikace pro Microsoft Store.  Také jsme diskutovali o tom, jak nastavit aktualizace plánu, který funguje nejlépe pro vaši třídu.  

## <a name="next-steps"></a>Další kroky
Přečtěte si článek o řízení chování při správě systému Windows, který vám pomůže se správou nákladů: [Průvodce řízením chování při vypínání systému Windows](how-to-windows-shutdown.md)
