---
title: Instalaci sady Office na hlavní imagi virtuálního pevného disku – Azure
description: Postup instalace a přizpůsobení sady Office na hlavní image ve verzi preview virtuálního klienta Windows do Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: cb9edbb508ddd993dcefbf69eb06b4f0d4156485
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742555"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalace sady Office do hlavní image virtuálního pevného disku

Tento článek vysvětluje, jak nainstalovat Office 365 ProPlus, OneDrive a další běžné aplikace na imagi hlavní virtuálního pevného disku (VHD) pro odeslání do Azure. Pokud budou uživatelé potřebovat přístup k určitým řádku obchodní (LOB) aplikace, doporučujeme že nainstalovat po dokončení pokyny v tomto článku.

Tento článek předpokládá, že už máte vytvořený virtuální počítač (VM). Pokud ne, přečtěte si téma [Prepare a přizpůsobit hlavní imagi virtuálního pevného disku](set-up-customize-master-image.md#create-a-vm)

Tento článek také předpokládá, že na virtuálním počítači, musí mít zvýšená přístup, jestli je zřízený v Azure nebo správce technologie Hyper-V. Pokud ne, přečtěte si téma [zvýšení úrovně přístupu ke správě Azure předplatného a Správa skupin](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Tyto pokyny se týkají konfigurace specifické pro virtuální plochy Windows ve verzi Preview, který lze použít s existujícími procesy vaší organizace.

## <a name="install-office-in-shared-computer-activation-mode"></a>Instalace Office v režimu aktivace sdíleného počítače

Aktivace pro sdílené počítače vám umožňují nasadit do počítače ve vaší organizaci, která se využívají více uživatelů Office 365 ProPlus. Další informace o aktivace pro sdílené počítače najdete v tématu [přehledu aktivace pro sdílené počítače pro Office 365 ProPlus](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Použití [nástroj pro nasazení Office](https://www.microsoft.com/download/details.aspx?id=49117) instalace Office. Windows 10 Enterprise více relace podporuje pouze následující verze Office:
- Office 365 ProPlus
- Office 365 Business, který je součástí předplatného Microsoft 365 Business

Nástroj pro nasazení Office vyžaduje soubor XML konfigurace. Následující ukázka přizpůsobit, přečtěte si článek [možnosti konfigurace pro nástroj pro nasazení Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Tahle vzorová konfigurace XML, poskytujeme bude provádět následující akce:

- Instalace Office z kanálu Insider a doručení aktualizací z kanálu Insider, když se zpracovává.
- Použití x64 architektury.
- Zakážete automatické aktualizace.
- Instalace aplikace Visio a Project.
- Odeberte všechny existující instalace sady Office a migraci jejich nastavení.
- Povolte aktivace pro sdílené počítače.

>[!NOTE]
>Vzorník funkce hledání ve Visiu nefunguje ve Windows virtuálního klienta během konfigurace ve verzi preview.

Zde je, co tahle vzorová konfigurace XML není:

- Instalaci Skypu pro firmy
- Instalace Onedrivu v režimu na uživatele. Další informace najdete v tématu [nainstalovat Onedrivu v režimu na počítač](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Aktivace pro sdílené počítače lze upravit pomocí nastavení registru nebo objekty zásad skupiny (GPO). Objekt zásad skupiny se nachází na **konfigurace počítače\\zásady\\šablony pro správu\\Microsoft Office 2016 (počítač)\\nastavení licencování**

Nástroj pro nasazení Office obsahuje setup.exe. K instalaci sady Office, spusťte následující příkaz v příkazovém řádku:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Ukázka configuration.xml

Následující ukázka XML nainstaluje verzi Insider.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
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
    <Updates Enabled="FALSE" UpdatePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Týmu služby Office doporučuje používat instalaci 64-bit **OfficeClientEdition** parametru.

Po instalaci sady Office, můžete aktualizovat výchozí chování sady Office. Spusťte následující příkazy, samostatně nebo v dávkovém souboru aktualizovat chování.

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

## <a name="install-onedrive-in-per-machine-mode"></a>Instalace Onedrivu v režimu na počítač

OneDrive je obvykle nainstalována pro jednotlivé uživatele. V tomto prostředí by měl být nainstalována na počítač.

Tady je postup instalace Onedrivu v režimu na počítač:

1. Nejprve vytvořte umístění, kam chcete připravit Onedrivu Instalační služby. Do složky místního disku nebo [\\\\unc] umístění (file://unc) je v pořádku.

2. Stáhněte si OneDriveSetup.exe dvoufázové instalace umístění s tímto odkazem: <https://aka.ms/OneDriveWVD-Installer>

3. Pokud jste nainstalovali office s Onedrivem vynecháním  **\<ExcludeApp ID = "Onedrivu" /\>** , odinstalovat všechny existující instalace OneDrive na uživatele z příkazového řádku se zvýšenými oprávněními spusťte následující příkaz:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Spusťte tento příkaz z příkazového řádku se zvýšenými oprávněními k nastavení **AllUsersInstall** hodnoty registru:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Spuštěním následujícího příkazu nainstalujte Onedrivu v režimu na počítač:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Spuštěním tohoto příkazu ke konfiguraci služby OneDrive pro spuštění při přihlášení pro všechny uživatele:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Povolit **tiše konfigurace uživatelského účtu** spuštěním následujícího příkazu.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Přesměrování a přesunout známé složky na OneDrive spuštěním následujícího příkazu Windows.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Týmy a Skype

Virtuální Desktop Windows nepodporuje Skype pro firmy a týmy.

## <a name="next-steps"></a>Další postup

Teď, když jste přidali Office do bitové kopie, můžete nadále upravit si hlavní image virtuálního pevného disku. Zobrazit [Prepare a přizpůsobit hlavní imagi virtuálního pevného disku](set-up-customize-master-image.md).
