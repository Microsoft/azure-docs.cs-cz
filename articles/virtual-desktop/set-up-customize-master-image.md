---
title: Příprava a přizpůsobení image hlavního virtuálního pevného disku – Azure
description: Postup přípravy, přizpůsobení a nahrání hlavní image virtuálního počítače s Windows do Azure
author: Heidilohr
ms.topic: how-to
ms.date: 01/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 654b5b293397af9737813d759a7c784d90629942
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445801"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Příprava a přizpůsobení hlavní image VHD

V tomto článku se dozvíte, jak připravit hlavní image virtuálního pevného disku (VHD) pro nahrání do Azure, včetně postupu při vytváření virtuálních počítačů a instalaci softwaru na tyto počítače. Tyto pokyny se týkají konfigurace specifické pro virtuální počítače s Windows, které se dají použít s existujícími procesy vaší organizace.

>[!IMPORTANT]
>Doporučujeme použít image z Galerie imagí Azure. Pokud ale potřebujete použít přizpůsobenou image, ujistěte se, že na svém VIRTUÁLNÍm počítači ještě není nainstalovaný agent virtuálních počítačů s Windows. Použití vlastní image s agentem virtuálních klientů Windows může způsobit problémy s imagí, jako je blokování registrace a prevence připojení uživatelské relace.  

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Více relací Windows 10 Enterprise je k dispozici v galerii imagí Azure. Existují dvě možnosti, jak tento obrázek přizpůsobit.

První možností je zřídit virtuální počítač (VM) v Azure podle pokynů v tématu [Vytvoření virtuálního počítače ze spravované image](../virtual-machines/windows/create-vm-generalized-managed.md)a následnou přeskočení na [přípravu softwaru a instalaci](set-up-customize-master-image.md#software-preparation-and-installation).

Druhou možností je vytvořit image místně tak, že si stáhnete image, zřídíte virtuální počítač s technologií Hyper-V a přizpůsobíte ji tak, aby vyhovovala vašim potřebám, které jsme si pokryli v následující části.

### <a name="local-image-creation"></a>Vytvoření místní image

Po stažení Image do místního umístění otevřete **Správce technologie Hyper-V** a vytvořte virtuální počítač se zkopírovaným virtuálním pevným diskem. V následujících pokynech je jednoduchá verze, ale můžete najít podrobnější pokyny v tématu [Vytvoření virtuálního počítače v Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Vytvoření virtuálního počítače se zkopírovaným virtuálním pevným diskem:

1. Otevřete **Průvodce novým virtuálním počítačem**.

2. Na stránce zadat generaci vyberte **generace 1**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se stránkou pro určení generace. Je vybrána možnost "generace 1".](media/a41174fd41302a181e46385e1e701975.png)

3. V části typ kontrolního bodu zakažte kontrolní body tím, že zrušíte jeho zrušení.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky oddílu s typem kontrolního bodu na stránce s kontrolními body](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Můžete taky spustit následující rutinu v PowerShellu a zakázat kontrolní body.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Pevný disk

Pokud vytvoříte virtuální počítač z existujícího virtuálního pevného disku, vytvoří se ve výchozím nastavení dynamický disk. Můžete ji změnit na pevný disk výběrem možnosti **Upravit disk...** , jak je znázorněno na následujícím obrázku. Podrobnější pokyny najdete v tématu [Příprava virtuálního pevného disku (VHDX) Windows pro nahrání do Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s možností upravit disk](media/35772414b5a0f81f06f54065561d1414.png)

Pomocí následující rutiny prostředí PowerShell můžete také změnit disk na pevný disk.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Příprava softwaru a instalace

V této části se dozvíte, jak připravit a instalovat FSLogix a Windows Defender a také některé základní možnosti konfigurace pro aplikace a registr vašich imagí.

Pokud na svém VIRTUÁLNÍm počítači instalujete Microsoft 365 aplikace pro Enterprise a OneDrive, přejděte k [instalaci Office na hlavní disk VHD](install-office-on-wvd-master-image.md) a postupujte podle pokynů pro instalaci aplikací. Až budete hotovi, vraťte se k tomuto článku.

Pokud uživatelé potřebují přístup k určitým aplikacím LOB, doporučujeme je nainstalovat po dokončení pokynů v této části.

### <a name="set-up-user-profile-container-fslogix"></a>Nastavení kontejneru profilu uživatele (FSLogix)

Pokud chcete zahrnout kontejner FSLogix jako součást image, postupujte podle pokynů v tématu [vytvoření kontejneru profilu pro fond hostitelů pomocí sdílené složky](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). V [tomto rychlém](/fslogix/configure-cloud-cache-tutorial/)startu můžete testovat funkčnost kontejneru FSLogix.

### <a name="configure-windows-defender"></a>Konfigurace programu Windows Defender

Pokud je ve virtuálním počítači nakonfigurovaný Windows Defender, ujistěte se, že je nakonfigurované tak, aby během přílohy nekontroloval celý obsah souborů VHD a VHDX.

Tato konfigurace odstraní jenom kontrolu souborů VHD a VHDX během přílohy, ale neovlivní kontrolu v reálném čase.

Podrobnější pokyny týkající se konfigurace Windows Defenderu na Windows serveru najdete v tématu [konfigurace vyloučení antivirové ochrany v programu Windows Defender na Windows serveru](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/).

Další informace o tom, jak nakonfigurovat Windows Defender pro vyloučení určitých souborů ze skenování, najdete v tématu [Konfigurace a ověření vyloučení na základě přípony souboru a umístění složky](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/).

### <a name="disable-automatic-updates"></a>Zakázat automatické aktualizace

Zakázání automatických aktualizací prostřednictvím místních Zásady skupiny:

1. Otevřete **Editor místních zásad skupiny \\ šablony pro správu \\ součásti systému Windows \\ web Windows Update**.
2. Klikněte pravým tlačítkem na **Konfigurovat Automatické aktualizace** a nastavte ji na **zakázáno**.

Můžete také spustit následující příkaz na příkazovém řádku a zakázat tak automatické aktualizace.

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Zadat počáteční rozložení pro počítače s Windows 10 (volitelné)

Spusťte tento příkaz a určete tak počáteční rozložení počítačů s Windows 10.

```cmd
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Nastavení přesměrování časového pásma

Přesměrování časového pásma se dá vyhovět na úrovni Zásady skupiny, protože všechny virtuální počítače ve fondu hostitelů jsou součástí stejné skupiny zabezpečení.

Postup přesměrování časových pásem:

1. Na serveru služby Active Directory otevřete **Konzola pro správu zásad skupiny**.
2. Rozbalíte své domény a Zásady skupiny objekty.
3. Klikněte pravým tlačítkem myši na **objekt Zásady skupiny** , který jste vytvořili pro nastavení zásad skupiny, a vyberte **Upravit**.
4. V **Editor pro správu zásad skupiny** přejděte na zásady **Konfigurace počítače**  >    >  **šablony pro správu**  >  **součásti systému Windows**  >  **Vzdálená plocha**  >  **hostitel relace vzdálené plochy**  >  **zařízení a přesměrování prostředků**.
5. Povolte nastavení **Povolit přesměrování časového pásma** .

Tento příkaz můžete také spustit v hlavní imagi pro přesměrování časových pásem:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Zakázat rozpoznávání úložiště

Pro hostitele relací virtuálních počítačů s Windows, kteří používají více relací Windows 10 Enterprise nebo Windows 10 Enterprise, doporučujeme zakázat smysl úložiště. Smysl úložiště můžete zakázat v nabídce nastavení v části **úložiště**, jak je znázorněno na následujícím snímku obrazovky:

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky nabídky úložiště v části nastavení. Možnost "rozpoznávání úložiště" je vypnuta.](media/storagesense.png)

Nastavení registru můžete změnit také spuštěním následujícího příkazu:

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Zahrnutí další jazykové podpory

Tento článek nepopisuje, jak nakonfigurovat jazyk a regionální podporu. Další informace najdete v následujících článcích:

- [Přidání jazyků do imagí Windows](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Funkce na vyžádání](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Funkce jazyků a oblastí na vyžádání (francouzské verze)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Další aplikace a konfigurace registru

Tato část se zabývá konfigurací aplikace a operačního systému. Veškerá konfigurace v této části se provádí prostřednictvím položek registru, které je možné spouštět pomocí nástrojů příkazového řádku a nástroje Regedit.

>[!NOTE]
>Osvědčené postupy v konfiguraci můžete implementovat buď pomocí objektů Zásady skupiny (GPO) nebo importů registru. Správce může zvolit jednu z možností podle požadavků organizace.

Pro shromažďování dat v centru Feedback v rámci více relací Windows 10 Enterprise můžete spustit tento příkaz:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Spusťte následující příkaz, který vyřeší chyby programu Watson:

```cmd
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Zadáním následujících příkazů do editoru registru opravíte podporu řešení 5k. Aby bylo možné povolit souběžný zásobník, je nutné spustit tyto příkazy.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Příprava image pro nahrání do Azure

Po dokončení konfigurace a instalace všech aplikací postupujte podle pokynů v tématu [Příprava virtuálního pevného disku (VHD) Windows nebo VHDX k nahrání do Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) pro přípravu image.

Po přípravě image pro nahrání se ujistěte, že virtuální počítač zůstane ve stavu vypnuto nebo zrušeno.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Nahrání hlavní image do účtu úložiště v Azure

Tato část platí jenom v případě, že se hlavní image vytvořila místně.

V následujících pokynech se dozvíte, jak nahrát hlavní bitovou kopii do účtu služby Azure Storage. Pokud ještě nemáte účet úložiště Azure, vytvořte ho podle pokynů v [tomto článku](/azure/developer/javascript/tutorial-vscode-static-website-node-03) .

1. Pokud jste to ještě neudělali, převeďte image virtuálního počítače (VHD) na pevnou. Pokud bitovou kopii nepřevedete na pevnou, nemůžete tuto image úspěšně vytvořit.

2. Nahrajte virtuální pevný disk do kontejneru objektů BLOB v účtu úložiště. Pomocí [nástroje Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/)můžete rychle nahrávat. Další informace o nástroji pro Průzkumník služby Storage najdete v [tomto článku](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows).

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky okna hledání nástroje pro Průzkumník služby Microsoft Azure Storage Zaškrtávací políčko nahrát soubory. VHD nebo VHDX jako objekty blob stránky (doporučeno) je zaškrtnuté.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. V dalším kroku přejdete do Azure Portal v prohlížeči a vyhledáte "image". Vaše hledání by vám mělo vést k **Vytvoření stránky s obrázkem** , jak je znázorněno na následujícím snímku obrazovky:

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se stránkou vytvořit obrázek v Azure Portal vyplněný příklady hodnot pro obrázek.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Po vytvoření image by se na následujícím snímku obrazovky měla zobrazit oznámení, jako je třeba ta:

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s oznámením o úspěšném vytvoření obrázku](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Další kroky

Teď, když máte image, můžete vytvořit nebo aktualizovat fondy hostitelů. Další informace o tom, jak vytvořit a aktualizovat fondy hostitelů, najdete v následujících článcích:

- [Vytvoření fondu hostitelů pomocí šablony Azure Resource Manageru](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)
- [Kurz: Vytvoření fondu hostitelů pomocí Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Vytvoření fondu hostitelů pomocí PowerShellu](create-host-pools-powershell.md)
- [Vytvoření kontejneru profilů pro fond hostitelů s využitím sdílené složky](create-host-pools-user-profile.md)
- [Konfigurace metody vyrovnávání zatížení Windows Virtual Desktop](configure-host-pool-load-balancing.md)

Pokud jste narazili na problém s připojením po přípravě nebo přizpůsobení image VHD, Projděte si nápovědu [Průvodce odstraňováním potíží](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) .
