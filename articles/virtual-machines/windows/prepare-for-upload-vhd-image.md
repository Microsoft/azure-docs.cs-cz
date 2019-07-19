---
title: Příprava virtuálního pevného disku s Windows pro nahrání do Azure | Microsoft Docs
description: Informace o přípravě virtuálního pevného disku (VHD) nebo VHDX Windows pro nahrání do Azure
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
ms.openlocfilehash: a0cd2952633293bfa1d29bf3a399c67bf092d288
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318317"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Příprava virtuálního pevného disku (VHD) nebo VHDX systému Windows pro nahrání do Azure

Než nahrajete virtuální počítač s Windows z místního počítače do Azure, musíte připravit virtuální pevný disk (VHD nebo VHDX). Azure podporuje virtuální počítače generace 1 i generace 2, které jsou ve formátu souboru VHD a mají disk s pevnou velikostí. Maximální velikost povolená pro virtuální pevný disk je 1 023 GB. 

V případě virtuálního počítače 1. generace můžete převést systém souborů VHDX na VHD. Dynamicky se zvětšující disk můžete také převést na disk s pevnou velikostí. Nemůžete ale změnit generaci virtuálního počítače. Další informace najdete v tématu [Vytvoření virtuálního počítače generace 1 nebo 2 v Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) a [Podpora Azure pro virtuální počítače 2. generace (Preview)](generation-2.md).

Informace o zásadách podpory pro virtuální počítače Azure najdete v tématu [podpora serverového softwaru společnosti Microsoft pro virtuální počítače Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> Pokyny v tomto článku se týkají:
>1. 64 verze systému Windows Server 2008 R2 a novějších operačních systémů Windows Server. Informace o spuštění 32 operačního systému v Azure najdete v tématu [Podpora pro 32 operační systémy ve virtuálních počítačích Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).
>2. Pokud bude k migraci úlohy, jako je Azure Site Recovery nebo Azure Migrate, použit libovolný nástroj pro zotavení po havárii, je nutné tento proces ještě provést a následně na hostovaném operačním systému připravit image před migrací.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Převést virtuální disk na pevnou velikost a na VHD

Pokud potřebujete převést virtuální disk na požadovaný formát pro Azure, použijte jednu z metod v této části:

1. Před spuštěním procesu převodu virtuálního disku zálohujte virtuální počítač.

1. Ujistěte se, že virtuální pevný disk Windows funguje na místním serveru správně. Než se pokusíte převést nebo odeslat do Azure, vyřešte všechny chyby v samotném virtuálním počítači.

1. O velikosti virtuálního pevného disku:

   1. Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnaná na 1 MB. Při převodu z nezpracovaného disku na VHD je potřeba zajistit, aby velikost nezpracovaného disku byla před převodem násobkem 1 MB. Zlomky megabajtů způsobí chyby při vytváření imagí z nahraného virtuálního pevného disku.

   2. Maximální velikost povolená pro virtuální pevný disk s operačním systémem je 2 TB.


Po převedení disku vytvořte virtuální počítač, který používá disk. Začněte tím, že se přihlásíte k virtuálnímu počítači a dokončíte přípravu k odeslání.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Použití Správce technologie Hyper-V k převedení disku 
1. Otevřete Správce technologie Hyper-V a na levé straně vyberte svůj místní počítač. V nabídce nad seznamem počítač vyberte **Akce** > **Upravit disk**.
2. Na stránce **najít virtuální pevný disk** vyberte svůj virtuální disk.
3. Na stránce **Zvolte akci** vyberte **převést** > **Další**.
4. Pokud potřebujete převod z VHDX, vyberte VHD**Next**( **virtuální pevný disk** > ).
5. Pokud potřebujete převod z dynamicky se zvětšující disk, vyberte v poli **Pevná velikost** > možnost**Další**.
6. Vyhledejte a vyberte cestu, do které chcete uložit nový soubor VHD.
7. Vyberte **Finish** (Dokončit).

> [!NOTE]
> Pomocí relace PowerShellu se zvýšenými oprávněními spusťte příkazy v tomto článku.

### <a name="use-powershell-to-convert-the-disk"></a>Použití PowerShellu k převedení disku 
Virtuální disk můžete převést pomocí příkazu [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) ve Windows PowerShellu. Po spuštění PowerShellu vyberte **Spustit jako správce** . 

Následující příklad příkazu převede disk z VHDX na VHD. Příkaz také převede disk z dynamicky se zvětšující disk na disk s pevnou velikostí.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

V tomto příkazu nahraďte hodnotu pro `-Path` cestu k virtuálnímu pevnému disku, který chcete převést. Nahraďte hodnotu pro `-DestinationPath` novou cestou a názvem převedeného disku.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Převést z formátu disku VMDK VMware
Pokud máte image virtuálního počítače s Windows ve [formátu souboru VMDK](https://en.wikipedia.org/wiki/VMDK), převeďte ji pomocí [převaděče Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) na formát VHD. Další informace najdete v tématu [Postup převedení VMDK VMDK na VHD na virtuální pevný disk Hyper-V](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Nastavení konfigurace systému Windows pro Azure

Na virtuálním počítači, který plánujete odeslat do Azure, spusťte následující příkazy z [okna příkazového řádku se zvýšenými oprávněními](https://technet.microsoft.com/library/cc947813.aspx):

1. Odeberte všechny statické trvalé trasy ve směrovací tabulce:
   
   * Chcete-li zobrazit směrovací tabulku, `route print` spusťte příkaz v příkazovém řádku.
   * Podívejte se `Persistence Routes` na oddíly. Pokud je k dispozici trvalá trasa, `route delete` odeberte ji pomocí příkazu.
2. Odeberte proxy server WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Pokud virtuální počítač potřebuje pracovat s konkrétním proxy serverem, přidejte výjimku proxy serveru do IP adresy Azure ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)), aby se virtuální počítač mohl připojit k Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Nastavte zásadu pro diskovou síť [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)San na:
   
    ```PowerShell
    diskpart 
    ```
    V okně otevřít příkazový řádek zadejte následující příkazy:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Nastavte koordinovaný světový čas (UTC) pro Windows. Nastavte také typ spouštění služby Windows Time Service (`w32time`) na: `Automatic`
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Nastavte profil napájení na vysoký výkon:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Zajistěte, aby `TEMP` byly `TMP` proměnné prostředí a nastaveny na výchozí hodnoty:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Kontrolovat služby systému Windows
Ujistěte se, že všechny následující služby systému Windows jsou nastaveny na výchozí hodnoty Windows. Tyto služby mají minimální hodnotu, která musí být nastavená tak, aby se zajistilo připojení virtuálního počítače. Chcete-li obnovit nastavení spuštění, spusťte následující příkazy:
   
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

## <a name="update-remote-desktop-registry-settings"></a>Aktualizovat nastavení registru vzdálené plochy
Ujistěte se, že jsou pro vzdálený přístup správně nakonfigurovaná následující nastavení:

>[!NOTE] 
>Při spuštění `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;object name&gt; -value &lt;value&gt;`aplikace se může zobrazit chybová zpráva. Tuto zprávu můžete bez obav ignorovat. Znamená to, že doména nenabízí tuto konfiguraci prostřednictvím objektu Zásady skupiny.

1. Protokol RDP (Remote Desktop Protocol) (RDP) je povolený:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. Port RDP je nastavený správně. Výchozí port je 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    Když nasadíte virtuální počítač, vytvoří se výchozí pravidla pro port 3389. Pokud chcete změnit číslo portu, udělejte to po nasazení virtuálního počítače v Azure.

3. Naslouchací proces naslouchá každé síťové rozhraní:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Nakonfigurujte režim ověřování na úrovni sítě (NLA) pro připojení RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. Nastavte hodnotu Keep-Alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. Znovu připojit
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. Omezte počet souběžných připojení:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. Odebrání všech certifikátů podepsaných svým držitelem vázaných na naslouchací proces protokolu RDP:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Tento kód zajišťuje, že se můžete připojit na začátku při nasazení virtuálního počítače. Pokud to budete potřebovat později, můžete tak učinit až po nasazení virtuálního počítače v Azure.

9. Pokud bude virtuální počítač součástí domény, zkontrolujte následující zásady, abyste se ujistili, že předchozí nastavení nebudou obnovená. 
    
    | Cíl                                     | Zásada                                                                                                                                                       | Hodnota                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Protokol RDP je povolený.                           | Computer cestě konfigurace Settings\Administrative Templates\Components\Remote Desktop – pracovní relace, Host\Connections         | Umožňuje uživatelům vzdálené připojení pomocí vzdálené plochy.                                  |
    | NLA – zásady skupiny                         | Settings\Administrative Templates\Components\Remote Desktop – relace pro pracovní plochu – Host\Security                                                    | Vyžadovat ověření uživatele pro vzdálený přístup pomocí NLA |
    | Nastavení Keep-Alive                      | Computer cestě konfigurace Settings\Administrative pro správu \ součásti systému \ pracovní plocha – relace – Host\Connections | Konfigurace intervalu připojení Keep-Alive                                                 |
    | Znovu připojit nastavení                       | Computer cestě konfigurace Settings\Administrative pro správu \ součásti systému \ pracovní plocha – relace – Host\Connections | Znovu připojit automaticky                                                                   |
    | Omezený počet nastavení připojení | Computer cestě konfigurace Settings\Administrative pro správu \ součásti systému \ pracovní plocha – relace – Host\Connections | Omezení počtu připojení                                                              |

## <a name="configure-windows-firewall-rules"></a>Konfigurace pravidel brány Windows Firewall
1. Zapnout bránu Windows Firewall na třech profilech (doména, Standard a veřejné):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Spuštěním následujícího příkazu v PowerShellu povolte WinRM přes tři profily brány firewall (domény, privátní a veřejné) a povolte vzdálenou službu prostředí PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Povolte následující pravidla brány firewall, aby se povolil provoz protokolu RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Povolte pravidlo pro sdílení souborů a tiskáren, aby virtuální počítač mohl reagovat na příkaz příkazu testovat v rámci virtuální sítě:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Pokud bude virtuální počítač součástí domény, zkontrolujte následující zásady služby Azure AD a ujistěte se, že předchozí nastavení nejsou obnovená. 

    | Cíl                                 | Zásada                                                                                                                                                  | Value                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Povolit profily brány Windows Firewall | Computer cestě konfigurace Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Chránit všechna síťová připojení         |
    | Povolit protokol RDP                           | Computer cestě konfigurace Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Povolit příchozí výjimky vzdálené plochy |
    |                                      | Computer cestě konfigurace Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows firewall | Povolit příchozí výjimky vzdálené plochy |
    | Povolit ICMP-v4                       | Computer cestě konfigurace Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Povolení výjimek ICMP                   |
    |                                      | Computer cestě konfigurace Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows firewall | Povolení výjimek ICMP                   |

## <a name="verify-the-vm"></a>Ověřit virtuální počítač 

Ujistěte se, že je virtuální počítač v pořádku, zabezpečený a dostupný protokol RDP: 

1. Pokud chcete mít jistotu, že je disk v pořádku a konzistentní, zkontrolujte disk při příštím restartování virtuálního počítače:

    ```PowerShell
    Chkdsk /f
    ```
    Ujistěte se, že se v sestavě zobrazuje disk s čistým a dobrým diskem.

2. Nastavte nastavení konfigurační data spouštění (BCD). 

    > [!NOTE]
    > Pro spuštění těchto příkazů použijte okno prostředí PowerShell se zvýšenými oprávněními.
   
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
3. Protokol výpisu paměti může být užitečný při řešení potíží s chybami systému Windows. Povolit shromažďování protokolů výpisu paměti:

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
4. Ověřte, že úložiště rozhraní WMI (Windows Management Instrumentation) (WMI) je konzistentní:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Pokud je úložiště poškozené, přečtěte [si téma WMI: Poškození úložiště nebo ne](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Ujistěte se, že žádná jiná aplikace nepoužívá port 3389. Tento port se používá pro službu RDP v Azure. Chcete-li zjistit, které porty jsou na virtuálním `netstat -anob`počítači používány, spusťte příkaz:

    ```PowerShell
    netstat -anob
    ```

6. Postup nahrání virtuálního pevného disku s Windows, který je řadičem domény:

   * Při přípravě disku postupujte podle [těchto dalších kroků](https://support.microsoft.com/kb/2904015) .

   * Ujistěte se, že znáte heslo režimu obnovení adresářových služeb (DSRM) pro případ, že je potřeba spustit virtuální počítač v režimu DSRM v nějakém okamžiku. Další informace najdete v tématu [Nastavení hesla pro režim DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Ujistěte se, že znáte integrovaný účet správce a heslo. Je možné obnovit aktuální heslo místního správce a ověřit, zda můžete použít tento účet pro přihlášení k systému Windows prostřednictvím připojení RDP. Tato přístupová oprávnění se řídí pomocí objektu Zásady skupiny oprávnění "povolování prostřednictvím služby Vzdálená plocha". Zobrazit tento objekt v Editor místních zásad skupiny zde:

    Přiřazení práv k počítači \ \ Konfigurace \ přiřazení uživatelských

8. Zkontrolujte následující zásady služby Azure AD, abyste se ujistili, že neblokujete přístup k protokolu RDP prostřednictvím protokolu RDP nebo ze sítě:

    - Počítač \ \ \ přiřazení uživatelských Rights odepřít přístup k tomuto počítači ze sítě

    - Počítač \ \ \ přiřazení uživatelských Rights Odepřít přihlášení prostřednictvím služby Vzdálená plocha


9. Zkontrolujte následující zásady služby Azure AD, abyste se ujistili, že neodebíráte žádné z požadovaných účtů pro přístup:

   - Počítač \ \ \ přiřazení uživatelských práva Assignment\Access tento počítač ze sítě

   Zásada by měla zobrazovat následující skupiny:

   - Správci

   - Operátoři zálohování

   - Všemi

   - Uživatelé

10. Restartujte virtuální počítač, abyste se ujistili, že je systém Windows stále v pořádku a že je možné ho spojit s připojením RDP. V tuto chvíli můžete chtít vytvořit virtuální počítač v místní technologii Hyper-V, abyste se ujistili, že se virtuální počítač spustí úplně. Pak se otestujte a ujistěte se, že máte přístup k virtuálnímu počítači přes RDP.

11. Odeberte jakékoli další filtry rozhraní TDI (Transport Driver Interface). Můžete například odebrat software, který analyzuje pakety TCP nebo brány firewall navíc. Pokud to budete potřebovat později, můžete tak učinit až po nasazení virtuálního počítače v Azure.

12. Odinstalujte všechny další software nebo ovladače třetí strany, které se vztahují k fyzickým součástem nebo jiné virtualizační technologii.

### <a name="install-windows-updates"></a>Nainstalovat aktualizace Windows
V ideálním případě byste měli udržovat počítač aktualizovaný na *úrovni opravy*. Pokud to není možné, ujistěte se, že jsou nainstalované následující aktualizace:

| Komponenta               | Binary         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Storage                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 – KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
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
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 – KB3000850         | 10.0.14393.0 – KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
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
       
### Určení použití nástroje Sysprep<a id="step23"></a>    

Nástroj pro přípravu systému (Sysprep) je proces, který můžete použít k resetování instalace Windows. Nástroj Sysprep nabízí "nepoužívané" prostředí odebráním všech osobních údajů a obnovením několika součástí. 

Pokud chcete vytvořit šablonu, ze které můžete nasadit několik dalších virtuálních počítačů, které mají specifickou konfiguraci, je obvykle třeba spustit nástroj Sysprep. Šablona se nazývá zobecněná *Image*.

Pokud chcete vytvořit jenom jeden virtuální počítač z jednoho disku, nemusíte používat nástroj Sysprep. Místo toho můžete vytvořit virtuální počítač z *specializované image*. Informace o tom, jak vytvořit virtuální počítač z specializovaného disku, najdete v těchto tématech:

- [Vytvoření virtuálního počítače z specializovaného disku](create-vm-specialized.md)
- [Vytvoření virtuálního počítače ze specializovaného disku VHD](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Pokud chcete vytvořit zobecněnou bitovou kopii, je nutné spustit nástroj Sysprep. Další informace najdete v tématu [použití nástroje Sysprep: Úvod](https://technet.microsoft.com/library/bb457073.aspx). 

Ne každá role nebo aplikace, která je nainstalovaná na počítači se systémem Windows, podporuje generalizované bitové kopie. Takže před spuštěním tohoto postupu se ujistěte, že nástroj Sysprep podporuje roli počítače. Další informace najdete v tématu [Podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Generalizace virtuálního pevného disku

>[!NOTE]
> Po spuštění `sysprep.exe` v následujících krocích vypněte virtuální počítač. Neměňte ho, dokud z něho nevytvoříte image v Azure.

1. Přihlaste se k virtuálnímu počítači s Windows.
1. Spusťte **příkazový řádek** jako správce. 
1. Změňte adresář na `%windir%\system32\sysprep`. Potom spusťte `sysprep.exe`.
1. V dialogovém okně **Nástroj pro přípravu systému** vyberte **Zobrazit prostředí prvního spuštění počítače** a ujistěte se, že je zaškrtnuté políčko **Generalizovat**.

    ![Nástroj pro přípravu systému](media/prepare-for-upload-vhd-image/syspre.png)
1. V **Možnosti vypnutí**vyberte **vypnout**.
1. Vyberte **OK**.
1. Po dokončení programu Sysprep vypněte virtuální počítač. Nepoužívejte **restart** pro vypnutí virtuálního počítače.

Virtuální pevný disk je teď připravený k nahrání. Další informace o tom, jak vytvořit virtuální počítač z zobecněného disku, najdete v tématu [nahrání zobecněného virtuálního pevného disku a jeho použití k vytvoření nového virtuálního počítače v Azure](sa-upload-generalized.md).


>[!NOTE]
> Vlastní soubor *Unattend. XML* není podporován. I když podporujeme `additionalUnattendContent` vlastnost, která poskytuje jenom omezené podpory pro přidání možností [Microsoft-Windows-Shell-Setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) do souboru *Unattend. XML* , který používá agent zřizování Azure. Můžete použít například [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) k přidání FirstLogonCommands a LogonCommands. Další informace najdete v tématu [AdditionalUnattendContent FirstLogonCommands example](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Dokončete Doporučené konfigurace.
Následující nastavení neovlivní nahrávání VHD. Důrazně ale doporučujeme, abyste je nakonfigurovali.

* Nainstalujte [agenta virtuálního počítače Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Pak můžete povolit rozšíření virtuálních počítačů. Rozšíření virtuálních počítačů implementují většinu nejdůležitějších funkcí, které byste mohli chtít použít u virtuálních počítačů. Budete potřebovat rozšíření, například pro resetování hesel nebo konfiguraci protokolu RDP. Další informace najdete v tématu [Přehled agenta virtuálních počítačů Azure](../extensions/agent-windows.md).
* Po vytvoření virtuálního počítače v Azure doporučujeme umístit stránkovací soubor na *svazek dočasné jednotky* , aby se zlepšil výkon. Umístění souboru můžete nastavit následujícím způsobem:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  Pokud je k virtuálnímu počítači připojený datový disk, je písmeno jednotky dočasné jednotky obvykle *D*. Toto označení může být odlišné v závislosti na nastavení a počtu dostupných jednotek.

## <a name="next-steps"></a>Další postup
* [Nahrání image virtuálního počítače s Windows do Azure pro nasazení Správce prostředků](upload-generalized-managed.md)
* [Řešení potíží s aktivací virtuálních počítačů Azure s Windows](troubleshoot-activation-problems.md)

