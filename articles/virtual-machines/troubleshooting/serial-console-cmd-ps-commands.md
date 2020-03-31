---
title: CMD a PowerShell ve virtuálních počítačích Azure pro Windows | Dokumenty společnosti Microsoft
description: Jak používat příkazy CMD a PowerShell v rámci SAC ve virtuálních počítačích Azure Windows
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 493340764f507c4fa364a5000f65cc232630b243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167032"
---
# <a name="windows-commands---cmd-and-powershell"></a>Příkazy systému Windows – CMD a prostředí PowerShell

Tato část obsahuje ukázkové příkazy pro provádění běžných úloh ve scénářích, kde může být nutné použít SAC pro přístup k virtuálnímu počítači systému Windows, například když potřebujete řešit selhání připojení RDP.

Jazyk SAC byl zahrnut do všech verzí systému Windows od systému Windows Server 2003, ale ve výchozím nastavení je zakázán. SAC spoléhá na `sacdrv.sys` ovladač jádra,`sacsvr`službu `sacsess.exe` `Special Administration Console Helper` ( ) a proces. Další informace naleznete v [tématu Nástroje a nastavení služby pro nouzovou správu](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

SAC umožňuje připojení k běžícímu osu přes sériový port. Když spustíte CMD `sacsess.exe` z `cmd.exe` SAC, spustí se v rámci běžícího operačního systému. Můžete vidět, že ve Správci úloh, pokud RDP k virtuálnímu počítači ve stejnou dobu jste připojeni k SAC prostřednictvím funkce sériové konzole. CMD, ke které přistupujete přes SAC, je stejný, `cmd.exe` který používáte při připojení přes RDP. K dispozici jsou všechny stejné příkazy a nástroje, včetně možnosti spouštět prostředí PowerShell z této instance CMD. To je hlavní rozdíl mezi SAC a Windows Recovery Environment (WinRE) v tom, že SAC vám umožní spravovat váš spuštěný operační systém, kde winre boty do jiného, minimální OS. Zatímco virtuální počítače Azure nepodporují možnost přístupu k winre, s funkcí sériové konzoly, virtuální počítače Azure lze spravovat prostřednictvím SAC.

Vzhledem k tomu, že SAC je omezena na vyrovnávací `| more` paměť obrazovky 80x24 bez posouvání zpět, přidejte příkazy a zobrazte výstup po jedné stránce. Slouží `<spacebar>` k zobrazení další `<enter>` stránky nebo k zobrazení dalšího řádku.

`SHIFT+INSERT`je zástupce pro vložení okna konzoly sériového zařízení.

Z důvodu omezené vyrovnávací paměti obrazovky SAC delší příkazy může být jednodušší zadat v místním textovém editoru a potom vložit do SAC.

## <a name="view-and-edit-windows-registry-settings"></a>Zobrazení a úprava nastavení registru systému Windows
### <a name="verify-rdp-is-enabled"></a>Ověření povolení programu RDP
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Druhý klíč (v části \Policies) bude existovat pouze v případě, že je nakonfigurováno příslušné nastavení zásad skupiny.

### <a name="enable-rdp"></a>Povolit prv
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0`

Druhý klíč (v části \Policy) by byl potřebný pouze v případě, že bylo nakonfigurováno příslušné nastavení zásad skupiny. Hodnota bude přepsána při další aktualizaci zásad skupiny, pokud je nakonfigurována v zásadách skupiny.

## <a name="manage-windows-services"></a>Správa služeb systému Windows

### <a name="view-service-state"></a>Zobrazit stav služby
`sc query termservice`
###  <a name="view-service-logon-account"></a>Zobrazit přihlašovací účet služby
`sc qc termservice`
### <a name="set-service-logon-account"></a>Nastavení přihlašovacího účtu služby
`sc config termservice obj= "NT Authority\NetworkService"`

Za znaménkem rovná se je vyžadována mezera.
### <a name="set-service-start-type"></a>Nastavit typ spuštění služby
`sc config termservice start= demand`

Za znaménkem rovná se je vyžadována mezera. Možné počáteční `boot`hodnoty `system` `auto`zahrnují `demand` `disabled`, `delayed-auto`, , , .
### <a name="set-service-dependencies"></a>Nastavení závislostí služeb
`sc config termservice depend= RPCSS`

Za znaménkem rovná se je vyžadována mezera.
### <a name="start-service"></a>Spustit službu
`net start termservice`

– nebo –

`sc start termservice`
### <a name="stop-service"></a>Zastavit službu
`net stop termservice`

– nebo –

`sc stop termservice`
## <a name="manage-networking-features"></a>Správa síťových funkcí
### <a name="show-nic-properties"></a>Zobrazit vlastnosti nic
`netsh interface show interface`
### <a name="show-ip-properties"></a>Zobrazit vlastnosti IP
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Zobrazit konfiguraci protokolu IPSec
`netsh nap client show configuration`
### <a name="enable-nic"></a>Povolit nic
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Nastavit nic pro použití dhcp
`netsh interface ip set address name="<interface name>" source=dhcp`

Pro více `netsh`informací [klikněte zde](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

Virtuální počítače Azure by měly být vždy nakonfigurovány v hostovaném ošetřivém systému, aby používaly dhcp k získání IP adresy. Nastavení statické IP adresy Azure stále používá DHCP k poskytnutí statické IP adresy virtuálnímu počítači.
### <a name="ping"></a>Ping
`ping 8.8.8.8`
### <a name="port-ping"></a>Ping portu
Instalace klienta telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Test připojení

`telnet bing.com 80`

Odebrání klienta telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Pokud jsou omezeny na metody dostupné v systému Windows ve výchozím nastavení, powershell může být lepší přístup pro testování připojení portu. Příklady najdete v části PowerShell uníže.
### <a name="test-dns-name-resolution"></a>Testování překladu názvů DNS
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Zobrazit pravidlo brány Windows Firewall
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Zakázání brány Windows Firewall
`netsh advfirewall set allprofiles state off`

Tento příkaz můžete použít při řešení potíží k dočasnému vyloučení brány Windows Firewall. To bude povolit při příštím restartu, nebo když jej povolíte pomocí příkazu níže. Nezastavujte službu Brána Firewall (MPSSVC) nebo base filtering engine (BFE) jako způsob vyloučení brány Windows Firewall. Zastavení MPSSVC nebo BFE bude mít za následek blokování veškeré konektivity.
### <a name="enable-windows-firewall"></a>Povolit bránu Windows Firewall
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Správa uživatelů a skupin
### <a name="create-local-user-account"></a>Vytvořit místní uživatelský účet
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Přidání místního uživatele do místní skupiny
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Ověření možnosti uživatelského účtu
`net user <username> | find /i "active"`

Virtuální počítače Azure vytvořené z generalizované image bude mít účet místního správce přejmenován na název určený během zřizování virtuálních počítačů. Takže to obvykle `Administrator`nebude .
### <a name="enable-user-account"></a>Povolení uživatelského účtu
`net user <username> /active:yes`
### <a name="view-user-account-properties"></a>Zobrazit vlastnosti uživatelského účtu
`net user <username>`

Příklady zajímavých řádků z účtu místního správce:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Zobrazit místní skupiny
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Správa protokolu událostí systému Windows
### <a name="query-event-log-errors"></a>Chyby protokolu událostí dotazu
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Změňte `/c:10` na požadovaný počet událostí, které chcete vrátit, nebo jej přesuňte, chcete-li vrátit všechny události odpovídající filtru.
### <a name="query-event-log-by-event-id"></a>Protokol událostí dotazu podle ID události
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Protokol událostí dotazu podle ID události a zprostředkovatele
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Protokol událostí dotazu podle ID události a zprostředkovatele za posledních 24 hodin
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Použijte `604800000` se podívat zpět 7 dní namísto 24 hodin.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Protokol událostí dotazu podle ID události, zprostředkovatele a eventdata za posledních 7 dní
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Zobrazení nebo odebrání nainstalovaných aplikací
### <a name="list-installed-applications"></a>Seznam nainstalovaných aplikací
`wmic product get Name,InstallDate | sort /r | more`

Druhy `sort /r` sestupující podle data instalace, aby bylo snadné vidět, co bylo nedávno nainstalováno. Slouží `<spacebar>` k převedení na další `<enter>` stránku výstupu nebo k posunu o jeden řádek.
### <a name="uninstall-an-application"></a>Odinstalace aplikace
`wmic path win32_product where name="<name>" call uninstall`

Nahraďte `<name>` název vrácený ve výše uvedeném příkazu pro aplikaci, kterou chcete odebrat.

## <a name="file-system-management"></a>Správa systému souborů
### <a name="get-file-version"></a>Získat verzi souboru
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Tento příklad vrátí verzi souboru virtuálního ovladače síťové karty, což je netvsc.sys, netvsc63.sys nebo netvsc60.sys v závislosti na verzi systému Windows.
### <a name="scan-for-system-file-corruption"></a>Hledání poškození systémových souborů
`sfc /scannow`

Viz také [Oprava bitové kopie systému Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Hledání poškození systémových souborů
`dism /online /cleanup-image /scanhealth`

Viz také [Oprava bitové kopie systému Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Export oprávnění k souborům do textového souboru
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Uložení oprávnění k souborům ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`
### <a name="restore-file-permissions-from-acl-file"></a>Obnovení oprávnění k souborům ze souboru ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Cesta při `/restore` použití musí být nadřazenou složkou `/save`složky, kterou jste zadali při použití aplikace . V tomto `\RSA` příkladu je `\MachineKeys` nadřazená složka zadaná ve výše uvedeném příkladu. `/save`
### <a name="take-ntfs-ownership-of-a-folder"></a>Převzetí vlastnictví složky systémem souborů NTFS
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Udělit oprávnění ntfs ke složce rekurzivně
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`
## <a name="manage-devices"></a>Správa zařízení
### <a name="remove-non-present-pnp-devices"></a>Odebrání nepřítomných zařízení PNP
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Správa zásad skupiny
### <a name="force-group-policy-update"></a>Vynutit aktualizaci zásad skupiny
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Různé úkoly
### <a name="show-os-version"></a>Zobrazit verzi operačního systému
`ver`

– nebo –

`wmic os get caption,version,buildnumber /format:list`

– nebo –

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Zobrazit datum instalace operačního systému
`systeminfo | find /i "original"`

– nebo –

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Zobrazit čas posledního spuštění
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Zobrazit časové pásmo
`systeminfo | find /i "time zone"`

– nebo –

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Restartování systému Windows
`shutdown /r /t 0`

Přidání `/f` vynutí ukončení spuštěné aplikace bez upozornění uživatelů.
### <a name="detect-safe-mode-boot"></a>Rozpoznat spuštění nouzového režimu
`bcdedit /enum | find /i "safeboot"`

## <a name="windows-commands---powershell"></a>Příkazy systému Windows – Prostředí PowerShell

Chcete-li spustit prostředí PowerShell v SAC, po dosažení výzvy CMD zadejte:

`powershell <enter>`

> [!CAUTION]
> Před spuštěním jiných příkazů prostředí PowerShell odeberte modul PSReadLine z relace prostředí PowerShell. Je známý problém, kde další znaky mohou být zavedeny v textu vložené ze schránky, pokud PSReadLine je spuštěnv relaci prostředí PowerShell v SAC.

Nejprve zkontrolujte, zda je načten psreadline. Ve výchozím nastavení se načítá v systémech Windows Server 2016, Windows 10 a novějších verzích Windows. Byl by k dispozici pouze v dřívějších verzích systému Windows, pokud by byl nainstalován ručně.

Pokud se tento příkaz vrátí do výzvy bez výstupu, modul nebyl načten a můžete pokračovat v použití relace prostředí PowerShell v SAC jako normální.

`get-module psreadline`

Pokud výše uvedený příkaz vrátí verzi modulu PSReadLine, spusťte následující příkaz a jej vyložte. Tento příkaz modul neodstraní ani neodinstaluje, uvolní jej pouze z aktuální relace prostředí PowerShell.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Zobrazení a úprava nastavení registru systému Windows
### <a name="verify-rdp-is-enabled"></a>Ověření povolení programu RDP
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Druhý klíč (v části \Policies) bude existovat pouze v případě, že je nakonfigurováno příslušné nastavení zásad skupiny.
### <a name="enable-rdp"></a>Povolit prv
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Druhý klíč (v části \Policy) by byl potřebný pouze v případě, že bylo nakonfigurováno příslušné nastavení zásad skupiny. Hodnota bude přepsána při další aktualizaci zásad skupiny, pokud je nakonfigurována v zásadách skupiny.
## <a name="manage-windows-services"></a>Správa služeb systému Windows
### <a name="view-service-details"></a>Zobrazit podrobnosti o službě
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service`lze použít, ale neobsahuje přihlašovací účet služby. `Get-WmiObject win32-service`dělá.
### <a name="set-service-logon-account"></a>Nastavení přihlašovacího účtu služby
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Při použití jiného `NT AUTHORITY\LocalService`účtu `NT AUTHORITY\NetworkService`služby než , nebo `LocalSystem`, zadejte heslo účtu jako poslední (osmý) argument za názvem účtu.
### <a name="set-service-startup-type"></a>Nastavit typ spuštění služby
`set-service termservice -startuptype Manual`

`Set-service`přijímá `Automatic`, `Manual`, `Disabled` nebo pro typ spuštění.
### <a name="set-service-dependencies"></a>Nastavení závislostí služeb
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Spustit službu
`start-service termservice`
### <a name="stop-service"></a>Zastavit službu
`stop-service termservice`
## <a name="manage-networking-features"></a>Správa síťových funkcí
### <a name="show-nic-properties"></a>Zobrazit vlastnosti nic
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

– nebo –

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter`je k dispozici v roce 2012+, `Get-WmiObject`pro použití 2008R2 .
### <a name="show-ip-properties"></a>Zobrazit vlastnosti IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Povolit nic
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

– nebo –

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter`je k dispozici v roce 2012+, `Get-WmiObject`pro použití 2008R2 .
### <a name="set-nic-to-use-dhcp"></a>Nastavit nic pro použití dhcp
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter`k dispozici na 2012+. Pro 2008R2 `Get-WmiObject`použití . Virtuální počítače Azure by měly být vždy nakonfigurovány v hostovaném ošetřivém systému, aby používaly dhcp k získání IP adresy. Nastavení statické IP adresy Azure stále používá DHCP k poskytnutí IP adresy virtuálnímu počítači.
### <a name="ping"></a>Ping
`test-netconnection`

> [!NOTE]
> Rutina Průběh zápisu nemusí s tímto příkazem fungovat. Jako zmírnění můžete spustit `$ProgressPreference = "SilentlyContinue"` v prostředí PowerShell zakázat indikátor průběhu.

– nebo –

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection`bez parametrů se pokusí `internetbeacon.msedge.net`ping . Je k dispozici na 2012 +. Pro 2008R2 `Get-WmiObject` použít jako v druhém příkladu.
### <a name="port-ping"></a>Ping portu
`test-netconnection -ComputerName bing.com -Port 80`

– nebo –

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection`k dispozici na 2012+. Pro použití 2008R2`Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Testování překladu názvů DNS
`resolve-dnsname bing.com`

– nebo –

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName`k dispozici na 2012+. Pro 2008R2 `System.Net.DNS`použití .
### <a name="show-windows-firewall-rule-by-name"></a>Zobrazit pravidlo brány firewall systému Windows podle názvu
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP`
### <a name="show-windows-firewall-rule-by-port"></a>Zobrazit pravidlo brány firewall systému Windows podle portu
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

– nebo –

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter`k dispozici na 2012+. Pro 2008R2 `hnetcfg.fwpolicy2` použijte com objekt.
### <a name="disable-windows-firewall"></a>Zakázání brány firewall systému Windows
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile`k dispozici na 2012+. Pro rok 2008R2 použít, `netsh advfirewall` jak je uvedeno v části CMD výše.
## <a name="manage-users-and-groups"></a>Správa uživatelů a skupin
### <a name="create-local-user-account"></a>Vytvořit místní uživatelský účet
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Ověření možnosti uživatelského účtu
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

– nebo –

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser`k dispozici na 2012+. Pro 2008R2 `Get-WmiObject`použití . Tento příklad ukazuje předdefinovaný účet místního správce, `S-1-5-21-*-500`který má vždy SID . Virtuální počítače Azure vytvořené z generalizované image bude mít účet místního správce přejmenován na název určený během zřizování virtuálních počítačů. Takže to obvykle `Administrator`nebude .
### <a name="add-local-user-to-local-group"></a>Přidání místního uživatele do místní skupiny
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Povolení místního uživatelského účtu
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser`

Tento příklad umožňuje předdefinovaný účet místního správce, `S-1-5-21-*-500`který má vždy sid . Virtuální počítače Azure vytvořené z generalizované image bude mít účet místního správce přejmenován na název určený během zřizování virtuálních počítačů. Takže to obvykle `Administrator`nebude .
### <a name="view-user-account-properties"></a>Zobrazit vlastnosti uživatelského účtu
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

– nebo –

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser`k dispozici na 2012+. Pro 2008R2 `Get-WmiObject`použití . Tento příklad ukazuje předdefinovaný účet místního správce, `S-1-5-21-*-500`který má vždy SID .
### <a name="view-local-groups"></a>Zobrazit místní skupiny
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser`k dispozici na 2012+. Pro 2008R2 `Get-WmiObject`použití .
## <a name="manage-the-windows-event-log"></a>Správa protokolu událostí systému Windows
### <a name="query-event-log-errors"></a>Chyby protokolu událostí dotazu
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Změňte `/c:10` na požadovaný počet událostí, které chcete vrátit, nebo jej přesuňte, chcete-li vrátit všechny události odpovídající filtru.
### <a name="query-event-log-by-event-id"></a>Protokol událostí dotazu podle ID události
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Protokol událostí dotazu podle ID události a zprostředkovatele
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Protokol událostí dotazu podle ID události a zprostředkovatele za posledních 24 hodin
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Použijte `604800000` se podívat zpět 7 dní namísto 24 hodin. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Protokol událostí dotazu podle ID události, zprostředkovatele a eventdata za posledních 7 dní
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Zobrazení nebo odebrání nainstalovaných aplikací
### <a name="list-installed-software"></a>Seznam nainstalovaného softwaru
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Odinstalovat software
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Správa systému souborů
### <a name="get-file-version"></a>Získat verzi souboru
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Tento příklad vrátí verzi souboru virtuálního ovladače síťové karty s názvem netvsc.sys, netvsc63.sys nebo netvsc60.sys v závislosti na verzi systému Windows.
### <a name="download-and-extract-file"></a>Stažení a extrahování souboru
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Tento příklad `c:\bin` vytvoří složku, pak stáhne a extrahuje sadu `c:\bin`Nástrojů Sysinternals do .
## <a name="miscellaneous-tasks"></a>Různé úkoly
### <a name="show-os-version"></a>Zobrazit verzi operačního systému
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber`
### <a name="view-os-install-date"></a>Zobrazit datum instalace operačního systému
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Zobrazit čas posledního spuštění
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Zobrazení doby zobrazení systému Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Vrátí dobu `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`uptime `49:16:48:00.00`jako , například .
### <a name="restart-windows"></a>Restartování systému Windows
`restart-computer`

Přidání `-force` vynutí ukončení spuštěné aplikace bez upozornění uživatelů.
## <a name="instance-metadata"></a>Instance Metadata

Můžete dotaz metadat instance Azure z vašeho virtuálního počítače Azure zobrazit podrobnosti, jako je osType, Umístění, vmSize, vmId, název, resourceGroupName, subscriptionId, privateIpAddress a publicIpAddress.

Dotazování metadat instance vyžaduje připojení sítě hosta v pořádku, protože umožňuje volání REST prostřednictvím hostitele Azure do služby metadat instance. Takže pokud jste schopni dotazovat metadata instance, která vám řekne, host je schopen komunikovat přes síť do služby hostované v Azure.

Další informace najdete v [tématu Azure Instance Metadata service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Metadata instance
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Typ operačního okna (metadata instance)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Umístění (metadata instance)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Velikost (metadata instance)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>ID virtuálního virtuálního zařízení (metadata instance)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Název virtuálního virtuálního zařízení (metadata instance)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Název skupiny prostředků (metadata instance)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>ID předplatného (metadata instance)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Značky (metadata instance)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>ID skupiny umístění (metadata instance)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Doména selhání platformy (metadata instance)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Doména aktualizace platformy (metadata instance)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>Privátní IP adresa IPv4 (metadata instance)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>Veřejná IP adresa IPv4 (metadata instance)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Adresa / předpona podsítě IPv4 (metadata instance)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IP adresa IPv6 (metadata instance)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>Adresa MAC (metadata instance)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Další kroky
* Hlavní stránka dokumentace sériové konzole Windows je [umístěna zde](serial-console-windows.md).
* Sériová konzola je k dispozici také pro virtuální počítače [s Linuxem.](serial-console-linux.md)
* Další informace o [diagnostice spuštění](boot-diagnostics.md).
