---
title: Instalace Office na hlavní bitovou kopii VHD – Azure
description: Postup instalace a přizpůsobení sady Office v hlavní imagi virtuálního počítače s Windows v Azure
author: Heidilohr
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: ce4eb3d556b6dff833129d11b26d439066b63f2e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446838"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalace sady Office do hlavní image virtuálního pevného disku

V tomto článku se dozvíte, jak nainstalovat aplikace Microsoft 365 pro podnikové, OneDrive a další běžné aplikace na hlavním obrázku virtuálního pevného disku (VHD) pro nahrání do Azure. Pokud uživatelé potřebují přístup k určitým obchodním aplikacím (LOB), doporučujeme je nainstalovat po dokončení pokynů v tomto článku.

V tomto článku se předpokládá, že už jste vytvořili virtuální počítač (VM). Pokud ne, přečtěte si téma [Příprava a přizpůsobení hlavního image virtuálního pevného disku](set-up-customize-master-image.md#create-a-vm) .

Tento článek také předpokládá, že máte na virtuálním počítači vyšší oprávnění, ať už je zřízené v Azure nebo ve Správci technologie Hyper-V. Pokud ne, přečtěte si téma [zvýšení přístupu ke správě všech předplatných a skupin pro správu Azure](../role-based-access-control/elevate-access-global-admin.md).

>[!NOTE]
>Tyto pokyny se týkají konfigurace specifické pro virtuální počítače s Windows, které se dají použít s existujícími procesy vaší organizace.

## <a name="install-office-in-shared-computer-activation-mode"></a>Nainstalovat Office v režimu aktivace sdíleného počítače

Aktivace pomocí sdíleného počítače vám umožní nasazovat aplikace Microsoft 365 pro podniky na počítač ve vaší organizaci, ke kterému má více uživatelů přistup. Další informace o aktivaci sdíleného počítače najdete v tématu [Přehled aktivace sdíleného počítače pro aplikace Microsoft 365](/deployoffice/overview-shared-computer-activation).

K instalaci Office použijte [Nástroj pro nasazení Office](https://www.microsoft.com/download/details.aspx?id=49117) . Windows 10 Enterprise multi-session podporuje jenom tyto verze Office:

   - Aplikace Microsoft 365 pro podniky
   - Microsoft 365 aplikace pro firmy, které jsou součástí předplatného Microsoft 365 Business Premium

Nástroj pro nasazení Office vyžaduje konfigurační soubor XML. Postup přizpůsobení následující ukázky najdete v tématu [Možnosti konfigurace pro nástroj pro nasazení Office](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Tato ukázka konfiguračního souboru XML provede následující akce:

   - Nainstalujte si Office z měsíčního podnikového kanálu a doručovat aktualizace z měsíčního podnikového kanálu.
   - Použijte architekturu x64.
   - Zakáže automatické aktualizace.
   - Odeberte všechny existující instalace Office a migrujte jeho nastavení.
   - Povolit aktivaci sdíleného počítače.

>[!NOTE]
>Funkce hledání ve vzorníku Visia nemusí fungovat podle očekávání na virtuálním počítači s Windows.

Tady je postup, jak tento ukázkový konfigurační soubor XML neprovede:

   - Nainstalovat Skype pro firmy
   - Nainstalujte OneDrive v režimu pro jednotlivé uživatele. Další informace najdete v tématu [instalace OneDrivu v režimu podle počítače](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Aktivace sdíleného počítače se dá nastavit prostřednictvím objektů Zásady skupiny (GPO) nebo nastavení registru. Objekt zásad skupiny se nachází v **zásadách konfigurace počítačů \\ \\ šablony pro správu \\ \\ Nastavení licencování systém Microsoft Office 2016 (počítač)** .

Nástroj pro nasazení Office obsahuje setup.exe. Pokud chcete nainstalovat Office, spusťte na příkazovém řádku následující příkaz:

```cmd
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Ukázka configuration.xml

V následující ukázce XML se nainstaluje měsíční verze podnikového kanálu.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="MonthlyEnterprise">
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
  <Logging Level="Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Tým sady Office doporučuje použít 64 instalaci pro parametr **OfficeClientEdition** .

Po instalaci Office můžete aktualizovat výchozí chování Office. Chcete-li aktualizovat chování, spusťte následující příkazy jednotlivě nebo v dávkovém souboru.

```cmd
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

1. Nejdřív vytvořte umístění pro přípravu instalačního programu OneDrivu. Umístění složky místního disku nebo \\ \\ umístění [UNC] (File://UNC) je v pořádku.

2. Pomocí tohoto odkazu Stáhněte OneDriveSetup.exe do připraveného umístění: <https://aka.ms/OneDriveWVD-Installer>

3. Pokud jste nainstalovali Office s OneDrivem vynecháte **\<ExcludeApp ID="OneDrive" /\>** , odinstalujte všechny stávající instalace OneDrivu na příkazovém řádku se zvýšenými oprávněními spuštěním tohoto příkazu:

    ```cmd
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Spusťte tento příkaz z příkazového řádku se zvýšenými oprávněními a nastavte hodnotu registru **AllUsersInstall** :

    ```cmd
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Spuštěním tohoto příkazu nainstalujete OneDrive v režimu podle počítače:

    ```cmd
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Spuštěním tohoto příkazu nakonfigurujte OneDrive, aby se spouštěl při přihlášení pro všechny uživatele:

    ```cmd
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Spuštěním následujícího příkazu povolte **tichou konfiguraci uživatelského účtu** .

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Přesměrujte a přesuňte známé složky Windows na OneDrive spuštěním následujícího příkazu.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Microsoft teams a Skype pro firmy

Virtuální počítač s Windows nepodporuje Skype pro firmy.

Nápovědu k instalaci Microsoft Teams najdete v tématu [použití Microsoft Teams na virtuálním počítači s Windows](teams-on-wvd.md). Optimalizace médií pro Microsoft Teams na virtuálním počítači s Windows je dostupná ve verzi Preview.

## <a name="next-steps"></a>Další kroky

Teď, když jste přidali Office k imagi, můžete pokračovat v přizpůsobení hlavní image VHD. Viz [Příprava a přizpůsobení hlavní image virtuálního pevného disku](set-up-customize-master-image.md).
