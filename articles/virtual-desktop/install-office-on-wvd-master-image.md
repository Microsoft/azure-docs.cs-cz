---
title: Instalace Office na hlavní bitovou kopii virtuálního pevného disku – Azure
description: Jak nainstalovat a přizpůsobit Office na image hlavního počítače Windows Virtual Desktop do Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b93f26a6799a50868feb1f3350a3dc4a73a0b2e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127854"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalace sady Office do hlavní image virtuálního pevného disku

V tomto článku se dozvíte, jak nainstalovat Office 365 ProPlus, OneDrive a další běžné aplikace na hlavní virtuální pevný disk (VHD) pro nahrání do Azure. Pokud uživatelé potřebují přístup k určitým obchodním aplikacím (LOB), doporučujeme je nainstalovat po dokončení pokynů v tomto článku.

Tento článek předpokládá, že jste už vytvořili virtuální počítač (VM). Pokud ne, přečtěte si viz [Příprava a přizpůsobení hlavního obrazu Virtuálního pevného disku.](set-up-customize-master-image.md#create-a-vm)

Tento článek také předpokládá, že máte zvýšený přístup na virtuálním počítači, ať už se zřazuje ve Správci Azure nebo Hyper-V. Pokud ne, přečtěte si informace [o zvýšení přístupu ke správě všech skupin předplatného a správy Azure](../role-based-access-control/elevate-access-global-admin.md).

>[!NOTE]
>Tyto pokyny jsou určeny pro konfiguraci specifickou pro virtuální plochu systému Windows, kterou lze použít se stávajícími procesy vaší organizace.

## <a name="install-office-in-shared-computer-activation-mode"></a>Instalace Office ve sdíleném režimu aktivace počítače

Aktivace sdíleného počítače umožňuje nasadit Office 365 ProPlus do počítače ve vaší organizaci, ke kterému má přístup více uživatelů. Další informace o aktivaci sdíleného počítače najdete v [tématu Přehled aktivace sdíleného počítače pro Office 365 ProPlus](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/).

K instalaci Office použijte [Nástroj pro nasazení Office.](https://www.microsoft.com/download/details.aspx?id=49117) Windows 10 Enterprise multi-session podporuje jenom následující verze Office:
- Office 365 ProPlus
- Office 365 Business s předplatným Microsoft 365 Business

Nástroj pro nasazení sady Office vyžaduje konfigurační soubor XML. Následující ukázku najdete v tématu [Možnosti konfigurace nástroje pro nasazení sady Office](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Tento ukázkový konfigurační XML, který jsme poskytli, bude dělat následující akce:

- Nainstalujte Office z měsíčního kanálu a po spouštění doručujte aktualizace z měsíčního kanálu.
- Použijte architekturu x64.
- Zakažte automatické aktualizace.
- Odeberte všechny existující instalace Office a migrujte jejich nastavení.
- Povolte aktivaci sdíleného počítače.

>[!NOTE]
>Funkce hledání vzorníku aplikace Visio nemusí ve virtuální ploše windows fungovat očekávaným způsobem.

Tady je to, co tato ukázková konfigurace XML nebude dělat:

- Instalace Skypu pro firmy
- Nainstalujte OneDrive v režimu pro jednotlivé uživatele. Další informace najdete [v tématu Instalace OneDrivu v režimu pro každý počítač](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Sdílenou aktivaci počítače lze nastavit pomocí objektů zásad skupiny (GPO) nebo nastavení registru. Zásady skupiny se nacházejí na adrese **\\Computer Configuration\\Policies Administrative Templates\\Microsoft Office 2016 (Machine)\\Licensing Settings**

Nástroj pro nasazení sady Office obsahuje nástroj setup.exe. Chcete-li nainstalovat Office, spusťte na příkazovém řádku následující příkaz:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Ukázková konfigurace.xml

Následující ukázka XML nainstaluje měsíční verzi.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Tým Office doporučuje používat 64bitovou instalaci pro parametr **OfficeClientEdition.**

Po instalaci Office můžete aktualizovat výchozí chování Office. Spusťte následující příkazy jednotlivě nebo v dávkovém souboru a aktualizujte chování.

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

## <a name="install-onedrive-in-per-machine-mode"></a>Instalace OneDrivu v režimu pro každý počítač

OneDrive se normálně instaluje pro jednotlivé uživatele. V tomto prostředí by měl být nainstalován na počítači.

Tady je postup, jak nainstalovat OneDrive v režimu pro každý počítač:

1. Nejprve vytvořte umístění pro instalaci instalačního programu OneDrivu. Umístění místního disku\\\\nebo [unc] (file://unc) je v pořádku.

2. Stáhněte si onedrivesetup.exe do připraveného umístění pomocí tohoto odkazu:<https://aka.ms/OneDriveWVD-Installer>

3. Pokud jste office nainstalovali s OneDrivem vynecháním ** \<excludeApp\>ID="OneDrive" /**, odinstalujte všechny existující instalace OneDrivu pro uživatele z příkazového řádku se zvýšenými oprávněními spuštěním následujícího příkazu:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Spusťte tento příkaz z příkazového řádku se zvýšenými oprávněními a nastavte hodnotu registru **AllUsersInstall:**

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Spuštěním tohoto příkazu nainstalujte OneDrive v režimu pro každý počítač:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Spuštěním tohoto příkazu nakonfigurujte OneDrive tak, aby se spouštěl při přihlášení pro všechny uživatele:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Povolte **nezvěstně konfigurovat uživatelský účet** spuštěním následujícího příkazu.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Přesměrujte a přesuňte složky Windows na OneDrive spuštěním následujícího příkazu.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Týmy a Skype

Virtuální plocha Windows nepodporuje Skype pro firmy a týmy.

## <a name="next-steps"></a>Další kroky

Teď, když jste do bitové kopie přidali Office, můžete dál přizpůsobovat svou hlavní bitovou kopii virtuálního pevného disku. Viz [Příprava a přizpůsobení hlavního obrazu Virtuálního pevného disku](set-up-customize-master-image.md).
