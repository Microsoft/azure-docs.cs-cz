---
title: Příprava virtuálního pevného disku Windows k nahrání do Azure | Dokumentace Microsoftu
description: Jak připravit Windows VHD nebo VHDX před nahráním do Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/10/2018
ms.author: genli
ms.openlocfilehash: 4d30cca0106e52706326bfd91a2d0dfb0a64ca04
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258448"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure
Před odesláním Windows virtuálních počítačů (VM) z místního na Microsoft Azure, musíte připravit virtuální pevný disk (VHD nebo VHDX). Azure podporuje **pouze virtuální počítače generace 1** , které jsou ve formátu souboru virtuálního pevného disku a mají pevnou velikostí disku. Maximální velikost povolenou pro virtuální pevný disk je 1,023 GB. Můžete převést generace 1 virtuální počítač z VHDX souborový systém pro virtuální pevný disk a z dynamicky se zvětšující disku na pevnou velikostí. Nelze však změnit generaci Virtuálního počítače. Další informace najdete v tématu [bych si měl vytvořit generace 1 nebo 2 virtuálních počítačů Hyper-v](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

Další informace o zásadách podpory pro virtuální počítač Azure najdete v tématu [podpora serverového softwaru Microsoftu pro virtuální počítače Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> Pokyny v tomto článku platí pro 64bitová verze systému Windows Server 2008 R2 a novější operační systém Windows server. Informace o spuštění 32bitové verzi operačního systému v Azure najdete v tématu [podpora pro 32bitové operační systémy ve službě Azure virtual machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Převést virtuální disk do virtuálního pevného disku a disk pevné velikosti 
Pokud je potřeba převést virtuální disk s požadovaným formátem pro Azure, použijte jednu z metod v této části. Zálohování virtuálního počítače před spuštěním procesu převodu virtuálního disku a ujistěte se, že virtuální pevný disk Windows funguje správně na místním serveru. Vyřešte všechny chyby v rámci Virtuálního počítače, než se pokusíte převést nebo nahrajte ho do Azure.

Po převedení disku vytvořte virtuální počítač, který používá převedený disk. Spuštění a přihlášení do virtuálního počítače k dokončení přípravy virtuálního počítače pro odesílání.

### <a name="convert-disk-using-hyper-v-manager"></a>Převod disku pomocí Správce technologie Hyper-V
1. Otevřete Správce technologie Hyper-V a vyberte místního počítače na levé straně. V nabídce nad seznamem počítačů, klikněte na tlačítko **akce** > **upravit Disk**.
2. Na **najít virtuální pevný Disk** obrazovky, vyhledejte a vyberte virtuální disk.
3. Na **vybrat akci** obrazovky a pak vyberte **převést** a **Další**.
4. Pokud je potřeba převést z VHDX, vyberte **virtuálního pevného disku** a potom klikněte na tlačítko **Další**.
5. Pokud je potřeba převést z dynamicky se zvětšující disku, vyberte **pevnou velikost** a potom klikněte na tlačítko **Další**.
6. Vyhledejte a vyberte cestu k uložit nový soubor virtuálního pevného disku.
7. Klikněte na **Dokončit**.

>[!NOTE]
>Příkazy v tomto článku je třeba spustit na relaci Powershellu se zvýšenými oprávněními.

### <a name="convert-disk-by-using-powershell"></a>Převod disku pomocí prostředí PowerShell
Můžete převést virtuální disk s použitím [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) příkazu v prostředí Windows PowerShell. Vyberte **spustit jako správce** při spuštění PowerShell. 

V tomto ukázkovém příkazu převede z VHDX na VHD a dynamicky se zvětšující disku pevné velikosti:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
V tomto příkazu nahraďte hodnotu pro "-cesta" s cestou virtuální pevný disk, který chcete převést a hodnota "-DestinationPath" s novou cestu a název převedený disku.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Převod z formátu VMware VMDK disku
Pokud máte image virtuálního počítače Windows ve [formát souboru VMDK](https://en.wikipedia.org/wiki/VMDK), převeďte jej na virtuální pevný disk s použitím [převaděč virtuálního počítače Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Další informace najdete v článku blogu [jak převést VMware VMDK na Hyper-V virtuálního pevného disku](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Konfigurace sady Windows Azure

Na virtuálním počítači, který chcete nahrát do Azure, spuštění všech příkazů v následujících krocích ze [okno příkazového řádku se zvýšenými oprávněními](https://technet.microsoft.com/library/cc947813.aspx):

1. Odeberte všechny statické trvalé trasy ve směrovací tabulce:
   
   * Chcete-li zobrazit směrovací tabulky, spusťte `route print` příkazového řádku.
   * Zkontrolujte **trasy trvalost** oddíly. Pokud trasu trvalé, použijte **trasy odstranit** příkazu odeberte ji.
2. Odebrání proxy serveru WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```
3. Nastavte zásady disku sítě SAN na [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    V otevřeném okně příkazového řádku zadejte následující příkazy:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Nastavení času na koordinovaný univerzální čas (UTC) pro Windows a typ spouštění služby Windows čas (w32time) k **automaticky**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Nastavte profil spotřeby na **vysoký výkon**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Ujistěte se, že proměnné prostředí **TEMP** a **TMP** jsou nastaveny na výchozí hodnoty:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Zkontrolujte službu Windows
Ujistěte se, že každý z následujících služeb Windows je nastavena na **Windows výchozí hodnoty**. Toto jsou minimální množství služeb, které musí být nastavené, abyste měli jistotu, že virtuální počítač je připojen. Pokud chcete resetovat nastavení spouštění, spusťte následující příkazy:
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>Aktualizace nastavení registru vzdálené plochy
Ujistěte se, že jsou správně nakonfigurované následující nastavení pro připojení ke vzdálené ploše:

>[!Note] 
>Můžete obdržet chybovou zprávu, když spustíte **Set-ItemProperty-cesta "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal služby - název &lt;název objektu&gt; – hodnota &lt;hodnotu&gt;**  v následujícím postupu. Zpráva chyby můžete bezpečně ignorovat. To pouze znamená, že doména není doručením (push) tuto konfiguraci pomocí objektu zásad skupiny.
>
>

1. Je povolen protokol RDP (Remote Desktop):
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. Je správně nastavený RDP port (výchozí port 3389):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    Při nasazení virtuálního počítače, výchozí pravidla se vytvářejí na port 3389. Pokud chcete změnit číslo portu, udělejte to po nasazení virtuálního počítače v Azure.

3. Naslouchací proces naslouchá v každé síťové rozhraní:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Konfigurovat režim ověření úrovně sítě pro připojení RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. Zachování hodnotu:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. Znovu připojte:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. Limit počtu souběžných připojení:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. Pokud neexistují žádné certifikáty podepsané svým držitelem vázané na naslouchací proces RDP, je odeberte:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Toto je chcete mít jistotu, že se můžete připojit na začátku při nasazení virtuálního počítače. Můžete také zkontrolovat to v pozdější fázi po nasazení virtuálního počítače v Azure v případě potřeby.

9. Pokud virtuální počítač součástí domény, zkontrolujte všechny následující nastavení, abyste měli jistotu, že se nevrátí původní nastavení. Zásady, které musí být zaškrtnuto, jsou následující:
    
    | Cíl                                     | Zásada                                                                                                                                                       | Hodnota                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Je povolen protokol RDP                           | Počítače\Zásady\Nastavení Settings\Administrative Templates\Components\Remote plocha\Hostitel relace vzdálené plochy\Připojení         | Umožní uživatelům vzdáleně připojit pomocí vzdálené plochy                                  |
    | Zásady skupiny NLA                         | Settings\Administrative Templates\Components\Remote plocha\Hostitel relace plochy plochy\Zabezpečení                                                    | Vyžadovat ověření uživatele pro vzdálená připojení pomocí ověření úrovně sítě. |
    | Zachovat nastavení Alive                      | Počítač počítače\Zásady\Nastavení Settings\Administrative pro správu\Součásti systému Windows\Vzdálená plocha\Hostitel vzdálené plochy\Připojení relace plochy | Nastavit interval zachování připojení                                                 |
    | Znovu připojte nastavení                       | Počítač počítače\Zásady\Nastavení Settings\Administrative pro správu\Součásti systému Windows\Vzdálená plocha\Hostitel vzdálené plochy\Připojení relace plochy | Automatické obnovení připojení                                                                   |
    | Limit počtu nastavení připojení | Počítač počítače\Zásady\Nastavení Settings\Administrative pro správu\Součásti systému Windows\Vzdálená plocha\Hostitel vzdálené plochy\Připojení relace plochy | Omezení počtu připojení                                                              |

## <a name="configure-windows-firewall-rules"></a>Konfigurace pravidel brány Windows Firewall
1. Zapněte bránu Windows Firewall na tři profily (domény, Standard a veřejný):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Spuštěním následujícího příkazu v prostředí PowerShell povolíte WinRM přes tří profilů brány firewall (Domain, Private a Public) a povolit službu vzdáleného prostředí PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Povolte následující pravidla brány firewall pro povolení provozu protokolu RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Povolení pravidla sdílení souborů a tiskáren tak, aby virtuální počítač může reagovat na příkaz ping uvnitř virtuální sítě:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Pokud virtuální počítač součástí domény, zkontrolujte následující nastavení a ujistěte se, že se nevrátí původní nastavení. Zásady AD, které musí být zaškrtnuto, jsou následující:

    | Cíl                                 | Zásada                                                                                                                                                  | Hodnota                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Povolení profilů brány Windows Firewall | Počítač počítače\Zásady\Nastavení Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Domain Profile\Windows brány Firewall   | Chránit všechna síťová připojení         |
    | Povolení protokolu RDP                           | Počítač počítače\Zásady\Nastavení Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Domain Profile\Windows brány Firewall   | Povolit výjimky příchozí vzdálené plochy |
    |                                      | Počítač počítače\Zásady\Nastavení Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Standard Profile\Windows brány Firewall | Povolit výjimky příchozí vzdálené plochy |
    | Povolte průchod protokolu ICMP V4                       | Počítač počítače\Zásady\Nastavení Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Domain Profile\Windows brány Firewall   | Povolit výjimky protokolu ICMP                   |
    |                                      | Počítač počítače\Zásady\Nastavení Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Standard Profile\Windows brány Firewall | Povolit výjimky protokolu ICMP                   |

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Ověřte, že virtuální počítač je v pořádku, zabezpečené a jsou přístupné pomocí protokolu RDP 
1. Pokud chcete mít jistotu, že disk je v pořádku a konzistentní vzhledem k aplikacím, spusťte operaci kontroly disku při dalším restartování virtuálního počítače:

    ```PowerShell
    Chkdsk /f
    ```
    Ujistěte se, že tato sestava zobrazuje v pořádku a vyčištění disku.

2. Nastavení konfiguračních dat spouštění (BCD). 

    > [!Note]
    > Ujistěte se, že spustíte tyto příkazy na okno prostředí PowerShell se zvýšenými oprávněními.
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. V protokolu s výpisem paměti mohou být užitečné při řešení potíží při selhání Windows. Povolení protokolu shromažďování výpisu stavu systému:

    ```powershell
    # Setup the Guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    #Setup the Guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Ověřte, zda je úložiště Windows Management Instrumentations konzistentní vzhledem k aplikacím. Chcete-li to provést, spusťte následující příkaz:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Pokud úložiště je poškozen, přečtěte si téma [rozhraní WMI: poškození úložiště, nebo Ne](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Ujistěte se, že všechny ostatní aplikace se používá port 3389. Tento port se používá pro protokol RDP službu v Azure. Můžete spustit **netstat - anob** zobrazíte, které jsou v používají porty na virtuálním počítači:

    ```PowerShell
    netstat -anob
    ```

6. Pokud virtuální pevný disk Windows, který chcete nahrát je řadič domény, postupujte podle těchto kroků:

    1. Postupujte podle [tyto dodatečné kroky](https://support.microsoft.com/kb/2904015) připravit na disku.

    1. Ujistěte se, že znáte heslo režimu obnovení adresářových služeb v případě, že budete muset spustit virtuální počítač v režimu obnovení adresářových služeb v určitém okamžiku. Možná budete chtít odkazovat na tento odkaz můžete nastavit [heslo režimu obnovení adresářových služeb](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Ujistěte se, že jsou pro vás známé předdefinovaného účtu správce a heslo. Můžete resetovat aktuální heslo místního správce a ujistěte se, že můžete použít tento účet pro přihlášení k Windows prostřednictvím připojení RDP. Tato oprávnění řídí objektu zásad skupiny "Povolit přihlášení prostřednictvím vzdálené plochy". Tento objekt lze zobrazit v Editor místních zásad skupiny v části:

    Konfigurace počítače\Nastavení systému Windows\Místní Zásady\přiřazení uživatelských práv

8. Zkontrolujte, že následující AD zásady, abyste měli jistotu, že nejsou blokuje přístup protokolu RDP přes protokol RDP ani ze sítě:

    - Konfigurace počítače\Nastavení systému Windows\Místní Zásady\přiřazení uživatelských práv\odepřít přístup počítačů k tomuto počítači ze sítě.

    - Počítač Konfigurace počítače\Nastavení systému Windows\Místní Zásady\přiřazení uživatelských práv\odepřít přihlášení prostřednictvím vzdálené plochy


9. Zkontrolujte tyto zásady AD, abyste měli jistotu, že neodstraníte kterýkoli z následujících požadovaných přístupové účty:

    - Počítače Konfigurace počítače\Nastavení systému Windows\Místní uživatelských práv Assignment\Access tato výpočetní ze sítě

    Tato zásada by měly být uvedeny následující skupiny:

    - Správci
    - Backup Operators
    - Všichni
    - Uživatelé

10. Restartování virtuálního počítače, abyste měli jistotu, že je Windows i nadále v pořádku lze dosáhnout pomocí připojení RDP. V tomto okamžiku můžete vytvořit virtuální počítač s vaší místní Hyper-v a ujistěte se, že virtuální počítač se spouští kompletně poté otestujte, zda je dostupný protokol RDP.

11. Odeberte všechny další filtry Transport Driver Interface, jako je například software, který analyzuje TCP paketů nebo další brány firewall. Můžete také zkontrolovat to v pozdější fázi po nasazení virtuálního počítače v Azure v případě potřeby.

12. Odinstalujte, další software třetích stran a ovladače, který souvisí s fyzické komponenty nebo jakoukoli jinou technologii virtualizace.

### <a name="install-windows-updates"></a>Instalace aktualizací Windows
Je ideální konfiguraci **počítače na nejnovější úroveň opravy**. Pokud to není možné, ujistěte se, že jsou nainstalované následující aktualizace:

| Komponenta               | Binární hodnota         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Verze Windows 10 verze serveru 2016 1607Windows 1607 | Windows 10 verze 1703    | Windows 10 1709 Windows serveru 2016 verze 1709 | Windows 10 1803Windows Server 2016 verzi 1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Úložiště                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
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
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Síť                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Jádro                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Vzdálená plocha | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Zabezpečení                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### Když použijete nástroj sysprep <a id="step23"></a>    

Balíček Sysprep je proces, který můžete spustit na instalaci systému windows, která resetuje instalace systému a bude poskytovat "připravené prostředí" odebrání všech osobních údajů a obnovení několika komponent. Obvykle to provedete Pokud chcete vytvořit šablonu, ze kterého můžete nasadit několik ostatní virtuální počítače, které mají určitou konfiguraci. Jedná se **generalizované image**.

Pokud místo toho chcete jenom pro účely vytvoření jednoho virtuálního počítače z jednoho disku, není nutné použít nástroj sysprep. V takovém případě stačí vytvořit virtuální počítač z toho, co se nazývá **specializované image**.

Další informace o vytvoření virtuálního počítače ze specializovaného disku najdete v tématu:

- [Vytvoření virtuálního počítače ze specializovaného disku](create-vm-specialized.md)
- [Vytvoření virtuálního počítače ze specializovaného disku VHD](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Pokud chcete vytvořit generalizované image, budete muset spustit nástroj sysprep. Další informace o nástroji Sysprep najdete v tématu [použití nástroje Sysprep: Úvod](https://technet.microsoft.com/library/bb457073.aspx). 

Ne každá role nebo aplikaci, která je nainstalovaná na počítači s Windows podporuje této generalizaci. Před použitím tohoto postupu naleznete v následujícím článku, abyste měli jistotu, že nástroj sysprep podporuje roli tohoto počítače. Další informace najdete [podpory nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="steps-to-generalize-a-vhd"></a>Kroky pro generalizaci virtuálního pevného disku

>[!NOTE]
> Po spuštění sysprep.exe uvedená v následujících krocích virtuální počítač vypnout a není ho znova zapnout až do vytvoření image z něj v Azure.

1. Přihlaste se k Windows virtuální počítač.
2. Spustit **příkazového řádku** jako správce. 
3. Změňte adresář na: **%windir%\system32\sysprep**a pak spusťte **sysprep.exe**.
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte **Zobrazit prostředí prvního spuštění počítače** a ujistěte se, že je zaškrtnuté políčko **Generalizovat**.

    ![Nástroj pro přípravu systému](media/prepare-for-upload-vhd-image/syspre.png)
4. V **možnosti vypnutí**vyberte **vypnutí**.
5. Klikněte na **OK**.
6. Po dokončení nástroj Sysprep vypnìte virtuální počítač. Nepoužívejte **restartovat** vypnutí virtuálního počítače.
7. Virtuální pevný disk je teď připravené k odeslání. Další informace o tom, jak vytvořit virtuální počítač z zobecněný disk najdete v tématu [nahrání generalizovaného virtuálního pevného disku a použít ho k vytvoření nové virtuální počítače v Azure](sa-upload-generalized.md).


## <a name="complete-recommended-configurations"></a>Dokončete doporučené konfigurace
Tato nastavení neovlivní nahrání virtuálního pevného disku. Nicméně důrazně doporučujeme, že jste je nakonfigurovali.

* Nainstalujte [agenta virtuální počítače Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Pak můžete povolit rozšíření virtuálních počítačů. Rozšíření virtuálních počítačů implementovat většinu důležitých funkcí, které může použít pomocí vašich virtuálních počítačů jako je resetování hesla, konfigurace protokolu RDP a tak dále. Další informace naleznete v tématu:

    - [Agent virtuálního počítače a rozšíření – část 1](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-1/)
    - [Agent virtuálního počítače a rozšíření – část 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)

*  Po vytvoření virtuálního počítače v Azure, doporučujeme umístit stránkovacího souboru na svazku "Dočasné jednotky" pro zlepšení výkonu. Můžete nastavit to následujícím způsobem:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile" -Type MultiString -force
    ```
Pokud je datový disk, který je připojen k virtuálnímu počítači, písmeno diskové jednotky svazku dočasné jednotky je obvykle "D" Toto označení se může lišit v závislosti na počtu dostupných jednotek a nastavení, které jste provedli.

## <a name="next-steps"></a>Další postup
* [Nahrání image virtuálního počítače Windows Azure pro nasazení Resource Manager](upload-generalized-managed.md)
* [Poradce při potížích aktivace virtuálního počítače Windows Azure](troubleshoot-activation-problems.md)

