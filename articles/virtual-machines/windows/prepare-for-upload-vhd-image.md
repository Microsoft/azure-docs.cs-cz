---
title: Příprava virtuálního pevného disku windows pro nahrání do Azure
description: Přečtěte si, jak připravit virtuální pevný disk windows nebo VHDX k nahrání do Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 8118ecde698b54213547e717d25613c0c3e0d3fd
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631560"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Příprava disku VHD nebo VHDX s Windows pro nahrání do Azure

Před nahráním virtuálního počítače (VM) Windows z místního do Azure je nutné připravit virtuální pevný disk (VHD nebo VHDX). Azure podporuje virtuální počítače generace 1 i generace 2, které jsou ve formátu souboru VHD a mají disk pevné velikosti. Maximální velikost pro virtuální pevný disk je 2 TB.

V generaci 1 Virtuální ho virtuálního disku můžete převést souborový systém VHDX na virtuální pevný disk. Dynamicky se rozšiřující disk můžete také převést na disk s pevnou velikostí. Ale nemůžete změnit generování virtuálního měn. Další informace najdete [v tématu Mám vytvořit generaci 1 nebo 2 virtuální počítač v Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) a [Azure podporu pro generování 2 virtuálních počítačů (preview)](generation-2.md).

Informace o zásadách podpory pro virtuální počítače Azure najdete v tématu [podpora softwaru pro servery Microsoft pro virtuální počítače Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> Pokyny v tomto článku se vztahují na:
>1. 64bitová verze operačních systémů Windows Server 2008 R2 a novějších. Informace o spuštění 32bitového operačního systému v Azure najdete v [tématu Podpora 32bitových operačních systémů v virtuálních počítačích Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).
>2. Pokud se k migraci úlohy použije nějaký nástroj zotavení po havárii, jako je Azure Site Recovery nebo Azure Migrate, je stále nutné tento proces provést a sledovat v hostovaném operačním systému k přípravě image před migrací.

## <a name="system-file-checker-sfc-command"></a>Příkaz Kontrola systémových souborů (SFC)

### <a name="run-windows-system-file-checker-utility-run-sfc-scannow-on-os-prior-to-generalization-step-of-creating-customer-os-image"></a>Spuštění nástroje Windows System File Checker (spuštění sfc /scannow) v osu před krokem generalizace vytvoření bitové kopie operačního systému zákazníka

Příkaz Kontrola systémových souborů (SFC) slouží k ověření a nahrazení systémových souborů systému Windows.

Spuštění příkazu SFC:

1. Otevřete výzvu CMD se zvýšenými oprávněními jako správce.
1. Zadejte `sfc /scannow` a vyberte **Enter**.

    ![Kontrola systémových souborů](media/prepare-for-upload-vhd-image/system-file-checker.png)


Po dokončení prohledávace SFC zkuste nainstalovat aktualizace systému Windows a restartujte počítač.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Převést virtuální disk na pevnou velikost a na Virtuální pevný disk

Pokud potřebujete převést virtuální disk do požadovaného formátu pro Azure, použijte jednu z metod v této části:

1. Před spuštěním procesu převodu virtuálního disku zálohte virtuální počítač.

1. Ujistěte se, že virtuální pevný disk systému Windows funguje správně na místním serveru. Vyřešte všechny chyby v rámci samotného virtuálního počítače, než se pokusíte převést nebo odeslat do Azure.

1. Pokud jde o velikost virtuálního pevného disku:

   1. Všechny virtuální počítače v Azure musí mít virtuální velikost zarovnanou na 1 MB. Při převodu ze surového disku na virtuální pevný disk je nutné zajistit, aby velikost nezpracovaného disku byla před převodem násobkem 1 MB. Zlomky megabajtu způsobí chyby při vytváření obrázků z nahraného virtuálního pevného disku.

   2. Maximální povolená velikost virtuálního pevného disku operačního systému je 2 TB.


Po převodu disku vytvořte virtuální počítače, který disk používá. Začněte a přihlaste se k virtuálnímu virtuálnímu virtuálnímu mněmu a dokončete jeho přípravu k nahrávání.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Převod disku pomocí Správce technologie Hyper-V 
1. Otevřete Správce technologie Hyper-V a vyberte místní počítač vlevo. V nabídce nad seznamem počítače vyberte **možnost Action** > **Edit Disk**.
2. Na stránce **Vyhledat virtuální pevný disk** vyberte virtuální disk.
3. Na stránce **Zvolit akci** vyberte **Převést** > **další**.
4. Pokud potřebujete převést z VHDX, vyberte **VHD** > **Další**.
5. Pokud potřebujete převést z dynamicky se rozšiřujícího disku, vyberte **Možnost Pevná velikost** > **Další**.
6. Vyhledejte a vyberte cestu, do které chcete uložit nový soubor VHD.
7. Vyberte **Finish** (Dokončit).

> [!NOTE]
> Ke spuštění příkazů v tomto článku použijte relaci prostředí PowerShell se zvýšenými oprávněními.

### <a name="use-powershell-to-convert-the-disk"></a>Převod disku pomocí prostředí PowerShell 
Virtuální disk můžete převést pomocí příkazu [Převést v rozlišení VHD](https://technet.microsoft.com/library/hh848454.aspx) v prostředí Windows PowerShell. Při spuštění PowerShellu vyberte **Spustit jako správce.** 

Následující ukázkový příkaz převede disk z vhdx na virtuální pevný disk. Příkaz také převede disk z dynamicky se rozšiřujícího disku na disk s pevnou velikostí.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

V tomto příkazu nahraďte hodnotu pro `-Path` cestou k virtuálnímu pevnému disku, který chcete převést. Nahraďte `-DestinationPath` hodnotu pro novou cestou a názvem převedeného disku.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Převod z formátu disku VMware VMDK
Pokud máte bitovou kopii virtuálního počítače se systémem Windows ve [formátu souboru VMDK](https://en.wikipedia.org/wiki/VMDK), převeďte ji do formátu [Virtuálního počítače.](https://www.microsoft.com/download/details.aspx?id=42497) Další informace naleznete [v tématu Převod sady VMware VMDK na virtuální pevné disky Hyper-V](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Nastavení konfigurací Windows pro Azure

> [!NOTE]
> Platforma Azure připojí soubor ISO na disk DVD-ROM při vytvoření virtuálního počítače se systémem Windows z generalizované bitové kopie.
> Z tohoto důvodu musí být disk DVD-ROM povolen v osu v generalizovaném obrázku. Pokud je zakázáno, virtuální ho důchl windows bude zaseknutý na OOBE.

Na virtuálním počítači, který chcete nahrát do Azure, spusťte následující příkazy z [okna příkazového řádku se zvýšenými oprávněními](https://technet.microsoft.com/library/cc947813.aspx):

1. Odeberte všechny statické trvalé trasy v směrovací tabulce:
   
   * Chcete-li zobrazit směrovací tabulku, spusťte `route print` na příkazovém řádku.
   * Zkontrolujte `Persistence Routes` sekce. Pokud existuje trvalá trasa, `route delete` odeberte ji pomocí příkazu.
2. Odebrání serveru proxy WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Pokud virtuální počítač potřebuje pracovat s konkrétním proxy serverem, přidejte výjimku proxy na IP adresu Azure ([168.63.129.16),](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)aby se virtuální počítač mohl připojit k Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Nastavte zásadu sítě [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)SAN na disk:
   
    ```PowerShell
    diskpart 
    ```
    Do otevřeného okna příkazového řádku zadejte následující příkazy:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Nastavte čas koordinovaného univerzálního času (UTC) pro systém Windows. Také nastavte typ spuštění služby`w32time`času `Automatic`systému Windows ( ) na :
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Nastavte profil napájení na vysoký výkon:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Ujistěte se, `TEMP` že `TMP` proměnné prostředí a jsou nastaveny na jejich výchozí hodnoty:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>Kontrola služeb pro Windows
Ujistěte se, že každá z následujících služeb systému Windows je nastavena na výchozí hodnoty systému Windows. Tyto služby jsou minimum, které musí být nastaveno k zajištění připojení virtuálních zařízení. Chcete-li obnovit nastavení spouštění, spusťte následující příkazy:
   
```PowerShell
Get-Service -Name bfe | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dhcp | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dnscache | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name IKEEXT | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name iphlpsvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name netlogon | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name netman | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name nsi | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name TermService | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name MpsSvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name RemoteRegistry | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
```
## <a name="update-remote-desktop-registry-settings"></a>Aktualizace nastavení registru vzdálené plochy
Zkontrolujte, zda jsou pro vzdálený přístup správně nakonfigurována následující nastavení:

>[!NOTE] 
>Při spuštění `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`se může zobrazit chybová zpráva . Tuto zprávu klidně ignorujte. To znamená pouze, že doména není tlačí tuto konfiguraci prostřednictvím objektu zásad skupiny.

1. Protokol RDP (Remote Desktop Protocol):
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. Port RDP je správně nastaven. Výchozí port je 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    Při nasazení virtuálního virtuálního vztahu se vytvoří výchozí pravidla proti portu 3389. Pokud chcete změnit číslo portu, udělejte to po nasazení virtuálního počítače v Azure.

3. Naslouchací proces naslouchá v každém síťovém rozhraní:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. Konfigurace režimu ověřování na úrovni sítě (NLA) pro připojení protokolu RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. Nastavte hodnotu keep-alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. Znovu:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. Omezte počet souběžných připojení:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. Odeberte všechny certifikáty podepsané svým držitelem svázané s naslouchací proces protokolu RDP:
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    Tento kód zajišťuje, že se můžete připojit na začátku při nasazení virtuálního aplikace. Pokud to potřebujete zkontrolovat později, můžete tak učinit po nasazení virtuálního počítače v Azure.

9. Pokud virtuální počítače bude součástí domény, zkontrolujte následující zásady a ujistěte se, že předchozí nastavení nejsou vráceny. 
    
    | Cíl                                     | Zásada                                                                                                                                                       | Hodnota                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Protokol RDP je povolen.                           | Konfigurace počítače\Zásady\Nastavení systému Windows\Šablony pro správu\Součásti\Služba vzdálená plocha\Hostitel relací vzdálené plochy\Připojení         | Povolit uživatelům vzdálené připojení pomocí vzdálené plochy                                  |
    | Zásady skupiny NLA                         | Nastavení\Šablony pro správu\Součásti\Služba vzdálená plocha\Hostitel relací vzdálené plochy\Zabezpečení                                                    | Vyžadovat ověření uživatele pro vzdálený přístup pomocí sítě NLA |
    | Nastavení udržování v životě                      | Konfigurace počítače\Zásady\Nastavení systému Windows\Šablony pro správu\Součásti systému Windows\Služba vzdálená plocha\Hostitel relací vzdálené plochy\Připojení | Konfigurace intervalu připojení udržování v stočné                                                 |
    | Znovu připojit nastavení                       | Konfigurace počítače\Zásady\Nastavení systému Windows\Šablony pro správu\Součásti systému Windows\Služba vzdálená plocha\Hostitel relací vzdálené plochy\Připojení | Automatické opětovné připojení                                                                   |
    | Omezený počet nastavení připojení | Konfigurace počítače\Zásady\Nastavení systému Windows\Šablony pro správu\Součásti systému Windows\Služba vzdálená plocha\Hostitel relací vzdálené plochy\Připojení | Omezit počet připojení                                                              |

## <a name="configure-windows-firewall-rules"></a>Konfigurace pravidel brány Windows Firewall
1. Zapněte bránu Windows Firewall na třech profilech (doména, standardní a veřejná):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Spuštěním následujícího příkazu v prostředí PowerShell povolte službu WinRM prostřednictvím tří profilů brány firewall (domény, soukromé a veřejné) a povolte vzdálenou službu Prostředí PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Povolte následující pravidla brány firewall, která povolují přenosy v programu RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Povolte pravidlo pro sdílení souborů a tiskáren, aby virtuální počítač mohl reagovat na příkaz ping uvnitř virtuální sítě:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Vytvořte pravidlo pro síť platformy Azure:

   ```PowerShell
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ``` 
6. Pokud virtuální počítač bude součástí domény, zkontrolujte následující zásady Azure AD a ujistěte se, že předchozí nastavení nejsou vráceny. 

    | Cíl                                 | Zásada                                                                                                                                                  | Hodnota                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Povolení profilů brány Windows Firewall | Konfigurace počítače\Zásady\Nastavení systému Windows\Šablony pro správu\Síť\Síťové připojení\Brána firewall systému Windows\Profil domény\Brána firewall systému Windows   | Ochrana všech síťových připojení         |
    | Povolit prv                           | Konfigurace počítače\Zásady\Nastavení systému Windows\Šablony pro správu\Síť\Síťové připojení\Brána firewall systému Windows\Profil domény\Brána firewall systému Windows   | Povolit výjimky příchozí vzdálené plochy |
    |                                      | Konfigurace počítače\Zásady\Nastavení systému Windows\Šablony pro správu\Síť\Síťové připojení\Brána firewall systému Windows\Standardní profil\Brána firewall systému Windows | Povolit výjimky příchozí vzdálené plochy |
    | Povolit ICMP-V4                       | Konfigurace počítače\Zásady\Nastavení systému Windows\Šablony pro správu\Síť\Síťové připojení\Brána firewall systému Windows\Profil domény\Brána firewall systému Windows   | Povolit výjimky PROTOKOLU ICMP                   |
    |                                      | Konfigurace počítače\Zásady\Nastavení systému Windows\Šablony pro správu\Síť\Síťové připojení\Brána firewall systému Windows\Standardní profil\Brána firewall systému Windows | Povolit výjimky PROTOKOLU ICMP                   |

## <a name="verify-the-vm"></a>Ověření virtuálního virtuálního mísy 

Ujistěte se, že virtuální hovirtuální ho dispoziční, zabezpečené a RDP přístupné: 

1. Chcete-li se ujistit, že je disk v pořádku a konzistentní, zkontrolujte disk při dalším restartování virtuálního počítače:

    ```PowerShell
    Chkdsk /f
    ```
    Ujistěte se, že sestava zobrazuje čistý a zdravý disk.

2. Nastavte nastavení spouštěcích konfiguračních dat (BCD). 

    > [!NOTE]
    > Pomocí okna prostředí PowerShell s vyšší mise spusťte tyto příkazy.
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. Protokol výpisu může být užitečný při řešení problémů se selháním systému Windows. Povolte kolekci protokolu výpisu:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Ověřte, zda je úložiště WMI (WMI) konzistentní:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Pokud je úložiště poškozeno, přečtěte si informace o poškození úložiště [nebo ne.](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)

5. Ujistěte se, že žádná jiná aplikace používá port 3389. Tento port se používá pro službu RDP v Azure. Chcete-li zjistit, které porty `netstat -anob`se používají na virtuálním počítači, spusťte :

    ```PowerShell
    netstat -anob
    ```

6. Odeslání virtuálního pevného disku se systémem Windows, který je řadičem domény:

   * Chcete-li připravit disk, postupujte [podle těchto dalších kroků.](https://support.microsoft.com/kb/2904015)

   * Ujistěte se, že znáte heslo režimu obnovení adresářových služeb (DSRM) v případě, že budete muset spustit virtuální ho v sm v DSRM v určitém okamžiku. Další informace naleznete [v tématu Nastavení hesla služby DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Ujistěte se, že znáte předdefinovaný účet správce a heslo. Můžete chtít obnovit aktuální heslo místního správce a ujistěte se, že tento účet můžete použít k přihlášení k systému Windows prostřednictvím připojení RDP. Toto přístupové oprávnění je řízeno objektem zásad skupiny "Povolit přihlášení prostřednictvím služby Vzdálená plocha". Tento objekt můžete zobrazit v Editoru místních zásad skupiny zde:

    Konfigurace počítače\Nastavení systému Windows\Nastavení zabezpečení\Místní zásady\Přiřazení uživatelských práv

8. Zkontrolujte následující zásady Azure AD a ujistěte se, že neblokujete přístup k RDP prostřednictvím RDP nebo ze sítě:

    - Konfigurace počítače\Nastavení systému Windows\Nastavení zabezpečení\Místní zásady\Přiřazení uživatelských práv\Odepřít přístup k tomuto počítači ze sítě

    - Konfigurace počítače\Nastavení systému Windows\Nastavení zabezpečení\Místní zásady\Přiřazení uživatelských práv\Odepřít přihlášení prostřednictvím služby Vzdálená plocha


9. Zkontrolujte následující zásady Azure AD a ujistěte se, že neodeberete žádný z požadovaných přístupových účtů:

   - Konfigurace počítače\Nastavení systému Windows\Nastavení zabezpečení\Místní zásady\Přiřazení uživatelských práv\Přístup k tomuto počítači ze sítě

   Zásady by měly uvádět následující skupiny:

   - Správci

   - Backup Operators

   - Everyone

   - Uživatelé

10. Restartujte virtuální počítač a ujistěte se, že systém Windows je stále v pořádku a lze dosáhnout prostřednictvím připojení RDP. V tomto okamžiku můžete chtít vytvořit virtuální počítač v místním Hyper-V a ujistěte se, že virtuální počítač spustí úplně. Pak otestujte, abyste se ujistili, že můžete dosáhnout virtuálního virtuálního připojení prostřednictvím RDP.

11. Odeberte všechny další filtry rozhraní transportního ovladače (TDI). Odstraňte například software, který analyzuje pakety TCP nebo další brány firewall. Pokud to potřebujete zkontrolovat později, můžete tak učinit po nasazení virtuálního počítače v Azure.

12. Odinstalujte jakýkoli jiný software nebo ovladač jiných výrobců, který souvisí s fyzickými součástmi nebo jinou virtualizační technologií.

### <a name="install-windows-updates"></a>Instalace aktualizací systému Windows
V ideálním případě byste měli udržovat počítač aktualizovaný na *úrovni opravy*. Pokud to není možné, ujistěte se, že jsou nainstalovány následující aktualizace. Nejnovější aktualizace najdete na stránkách historie aktualizací systému Windows: [Windows 10 a Windows Server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1 a Windows Server 2012 R2](https://support.microsoft.com/help/4009470) a [Windows 7 SP1 a Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469).

| Komponenta               | binární         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Úložiště                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | Ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Soubor Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Soubor Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Síť                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | Tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Jádro                    | Ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Vzdálená plocha | soubor rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | Termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | soubor rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Zabezpečení                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> Chcete-li se vyhnout náhodnému restartování během zřizování virtuálních počítačů, doporučujeme zajistit, aby všechny instalace služby Windows Update byly dokončeny a že nečekají na vyřízení žádné aktualizace. Jedním ze způsobů, jak to provést, je nainstalovat všechny možné aktualizace systému Windows a restartovat jednou před spuštěním příkazu Sysprep.

### <a name="determine-when-to-use-sysprep"></a>Určení doby použití sysprepu<a id="step23"></a>    

Nástroj Pro přípravu systému (Sysprep) je proces, který můžete spustit při resetování instalace systému Windows. Sysprep poskytuje prostředí "out of the box" odebráním všech osobních údajů a obnovením několika součástí. 

Obvykle spustíte sysprep vytvořit šablonu, ze které můžete nasadit několik dalších virtuálních počítačů, které mají určitou konfiguraci. Šablona se nazývá *zobecněný obrázek*.

Pokud chcete vytvořit pouze jeden virtuální virtuální počítače z jednoho disku, nemusíte používat program Sysprep. Místo toho můžete vytvořit virtuální hod ze *specializované image*. Informace o tom, jak vytvořit virtuální hod ze specializovaného disku, najdete v tématu:

- [Vytvoření virtuálního počítače ze specializovaného disku](create-vm-specialized.md)
- [Vytvoření virtuálního počítače ze specializovaného disku VHD](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Chcete-li vytvořit zobecněnou bitovou kopii, je třeba spustit sysprep. Další informace naleznete v tématu [Použití sysprepu: Úvod](https://technet.microsoft.com/library/bb457073.aspx). 

Ne každá role nebo aplikace, která je nainstalována v počítači se systémem Windows, podporuje generalizované bitové kopie. Před spuštěním tohoto postupu se tedy ujistěte, že program Sysprep podporuje roli počítače. Další informace naleznete v [tématu Podpora sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Generalize virtuálního pevného disku

>[!NOTE]
> Po spuštění `sysprep.exe` v následujících krocích vypněte virtuální ho. Nezapírejte ji znovu, dokud z ní v Azure nevytvoříte image.

1. Přihlaste se k virtuálnímu virtuálnímu mísu Windows.
1. Spusťte **příkazový řádek** jako správce. 
1. Změňte adresář `%windir%\system32\sysprep`na . Potom spusťte `sysprep.exe`.
1. V dialogovém okně **Nástroj pro přípravu systému** vyberte **Zobrazit prostředí prvního spuštění počítače** a ujistěte se, že je zaškrtnuté políčko **Generalizovat**.

    ![Nástroj pro přípravu systému](media/prepare-for-upload-vhd-image/syspre.png)
1. V **části Možnosti vypnutí**vyberte příkaz **Vypnout**.
1. Vyberte **OK**.
1. Po dokončení sysprep, vypněte virtuální ho. Nepoužívejte **restart k** vypnutí virtuálního počítače.

Nyní je virtuální disk připraven k nahrání. Další informace o tom, jak vytvořit virtuální počítač z generalizovaného disku, najdete v článku [Nahrání generalizovaného virtuálního pevného disku a jeho použití k vytvoření nového virtuálního počítače v Azure](sa-upload-generalized.md).


>[!NOTE]
> Vlastní soubor *unattend.xml* není podporován. I když podporujeme vlastnost, která poskytuje pouze omezenou `additionalUnattendContent` podporu pro přidání možností nastavení [microsoft-windows-shell](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) do souboru *unattend.xml,* který používá agent azure zřizování. Můžete například použít [dalšíUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) přidat FirstLogonCommands a LogonCommands. Další informace naleznete v [příkladu dalšíunattendContent FirstLogonCommands](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Dokončení doporučených konfigurací
Následující nastavení nemají vliv na nahrávání virtuálního pevného disku. Důrazně však doporučujeme, abyste je nakonfigurovali.

* Nainstalujte [agenta virtuálního počítače Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Pak můžete povolit rozšíření virtuálních míchacích. Rozšíření virtuálních počítače implementovat většinu důležité funkce, které můžete chtít použít s virtuálními počítači. Budete potřebovat rozšíření, například k resetování hesel nebo konfiguraci protokolu RDP. Další informace najdete v tématu [Azure Virtual Machine Agent overview](../extensions/agent-windows.md).
* Po vytvoření virtuálního počítače v Azure doporučujeme umístit stránkovací soubor na *objem časové jednotky* ke zlepšení výkonu. Umístění souboru můžete nastavit následujícím způsobem:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  Pokud je datový disk připojen k virtuálnímu počítači, je písmeno svazku časové jednotky obvykle *D*. Toto označení se může lišit v závislosti na vašem nastavení a počtu dostupných jednotek.
  * Doporučujeme zakázat blokování skriptů, které by mohly být poskytovány antivirovým softwarem. Mohou rušit a blokovat skripty Windows Provisioning Agent spuštěné při nasazení nového virtuálního počítače z vaší image.
  
## <a name="next-steps"></a>Další kroky
* [Nahrání image virtuálního počítače s Windows do nasazení Azure for Resource Manager](upload-generalized-managed.md)
* [Řešení potíží s aktivací virtuálního počítače Azure v systému Windows](troubleshoot-activation-problems.md)

