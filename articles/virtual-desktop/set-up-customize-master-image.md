---
title: Příprava a přizpůsobit hlavní imagi virtuálního pevného disku – Azure
description: Jak můžete připravit, přizpůsobit a nahrajte virtuální plochy Windows hlavní image ve verzi preview do Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2019
ms.author: helohr
ms.openlocfilehash: 9d69a24b106fc39c03b8f95758bc240916023456
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698311"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Příprava a přizpůsobení hlavní image VHD

Tento článek vám sdělí postup přípravy bitové kopie hlavního virtuálního pevného disku (VHD) pro odeslání do Azure, jak vytvořit virtuální počítače (VM) a instalace a konfigurace softwaru na ně. Tyto pokyny se týkají konfigurace specifické pro virtuální plochy Windows ve verzi Preview, který lze použít s existujícími procesy vaší organizace.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Windows 10 Enterprise více relací je k dispozici v galerii Imagí Azure. Existují dvě možnosti pro přizpůsobení této bitové kopie.

Prvním způsobem je ke zřízení virtuálních počítačů (VM) v Azure podle pokynů v [vytvoření virtuálního počítače ze spravované image](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)a pak pokračujte [přípravy softwaru a instalace](set-up-customize-master-image.md#software-preparation-and-installation).

Druhou možností je vytvoření bitové kopie místně stažením image, zřizování virtuálních počítačů Hyper-V a přizpůsobení tak, aby odpovídala vašim potřebám, což si probereme v následující části.

### <a name="local-image-creation"></a>Vytvoření místní image

Po stažení image do místního umístění otevřete **Správce technologie Hyper-V** vytvoření virtuálního počítače pomocí virtuálního pevného disku právě zkopírovali. Tady je jednoduchá verze, ale můžete najít další podrobné pokyny v [vytvoření virtuálního počítače Hyper-v](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).

Chcete-li vytvořit virtuální počítač pomocí zkopírovaného virtuálního pevného disku:

1. Otevřít **Průvodce novým virtuálním počítačem**.

2. Na stránce zadat generaci vyberte **1. generace**.

    ![Snímek obrazovky stránky zadat generaci. Je vybraná možnost "1. generace".](media/a41174fd41302a181e46385e1e701975.png)

3. V části Typ kontrolního bodu zakázání kontrolních bodů zrušením zaškrtnutí políčka.

    ![Snímek obrazovky oddílu typ kontrolního bodu na stránce kontrolní body.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

V prostředí PowerShell k zakázání kontrolních bodů můžete spustit taky následující rutinu.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Pevný disk

Pokud vytvoříte virtuální počítač z existujícího virtuálního pevného disku, vytvoří dynamický disk ve výchozím nastavení. Lze jej změnit na pevný disk tak, že vyberete **upravit disku...**  jak je znázorněno na následujícím obrázku. Podrobnější pokyny najdete v článku [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

![Snímek obrazovky možnost upravit Disk.](media/35772414b5a0f81f06f54065561d1414.png)

Můžete také spouštět následující rutiny prostředí PowerShell můžete změnit disk na pevný disk.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Příprava softwaru a instalace

Tato část popisuje, jak připravit a nainstalujte Office 365 ProPlus, OneDrive, FSLogix, program Windows Defender a dalších běžných aplikací. Pokud budou uživatelé potřebovat přístup k určité obchodní aplikace, doporučujeme že nainstalovat po dokončení této části pokyny.

Této části se předpokládá, že na virtuálním počítači, musí mít zvýšená přístup, jestli je zřízený v Azure nebo správce technologie Hyper-V.

### <a name="install-office-in-shared-computer-activation-mode"></a>Instalace Office v režimu aktivace sdíleného počítače

Použití [nástroj pro nasazení Office](https://www.microsoft.com/download/details.aspx?id=49117) instalace Office. Windows 10 Enterprise více relace podporuje pouze Office 365 ProPlus, ne Perpetual 2019 Office.

Nástroj pro nasazení Office vyžaduje soubor XML konfigurace. Následující ukázka přizpůsobit, přečtěte si článek [možnosti konfigurace pro nástroj pro nasazení Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Tahle vzorová konfigurace XML, poskytujeme bude provádět následující akce:

- Instalace Office z kanálu Insider a doručení aktualizací z kanálu Insider, když se zpracovává.
- Použití x64 architektury.
- Zakážete automatické aktualizace.
- Instalace aplikace Visio a Project.
- Odeberte všechny existující instalace sady Office a migraci jejich nastavení.
- Povolte sdílené počítače licencování pro operace v prostředí terminálového serveru.

Zde je, co tahle vzorová konfigurace XML není:

- Instalaci Skypu pro firmy
- Instalace Onedrivu v režimu na uživatele. Další informace najdete v tématu [nainstalovat Onedrivu v režimu na počítač](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Sdílené počítačové licence lze nastavit pomocí nastavení registru nebo objekty zásad skupiny (GPO). Objekt zásad skupiny se nachází na **konfigurace počítače\\zásady\\šablony pro správu\\Microsoft Office 2016 (počítač)\\nastavení licencování**

Nástroj pro nasazení Office obsahuje setup.exe. K instalaci sady Office, spusťte následující příkaz v příkazovém řádku:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Ukázka configuration.xml

Následující ukázka XML nainstaluje verzi Insider, označované také jako Insiders rychlé nebo programu Insider hlavní.

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

### <a name="disable-automatic-updates"></a>Zakázat automatické aktualizace

Chcete-li zakázat automatické aktualizace pomocí místních zásad skupiny:

1. Otevřít **Editor místních zásad skupiny\\šablony pro správu\\součásti Windows\\Windows Update**.
2. Klikněte pravým tlačítkem na **Konfigurace automatických aktualizací** a nastavte ho na **zakázané**.

Můžete také spustit následující příkaz na příkazovém řádku, chcete-li zakázat automatické aktualizace.

```batch
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Zadejte počáteční rozložení pro počítače s Windows 10 (volitelné)

Spuštěním tohoto příkazu zadejte počáteční rozložení pro počítače s Windows 10.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="install-onedrive-in-per-machine-mode"></a>Instalace Onedrivu v režimu na počítač

OneDrive je obvykle nainstalována pro jednotlivé uživatele. V tomto prostředí by měl být nainstalována na počítač.

Tady je postup instalace Onedrivu v režimu na počítač:

1. Nejprve vytvořte umístění, kam chcete připravit Onedrivu Instalační služby. Do složky místního disku nebo [\\\\unc] umístění (file://unc) je v pořádku.

2. Stáhněte si OneDriveSetup.exe dvoufázové instalace umístění s tímto odkazem: <https://aka.ms/OneDriveWVD-Installer>

3. Pokud jste nainstalovali office s Onedrivem vynecháním  **\<ExcludeApp ID = "Onedrivu" /\>**, odinstalovat všechny existující instalace OneDrive na uživatele z příkazového řádku se zvýšenými oprávněními spusťte následující příkaz:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Spusťte tento příkaz z příkazového řádku se zvýšenými oprávněními k nastavení **AllUsersInstall** hodnoty registru:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Spuštěním následujícího příkazu nainstalujte Onedrivu v režimu na počítač:

    ```batch
    Run "[staged location]\OneDriveSetup.exe /allusers"
    ```

6. Spuštěním tohoto příkazu ke konfiguraci služby OneDrive pro spuštění při přihlášení pro všechny uživatele:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Povolit **tiše konfigurace uživatelského účtu** spuštěním následujícího příkazu.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Přesměrování a přesunout známé složky na OneDrive spuštěním následujícího příkazu Windows.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

### <a name="teams-and-skype"></a>Týmy a Skype

Virtuální Desktop Windows oficiálně nepodporuje Skype pro firmy a týmy.

### <a name="set-up-user-profile-container-fslogix"></a>Nastavit kontejner profilu uživatele (FSLogix)

Chcete-li zahrnout jako součást image kontejneru FSLogix, postupujte podle pokynů v [nastavení sdílené složky profilu uživatele pro hostitele fond](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Můžete otestovat funkci FSLogix kontejner s [v tomto rychlém startu](https://docs.fslogix.com/display/20170529/Profile+Containers+-+Quick+Start).

### <a name="configure-windows-defender"></a>Konfigurace programu Windows Defender

Pokud program Windows Defender je nakonfigurovaný na virtuálním počítači, ujistěte se, že se má nakonfigurovat pro není kontroly celý obsah souborů VHD a VHDX během stejné připojení.

Tato konfigurace pouze odebere prohledávání souborů VHD a VHDX během přílohu, ale neovlivní v reálném čase.

Podrobnější pokyny pro postup konfigurace programu Windows Defender ve Windows serveru, najdete v článku [vyloučení konfigurace Windows Defenderu antivirové ochrany v programu v systému Windows Server](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus).

Další informace o tom, jak nakonfigurovat programu Windows Defender z kontroly vyloučit některé soubory, naleznete v tématu [konfigurace a ověření vyloučení na základě umístění souboru rozšíření a složku](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus).

### <a name="configure-session-timeout-policies"></a>Konfigurace zásad vypršení časového limitu relace

Zásady vzdálené relace je možné vynucovat na úrovni zásad skupiny, protože všechny virtuální počítače ve fondu hostitelů jsou součástí stejné skupiny zabezpečení.

Před konfigurací zásad vzdálené relace:

1. Přejděte do **šablony pro správu** > **součásti Windows** > **služby Vzdálená plocha**  >  **Hostitel relace vzdálené plochy** > **časové limity relací**.
2. V panelu napravo, vyberte **nastavení časového limitu pro aktivní, ale nečinnosti relace služby Vzdálená plocha** zásad.
3. Po zobrazení modálního dialogového okna změnit možnost zásad z **Nenakonfigurováno** k **povoleno** aktivovat zásady.
4. V rozevírací nabídce pod možnost zásad, nastavit dobu, po kterou se má **4 hodiny**.

Relace vzdálené zásady můžete taky nakonfigurovat ručně spuštěním následujících příkazů:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fResetBroken /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 10800000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 7200000 /f
```

### <a name="set-up-time-zone-redirection"></a>Nastavit časové pásmo přesměrování

Časové pásmo přesměrování lze vynutit na úrovni zásad skupiny, protože všechny virtuální počítače ve fondu hostitelů jsou součástí stejné skupiny zabezpečení.

Chcete-li přesměrovat časová pásma:

1. Na serveru, Active Directory, spusťte **konzoly pro správu zásad skupiny**.
2. Rozbalte doménu a objekty zásad skupiny.
3. Klikněte pravým tlačítkem myši **objektu zásad skupiny** , který jste vytvořili pro nastavení zásad skupiny a vyberte **upravit**.
4. V **Editor správy zásad skupiny**, přejděte na **konfigurace počítače** > **zásady** > **správy Šablony** > **součásti Windows** > **služby Vzdálená plocha** > **hostitel relace vzdálené plochy**   >  **Zařízení a prostředků**.
5. Povolit **povolí přesměrování při časové pásmo** nastavení.

Tento příkaz můžete spustit také na hlavní bitová kopie pro přesměrování časová pásma:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Zakázání služby Sense úložiště

Pro hostitele relace virtuální plochy Windows, používající Windows 10 Enterprise nebo Windows 10 Enterprise více relací doporučujeme zakázat inteligentní úložiště. Inteligentní úložiště můžete zakázat v nabídce nastavení v části **úložiště**, jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky nabídky úložiště v části nastavení. Možnost "Inteligentní úložiště" je vypnutý.](media/storagesense.png)

Spuštěním následujícího příkazu můžete také změnit nastavení v registru:

```batch
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Zahrnout podpory dalších jazyků

Tento článek nepopisuje, jak nakonfigurovat jazyk a místní podpory. Další informace najdete v následujících článcích:

- [Přidat jazyky do imagí Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/add-language-packs-to-windows)
- [Funkce na vyžádání](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities)
- [Jazyk a oblast funkcí na vyžádání (zámořských)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

### <a name="other-applications-and-registry-configuration"></a>Další aplikace a konfigurace registru

Tato část zahrnuje aplikace a konfigurace operačního systému. Všechny konfigurace v této části se provádí prostřednictvím položky registru, které mohou být provedeny pomocí příkazového řádku a nástroje regedit.

>[!NOTE]
>Osvědčené postupy můžete implementovat v konfiguraci se objekty zásad obecné (GPO) nebo registru importy. Správce můžete zvolit jednu z možností na základě požadavků organizace.

Pro zpětnou vazbu centra shromažďování telemetrických dat na Windows 10 Enterprise více relací spusťte tento příkaz:

```batch
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection "AllowTelemetry"=dword:00000003
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection /v AllowTelemetry /d 3
```

Spusťte následující příkaz k vyřešení selhání aplikace Watson:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Zadejte následující příkazy do editoru registru a opravte 5 podporu k řešení. Před povolením zásobníku vedle sebe, je nutné spustit příkazy.

```batch
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Příprava bitové kopie nahrát do Azure

Po dokončení konfigurace a nainstalované všechny aplikace, postupujte podle pokynů v [Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) připravte si bitovou kopii.

Po přípravě image pro nahrávání, ujistěte se, že virtuální počítač zůstane ve stavu vypnuto nebo uvolnění.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Hlavní image odeslat do účtu úložiště v Azure

Tato část se týká pouze pokud hlavní bitová kopie bylo vytvořeno místně.

Postupujte podle následujících pokynů zjistíte, jak nahrát hlavní image do účtu služby Azure storage. Pokud ještě nemáte účet úložiště Azure, postupujte podle pokynů v [v tomto článku](https://code.visualstudio.com/tutorials/static-website/create-storage) k jejímu vytvoření.

1. Pokud jste tak dosud neučinili, převeďte na pevné image virtuálního počítače (VHD). Pokud bitovou kopii není převést na pevný, nelze úspěšně vytvořit image.

2. Nahrání virtuálního pevného disku do kontejneru objektů blob v účtu úložiště. Můžete nahrát rychle s [nástroj Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/). Další informace o nástroji Průzkumník služby Storage najdete v tématu [v tomto článku](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![Snímek obrazovky okna nástroje Microsoft Azure Storage Explorer vyhledávání. Je zaškrtnuto políčko "Nahrát VHD nebo vhdx soubory jako objekty BLOB stránky (doporučeno)".](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Dále přejděte na web Azure Portal ve svém prohlížeči a vyhledejte "Image". Hledání by měla vést k **vytvoření image** stránce, jak je znázorněno na následujícím snímku obrazovky:

    ![Snímek obrazovky stránky vytvořit image na webu Azure portal, naplní se ukázkové hodnoty pro bitovou kopii.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Po vytvoření image, by se zobrazit oznámení jako na následujícím snímku obrazovky:

    ![Snímek obrazovky oznámení "byla úspěšně vytvořena image".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Další postup

Teď, když máte image, můžete vytvořit nebo aktualizovat hostitele fondy. Další informace o tom, jak vytvořit a aktualizovat hostitele fondy, najdete v následujících článcích:

- [Vytvoření fondu hostitelů pomocí šablony Azure Resource Manageru](create-host-pools-arm-template.md)
- [Kurz: Vytvoření fondu hostitele pomocí webu Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Vytvoření hostitele fondu pomocí Powershellu](create-host-pools-powershell.md)
- [Nastavení sdílené složky profilu uživatele pro fond hostitele](create-host-pools-user-profile.md)
- [Konfigurace metody vyrovnávání zatížení virtuálního klienta Windows](configure-host-pool-load-balancing.md)
