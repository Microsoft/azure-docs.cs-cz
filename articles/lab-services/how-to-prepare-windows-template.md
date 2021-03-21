---
title: Průvodce nastavením počítače se šablonou Windows | Microsoft Docs
description: Obecné kroky pro přípravu počítače se šablonou Windows ve službě Lab Services.  Tyto kroky zahrnují nastavení web Windows Update plán, instalaci OneDrivu a instalaci Office.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 216dc843b31eac355e1d818014f3d70b2ef83132
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647898"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Průvodce nastavením počítače se šablonou Windows v Azure Lab Services

Pokud nastavujete počítač šablony Windows 10 pro Azure Lab Services, tady je několik osvědčených postupů a tipů, které byste měli zvážit. Níže uvedené konfigurační kroky jsou volitelné.  Tyto přípravné kroky ale můžou pomoci zajistit vyšší produktivitu studentů, minimalizovat dobu přerušení a zajistit, aby používali nejnovější technologie.

>[!IMPORTANT]
>Tento článek obsahuje fragmenty kódu prostředí PowerShell pro zjednodušení procesu změny šablony počítače.  Pro všechny zobrazené skripty PowerShellu je budete chtít spouštět ve Windows PowerShellu s oprávněními správce. V systému Windows 10 je rychlý způsob, jak to provést, je kliknout pravým tlačítkem myši na nabídku Start a vybrat možnost Windows PowerShell (správce).

## <a name="install-and-configure-onedrive"></a>Instalace a konfigurace OneDrivu

Aby bylo možné chránit data studentů při obnovení virtuálního počítače, doporučujeme sami studentům vrátit data do cloudu.  Microsoft OneDrive může pomáhat studentům chránit svá data.  

### <a name="install-onedrive"></a>Nainstalovat OneDrive

Pokud chcete ručně stáhnout a nainstalovat OneDrive, přečtěte si stránky pro stažení na [OneDrivu](https://onedrive.live.com/about/download/) nebo [OneDrivu pro firmy](https://onedrive.live.com/about/business/) .

Můžete také použít následující skript prostředí PowerShell.  Bude automaticky stahovat a instalovat nejnovější verzi OneDrivu.  Po instalaci klienta OneDrive spusťte instalační program.  V našem příkladu používáme `/allUsers` přepínač k instalaci OneDrivu pro všechny uživatele na počítači. `/silent`K tiché instalaci OneDrivu používáme také přepínač.

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

### <a name="onedrive-customizations"></a>Přizpůsobení OneDrivu

[Na OneDrivu se dá udělat](/onedrive/use-group-policy)spousta úprav. Pojďme se pokrývat s některými častými vlastními úpravami.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Bezobslužné přesunutí známých složek Windows na OneDrive

Složky, jako jsou dokumenty, soubory ke stažení a obrázky, se často používají k ukládání souborů studenta. Abyste měli jistotu, že se tyto složky zálohují do OneDrivu, doporučujeme přesunout tyto složky na OneDrive.

Pokud jste na počítači, který nepoužívá službu Active Directory, mohou uživatelé ručně přesunout tyto složky na OneDrive, jakmile se ověří na OneDrive.

1. Otevřít Průzkumníka souborů
2. Klikněte pravým tlačítkem myši na složku dokumenty, soubory ke stažení nebo obrázky.
3. Přejít na vlastnosti > umístění.  Přesuňte složku do nové složky v adresáři OneDrive.

Pokud je váš virtuální počítač připojený ke službě Active Directory, můžete nastavit, aby počítač s šablonou automaticky vyzval své studenty a přesunuli známé složky na OneDrive.  

Nejdřív budete muset načíst ID vaší organizace.  Další pokyny najdete v tématu [Vyhledání ID vaší Microsoft 365 organizace](/onedrive/find-your-office-365-tenant-id).  ID organizace můžete také získat pomocí následujícího prostředí PowerShell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Jakmile budete mít ID vaší organizace, nastavte OneDrive tak, aby se na OneDrive přesunuly známé složky pomocí následujícího prostředí PowerShell.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Použití souborů OneDrive na vyžádání

Studenti můžou mít v rámci svých účtů OneDrive spoustu souborů. Abychom vám pomohli ušetřit místo na počítači a zkrátit dobu stahování, doporučujeme, abyste všechny soubory uložené v účtu OneDrive na vyžádání potlačili.  Soubory na vyžádání se stahují jenom tehdy, když uživatel přistupuje k souboru.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Tiché přihlášení uživatelů do OneDrivu

OneDrive se dá nastavit tak, aby se automaticky přihlásil s přihlašovacími údaji Windows přihlášeného uživatele.  Automatické přihlašování je užitečné pro třídy, kde se student přihlásí pomocí svých školních přihlašovacích údajů.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Vypnutí kurzu, který se zobrazí na konci instalace OneDrivu

Toto nastavení umožňuje zabránit spuštění tohoto kurzu ve webovém prohlížeči na konci instalace OneDrive.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Nastavte maximální velikost souboru, který se má automaticky stáhnout.

Toto nastavení se používá společně s bezobslužným přihlášením uživatelů do synchronizačního klienta OneDrivu s přihlašovacími údaji Windows na zařízeních, která nemají povolený soubor OneDrive na vyžádání. Každý uživatel, který má OneDrive větší, než je zadaná prahová hodnota (v MB), bude vyzván k výběru složek, které chtějí synchronizovat před tím, než klient synchronizace OneDrive (OneDrive.exe) stáhne soubory.  V našem příkladu je "1111-2222-3333-4444" ID organizace a 0005000 nastaví prahovou hodnotu 5 GB.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-365"></a>Instalace a konfigurace Microsoft 365

### <a name="install-microsoft-365"></a>Nainstalovat Microsoft 365

Pokud Váš počítač šablony potřebuje sadu Office, doporučujeme nainstalovat Office prostřednictvím nástroje pro [nasazení Office (ODT)](https://www.microsoft.com/download/details.aspx?id=49117). Budete muset vytvořit opakovaně použitelný konfigurační soubor pomocí [centra pro správu Microsoft 365Ch aplikací](https://config.office.com/) , abyste si zvolili, jakou architekturu budete potřebovat v Office, a jak často se aktualizuje.

1. Přejít do [centra pro správu Microsoft 365Ch aplikací](https://config.office.com/) a stáhnout vlastní konfigurační soubor.
2. Stáhněte si [Nástroj pro nasazení Office](https://www.microsoft.com/download/details.aspx?id=49117).  Stažený soubor bude `setup.exe` .
3. Spusťte `setup.exe /download configuration.xml` a stáhněte součásti Office.
4. Spusťte `setup.exe /configure configuration.xml` pro instalaci součástí systému Office.

### <a name="change-the-microsoft-365-update-channel"></a>Změna kanálu aktualizace Microsoft 365

Pomocí nástroje Konfigurace Office můžete nastavit, jak často Office dostávají aktualizace. Pokud ale potřebujete změnit, jak často Office obdrží aktualizace po instalaci, můžete změnit adresu URL kanálu aktualizace. Adresy URL kanálu aktualizace se dají najít při [změně kanálu aktualizace Microsoft 365 Apps pro zařízení ve vaší organizaci](/deployoffice/change-update-channels). Následující příklad ukazuje, jak nastavit Microsoft 365 pro použití měsíčního aktualizačního kanálu.

```powershell
# Update to the Microsoft 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Instalace a konfigurace aktualizací

### <a name="install-the-latest-windows-updates"></a>Nainstalovat nejnovější aktualizace Windows

Před publikováním virtuálního počítače šablony doporučujeme nainstalovat na počítač s šablonou nejnovější aktualizace od společnosti Microsoft.  Také potenciálně brání v jejich práci v práci, když se aktualizace spouštějí v neočekávaných časech.

1. **Nastavení** spuštění z nabídky Start
2. Klikněte na **aktualizovat** & zabezpečení.
3. Klikněte na **Vyhledat aktualizace** .
4. Aktualizace se stáhnou a nainstalují.

K aktualizaci počítače šablony můžete také použít PowerShell.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Některé aktualizace můžou vyžadovat restartování počítače.  Zobrazí se výzva, pokud je potřeba restartovat počítač.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Nainstalovat nejnovější aktualizace pro aplikace Microsoft Store

Doporučujeme, aby se všechny Microsoft Store aplikace aktualizovaly na nejnovější verze.  Tady jsou pokyny, jak ručně aktualizovat aplikace z Microsoft Store.  

1. Spusťte aplikaci **Microsoft Store** .
2. Klikněte na tři tečky (...) vedle uživatelské fotografie v horním rohu aplikace.
3. V rozevírací nabídce vyberte **Stáhnout** a aktualizace.
4. Klikněte na tlačítko **získat aktualizaci** .

Pomocí prostředí PowerShell můžete také aktualizovat Microsoft Store aplikace, které jsou již nainstalovány.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Zastavit automatické aktualizace Windows

Po aktualizaci Windows na nejnovější verzi můžete zvážit zastavení aktualizací Windows.  Automatické aktualizace můžou narušit naplánovaný čas třídy.  Pokud je váš kurz delší než jeden, zvažte, jestli studenti budou ručně vyhledávat aktualizace nebo nastavovat automatické aktualizace po dobu mimo plánované hodiny třídy.  Další informace o možnostech přizpůsobení pro web Windows Update najdete v tématu [Správa dalších nastavení web Windows Update](/windows/deployment/update/waas-wu-settings).

Automatické aktualizace Windows se můžou zastavit pomocí následujícího skriptu PowerShellu.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Nainstalovat cizí jazykové sady

Pokud na virtuálním počítači potřebujete nainstalované další jazyky, můžete je přidat prostřednictvím Microsoft Store.

1. Spustit Microsoft Store
2. Vyhledejte "jazykovou sadu"
3. Vyberte jazyk, který chcete nainstalovat.

Pokud jste už přihlášení k virtuálnímu počítači šablony, použijte klávesovou zkratku "nainstalovat jazykovou sadu" ( `ms-settings:regionlanguage?activationSource=SMC-IA-4027670` ) k přechodu přímo na stránku příslušné nastavení.

## <a name="remove-unneeded-built-in-apps"></a>Odebrání nepotřebných integrovaných aplikací

Windows 10 obsahuje mnoho integrovaných aplikací, které nemusí být potřeba pro konkrétní třídu. Chcete-li zjednodušit image počítače pro studenty, můžete chtít odinstalovat některé aplikace z počítače šablony.  Pokud chcete zobrazit seznam nainstalovaných aplikací, použijte `Get-AppxPackage` rutinu PowerShellu.  Následující příklad zobrazuje všechny nainstalované aplikace, které je možné odebrat.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Chcete-li odebrat aplikaci, použijte rutinu Remove-Appx.  Následující příklad ukazuje, jak odebrat všechny související položky XBox.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Instalace běžných aplikací souvisejících s výukou

Nainstalujte další aplikace, které se běžně používají k výuce v aplikaci pro Windows Store. Návrhy zahrnují aplikace, jako je [Microsoft](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)informing, [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)a [Minecraftu školství Edition](https://education.minecraft.net/). Tyto aplikace musí být nainstalovány ručně prostřednictvím Windows Storu nebo prostřednictvím příslušných webů na virtuálním počítači šablony.

## <a name="conclusion"></a>Závěr

V tomto článku jste si ukázali volitelné kroky pro přípravu virtuálního počítače šablony Windows pro efektivní třídu.  Postup zahrnuje instalaci OneDrivu a instalaci Microsoft 365, instalaci aktualizací pro Windows a instalaci aktualizací pro Microsoft Store aplikace.  Také jsme probrali, jak nastavit aktualizace na plán, který bude pro vaši třídu nejlépe fungovat.  

## <a name="next-steps"></a>Další kroky
Přečtěte si článek o tom, jak řídit chování při vypnutí Windows, které vám pomůže se správou nákladů: [Průvodce řízením chování při vypínání Windows](how-to-windows-shutdown.md)