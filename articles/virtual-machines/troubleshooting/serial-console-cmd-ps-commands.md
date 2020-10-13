---
title: CMD a PowerShell na virtuálních počítačích Azure s Windows | Microsoft Docs
description: Použití příkazů CMD a PowerShellu v konzole SAC na virtuálních počítačích Azure s Windows
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
ms.openlocfilehash: ef533b3566ac557b57f1435a2a9b2dbe26896993
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306884"
---
# <a name="windows-commands---cmd-and-powershell"></a>Příkazy Windows – CMD a PowerShell

Tato část obsahuje příklady příkazů pro provádění běžných úloh ve scénářích, kdy možná budete potřebovat konzolu SAC použít pro přístup k VIRTUÁLNÍmu počítači s Windows, například když potřebujete řešit chyby připojení RDP.

Konzola SAC byla součástí všech verzí systému Windows, protože systém Windows Server 2003 je ve výchozím nastavení zakázán. Konzola SAC spoléhá na `sacdrv.sys` ovladač jádra, `Special Administration Console Helper` službu ( `sacsvr` ) a `sacsess.exe` proces. Další informace najdete v tématu [nástroje a nastavení služby pro nouzovou správu](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

Konzola SAC umožňuje připojit se k běžícímu operačnímu systému prostřednictvím sériového portu. Při spuštění příkazu CMD z konzoly SAC se `sacsess.exe` spustí `cmd.exe` v běžícím operačním systému. Pokud se k VIRTUÁLNÍmu počítači připojíte pomocí funkce sériová konzola, můžete to zobrazit v části Správce úloh. Rozhraní CMD, ke kterému přistupujete přes konzolu SAC, je totéž `cmd.exe` , co používáte při připojení přes RDP. K dispozici jsou všechny stejné příkazy a nástroje, včetně možnosti Spustit PowerShell z této instance CMD. To je zásadní rozdíl mezi konzolou SAC a prostředím Windows Recovery Environment (WinRE) v konzole SAC vám umožní spravovat běžící operační systém, kde se WinRE spouští do jiného minimálního operačního systému. I když virtuální počítače Azure nepodporují možnost přístupu k rozhraní WinRE s funkcí sériové konzoly, můžete virtuální počítače Azure spravovat přes konzolu SAC.

Vzhledem k tomu, že se Konzola SAC omezí na vyrovnávací paměť obrazovky 80x24 bez posouvání, přidejte `| more` do příkazů příkazy, aby se zobrazoval výstup jedné stránky. Použijte `<spacebar>` k zobrazení další stránky nebo `<enter>` k zobrazení dalšího řádku.

`SHIFT+INSERT` je zástupce pro vložení v okně sériové konzoly.

Z důvodu omezené vyrovnávací paměti obrazovky konzoly SAC může být snazší zadat do místního textového editoru delší příkazy, které pak vložíte do konzoly SAC.

## <a name="view-and-edit-windows-registry-settings-using-cmd"></a>Zobrazení a úprava nastavení registru Windows pomocí příkazu CMD
### <a name="verify-rdp-is-enabled"></a>Ověřte, že je povolený protokol RDP.
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Druhý klíč (v rámci \Policies) bude existovat pouze v případě, že je nakonfigurováno příslušné nastavení zásad skupiny.

### <a name="enable-rdp"></a>Povolit protokol RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0`

Druhý klíč (v rámci \Policies) by byl nutný pouze v případě, že bylo nakonfigurováno příslušné nastavení zásad skupiny. Hodnota bude přepsána při další aktualizaci zásad skupiny, pokud je nakonfigurována v zásadách skupiny.

## <a name="manage-windows-services-using-cmd"></a>Správa služeb systému Windows pomocí příkazu CMD

### <a name="view-service-state"></a>Zobrazit stav služby
`sc query termservice`
###  <a name="view-service-logon-account"></a>Zobrazit přihlašovací účet služby
`sc qc termservice`
### <a name="set-service-logon-account"></a>Nastavení přihlašovacího účtu služby
`sc config termservice obj= "NT Authority\NetworkService"`

Po znaménku rovná se vyžaduje mezera.
### <a name="set-service-start-type"></a>Nastavit typ spuštění služby
`sc config termservice start= demand`

Po znaménku rovná se vyžaduje mezera. Možné hodnoty zahájení zahrnují `boot` , `system` , `auto` , `demand` , `disabled` , `delayed-auto` .
### <a name="set-service-dependencies"></a>Nastavit závislosti služby
`sc config termservice depend= RPCSS`

Po znaménku rovná se vyžaduje mezera.
### <a name="start-service"></a>Spustit službu
`net start termservice`

nebo

`sc start termservice`
### <a name="stop-service"></a>Zastavit službu
`net stop termservice`

nebo

`sc stop termservice`
## <a name="manage-networking-features-using-cmd"></a>Správa síťových funkcí pomocí příkazu CMD
### <a name="show-nic-properties"></a>Zobrazit vlastnosti síťové karty
`netsh interface show interface`
### <a name="show-ip-properties"></a>Zobrazit vlastnosti IP adresy
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Zobrazit konfiguraci protokolu IPSec
`netsh nap client show configuration`
### <a name="enable-nic"></a>Povolit síťové rozhraní
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Nastavit síťovou kartu pro použití DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Další informace o nástroji `netsh` získáte [kliknutím sem](/windows-server/networking/technologies/netsh/netsh-contexts).

Virtuální počítače Azure by měly být vždy nakonfigurované v hostovaném operačním systému, aby k získání IP adresy používaly protokol DHCP. Nastavení statické IP adresy Azure pořád používá protokol DHCP k přidělení statické IP adresy virtuálnímu počítači.
### <a name="ping"></a>Ping
`ping 8.8.8.8`
### <a name="port-ping"></a>Příkazy pro odeslání portu
Instalace klienta Telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Test připojení

`telnet bing.com 80`

Odebrání klienta služby Telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Pokud je ve výchozím nastavení omezen na metody dostupné ve Windows, může být PowerShell lepším řešením pro testování připojení portů. Příklady najdete v níže uvedené části prostředí PowerShell.
### <a name="test-dns-name-resolution"></a>Testování překladu názvů DNS
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Zobrazit pravidlo brány Windows Firewall
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Zakázat bránu Windows Firewall
`netsh advfirewall set allprofiles state off`

Tento příkaz můžete použít při odstraňování potíží, abyste dočasně využívali bránu Windows Firewall. Povolí se při příštím restartování nebo když ho povolíte pomocí následujícího příkazu. Zastavte službu brány Windows Firewall (MPSSVC) nebo službu BFE (Base Filtering Engine) jako způsob, jak vyfiltrovat bránu Windows Firewall. Zastavení služby MPSSVC nebo BFE bude mít za následek blokování všech připojení.
### <a name="enable-windows-firewall"></a>Povolit bránu Windows Firewall
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups-using-cmd"></a>Správa uživatelů a skupin pomocí příkazu CMD
### <a name="create-local-user-account"></a>Vytvořit místní uživatelský účet
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Přidat místního uživatele do místní skupiny
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Ověřte, že je povolený uživatelský účet.
`net user <username> | find /i "active"`

Virtuální počítače Azure vytvořené z generalizované image budou mít účet místního správce přejmenovaný na název zadaný při zřizování virtuálních počítačů. Takže to obvykle nebude `Administrator` .
### <a name="enable-user-account"></a>Povolit uživatelský účet
`net user <username> /active:yes`
### <a name="view-user-account-properties"></a>Zobrazení vlastností uživatelského účtu
`net user <username>`

Příklady řádků zájmu z místního účtu správce:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Zobrazit místní skupiny
`net localgroup`
## <a name="manage-the-windows-event-log-using-cmd"></a>Správa protokolu událostí systému Windows pomocí příkazu CMD
### <a name="query-event-log-errors"></a>Chyby protokolu událostí dotazu
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Přejděte `/c:10` na požadovaný počet událostí, které se mají vrátit, nebo je přesuňte, aby se vracely všechny události, které filtr odpovídají.
### <a name="query-event-log-by-event-id"></a>Dotaz na protokol událostí podle ID události
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Dotazování protokolu událostí podle ID a poskytovatele události
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Dotaz na protokol událostí podle ID a poskytovatele události za posledních 24 hodin
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Použijte `604800000` k prohlédnutí 7 dní místo 24 hodin.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Dotazování protokolu událostí podle ID události, poskytovatele a EventData za posledních 7 dnů
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications-using-cmd"></a>Zobrazení nebo odebrání nainstalovaných aplikací pomocí příkazu CMD
### <a name="list-installed-applications"></a>Výpis nainstalovaných aplikací
`wmic product get Name,InstallDate | sort /r | more`

`sort /r`Seřadí sestupně podle data instalace a usnadňuje tak zobrazení, co bylo nedávno nainstalováno. Slouží `<spacebar>` k přechodu na další stránku výstupu nebo `<enter>` pro posunutí na jeden řádek.
### <a name="uninstall-an-application"></a>Odinstalace aplikace
`wmic path win32_product where name="<name>" call uninstall`

Nahraďte `<name>` názvem vráceným výše uvedeným příkazem pro aplikaci, kterou chcete odebrat.

## <a name="file-system-management-using-cmd"></a>Správa systému souborů pomocí příkazu CMD
### <a name="get-file-version"></a>Získat verzi souboru
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Tento příklad vrátí verzi souboru ovladače virtuální síťové karty, která je netvsc.sys, netvsc63.sys nebo netvsc60.sys v závislosti na verzi systému Windows.
### <a name="scan-for-system-file-corruption"></a>Vyhledat poškození systémového souboru
`sfc /scannow`

Viz také [Oprava bitové kopie systému Windows](/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Vyhledat poškození systémového souboru
`dism /online /cleanup-image /scanhealth`

Viz také [Oprava bitové kopie systému Windows](/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Exportovat oprávnění souboru do textového souboru
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Uložit oprávnění souboru do souboru ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`
### <a name="restore-file-permissions-from-acl-file"></a>Obnovit oprávnění souboru ze souboru ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Cesta při použití `/restore` musí být nadřazenou složkou složky, kterou jste zadali při použití `/save` . V tomto příkladu `\RSA` je nadřazená složka, kterou jste `\MachineKeys` zadali v `/save` předchozím příkladu.
### <a name="take-ntfs-ownership-of-a-folder"></a>Převzít vlastnictví složky v systému souborů NTFS
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Rekurzivní udělení oprávnění systému souborů NTFS ke složce
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`
## <a name="manage-devices"></a>Správa zařízení
### <a name="remove-non-present-pnp-devices"></a>Odebrat zařízení PNP, která nejsou přítomná
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Správa zásad skupiny
### <a name="force-group-policy-update"></a>Vynutit aktualizaci zásad skupiny
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks-using-cmd"></a>Různé úlohy pomocí příkazu CMD
### <a name="show-os-version"></a>Zobrazit verzi operačního systému
`ver`

nebo

`wmic os get caption,version,buildnumber /format:list`

nebo

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Zobrazit datum instalace operačního systému
`systeminfo | find /i "original"`

nebo

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Zobrazit čas posledního spuštění
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Zobrazit časové pásmo
`systeminfo | find /i "time zone"`

nebo

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Restartovat systém Windows
`shutdown /r /t 0`

`/f`Při přidání se vynutí ukončení spuštěných aplikací bez upozornění uživatelů.
### <a name="detect-safe-mode-boot"></a>Rozpoznat spuštění v bezpečném režimu
`bcdedit /enum | find /i "safeboot"`

## <a name="windows-commands---powershell"></a>Příkazy Windows – PowerShell

Pokud chcete spustit PowerShell v konzole SAC, po dosažení příkazového řádku zadejte:

`powershell <enter>`

> [!CAUTION]
> Před spuštěním jakýchkoli dalších příkazů PowerShellu odeberte z relace PowerShellu modul PSReadLine. Došlo k známému problému, při kterém se do textu vloženého ze schránky můžou zavádět nadbytečné znaky, pokud je PSReadLine spuštěný v relaci PowerShellu v konzole SAC.

Nejprve ověřte, zda je PSReadLine načten. Ve výchozím nastavení je načtena v systému Windows Server 2016, Windows 10 a novějších verzích systému Windows. Je k dispozici pouze v dřívějších verzích systému Windows, pokud byla ručně nainstalována.

Pokud se tento příkaz vrátí k příkazovému řádku bez výstupu, modul se nenačetl a v konzole SAC můžete jako normální používat relaci PowerShellu.

`get-module psreadline`

Pokud výše uvedený příkaz vrátí verzi modulu PSReadLine, spusťte následující příkaz a uvolněte ho. Tento příkaz neodstraní ani neodinstaluje modul, ale uvolní ho jenom z aktuální relace PowerShellu.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings-using-powershell"></a>Zobrazení a úprava nastavení registru Windows pomocí prostředí PowerShell
### <a name="verify-rdp-is-enabled"></a>Ověřte, že je povolený protokol RDP.
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Druhý klíč (v rámci \Policies) bude existovat pouze v případě, že je nakonfigurováno příslušné nastavení zásad skupiny.
### <a name="enable-rdp"></a>Povolit protokol RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Druhý klíč (v rámci \Policies) by byl nutný pouze v případě, že bylo nakonfigurováno příslušné nastavení zásad skupiny. Hodnota bude přepsána při další aktualizaci zásad skupiny, pokud je nakonfigurována v zásadách skupiny.
## <a name="manage-windows-services-using-powershell"></a>Správa služeb systému Windows pomocí prostředí PowerShell
### <a name="view-service-details"></a>Zobrazit podrobnosti služby
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` dá se použít, ale nezahrnuje přihlašovací účet služby. `Get-WmiObject win32-service` podporují.
### <a name="set-service-logon-account"></a>Nastavení přihlašovacího účtu služby
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Pokud používáte jiný účet služby než `NT AUTHORITY\LocalService` , `NT AUTHORITY\NetworkService` nebo `LocalSystem` , zadejte heslo účtu jako poslední (osmý) argument za názvem účtu.
### <a name="set-service-startup-type"></a>Nastavit typ spouštění služby
`set-service termservice -startuptype Manual`

`Set-service` akceptuje `Automatic` , `Manual` nebo `Disabled` pro typ spuštění.
### <a name="set-service-dependencies"></a>Nastavit závislosti služby
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Spustit službu
`start-service termservice`
### <a name="stop-service"></a>Zastavit službu
`stop-service termservice`
## <a name="manage-networking-features-using-powershell"></a>Správa síťových funkcí pomocí prostředí PowerShell
### <a name="show-nic-properties"></a>Zobrazit vlastnosti síťové karty
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

nebo

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` je k dispozici v roce 2012 +, pro 2008 R2 použití `Get-WmiObject` .
### <a name="show-ip-properties"></a>Zobrazit vlastnosti IP adresy
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Povolit síťové rozhraní
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

nebo

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` je k dispozici v roce 2012 +, pro 2008 R2 použití `Get-WmiObject` .
### <a name="set-nic-to-use-dhcp"></a>Nastavit síťovou kartu pro použití DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` je k dispozici v roce 2012 +. Pro 2008 R2 použití `Get-WmiObject` . Virtuální počítače Azure by měly být vždy nakonfigurované v hostovaném operačním systému, aby k získání IP adresy používaly protokol DHCP. Nastavení statické IP adresy Azure pořád používá protokol DHCP k přidělení IP adresy virtuálnímu počítači.
### <a name="ping"></a>Ping
`test-netconnection`

> [!NOTE]
> Rutina Write-Progress možná nebude s tímto příkazem fungovat. V rámci zmírnění rizika můžete pomocí `$ProgressPreference = "SilentlyContinue"` prostředí PowerShell zakázat indikátor průběhu.

nebo

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` bez jakýchkoli parametrů se pokusí provést test z pøíkazového testu `internetbeacon.msedge.net` . Je k dispozici v roce 2012 +. Pro 2008 R2 použijte `Get-WmiObject` jako v druhém příkladu.
### <a name="port-ping"></a>Příkazy pro odeslání portu
`test-netconnection -ComputerName bing.com -Port 80`

nebo

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` je k dispozici v roce 2012 +. Pro 2008 R2 použití `Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Testování překladu názvů DNS
`resolve-dnsname bing.com`

nebo

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` je k dispozici v roce 2012 +. Pro 2008 R2 použití `System.Net.DNS` .
### <a name="show-windows-firewall-rule-by-name"></a>Zobrazit pravidlo brány Windows Firewall podle názvu
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP`
### <a name="show-windows-firewall-rule-by-port"></a>Zobrazit pravidlo brány Windows Firewall podle portu
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

nebo

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` je k dispozici v roce 2012 +. Pro 2008 R2 použijte `hnetcfg.fwpolicy2` objekt com.
### <a name="disable-windows-firewall"></a>Zakázat bránu Windows Firewall
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` je k dispozici v roce 2012 +. Pro 2008 R2 použijte `netsh advfirewall` jako odkaz v části cmd výše.
## <a name="manage-users-and-groups-using-powershell"></a>Správa uživatelů a skupin pomocí PowerShellu
### <a name="create-local-user-account"></a>Vytvořit místní uživatelský účet
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Ověřte, že je povolený uživatelský účet.
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

nebo

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` je k dispozici v roce 2012 +. Pro 2008 R2 použití `Get-WmiObject` . Tento příklad ukazuje vestavěný účet místního správce, který má vždycky identifikátor SID `S-1-5-21-*-500` . Virtuální počítače Azure vytvořené z generalizované image budou mít účet místního správce přejmenovaný na název zadaný při zřizování virtuálních počítačů. Takže to obvykle nebude `Administrator` .
### <a name="add-local-user-to-local-group"></a>Přidat místního uživatele do místní skupiny
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Povolit místní uživatelský účet
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser`

Tento příklad povoluje integrovaný účet místního správce, který má vždycky identifikátor SID `S-1-5-21-*-500` . Virtuální počítače Azure vytvořené z generalizované image budou mít účet místního správce přejmenovaný na název zadaný při zřizování virtuálních počítačů. Takže to obvykle nebude `Administrator` .
### <a name="view-user-account-properties"></a>Zobrazení vlastností uživatelského účtu
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

nebo

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` je k dispozici v roce 2012 +. Pro 2008 R2 použití `Get-WmiObject` . Tento příklad ukazuje vestavěný účet místního správce, který má vždycky identifikátor SID `S-1-5-21-*-500` .
### <a name="view-local-groups"></a>Zobrazit místní skupiny
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` je k dispozici v roce 2012 +. Pro 2008 R2 použití `Get-WmiObject` .
## <a name="manage-the-windows-event-log-using-powershell"></a>Správa protokolu událostí Windows pomocí prostředí PowerShell
### <a name="query-event-log-errors"></a>Chyby protokolu událostí dotazu
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Přejděte `/c:10` na požadovaný počet událostí, které se mají vrátit, nebo je přesuňte, aby se vracely všechny události, které filtr odpovídají.
### <a name="query-event-log-by-event-id"></a>Dotaz na protokol událostí podle ID události
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Dotazování protokolu událostí podle ID a poskytovatele události
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Dotaz na protokol událostí podle ID a poskytovatele události za posledních 24 hodin
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Použijte `604800000` k prohlédnutí 7 dní místo 24 hodin. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Dotazování protokolu událostí podle ID události, poskytovatele a EventData za posledních 7 dnů
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications-using-powershell"></a>Zobrazení nebo odebrání nainstalovaných aplikací s využitím PowerShellu
### <a name="list-installed-software"></a>Výpis nainstalovaného softwaru
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Odinstalace softwaru
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management-using-powershell"></a>Správa systému souborů pomocí prostředí PowerShell
### <a name="get-file-version"></a>Získat verzi souboru
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Tento příklad vrátí verzi souboru ovladače virtuální síťové karty, která je pojmenována netvsc.sys, netvsc63.sys nebo netvsc60.sys v závislosti na verzi systému Windows.
### <a name="download-and-extract-file"></a>Stažení a extrakce souboru
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Tento příklad vytvoří `c:\bin` složku a pak stáhne a extrahuje sadu nástrojů na webu Sysinternals `c:\bin` .
## <a name="miscellaneous-tasks-using-powershell"></a>Různé úlohy s využitím PowerShellu
### <a name="show-os-version"></a>Zobrazit verzi operačního systému
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber`
### <a name="view-os-install-date"></a>Zobrazit datum instalace operačního systému
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Zobrazit čas posledního spuštění
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Zobrazit dobu provozu Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Vrátí dobu provozu `<days>:<hours>:<minutes>:<seconds>:<milliseconds>` , například `49:16:48:00.00` .
### <a name="restart-windows"></a>Restartovat systém Windows
`restart-computer`

`-force`Při přidání se vynutí ukončení spuštěných aplikací bez upozornění uživatelů.
## <a name="instance-metadata"></a>Metadata instance

Pomocí dotazu na metadata instance Azure z virtuálního počítače Azure můžete zobrazit podrobnosti, jako je osType, Location, vmSize, vmId, Name, resourceGroupName, subscriptionId, privateIpAddress a publicIpAddress.

Dotazování metadat instance vyžaduje v pořádku připojení k hostované síti, protože provádí volání REST prostřednictvím hostitele Azure do služby metadat instance. Takže pokud se můžete dotazovat na metadata instance, získáte informace o tom, že host bude schopný komunikovat přes síť do služby hostované v Azure.

Další informace najdete v tématu [Služba metadat instance Azure](../windows/instance-metadata-service.md).

### <a name="instance-metadata"></a>Metadata instance
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Typ operačního systému (metadata instance)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Umístění (metadata instance)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Velikost (metadata instance)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>ID virtuálního počítače (metadata instance)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Název virtuálního počítače (metadata instance)
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
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Adresa podsítě IPv4/předpona (metadata instance)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IP adresa IPv6 (metadata instance)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>Adresa MAC (metadata instance)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Další kroky
* Hlavní stránka dokumentace Windows pro sériová konzola se nachází [tady](serial-console-windows.md).
* K dispozici je také konzola sériového portu pro virtuální počítače se [systémem Linux](serial-console-linux.md) .
* Přečtěte si další informace o [diagnostice spouštění](boot-diagnostics.md).
