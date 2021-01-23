---
title: Příprava virtuálního pevného disku s Windows pro nahrání do Azure
description: Informace o přípravě virtuálního pevného disku (VHD) nebo VHDX Windows pro nahrání do Azure
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: genli
ms.openlocfilehash: e409211c167f7b29128faf9fdfc02aa5c0a7d0e3
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736250"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Příprava disku VHD nebo VHDX s Windows pro nahrání do Azure

Než nahrajete virtuální počítač s Windows z místního počítače do Azure, musíte připravit virtuální pevný disk (VHD nebo VHDX). Azure podporuje virtuální počítače generace 1 i generace 2, které jsou ve formátu souboru VHD a mají disk s pevnou velikostí. Maximální velikost povolená pro virtuální pevný disk operačního systému na virtuálním počítači generace 1 je 2 TB.

Soubor VHDX můžete převést na VHD, převést dynamicky se zvětšující disk na disk s pevnou velikostí, ale nemůžete změnit generaci virtuálního počítače. Další informace najdete v tématu [Vytvoření virtuálního počítače generace 1 nebo 2 v Hyper-V?](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V) a [Podpora virtuálních počítačů 2. generace v Azure](../generation-2.md).

Informace o zásadách podpory pro virtuální počítače Azure najdete v tématu [podpora serverového softwaru společnosti Microsoft pro virtuální počítače Azure](https://support.microsoft.com/help/2721672/).

> [!NOTE]
> Pokyny v tomto článku se týkají:
>
> - 64 verze systému Windows Server 2008 R2 a novějších operačních systémů Windows Server. Informace o spuštění 32 operačního systému v Azure najdete v tématu [Podpora pro 32 operační systémy ve virtuálních počítačích Azure](https://support.microsoft.com/help/4021388/).
> - Pokud bude k migraci zatížení použit libovolný nástroj pro zotavení po havárii, například Azure Site Recovery nebo Azure Migrate, je tento proces stále požadován v hostovaném operačním systému, aby mohl připravit bitovou kopii před migrací.

## <a name="system-file-checker"></a>Kontrola systémových souborů

### <a name="run-windows-system-file-checker-utility-before-generalization-of-os-image"></a>Spuštění nástroje pro kontrolu systémových souborů systému Windows před generalizací bitové kopie operačního systému

Nástroj System File Checker (SFC) slouží k ověření a nahrazení systémových souborů systému Windows.

> [!IMPORTANT]
> Použijte relaci PowerShellu se zvýšenými oprávněními ke spuštění příkladů v tomto článku.

Spusťte příkaz SFC:

```powershell
sfc.exe /scannow
```

```Output
Beginning system scan.  This process will take some time.

Beginning verification phase of system scan.
Verification 100% complete.

Windows Resource Protection did not find any integrity violations.
```

Po dokončení kontroly SFC nainstalujte aktualizace Windows a restartujte počítač.

## <a name="set-windows-configurations-for-azure"></a>Nastavení konfigurací Windows pro Azure

> [!NOTE]
> Platforma Azure připojí soubor ISO k DVD-ROM, když se z generalizované image vytvoří virtuální počítač s Windows. Z tohoto důvodu musí být v operačním systému v generalizované imagi povolený disk DVD-ROM. Pokud je tato možnost zakázaná, bude se virtuální počítač s Windows zablokovat při aktivovaném prostředí (OOBE).

1. Odeberte všechny statické trvalé trasy ve směrovací tabulce:

   - Chcete-li zobrazit směrovací tabulku, spusťte příkaz `route.exe print` .
   - Podívejte se na část **trvalé trasy** . Pokud je k dispozici trvalá trasa, `route.exe delete` odeberte ji pomocí příkazu.

1. Odeberte proxy server WinHTTP:

   ```powershell
   netsh.exe winhttp reset proxy
   ```

    Pokud virtuální počítač potřebuje pracovat s konkrétním proxy serverem, přidejte výjimku proxy serveru pro IP adresu Azure ([168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)), aby se virtuální počítač mohl připojit k Azure:

    ```
    $proxyAddress='<your proxy server>'
    $proxyBypassList='<your list of bypasses>;168.63.129.16'
    netsh.exe winhttp set proxy $proxyAddress $proxyBypassList
    ```

1. Spusťte nástroj DiskPart:

   ```powershell
   diskpart.exe
   ```

   Nastavte zásadu pro diskovou síť SAN na [`Onlineall`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg252636(v=ws.11)) :

   ```DiskPart
   DISKPART> san policy=onlineall
   DISKPART> exit
   ```

1. Nastavte koordinovaný světový čas (UTC) pro Windows. Také nastavte typ spouštění služby Windows Time Service **W32Time** na **automaticky**:

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation -Name RealTimeIsUniversal -Value 1 -Type DWord -Force
   Set-Service -Name w32time -StartupType Automatic
   ```

1. Nastavte profil napájení na vysoký výkon:

   ```powershell
   powercfg.exe /setactive SCHEME_MIN
   ```

1. Ujistěte se, že jsou proměnné prostředí **TEMP** a **TMP** nastavené na výchozí hodnoty:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TEMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   ```

## <a name="check-the-windows-services"></a>Kontrola služeb pro Windows

Ujistěte se, že všechny následující služby systému Windows jsou nastaveny na výchozí hodnotu Windows. Tyto služby jsou minimální, které je nutné nakonfigurovat, aby bylo zajištěno připojení k virtuálnímu počítači. Chcete-li nastavit nastavení spouštění, spusťte následující příklad:

```powershell
Get-Service -Name BFE, Dhcp, Dnscache, IKEEXT, iphlpsvc, nsi, mpssvc, RemoteRegistry |
  Where-Object StartType -ne Automatic |
    Set-Service -StartupType Automatic

Get-Service -Name Netlogon, Netman, TermService |
  Where-Object StartType -ne Manual |
    Set-Service -StartupType Manual
```

## <a name="update-remote-desktop-registry-settings"></a>Aktualizovat nastavení registru vzdálené plochy

Ujistěte se, že jsou pro vzdálený přístup správně nakonfigurovaná následující nastavení:

> [!NOTE]
> Pokud se při spuštění zobrazí chybová zpráva `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <string> -Value <object>` , můžete ji bezpečně ignorovat. Znamená to, že doména nenastavuje tuto konfiguraci prostřednictvím objektu Zásady skupiny.

1. Protokol RDP (Remote Desktop Protocol) (RDP) je povolený:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   ```

1. Port RDP se správně nastaví pomocí výchozího portu 3389:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name PortNumber -Value 3389 -Type DWord -Force
   ```

   Když nasadíte virtuální počítač, vytvoří se výchozí pravidla pro port 3389. Pokud chcete změnit číslo portu, udělejte to po nasazení virtuálního počítače v Azure.

1. Naslouchací proces naslouchá na každém síťovém rozhraní:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name LanAdapter -Value 0 -Type DWord -Force
   ```

1. Nakonfigurujte režim ověřování na úrovni sítě (NLA) pro připojení RDP:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name UserAuthentication -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SecurityLayer -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name fAllowSecProtocolNegotiation -Value 1 -Type DWord -Force
   ```

1. Nastavte hodnotu Keep-Alive:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveEnable -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveInterval -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name KeepAliveTimeout -Value 1 -Type DWord -Force
   ```

1. Nastavte možnosti opětovného připojení:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDisableAutoReconnect -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fInheritReconnectSame -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fReconnectSame -Value 0 -Type DWord -Force
   ```

1. Omezte počet souběžných připojení:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name MaxInstanceCount -Value 4294967295 -Type DWord -Force
   ```

1. Odebrání všech certifikátů podepsaných svým držitelem vázaných na naslouchací proces protokolu RDP:

   ```powershell
   if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains 'SSLCertificateSHA1Hash')
   {
       Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SSLCertificateSHA1Hash -Force
   }
   ```

   Tento kód zajišťuje, že se můžete připojit při nasazení virtuálního počítače. Tato nastavení můžete zkontrolovat i po nasazení virtuálního počítače v Azure.

1. Pokud je virtuální počítač součástí domény, zkontrolujte následující zásady, abyste se ujistili, že předchozí nastavení se nevrátí.

    |                 Cíl                  |                                                                            Zásady                                                                            |                           Hodnota                            |
    | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
    | Protokol RDP je povolený.                        | Computer cestě konfigurace Settings\Administrative Templates\Components\Remote Desktop – pracovní relace, Host\Connections         | Umožňuje uživatelům vzdálené připojení pomocí vzdálené plochy.    |
    | NLA – zásady skupiny                      | Settings\Administrative Templates\Components\Remote Desktop – relace pro pracovní plochu – Host\Security                                                    | Vyžadovat ověření uživatele pro vzdálený přístup pomocí NLA |
    | Nastavení Keep-Alive                   | Computer cestě konfigurace Settings\Administrative pro správu \ součásti systému \ pracovní plocha – relace – Host\Connections | Konfigurace intervalu připojení Keep-Alive                   |
    | Znovu připojit nastavení                    | Computer cestě konfigurace Settings\Administrative pro správu \ součásti systému \ pracovní plocha – relace – Host\Connections | Znovu připojit automaticky                                    |
    | Omezený počet nastavení připojení | Computer cestě konfigurace Settings\Administrative pro správu \ součásti systému \ pracovní plocha – relace – Host\Connections | Omezení počtu připojení                                |

## <a name="configure-windows-firewall-rules"></a>Konfigurace pravidel brány Windows Firewall

1. Zapnout bránu Windows Firewall na třech profilech (doména, Standard a veřejné):

   ```powershell
   Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled True
   ```

1. Spusťte následující příklad, který povolí WinRM přes tři profily brány firewall (domény, privátní a veřejné) a povolte vzdálenou službu prostředí PowerShell:

   ```powershell
   Enable-PSRemoting -Force
   Set-NetFirewallRule -DisplayName 'Windows Remote Management (HTTP-In)' -Enabled True
   ```

1. Povolte následující pravidla brány firewall, aby se povolil provoz protokolu RDP:

   ```powershell
   Set-NetFirewallRule -DisplayGroup 'Remote Desktop' -Enabled True
   ```

1. Povolte pravidlo pro sdílení souborů a tiskáren, aby virtuální počítač mohl reagovat na požadavky na příkazy testu v rámci virtuální sítě:

   ```powershell
   Set-NetFirewallRule -DisplayName 'File and Printer Sharing (Echo Request - ICMPv4-In)' -Enabled True
   ```

1. Vytvořte pravidlo pro síť platformy Azure:

   ```powershell
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ```

1. Pokud je virtuální počítač součástí domény, zkontrolujte následující zásady služby Azure AD a ujistěte se, že předchozí nastavení nejsou obnovena.

    |                 Cíl                 |                                                                         Zásady                                                                          |                  Hodnota                  |
    | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
    | Povolit profily brány Windows Firewall | Computer cestě konfigurace Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Chránit všechna síťová připojení         |
    | Povolit protokol RDP                           | Computer cestě konfigurace Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Povolit příchozí výjimky vzdálené plochy |
    |                                      | Computer cestě konfigurace Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows firewall | Povolit příchozí výjimky vzdálené plochy |
    | Povolit ICMP-v4                       | Computer cestě konfigurace Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Povolení výjimek ICMP                   |
    |                                      | Computer cestě konfigurace Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows firewall | Povolení výjimek ICMP                   |

## <a name="verify-the-vm"></a>Ověřit virtuální počítač

Ujistěte se, že je virtuální počítač v pořádku, zabezpečený a dostupný protokol RDP:

1. Pokud chcete mít jistotu, že je disk v pořádku a konzistentní, zkontrolujte disk při příštím restartování virtuálního počítače:

   ```powershell
   chkdsk.exe /f
   ```

   Ujistěte se, že se v sestavě zobrazuje disk s čistým a dobrým diskem.

1. Nastavte nastavení konfigurační data spouštění (BCD).

   ```powershell
   bcdedit.exe /set "{bootmgr}" integrityservices enable
   bcdedit.exe /set "{default}" device partition=C:
   bcdedit.exe /set "{default}" integrityservices enable
   bcdedit.exe /set "{default}" recoveryenabled Off
   bcdedit.exe /set "{default}" osdevice partition=C:
   bcdedit.exe /set "{default}" bootstatuspolicy IgnoreAllFailures

   #Enable Serial Console Feature
   bcdedit.exe /set "{bootmgr}" displaybootmenu yes
   bcdedit.exe /set "{bootmgr}" timeout 5
   bcdedit.exe /set "{bootmgr}" bootems yes
   bcdedit.exe /ems "{current}" ON
   bcdedit.exe /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Protokol výpisu paměti může být užitečný při řešení potíží s chybami systému Windows. Povolit shromažďování protokolů výpisu paměti:

   ```powershell
   # Set up the guest OS to collect a kernel dump on an OS crash event
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

   # Set up the guest OS to collect user mode dumps on a service crash event
   $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
   if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
   New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value 'C:\CrashDumps'
   New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
   New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
   Set-Service -Name WerSvc -StartupType Manual
   ```

1. Ověřte, že úložiště rozhraní WMI (Windows Management Instrumentation) (WMI) je konzistentní:

   ```powershell
   winmgmt.exe /verifyrepository
   ```

   Pokud je úložiště poškozené, přečtěte si téma [WMI: poškození úložiště nebo ne](https://techcommunity.microsoft.com/t5/ask-the-performance-team/wmi-repository-corruption-or-not/ba-p/375484).

1. Ujistěte se, že žádná jiná aplikace nepoužívá port 3389. Tento port se používá pro službu RDP v Azure. Chcete-li zjistit, které porty jsou na virtuálním počítači používány, spusťte příkaz `netstat.exe -anob` :

   ```powershell
   netstat.exe -anob
   ```

1. Postup nahrání virtuálního pevného disku s Windows, který je řadičem domény:

   - Při přípravě disku postupujte podle [těchto dalších kroků](https://support.microsoft.com/kb/2904015) .

   - Ujistěte se, že znáte heslo režimu obnovení adresářových služeb (DSRM) pro případ, že byste někdy museli spustit virtuální počítač v režimu DSRM. Další informace najdete v tématu [Nastavení hesla pro režim DSRM](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754363(v=ws.11)).

1. Ujistěte se, že znáte integrovaný účet správce a heslo. Je možné obnovit aktuální heslo místního správce a ověřit, zda můžete použít tento účet pro přihlášení k systému Windows prostřednictvím připojení RDP. Tato přístupová oprávnění se řídí pomocí objektu Zásady skupiny oprávnění "povolování prostřednictvím služby Vzdálená plocha". Zobrazit tento objekt v Editor místních zásad skupiny:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment`

1. Zkontrolujte následující zásady služby Azure AD, abyste se ujistili, že neblokují přístup protokolu RDP:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny access to this computer from the network`

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny log on through Remote Desktop Services`

1. Zkontrolujte následující zásady služby Azure AD, abyste se ujistili, že neodstraňují žádné z požadovaných účtů pro přístup:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Access this computer from the network`

   Zásada by měla zobrazovat následující skupiny:

   - Administrators

   - Backup Operators

   - Všichni

   - Uživatelé

1. Restartujte virtuální počítač, abyste se ujistili, že je systém Windows stále v pořádku a že je možné ho spojit s připojením RDP. V tomto okamžiku zvažte vytvoření virtuálního počítače na místním serveru Hyper-V, abyste se ujistili, že se virtuální počítač spustí úplně. Pak se otestujte a ujistěte se, že máte přístup k virtuálnímu počítači přes RDP.

1. Odeberte jakékoli další filtry rozhraní TDI (Transport Driver Interface). Můžete například odebrat software, který analyzuje pakety TCP nebo brány firewall navíc.

1. Odinstalujte všechny další software nebo ovladače třetí strany, které se vztahují k fyzickým součástem nebo jiné virtualizační technologii.

### <a name="install-windows-updates"></a>Nainstalovat aktualizace Windows

V ideálním případě byste měli udržovat počítač aktualizovaný na *úrovni oprav*, pokud to není možné, zajistěte, aby byly nainstalované následující aktualizace. Nejnovější aktualizace získáte na stránkách historie Windows Update: [Windows 10 a Windows server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1 a Windows Server 2012 R2](https://support.microsoft.com/help/4009470) a [Windows 7 SP1 a Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469).

<br />

|        Součást        |     Binární     | Windows 7 SP1, Windows Server 2008 R2 SP1 |       Windows 8, Windows Server 2012        | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 |      Windows 10 v1703      | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
| ----------------------- | -------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- | ------------------------------------------- | ------------------------------------------- |
| Storage                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061          | -                                           | -                          | -                                           | -                                           |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.332             | -                                           | -                                           |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614          | 10.0.14393.953 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384          | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 – KB3000850          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | volmgr.sys     |                                           |                                             |                                     |                                             | 10.0.15063.0               | -                                           | -                                           |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726          | 10.0.14393.1066 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726          | -                                           | -                          | -                                           | -                                           |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726          | 10.0.14393.1198 - KB4022715                 | -                          | -                                           | -                                           |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650          | 10.0.14393.2007 - KB4345418                 | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                  | -                                           |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614          | 10.0.14393.576 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614          | 10.0.14393.206 - KB4022715                  | -                          | -                                           | -                                           |
| Síť                 | netvsc.sys     | -                                         | -                                           | -                                   | 10.0.14393.1198 - KB4022715                 | 10.0.15063.250 - KB4020001 | -                                           | -                                           |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726          | 10.0.14393.479 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726          | 10.0.14393.251 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.138             | -                                           | -                                           |
| Jádro                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.483             | -                                           | -                                           |
| Vzdálená plocha | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.0               | -                                           | -                                           |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 – KB3000850          | 10.0.14393.0 – KB4022715                    | 10.0.15063.0               | -                                           | -                                           |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726          | 10.0.14393.594 - KB4022715                  | -                          | -                                           | -                                           |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
| Zabezpečení                | MS17 – 010       | KB4012212                                 | KB4012213                                   | KB4012213                           | KB4012606                                   | KB4012606                  | -                                           | -                                           |
|                         |                |                                           | KB4012216                                   |                                     | KB4013198                                   | KB4013198                  | -                                           | -                                           |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                           | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         |                |                                           | KB4012217                                   |                                     | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         | CVE-2018-0886  | KB4103718                                 | KB4103730                                   | KB4103725                           | KB4103723                                   | KB4103731                  | KB4103727                                   | KB4103721                                   |
|                         |                | KB4103712                                 | KB4103726                                   | KB4103715                           |                                             |                            |                                             |                                             |

> [!NOTE]
> Abyste se vyhnuli nechtěnému restartování během zřizování virtuálních počítačů, doporučujeme, abyste zajistili, že všechny instalace web Windows Update jsou dokončené a že nečekají žádné aktualizace. Jedním ze způsobů, jak to provést, je nainstalovat všechny možné aktualizace systému Windows a restartovat jednou před spuštěním `sysprep.exe` příkazu.

## <a name="determine-when-to-use-sysprep"></a>Určení použití nástroje Sysprep

Nástroj pro přípravu systému ( `sysprep.exe` ) je proces, který můžete použít k resetování instalace Windows.
Nástroj Sysprep nabízí "nepoužívané" prostředí odebráním všech osobních údajů a obnovením několika součástí.

Obvykle se spouštíte, `sysprep.exe` abyste vytvořili šablonu, ze které můžete nasadit několik dalších virtuálních počítačů, které mají konkrétní konfiguraci. Šablona se nazývá *zobecněná image*.

Pokud chcete vytvořit jenom jeden virtuální počítač z jednoho disku, nemusíte používat nástroj Sysprep. Místo toho můžete vytvořit virtuální počítač z *specializované image*. Informace o tom, jak vytvořit virtuální počítač z specializovaného disku, najdete v těchto tématech:

- [Vytvoření virtuálního počítače ze specializovaného disku](create-vm-specialized.md)
- [Vytvoření virtuálního počítače ze specializovaného disku VHD](./create-vm-specialized-portal.md)

Chcete-li vytvořit zobecněnou bitovou kopii, je nutné spustit nástroj Sysprep. Další informace najdete v tématu [použití nástroje Sysprep: Úvod](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10)).

Ne každá role nebo aplikace, která je nainstalovaná na počítači se systémem Windows, podporuje generalizované bitové kopie. Před použitím tohoto postupu se ujistěte, že nástroj Sysprep podporuje roli počítače. Další informace najdete v tématu [Podpora nástroje Sysprep pro role serveru](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

Nástroj Sysprep vyžaduje, aby před provedením plně dešifroval jednotky. Pokud jste na svém VIRTUÁLNÍm počítači povolili šifrování, před spuštěním nástroje Sysprep ho zakažte.


### <a name="generalize-a-vhd"></a>Generalizace virtuálního pevného disku

>[!NOTE]
> Po spuštění `sysprep.exe` v následujících krocích vypněte virtuální počítač. Neměňte ho, dokud z něho nevytvoříte image v Azure.

1. Přihlaste se k virtuálnímu počítači s Windows.
1. Spusťte relaci PowerShellu jako správce.
1. Odstraňte adresář Panther (C:\Windows\Panther).
1. Změňte adresář na `%windir%\system32\sysprep` . Potom spusťte `sysprep.exe`.
1. V dialogovém okně **Nástroj pro přípravu systému** vyberte možnost spustit **prostředí při spuštění v systému (OOBE)** a ujistěte se, že je zaškrtnuté políčko **generalizace** .

    ![Nástroj pro přípravu systému](media/prepare-for-upload-vhd-image/syspre.png)
1. V **Možnosti vypnutí** vyberte **vypnout**.
1. Vyberte **OK**.
1. Po dokončení programu Sysprep vypněte virtuální počítač. Nepoužívejte **restart** pro vypnutí virtuálního počítače.

Virtuální pevný disk je teď připravený k nahrání. Další informace o tom, jak vytvořit virtuální počítač z zobecněného disku, najdete v tématu [nahrání zobecněného virtuálního pevného disku a jeho použití k vytvoření nového virtuálního počítače v Azure](/previous-versions/azure/virtual-machines/windows/sa-upload-generalized).

>[!NOTE]
> Vlastní soubor *unattend.xml* není podporován. I když podporujeme vlastnost **additionalUnattendContent** , která poskytuje jenom omezené podpory pro přidání možností [Microsoft-Windows-Shell-setup](/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) do souboru *unattend.xml* , který používá agent zřizování Azure. Můžete použít například [additionalUnattendContent](/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent) k přidání FirstLogonCommands a LogonCommands. Další informace najdete v tématu [AdditionalUnattendContent FirstLogonCommands example](https://github.com/Azure/azure-quickstart-templates/issues/1407).

## <a name="convert-the-virtual-disk-to-a-fixed-size-vhd"></a>Převést virtuální disk na virtuální pevný disk s pevnou velikostí

Pomocí jedné z metod v této části můžete převést virtuální disk a změnit jeho velikost na požadovaný formát pro Azure:

1. Před spuštěním převodu virtuálního disku nebo procesu změny velikosti zálohujte virtuální počítač.

1. Ujistěte se, že virtuální pevný disk Windows funguje na místním serveru správně. Než se pokusíte převést nebo odeslat do Azure, vyřešte všechny chyby v samotném virtuálním počítači.

1. Převeďte virtuální disk na pevný typ.

1. Změnit velikost virtuálního disku na splnění požadavků Azure:

   1. Disky v Azure musí mít virtuální velikost zarovnaná na 1 MiB. Pokud je váš virtuální pevný disk zlomek 1 MiB, budete muset změnit velikost disku na násobek 1 MiB. Disky, které jsou zlomky souboru MiB, způsobují chyby při vytváření imagí z nahraného virtuálního pevného disku. Pokud chcete ověřit velikost, můžete použít rutinu [Get-VHD](/powershell/module/hyper-v/get-vhd) PowerShellu k zobrazení "size", která musí být násobkem 1 MiB v Azure a "velikost souboru", která bude odpovídat velikosti a 512 bajtů pro zápatí VHD.
   
   1. Maximální velikost povolená pro virtuální pevný disk s operačním systémem s virtuálním počítačem 1. generace je 2 048 GiB (2 TiB). 
   1. Maximální velikost datového disku je 32 767 GiB (32 TiB).

> [!NOTE]
> - Pokud připravujete disk s operačním systémem Windows po převodu na pevný disk a v případě potřeby změníte jeho velikost, vytvořte virtuální počítač, který disk používá. Spusťte a přihlaste se k virtuálnímu počítači a pokračujte v částech tohoto článku a dokončete přípravu na odeslání.  
> - Pokud připravujete datový disk, který můžete s touto částí zastavit, pokračujte v nahrávání disku.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Použití Správce technologie Hyper-V k převedení disku

1. Otevřete Správce technologie Hyper-V a na levé straně vyberte svůj místní počítač. V nabídce nad seznamem počítač vyberte **Akce**  >  **Upravit disk**.
1. Na stránce **najít virtuální pevný disk** vyberte svůj virtuální disk.
1. Na stránce **Zvolte akci** vyberte **převést**  >  **Další**.
1. Pro převod z VHDX vyberte **virtuální pevný disk**  >  **Next**.
1. Chcete-li se převést na dynamicky se zvětšující disk, vyberte možnost **Pevná velikost**(  >  **Další**).
1. Vyhledejte a vyberte cestu, kam chcete uložit nový soubor VHD.
1. Vyberte **Dokončit**.

### <a name="use-powershell-to-convert-the-disk"></a>Použití PowerShellu k převedení disku

Virtuální disk můžete převést pomocí rutiny [Convert-VHD](/powershell/module/hyper-v/convert-vhd) v prostředí PowerShell. Pokud potřebujete informace o instalaci této rutiny, přečtěte si téma [instalace role Hyper-V](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

Následující příklad převede disk z VHDX na VHD. Také převede disk z dynamicky se zvětšující disk na disk s pevnou velikostí.

```powershell
Convert-VHD -Path C:\test\MyVM.vhdx -DestinationPath C:\test\MyNewVM.vhd -VHDType Fixed
```

V tomto příkladu nahraďte hodnotu pro **cestu** cestou k virtuálnímu pevnému disku, který chcete převést. Hodnotu **DestinationPath** nahraďte novou cestou a názvem převedeného disku.

### <a name="use-hyper-v-manager-to-resize-the-disk"></a>Použití Správce technologie Hyper-V ke změně velikosti disku

1. Otevřete Správce technologie Hyper-V a na levé straně vyberte svůj místní počítač. V nabídce nad seznamem počítač vyberte **Akce**  >  **Upravit disk**.
1. Na stránce **najít virtuální pevný disk** vyberte svůj virtuální disk.
1. Na stránce **Zvolte akci** vyberte **Rozbalit**  >  **Další**.
1. Na stránce **najít virtuální pevný disk** zadejte novou velikost v GIB > **Další**.
1. Vyberte **Dokončit**.

### <a name="use-powershell-to-resize-the-disk"></a>Použití PowerShellu ke změně velikosti disku

Velikost virtuálního disku můžete změnit pomocí rutiny [změnit velikost-VHD](/powershell/module/hyper-v/resize-vhd) v prostředí PowerShell. Pokud potřebujete informace o instalaci této rutiny, přečtěte si téma [instalace role Hyper-V](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

Následující příklad změní velikost disku z 100,5 MiB na 101 MiB, aby splňoval požadavek na zarovnání Azure.

```powershell
Resize-VHD -Path C:\test\MyNewVM.vhd -SizeBytes 105906176
```

V tomto příkladu nahraďte hodnotu pro **cestu** cestou k virtuálnímu pevnému disku, u kterého chcete změnit velikost. Nahraďte hodnotu pro **SizeBytes** novou velikostí v bajtech pro disk.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Převést z formátu disku VMDK VMware

Pokud máte image virtuálního počítače s Windows ve [formátu souboru VMDK](https://en.wikipedia.org/wiki/VMDK), můžete k převedení VMDK a jeho nahrání do Azure použít [Azure Migrate](../../migrate/server-migrate-overview.md) .

## <a name="complete-the-recommended-configurations"></a>Dokončete Doporučené konfigurace.

Následující nastavení neovlivní nahrávání VHD. Důrazně ale doporučujeme, abyste je nakonfigurovali.

- Nainstalujte [agenta virtuálního počítače Azure](https://go.microsoft.com/fwlink/?LinkID=394789). Pak můžete povolit rozšíření virtuálních počítačů. Rozšíření virtuálních počítačů implementují většinu nejdůležitějších funkcí, které byste mohli chtít použít u virtuálních počítačů. Budete potřebovat rozšíření, například pro resetování hesel nebo konfiguraci protokolu RDP. Další informace najdete v tématu [Přehled agenta virtuálního počítače Azure](../extensions/agent-windows.md).
- Po vytvoření virtuálního počítače v Azure doporučujeme umístit stránkovací soubor na *svazek dočasné jednotky* , aby se zlepšil výkon. Umístění souboru můžete nastavit následujícím způsobem:

  ```powershell
  Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name PagingFiles -Value 'D:\pagefile.sys' -Type MultiString -Force
  ```

  Pokud je k virtuálnímu počítači připojený datový disk, je písmeno jednotky dočasné jednotky obvykle *D*. Toto označení může být odlišné v závislosti na nastavení a počtu dostupných jednotek.

  - Doporučujeme zakázat blokování skriptů, které může poskytovat antivirový software. Můžou narušit a blokovat spuštění skriptů agenta zřizování systému Windows, když nasadíte nový virtuální počítač z image.

## <a name="next-steps"></a>Další kroky

- [Nahrání image virtuálního počítače s Windows do Azure pro nasazení Správce prostředků](upload-generalized-managed.md)
- [Řešení potíží s aktivací virtuálních počítačů Azure s Windows](../troubleshooting/troubleshoot-activation-problems.md)
