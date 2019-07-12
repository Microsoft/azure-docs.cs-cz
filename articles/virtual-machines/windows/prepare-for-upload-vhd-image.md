---
title: Příprava virtuálního pevného disku Windows k nahrání do Azure | Dokumentace Microsoftu
description: Zjistěte, jak připravit Windows VHD nebo VHDX, který chcete nahrát do Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: f40b3e0d2a49f6522149a977572d4f3c12e34255
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720063"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Příprava Windows VHD nebo VHDX, který chcete nahrát do Azure

Než nahrajete z místního Windows virtuální počítač (VM) do Azure, musíte připravit virtuální pevný disk (VHD nebo VHDX). Azure podporuje generaci 1 i 2. generace virtuálních počítačů, které jsou ve formátu souboru virtuálního pevného disku a, které mají pevnou velikost disku. Maximální velikost povolenou pro virtuální pevný disk je 1,023 GB. 

V generaci 1 virtuální počítač, systém souborů VHDX můžete převést na virtuální pevný disk. Můžete také převést dynamicky se zvětšující disk na disk pevné velikosti. Nelze však změnit generaci Virtuálního počítače. Další informace najdete v tématu [bych si měl vytvořit generace 1 nebo 2 virtuálních počítačů Hyper-v?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) a [Azure podpora generace 2 virtuální počítače (preview)](generation-2.md).

Informace o zásadách podpory pro virtuální počítače Azure najdete v tématu [podpora serverového softwaru Microsoftu pro virtuální počítače Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> Pokyny v tomto článku platí pro 64bitová verze systému Windows Server 2008 R2 a novějších operačních systémech Windows Server. Informace o 32bitový operační systém v Azure najdete v tématu [podpora pro 32bitové operační systémy ve virtuálních počítačích Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Převést virtuální disk s pevnou velikostí a virtuálního pevného disku 
Pokud je potřeba převést virtuální disk s požadovaným formátem pro Azure, použijte jednu z metod v této části. Zálohování virtuálního počítače před převodem virtuálního disku. Ujistěte se, že virtuální pevný disk Windows správně fungovat na místním serveru. Vyřešte všechny chyby v rámci Virtuálního počítače, pak před pokusem o převod nebo nahrajte ho do Azure.

Po převodu na disk, vytvořte virtuální počítač, který používá disky. Spuštění a přihlášení do virtuálního počítače k dokončení přípravy pro nahrávání.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Pomocí Správce technologie Hyper-V můžete převést na disk 
1. Otevřete Správce technologie Hyper-V a vyberte místního počítače na levé straně. V nabídce nad seznamem počítače, vyberte **akce** > **upravit Disk**.
2. Na **najít virtuální pevný Disk** vyberte virtuální disk.
3. Na **vybrat akci** stránce **převést** > **Další**.
4. Pokud je potřeba převést z VHDX, vyberte **virtuálního pevného disku** > **Další**.
5. Pokud je potřeba převést z dynamicky se zvětšující disku, vyberte **pevnou velikost** > **Další**.
6. Vyhledejte a vyberte cestu k uložit nový soubor virtuálního pevného disku.
7. Vyberte **Finish** (Dokončit).

> [!NOTE]
> Ke spuštění příkazů v tomto článku použijte relaci Powershellu se zvýšenými oprávněními.

### <a name="use-powershell-to-convert-the-disk"></a>Použití Powershellu k převodu na disk 
Můžete převést virtuální disk s použitím [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) příkazu v prostředí Windows PowerShell. Vyberte **spustit jako správce** při spuštění PowerShell. 

V tomto ukázkovém příkazu převede disk z VHDX na VHD. Příkaz také převádí na disku z dynamicky se zvětšující disku na disk pevné velikosti.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

V tomto příkazu nahraďte hodnotu pro `-Path` s cestou na virtuální pevný disk, který má být převeden. Nahraďte hodnotu pro `-DestinationPath` s novou cestu a název převedený disku.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Převod z formátu VMware VMDK disku
Pokud máte image virtuálního počítače Windows ve [formát souboru VMDK](https://en.wikipedia.org/wiki/VMDK), použijte [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) jej převést na formát virtuálního pevného disku. Další informace najdete v tématu [jak převést VMware VMDK na Hyper-V virtuálního pevného disku](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Konfigurace sady Windows Azure

Na virtuálním počítači, který chcete nahrát do Azure, spusťte následující příkazy z [okno příkazového řádku se zvýšenými oprávněními](https://technet.microsoft.com/library/cc947813.aspx):

1. Odeberte všechny statické trvalé trasy ve směrovací tabulce:
   
   * Chcete-li zobrazit směrovací tabulky, spusťte `route print` příkazového řádku.
   * Zkontrolujte, `Persistence Routes` oddíly. Pokud je trvalou trasu, použijte `route delete` příkazu odeberte ji.
2. Odebrání proxy serveru WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Pokud virtuální počítač potřebuje pro práci s specifický proxy server, přidejte výjimky proxy serveru do Azure IP adresu ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)) tak, že virtuální počítač může připojit k Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Nastavte zásady disku sítě SAN na [ `Onlineall` ](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    V okně otevřít příkazový řádek zadejte následující příkazy:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Nastavení času na koordinovaný univerzální čas (UTC) pro Windows. Také nastavení typu spouštění služby Windows čas (`w32time`) k `Automatic`:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Nastavte profil spotřeby na vysoký výkon:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Ujistěte se, že proměnné prostředí `TEMP` a `TMP` jsou nastaveny na výchozí hodnoty:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Zkontrolujte službu Windows
Ujistěte se, že každý z následujících služeb Windows nastavený na výchozí hodnoty pro Windows. Tyto služby jsou minimální, který musí být nastavené pro zajištění možnosti připojení virtuálního počítače. Pokud chcete resetovat nastavení spouštění, spusťte následující příkazy:
   
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

## <a name="update-remote-desktop-registry-settings"></a>Aktualizace nastavení registru Vzdálená plocha
Ujistěte se, že následující nastavení jsou správně nakonfigurovány pro vzdálený přístup:

>[!NOTE] 
>Zobrazí se chybová zpráva při spuštění `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;object name&gt; -value &lt;value&gt;`. Tuto zprávu můžete ignorovat. To pouze znamená, že doména není doručením (push) tuto konfiguraci prostřednictvím objektu zásad skupiny.

1. Je povolen protokol RDP (Remote Desktop):
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. RDP port je správně nastavené. Výchozí port je 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    Při nasazení virtuálního počítače, výchozí pravidla se vytvářejí na port 3389. Pokud chcete změnit číslo portu, udělejte to po nasazení virtuálního počítače v Azure.

3. Naslouchací proces naslouchá v každé síťové rozhraní:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Konfigurovat režim ověření na úrovni síti (NLA) pro připojení RDP:
   
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
8. Odeberte jakékoli certifikáty podepsané svým držitelem vázané na naslouchací proces RDP:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Tento kód se zajistí, že se můžete připojit na začátku při nasazení virtuálního počítače. Pokud je potřeba zkontrolovat to později, můžete tak učinit po nasazení virtuálního počítače v Azure.

9. Pokud virtuální počítač součástí domény, zkontrolujte tyto zásady, abyste měli jistotu, že se nevrátí původní nastavení. 
    
    | Cíl                                     | Zásada                                                                                                                                                       | Value                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Je povolen protokol RDP                           | Počítače\Zásady\Nastavení Settings\Administrative Templates\Components\Remote plocha\Hostitel relace vzdálené plochy\Připojení         | Umožní uživatelům vzdáleně připojit pomocí vzdálené plochy                                  |
    | Zásady skupiny NLA                         | Settings\Administrative Templates\Components\Remote plocha\Hostitel relace plochy plochy\Zabezpečení                                                    | Vyžadovat ověření uživatele pro vzdálený přístup s použitím NLA |
    | Keep-alive nastavení                      | Počítač počítače\Zásady\Nastavení Settings\Administrative pro správu\Součásti systému Windows\Vzdálená plocha\Hostitel vzdálené plochy\Připojení relace plochy | Nastavit interval zachování připojení                                                 |
    | Znovu připojte nastavení                       | Počítač počítače\Zásady\Nastavení Settings\Administrative pro správu\Součásti systému Windows\Vzdálená plocha\Hostitel vzdálené plochy\Připojení relace plochy | Automaticky připojit znovu                                                                   |
    | Omezený počet nastavení připojení | Počítač počítače\Zásady\Nastavení Settings\Administrative pro správu\Součásti systému Windows\Vzdálená plocha\Hostitel vzdálené plochy\Připojení relace plochy | Omezení počtu připojení                                                              |

## <a name="configure-windows-firewall-rules"></a>Konfigurace pravidel brány Windows Firewall
1. Zapněte bránu Windows Firewall na tři profily (standardní a veřejné domény):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Spuštěním následujícího příkazu v Powershellu, kterou povolíte WinRM přes tří profilů brány firewall (privátní a veřejné domény) a povolit službu vzdáleného prostředí PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Povolte následující pravidla brány firewall pro povolení provozu protokolu RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Povolení pravidla pro souborovou službu a sdílení mohli reagovat virtuální počítač na příkaz ping uvnitř virtuální sítě:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Pokud virtuální počítač součástí domény, zkontrolujte následující zásady Azure AD, abyste měli jistotu, že se nevrátí původní nastavení. 

    | Cíl                                 | Zásada                                                                                                                                                  | Hodnota                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Povolení profilů brány Windows Firewall | Počítač počítače\Zásady\Nastavení Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Domain Profile\Windows brány Firewall   | Chránit všechna síťová připojení         |
    | Povolení protokolu RDP                           | Počítač počítače\Zásady\Nastavení Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Domain Profile\Windows brány Firewall   | Povolit výjimky příchozí vzdálené plochy |
    |                                      | Počítač počítače\Zásady\Nastavení Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Standard Profile\Windows brány Firewall | Povolit výjimky příchozí vzdálené plochy |
    | Povolte průchod protokolu ICMP V4                       | Počítač počítače\Zásady\Nastavení Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Domain Profile\Windows brány Firewall   | Povolit výjimky protokolu ICMP                   |
    |                                      | Počítač počítače\Zásady\Nastavení Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Standard Profile\Windows brány Firewall | Povolit výjimky protokolu ICMP                   |

## <a name="verify-the-vm"></a>Ověřte, virtuální počítač 

Ujistěte se, že virtuální počítač je v pořádku, zabezpečené a dostupný protokol RDP: 

1. Pokud chcete mít jistotu, že disk je v pořádku a konzistentní vzhledem k aplikacím, zkontrolujte disk v dalším restartování virtuálního počítače:

    ```PowerShell
    Chkdsk /f
    ```
    Ujistěte se, že tato sestava zobrazuje v pořádku a vyčištění disku.

2. Nastavení konfiguračních dat spouštění (BCD). 

    > [!NOTE]
    > Použijte okno prostředí PowerShell se zvýšenými oprávněními ke spuštění těchto příkazů.
   
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
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Ověřte, zda je úložiště Windows Management Instrumentation (WMI) konzistentní vzhledem k aplikacím:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Pokud úložiště je poškozen, přečtěte si téma [rozhraní WMI: Úložiště poškození nebo Ne](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Ujistěte se, že žádná jiná aplikace používá port 3389. Tento port se používá pro protokol RDP službu v Azure. Pokud chcete zobrazit porty, které se používají ve virtuálním počítači, spusťte `netstat -anob`:

    ```PowerShell
    netstat -anob
    ```

6. Nahrání virtuálního pevného disku Windows, který je řadič domény:

   * Postupujte podle [tyto dodatečné kroky](https://support.microsoft.com/kb/2904015) připravit na disku.

   * Zajistěte, aby že v případě, že budete muset spustit virtuální počítač v režimu obnovení adresářových služeb v určitém okamžiku se znát heslo Directory režimu obnovení služeb (DSRM). Další informace najdete v tématu [nastavit heslo režimu obnovení adresářových služeb](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Ujistěte se, že budete vědět, předdefinovaný účet správce a heslo. Můžete chtít resetovat aktuální heslo místního správce a ujistěte se, že tento účet můžete použít pro přihlášení k Windows prostřednictvím připojení RDP. Tato oprávnění řídí "Povolit přihlášení prostřednictvím vzdálené plochy" objekt zásad skupiny. Zobrazení tohoto objektu v Editor místních zásad skupiny tady:

    Konfigurace počítače\Nastavení systému Windows\Místní Zásady\přiřazení uživatelských práv

8. Zkontrolujte následující zásady Azure AD, abyste měli jistotu, že neblokují přístup protokolu RDP přes RDP nebo sítě:

    - Konfigurace počítače\Nastavení systému Windows\Místní Zásady\přiřazení uživatelských práv\odepřít přístup počítačů k tomuto počítači ze sítě.

    - Počítač Konfigurace počítače\Nastavení systému Windows\Místní Zásady\přiřazení uživatelských práv\odepřít přihlášení prostřednictvím vzdálené plochy


9. Zkontrolujte tyto zásady služby Azure AD k Ujistěte se, že neodstraníte jakýkoli požadovaný přístup:

   - Počítač Konfigurace počítače\Nastavení systému Windows\Místní uživatelských práv Assignment\Access tomuto počítači ze sítě

   Zásady by měly zobrazit následující skupiny:

   - Správci

   - Backup Operators

   - Všem uživatelům

   - Uživatelé

10. Restartujte virtuální počítač, abyste měli jistotu, že Windows i nadále v pořádku a lze dosáhnout prostřednictvím připojení RDP. V tomto okamžiku můžete chtít vytvořit virtuální počítač s vaší místní Hyper-v zajistit, aby že zcela spuštěním virtuálního počítače. Otestujte Ujistěte se, že můžete oslovit virtuální počítač prostřednictvím protokolu RDP.

11. Odeberte všechny další filtry rozhraní TDI (Transport Driver Interface). Třeba odebrat software, který analyzuje TCP paketů nebo další brány firewall. Pokud je potřeba zkontrolovat to později, můžete tak učinit po nasazení virtuálního počítače v Azure.

12. Odinstalujte, další software třetích stran nebo ovladač, který je součástí týkajících se fyzického nebo jakoukoli jinou technologii virtualizace.

### <a name="install-windows-updates"></a>Instalace aktualizací Windows
V ideálním případě byste měli mít na počítači aktualizovat i obsah *úroveň oprav*. Pokud to není možné, ujistěte se, že jsou nainstalované následující aktualizace:

| Komponenta               | Binary         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Storage                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
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
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
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
       
### Určení toho, kdy použijete nástroj Sysprep <a id="step23"></a>    

Nástroj pro přípravu systému (Sysprep) je proces, můžete spustit resetování instalace Windows. Nástroj Sysprep poskytuje funkce "mimo pole" tak, že odebrání všech osobních údajů a obnovení několika komponent. 

Obvykle běží nástroj Sysprep vytvořit šablonu, ze kterého můžete nasadit několik ostatní virtuální počítače, které mají určitou konfiguraci. Šablona je volána *generalizované image*.

Pokud chcete vytvořit pouze jeden virtuální počítač z jednoho disku, není nutné použít nástroj Sysprep. Místo toho můžete vytvořit virtuální počítač z *specializované image*. Informace o vytvoření virtuálního počítače ze specializovaného disku najdete tady:

- [Vytvoření virtuálního počítače ze specializovaného disku](create-vm-specialized.md)
- [Vytvoření virtuálního počítače ze specializovaného disku VHD](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Pokud chcete vytvořit generalizované image, budete muset spustit nástroj Sysprep. Další informace najdete v tématu [způsob použití nástroje Sysprep: Úvod](https://technet.microsoft.com/library/bb457073.aspx). 

Ne každá role nebo aplikaci, která je nainstalovaná na počítači s Windows podporuje generalizované Image. Proto předtím, než spustíte tuto proceduru, ujistěte se, že nástroj Sysprep podporuje roli počítače. Další informace najdete v tématu [podpory nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Generalizace virtuálního pevného disku

>[!NOTE]
> Po spuštění `sysprep.exe` v následujících krocích, virtuální počítač vypnout. Nemusíte ho znova zapnout až do vytvoření image z něj v Azure.

1. Přihlaste se k Windows virtuální počítač.
1. Spustit **příkazového řádku** jako správce. 
1. Změňte adresář na `%windir%\system32\sysprep`. Potom spusťte `sysprep.exe`.
1. V dialogovém okně **Nástroj pro přípravu systému** vyberte **Zobrazit prostředí prvního spuštění počítače** a ujistěte se, že je zaškrtnuté políčko **Generalizovat**.

    ![Nástroj pro přípravu systému](media/prepare-for-upload-vhd-image/syspre.png)
1. V **možnosti vypnutí**vyberte **vypnutí**.
1. Vyberte **OK**.
1. Po dokončení nástroj Sysprep, vypněte virtuální počítač. Nepoužívejte **restartovat** vypnutí virtuálního počítače.

Virtuální pevný disk je teď připravené k odeslání. Další informace o tom, jak vytvořit virtuální počítač z zobecněný disk najdete v tématu [nahrání generalizovaného virtuálního pevného disku a použít ho k vytvoření nového virtuálního počítače v Azure](sa-upload-generalized.md).


>[!NOTE]
> Vlastní *unattend.xml* souboru se nepodporuje. I když se podporuje `additionalUnattendContent` vlastnost, která poskytuje jenom omezenou podporu pro přidání [microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) možnosti do *unattend.xml* souboru, který zřizování Azure Agent použije. Můžete použít, například [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) přidat FirstLogonCommands a LogonCommands. Další informace najdete v tématu [additionalUnattendContent FirstLogonCommands příklad](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Dokončete doporučené konfigurace
Tato nastavení nemají vliv na nahrání virtuálního pevného disku. Nicméně důrazně doporučujeme, že jste je nakonfigurovali.

* Nainstalujte [Agent virtuálního počítače Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Pak můžete povolit rozšíření virtuálních počítačů. Rozšíření virtuálních počítačů implementovat většinu důležitých funkcí, které můžete chtít použít pomocí vašich virtuálních počítačů. Rozšíření, budete potřebovat například k resetování hesel nebo konfigurace protokolu RDP. Další informace najdete v tématu [agenta virtuálního počítače Azure přehled](../extensions/agent-windows.md).
* Po vytvoření virtuálního počítače v Azure, doporučujeme umístit stránkovacího souboru *dočasné jednotky svazku* ke zlepšení výkonu. Můžete nastavit umístění souboru následujícím způsobem:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  Pokud do virtuálního počítače je připojen datový disk, písmeno svazku dočasné jednotky je obvykle *D*. Toto označení se může lišit v závislosti na nastavení a počtu dostupných jednotek.

## <a name="next-steps"></a>Další postup
* [Nahrání image virtuálního počítače Windows Azure pro nasazení Resource Manager](upload-generalized-managed.md)
* [Poradce při potížích aktivace virtuálního počítače Windows Azure](troubleshoot-activation-problems.md)

