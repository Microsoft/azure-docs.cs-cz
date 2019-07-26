---
title: Instalace Office na hlavní bitovou kopii VHD – Azure
description: Postup instalace a přizpůsobení sady Office v hlavní imagi Windows Virtual Desktop Preview na Azure
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: 9243b3e6ae9e164930373960757109ae8a4512e4
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501195"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalace sady Office do hlavní image virtuálního pevného disku

V tomto článku se dozvíte, jak nainstalovat Office 365 ProPlus, OneDrive a další běžné aplikace na hlavní image virtuálního pevného disku (VHD) pro nahrání do Azure. Pokud uživatelé potřebují přístup k určitým obchodním aplikacím (LOB), doporučujeme je nainstalovat po dokončení pokynů v tomto článku.

V tomto článku se předpokládá, že už jste vytvořili virtuální počítač (VM). Pokud ne, přečtěte si téma [Příprava a přizpůsobení hlavního image virtuálního pevného disku](set-up-customize-master-image.md#create-a-vm) .

Tento článek také předpokládá, že máte na virtuálním počítači vyšší oprávnění, ať už je zřízené v Azure nebo ve Správci technologie Hyper-V. Pokud ne, přečtěte si téma [zvýšení přístupu ke správě všech předplatných a skupin pro správu Azure](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Tyto pokyny jsou určené pro konfiguraci pro virtuální počítače s Windows ve verzi Preview, která se dá použít spolu s existujícími procesy vaší organizace.

## <a name="install-office-in-shared-computer-activation-mode"></a>Nainstalovat Office v režimu aktivace sdíleného počítače

Aktivace pomocí sdíleného počítače vám umožní nasadit Office 365 ProPlus na počítač ve vaší organizaci, ke kterému má více uživatelů přistup. Další informace o aktivaci sdíleného počítače najdete v tématu [Přehled aktivace sdíleného počítače pro Office 365 ProPlus](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

K instalaci Office použijte [Nástroj pro nasazení Office](https://www.microsoft.com/download/details.aspx?id=49117) . Windows 10 Enterprise multi-session podporuje jenom tyto verze Office:
- Office 365 ProPlus
- Office 365 Business, který je součástí předplatného Microsoft 365 Business

Nástroj pro nasazení Office vyžaduje konfigurační soubor XML. Postup přizpůsobení následující ukázky najdete v tématu [Možnosti konfigurace pro nástroj pro nasazení Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Tato ukázka konfiguračního souboru XML provede následující akce:

- Nainstalujte si Office z kanálu Insider a dodávejte aktualizace z kanálu Insider po jejich spuštění.
- Použijte architekturu x64.
- Zakáže automatické aktualizace.
- Nainstalujte aplikaci Visio a projekt.
- Odeberte všechny existující instalace Office a migrujte jeho nastavení.
- Povolit aktivaci sdíleného počítače.

>[!NOTE]
>Funkce hledání ve vzorníku v aplikaci Visio nefunguje během konfigurace verze Preview na virtuálním počítači s Windows.

Tady je postup, jak tento ukázkový konfigurační soubor XML neprovede:

- Nainstalovat Skype pro firmy
- Nainstalujte OneDrive v režimu pro jednotlivé uživatele. Další informace najdete v tématu [instalace OneDrivu v režimu podle počítače](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Aktivace sdíleného počítače se dá nastavit prostřednictvím objektů Zásady skupiny (GPO) nebo nastavení registru. Objekt zásad skupiny se nachází v **zásadách\\\\konfigurace počítačů\\šablony pro správu nastavení licencování systém Microsoft Office\\2016 (počítač)** .

Nástroj pro nasazení Office obsahuje Setup. exe. Pokud chcete nainstalovat Office, spusťte na příkazovém řádku následující příkaz:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Ukázka Configuration. XML

V následující ukázce XML se nainstaluje verze Insiders.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Tým sady Office doporučuje použít 64 instalaci pro parametr **OfficeClientEdition** .

Po instalaci Office můžete aktualizovat výchozí chování Office. Chcete-li aktualizovat chování, spusťte následující příkazy jednotlivě nebo v dávkovém souboru.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>Instalace OneDrivu v režimu podle počítače

OneDrive je obvykle nainstalovaný pro jednotlivé uživatele. V tomto prostředí by měl být nainstalovaný pro jednotlivé počítače.

Tady je postup, jak nainstalovat OneDrive v režimu podle počítače:

1. Nejdřív vytvořte umístění pro přípravu instalačního programu OneDrivu. Umístění složky místního disku nebo umístění\\[\\UNC] (File://UNC) je v pořádku.

2. Stáhněte si OneDriveSetup. exe do připraveného umístění pomocí tohoto odkazu:<https://aka.ms/OneDriveWVD-Installer>

3. Pokud jste nainstalovali Office s OneDrivem tím, že vynecháte  **\<ExcludeApp ID = "\>OneDrive"/** , odinstalujte všechny existující instalace OneDrivu na příkazovém řádku se zvýšenými oprávněními spuštěním tohoto příkazu:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Spusťte tento příkaz z příkazového řádku se zvýšenými oprávněními a nastavte hodnotu registru **AllUsersInstall** :

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Spuštěním tohoto příkazu nainstalujete OneDrive v režimu podle počítače:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Spuštěním tohoto příkazu nakonfigurujte OneDrive, aby se spouštěl při přihlášení pro všechny uživatele:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Spuštěním následujícího příkazu povolte **tichou konfiguraci uživatelského účtu** .

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Přesměrujte a přesuňte známé složky Windows na OneDrive spuštěním následujícího příkazu.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Týmy a Skype

Virtuální počítač s Windows nepodporuje Skype pro firmy a týmy.

## <a name="next-steps"></a>Další postup

Teď, když jste přidali Office k imagi, můžete pokračovat v přizpůsobení hlavní image VHD. Viz [Příprava a přizpůsobení hlavní image virtuálního pevného disku](set-up-customize-master-image.md).
