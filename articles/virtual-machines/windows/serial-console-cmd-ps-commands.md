---
title: Příkaz CMD a prostředí PowerShell ve virtuálních počítačích Windows Azure | Dokumentace Microsoftu
description: Jak používat příkazy CMD a prostředí PowerShell v rámci SAC ve virtuálních počítačích Windows Azure
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 83b3aa1efdde367577a563b477403c313a51d4fe
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "40177576"
---
# <a name="windows-commands---cmd"></a>Příkazy Windows - CMD 

Tato část obsahuje příklady příkazů pro provádění běžných úkolů ve scénářích, kde budete muset použít SAC pro přístup k virtuálnímu počítači Windows, například když potřebujete řešení potíží s připojením RDP.

SAC byl zahrnut ve všech verzích Windows od verze Windows Server 2003, ale je ve výchozím nastavení zakázané. SAC spoléhá na `sacdrv.sys` ovladač jádra `Special Administration Console Helper` service (`sacsvr`) a `sacsess.exe` procesu. Další informace najdete v tématu [nástroje služby pro nouzovou správu a nastavení](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

SAC umožňuje připojení k běžící operační systém přes sériový port. Při spuštění příkazového řádku z SAC, `sacsess.exe` spustí `cmd.exe` v běžícím operačním systému. Uvidíte, že v úkolu Správce připojení RDP k virtuálnímu počítači ve stejné čas jste připojeni k SAC prostřednictvím funkce konzoly sériového portu. CMD přistupujete prostřednictvím SAC je stejný `cmd.exe` použít při připojení přes protokol RDP. Stejné příkazy a nástroje jsou k dispozici, včetně možnosti z této instance CMD spusťte prostředí PowerShell. Hlavní rozdíl mezi SAC a prostředí Windows Recovery (WinRE) v tomto SAC je umožňuje spravovat váš spuštěný operační systém, kde WinRE ochránil před vytvořením různých, minimální OS. Když virtuální počítače Azure nepodporují možnost přístupu k prostředí WinRE, pomocí funkce konzoly sériového portu, virtuální počítače Azure je možné spravovat prostřednictvím SAC.

Protože SAC je omezen na vyrovnávací paměť obrazovky 80 x 24 s žádné přejděte zpět, přidejte `| more` do příkazů, chcete-li zobrazit jednu stránku výstup najednou. Použití `<spacebar>` zobrazíte na další stránku, nebo `<enter>` zobrazíte další řádek.  

`SHIFT+INSERT` je vložit zástupce pro okna konzoly sériového portu.

Z důvodu vyrovnávací paměti pro SAC omezené zobrazení delší příkazů může být jednodušší zadejte si do místního textového editoru a potom vložen SAC.

## <a name="view-and-edit-windows-registry-settings"></a>Zobrazení a úpravy nastavení registru Windows
### <a name="verify-rdp-is-enabled"></a>Ověřte, zda že je povolen protokol RDP
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Druhý klíč (v rámci \Policies) bude existovat, pouze pokud je nakonfigurováno nastavení zásad příslušné skupiny.

### <a name="enable-rdp"></a>Povolení protokolu RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

Druhý klíč (v rámci \Policies) by potřeba pouze v případě nakonfiguroval zásadu příslušné skupiny. Hodnota bude přepsán při další aktualizaci zásad skupiny, pokud je nakonfigurovaný v zásadách skupiny.

## <a name="manage-windows-services"></a>Správa služeb Windows

### <a name="view-service-state"></a>Zobrazení stavu služby
`sc query termservice`
###  <a name="view-service-logon-account"></a>Zobrazit služby přihlašovací účet
`sc qc termservice`
### <a name="set-service-logon-account"></a>Nastavte přihlašovací účet služby 
`sc config termservice obj= "NT Authority\NetworkService"`

Za znaménko rovná se vyžádáním mezerou.
### <a name="set-service-start-type"></a>Typ spouštění služby sady
`sc config termservice start= demand` 

Za znaménko rovná se vyžádáním mezerou. Start možné hodnoty zahrnují `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
### <a name="set-service-dependencies"></a>Nastavte službu závislosti
`sc config termservice depend= RPCSS`

Za znaménko rovná se vyžádáním mezerou.
### <a name="start-service"></a>Spustit službu
`net start termservice`

nebo

`sc start termservice`
### <a name="stop-service"></a>Zastavit službu
`net stop termservice`

nebo

`sc stop termservice`
## <a name="manage-networking-features"></a>Spravovat síťovým funkcím
### <a name="show-nic-properties"></a>Zobrazit vlastnosti síťového adaptéru
`netsh interface show interface` 
### <a name="show-ip-properties"></a>Zobrazit vlastnosti IP adresy
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Zobrazit konfiguraci protokolu IPSec
`netsh nap client show configuration`  
### <a name="enable-nic"></a>Povolení síťové karty
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Nastavte síťové karty pro používání protokolu DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Virtuální počítače Azure by měl vždy nakonfiguruje v hostovaném operačním systému pro používání protokolu DHCP k získání IP adresy. Nastavení statické IP Azure stále používá protokol DHCP přidělit statickou IP adresu virtuálního počítače.
### <a name="ping"></a>Ping
`ping 8.8.8.8` 
### <a name="port-ping"></a>Port ping  
Nainstalovat klient služby telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Test připojení

`telnet bing.com 80`

Chcete-li odebrat klient služby telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Pokud jiné metody, která je k dispozici ve Windows ve výchozím nastavení prostředí PowerShell může být lepším řešením pro testování připojení portů. V části prostředí PowerShell následující příklady.
### <a name="test-dns-name-resolution"></a>Otestujte překlad DNS názvu
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Zobrazit pravidla brány Windows Firewall
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Zakázat bránu Windows Firewall
`netsh advfirewall set allprofiles state off`

Tento příkaz můžete použít při řešení potíží s dočasně vyloučit brány Windows Firewall. Bude možné povolit při příštím restartování počítače nebo při enaable můžete pomocí následujícího příkazu. Nedojde k zastavení služby brány Windows Firewall (MPSSVC) nebo službu Base Filtering Engine (BFE) jako způsob, jak vyloučit brány Windows Firewall. Blokuje všechna připojení způsobí zastavení MPSSVC nebo BFE.
### <a name="enable-windows-firewall"></a>Povolit bránu Windows Firewall
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Správa uživatelů a skupin
### <a name="create-local-user-account"></a>Vytvořit místní uživatelský účet
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Přidání místního uživatele do místní skupiny
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Ověřte, že uživatelský účet je povolená.
`net user <username> | find /i "active"`

Virtuální počítače Azure vytvořené z generalizované image bude mít účet místního správce, přejmenovat na název zadaný během zřizování virtuálních počítačů. Takže obvykle nebudou `Administrator`.
### <a name="enable-user-account"></a>Povolení uživatelského účtu
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>Zobrazení vlastností uživatelského účtu
`net user <username>`

Příklady řádků oblastí zájmu, od místního správce účtu:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Zobrazení místních skupin
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Správa protokolu událostí Windows
### <a name="query-event-log-errors"></a>Chyby v protokolu událostí dotazu
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Změna `/c:10` na požadovaný počet událostí na vrátit, nebo ho přesunout do vrátí všechny události filtru neodpovídají.
### <a name="query-event-log-by-event-id"></a>Protokol událostí dotazů podle ID události
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Protokol událostí dotazů podle ID události a zprostředkovatele
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Dotazování protokolu událostí podle ID události a zprostředkovatele za posledních 24 hodin
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Použití `604800000` vás pod rouškou zpět 7 dní, ne za 24 hodin.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Dotazování protokolu událostí podle ID události, zprostředkovatele a EventData za posledních 7 dní
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Zobrazit či odebrat nainstalovaných aplikací
### <a name="list-installed-applications"></a>Výpis nainstalovaných aplikací
`wmic product get Name,InstallDate | sort /r | more`

`sort /r` Řazení sestupně podle datum, aby byl snadno zjistit, co byl nedávno nainstalován nástroj instalace. Použití `<spacebar>` k přechodu na další stránku výstupu nebo `<enter>` závěry pro jeden řádek.
### <a name="uninstall-an-application"></a>Odinstalace aplikace
`wmic path win32_product where name="<name>" call uninstall`

Nahraďte `<name>` s názvem vrátil v předchozím příkazu pro aplikaci, kterou chcete odebrat.

## <a name="file-system-management"></a>Správa systému souborů
### <a name="get-file-version"></a>Získání verze souboru
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

V tomto příkladu vrátí verzi souboru virtuální ovladače síťové karty, což je netvsc.sys, netvsc63.sys nebo netvsc60.sys v závislosti na verzi Windows.
### <a name="scan-for-system-file-corruption"></a>Vyhledání poškození systémového souboru
`sfc /scannow`

Viz také [opravit Windows Image](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Vyhledání poškození systémového souboru
`dism /online /cleanup-image /scanhealth`

Viz také [opravit Windows Image](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Export oprávnění k souboru do textového souboru
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Uložit oprávnění k souboru do seznamu ACL souboru
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>Obnovit soubor oprávnění ze seznamu ACL souboru
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Cesta při použití `/restore` musí být nadřazená složka složky, které jste zadali při použití `/save`. V tomto příkladu `\RSA` je nadřazeného člena `\MachineKeys` složky zadané v `/save` výše uvedený příklad.
### <a name="take-ntfs-ownership-of-a-folder"></a>Převzít vlastnictví složky systému souborů NTFS
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Udělení oprávnění NTFS pro složky rekurzivně
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>Správa zařízení
### <a name="remove-non-present-pnp-devices"></a>Odebrat zařízení PNP – k dispozici
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Správa zásad skupiny
### <a name="force-group-policy-update"></a>Vynutit aktualizaci zásad skupiny
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Různé úlohy
### <a name="show-os-version"></a>Zobrazit verzi operačního systému
`ver`

nebo 

`wmic os get caption,version,buildnumber /format:list`

nebo 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Datum instalace operačního systému zobrazení
`systeminfo | find /i "original"`

nebo 

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Zobrazit čas posledního spuštění
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Zobrazení časové pásmo
`systeminfo | find /i "time zone"`

nebo

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Restartovat Windows
`shutdown /r /t 0`

Přidání `/f` vynutí spouštění aplikací okno zavřít bez upozornění uživatele.
### <a name="detect-safe-mode-boot"></a>Zjištění Nouzový režim spuštění
`bcdedit /enum | find /i "safeboot"` 

# <a name="windows-commands---powershell"></a>Příkazy Windows – PowerShell

Pokud chcete spustit prostředí PowerShell v SAC, až se dostanete příkazový řádek, zadejte:

`powershell <enter>`

>[!CAUTION]
Odebrání modulu PSReadLine z relace prostředí PowerShell před spuštěním dalších příkazů Powershellu. Existuje známý problém ve kterém mohou být zavedena nadbytečné znaky v textu ze schránky vložit, pokud je v relaci Powershellu v SAC PSReadLine.

Nejprve zkontrolujte, zda je načtena PSReadLine. Načte se ve výchozím nastavení ve Windows serveru 2016, Windows 10 a novějších verzích Windows. Pouze je k dispozici v dřívějších verzích Windows Pokud měl byla ručně nainstalovaná. 

Pokud tento příkaz vrátí na příkazový řádek se žádný výstup, potom modul nebyl načten a můžete pokračovat v používání relaci Powershellu v SAC jako za normálních okolností.

`get-module psreadline`

Pokud výše uvedený příkaz vrátí verzi modulu PSReadLine, spusťte následující příkaz k uvolněn. Tento příkaz neodstraní ani odinstalace modulu, je to jenom uvolněn z aktuální relace prostředí PowerShell.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Zobrazení a úpravy nastavení registru Windows
### <a name="verify-rdp-is-enabled"></a>Ověřte, zda že je povolen protokol RDP
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Druhý klíč (v rámci \Policies) bude existovat, pouze pokud je nakonfigurováno nastavení zásad příslušné skupiny.
### <a name="enable-rdp"></a>Povolení protokolu RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Druhý klíč (v rámci \Policies) by potřeba pouze v případě nakonfiguroval zásadu příslušné skupiny. Hodnota bude přepsán při další aktualizaci zásad skupiny, pokud je nakonfigurovaný v zásadách skupiny.
## <a name="manage-windows-services"></a>Správa služeb Windows
### <a name="view-service-details"></a>Zobrazit podrobnosti služby
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` je možné to však nezahrnuje přihlašovacího účtu služby. `Get-WmiObject win32-service` nepodporuje.
### <a name="set-service-logon-account"></a>Nastavte přihlašovací účet služby
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Při použití účtu služby jiné než `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`, nebo `LocalSystem`, zadejte heslo k účtu jako poslední argument (osmého) po názvu účtu.
### <a name="set-service-startup-type"></a>Nastavení typu spouštění služby
`set-service termservice -startuptype Manual`

`Set-service` přijímá `Automatic`, `Manual`, nebo `Disabled` pro typ spouštění.
### <a name="set-service-dependencies"></a>Nastavte službu závislosti
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Spustit službu
`start-service termservice`
### <a name="stop-service"></a>Zastavit službu
`stop-service termservice`
## <a name="manage-networking-features"></a>Spravovat síťovým funkcím
### <a name="show-nic-properties"></a>Zobrazit vlastnosti síťového adaptéru
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

nebo 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` je k dispozici v 2012 +, pro použití 2008R2 `Get-WmiObject`.
### <a name="show-ip-properties"></a>Zobrazit vlastnosti IP adresy
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Povolení síťové karty
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

nebo

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` je k dispozici v 2012 +, pro použití 2008R2 `Get-WmiObject`.
### <a name="set-nic-to-use-dhcp"></a>Nastavte síťové karty pro používání protokolu DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` je k dispozici na 2012 +. 2008R2 použít `Get-WmiObject`. Virtuální počítače Azure by měl vždy nakonfiguruje v hostovaném operačním systému pro používání protokolu DHCP k získání IP adresy. Nastavení statické IP Azure stále používá protokol DHCP přidělit IP adresu virtuálního počítače.
### <a name="ping"></a>Ping
`test-netconnection`

nebo

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` bez parametrů se zkuste příkaz ping `internetbeacon.msedge.net`. Je k dispozici na 2012 +. 2008R2 použít `Get-WmiObject` viz druhý příklad.
### <a name="port-ping"></a>Port Ping
`test-netconnection -ComputerName bing.com -Port 80`

nebo

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` je k dispozici na 2012 +. Pro použití 2008 R2 `Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Otestujte překlad DNS názvu
`resolve-dnsname bing.com` 

nebo 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` je k dispozici na 2012 +. 2008R2 použít `System.Net.DNS`.
### <a name="show-windows-firewall-rule-by-name"></a>Zobrazit pravidla brány firewall Windows podle názvu
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>Zobrazit Windows pravidlo brány firewall na portu
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

nebo

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` je k dispozici na 2012 +. 2008R2 použít `hnetcfg.fwpolicy2` objekt modelu COM. 
### <a name="disable-windows-firewall"></a>Zakázat bránu Windows firewall
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` je k dispozici na 2012 +. 2008R2 použít `netsh advfirewall` jako odkazovaná CMD výše v části.
## <a name="manage-users-and-groups"></a>Správa uživatelů a skupin
### <a name="create-local-user-account"></a>Vytvořit místní uživatelský účet
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Ověřte, že uživatelský účet je povolená.
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

nebo 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` je k dispozici na 2012 +. 2008R2 použít `Get-WmiObject`. Tento příklad ukazuje předdefinovaný účet místního správce, který má vždy SID `S-1-5-21-*-500`. Virtuální počítače Azure vytvořené z generalizované image bude mít účet místního správce, přejmenovat na název zadaný během zřizování virtuálních počítačů. Takže obvykle nebudou `Administrator`.
### <a name="add-local-user-to-local-group"></a>Přidání místního uživatele do místní skupiny
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Povolit místní uživatelský účet
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Tento příklad povolí předdefinovaný účet místního správce, který má vždy SID `S-1-5-21-*-500`. Virtuální počítače Azure vytvořené z generalizované image bude mít účet místního správce, přejmenovat na název zadaný během zřizování virtuálních počítačů. Takže obvykle nebudou `Administrator`.
### <a name="view-user-account-properties"></a>Zobrazení vlastností uživatelského účtu
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

nebo 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` je k dispozici na 2012 +. 2008R2 použít `Get-WmiObject`. Tento příklad ukazuje předdefinovaný účet místního správce, který má vždy SID `S-1-5-21-*-500`.
### <a name="view-local-groups"></a>Zobrazení místních skupin
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` je k dispozici na 2012 +. 2008R2 použít `Get-WmiObject`.
## <a name="manage-the-windows-event-log"></a>Správa protokolu událostí Windows
### <a name="query-event-log-errors"></a>Chyby v protokolu událostí dotazu
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Změna `/c:10` na požadovaný počet událostí na vrátit, nebo ho přesunout do vrátí všechny události filtru neodpovídají.
### <a name="query-event-log-by-event-id"></a>Protokol událostí dotazů podle ID události
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Protokol událostí dotazů podle ID události a zprostředkovatele
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Dotazování protokolu událostí podle ID události a zprostředkovatele za posledních 24 hodin
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Použití `604800000` vás pod rouškou zpět 7 dní, ne za 24 hodin. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Dotazování protokolu událostí podle ID události, zprostředkovatele a EventData za posledních 7 dní
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Zobrazit či odebrat nainstalovaných aplikací
### <a name="list-installed-software"></a>Seznam nainstalovaný software
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Odinstalace softwaru
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Správa systému souborů
### <a name="get-file-version"></a>Získání verze souboru
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

V tomto příkladu vrátí verzi souboru virtuální ovladače síťové karty, který se nazývá netvsc.sys netvsc63.sys či netvsc60.sys v závislosti na verzi Windows.
### <a name="download-and-extract-file"></a>Stažení a extrakci souboru
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Tento příklad vytvoří `c:\bin` složku, pak stáhne a extrahuje Sysinternals sady nástrojů do `c:\bin`.
## <a name="miscellaneous-tasks"></a>Různé úlohy
### <a name="show-os-version"></a>Zobrazit verzi operačního systému
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>Datum instalace operačního systému zobrazení
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Zobrazit čas posledního spuštění
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Zobrazení doby provozu pro Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Vrátí dobu provozu jako `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, například `49:16:48:00.00`. 
### <a name="restart-windows"></a>Restartovat Windows
`restart-computer`

Přidání `-force` vynutí spouštění aplikací okno zavřít bez upozornění uživatele.
## <a name="instance-metadata"></a>Instance Metadata

Ve vašem virtuálním počítači Azure, chcete-li zobrazit podrobnosti, jako je osType, umístění, vmSize, vmId, název, název skupiny prostředků, ID předplatného, privateIpAddress a publicIpAddress se můžete dotazovat Azure instance metadata z.

Hostovaný v pořádku síťové připojení, dotazování metadat instance vyžaduje, protože provede volání REST prostřednictvím Azure hostitele tak, aby služba instance metadata. Takže pokud máte možnost provést dotaz na instance metadata, které sděluje, hosta je schopen komunikovat přes síť do Azure hostovaná služba.

Další informace najdete v tématu [služby Azure Instance Metadata](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Instance metadata
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Typ operačního systému (Instance metadat)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Umístění (Instance metadat)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Velikost (Instance metadat)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>ID virtuálního počítače (Instance metadat)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Název virtuálního počítače (Instance metadat)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Název skupiny prostředků (Instance metadat)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>ID předplatného (Instance metadat)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Značky (Instance metadat)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>ID skupiny umístění (Instance metadat)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Doména selhání platformy (Instance metadat)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Aktualizační doména platformy (Instance metadat)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 privátní IP adresa (Instance metadat)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>Veřejná IP adresa protokolu IPv4 (Instance metadat)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Adresa podsítě IPv4 / předpony (Instance metadat)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IP adresa protokolu IPv6 (Instance metadat)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>Adresa MAC (Instance metadat)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Další postup
* Na stránce dokumentace ke službě Windows hlavní konzoly sériového portu se nachází [tady](serial-console.md).
* Je taky dostupná ke konzole sériového portu [Linux](../linux/serial-console.md) virtuálních počítačů.
* Další informace o [Diagnostika spouštění](boot-diagnostics.md).
