---
title: Virtuální počítač Azure konzoly sériového portu | Microsoft Docs
description: Obousměrná sériové konzoly pro virtuální počítače Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 8e9e98a0c9f1db805d62cdee038fb4754ff047e2
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960436"
---
# <a name="virtual-machine-serial-console-preview"></a>Virtuální počítač konzoly sériového portu (preview) 


Konzole sériového portu virtuálního počítače na platformě Azure poskytuje přístup k založený na textu konzoly pro virtuální počítače Linux a Windows. Toto sériové připojení je COM1 sériového portu virtuálního počítače a poskytuje přístup k virtuálnímu počítači a nesouvisí se síť virtuálních počítačů / operačního stavu systému. Přístup ke konzole sériového portu pro virtuální počítač, můžete provést pouze prostřednictvím portálu Azure aktuálně a povoleny pouze pro uživatele, kteří mají Přispěvatel virtuálních počítačů nebo vyšší přístup k virtuálnímu počítači. 

> [!Note] 
> Verze Preview jsou k dispozici pro vás, za předpokladu, že souhlasíte s podmínkami použití. Další informace najdete v tématu [Microsoft Azure doplňkové podmínky použití pro Microsoft Azure Preview.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Aktuálně tato služba je v **verzi public preview** a přístup ke konzole sériového portu pro virtuální počítače je k dispozici pro globální oblastech Azure. V tomto okamžiku konzoly sériového portu, není k dispozici cloudu Azure Government, Azure v Německu a Azure China.

 

## <a name="prerequisites"></a>Požadavky 

* Musí používat model nasazení prostředků správy. Nasazení Classic nejsou podporovány. 
* Virtuální počítač musí mít [spouštění diagnostiky](boot-diagnostics.md) povoleno 
* Účet, pomocí konzoly sériového portu, musí mít [role Přispěvatel](../../role-based-access-control/built-in-roles.md) pro virtuální počítač a [spouštění diagnostiky](boot-diagnostics.md) účet úložiště. 

## <a name="open-the-serial-console"></a>Otevření konzole sériového portu
konzoly sériového portu pro virtuální počítače je k dispozici pouze prostřednictvím [portál Azure](https://portal.azure.com). Níže jsou uvedené kroky pro přístup k konzole sériového portu pro virtuální počítače prostřednictvím portálu 

  1. Otevřete portál Azure
  2. V nabídce vlevo vyberte virtuální počítače.
  3. Klikněte na virtuální počítač v seznamu. Otevře se stránka Přehled pro virtuální počítač.
  4. Posuňte se na podporu + řešení potíží s části a klikněte na možnost konzoly sériového portu (Preview). Nové podokno s konzole sériového portu se jim otevře a spuštění připojení.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>Zakázat funkci
Funkce konzoly sériového portu můžete deaktivovat pro konkrétní virtuální počítače zakázáním nastavení diagnostiky spouštění tohoto virtuálního počítače.

## <a name="serial-console-security"></a>Zabezpečení konzoly sériového portu 

### <a name="access-security"></a>Zabezpečení přístupu 
Přístup k konzoly sériového portu je omezená na uživatele, kteří mají [virtuálních počítačů přispěvatelé](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebo vyšší přístup k virtuálnímu počítači. Pokud klienta služby AAD vyžaduje Vícefaktorové ověřování, tak přístup ke konzole sériového portu bude také nutné vícefaktorového ověřování, jako je přístup přes [portál Azure](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Všechna data odeslána zpět a stanovilo se šifrují v drátové síti.

### <a name="audit-logs"></a>Protokoly auditu
Veškerý přístup ke konzole sériového portu je právě přihlášen [spouštění diagnostiky](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) protokoly virtuálního počítače. Přístup k tyto protokoly jsou ve vlastnictví a řídí správce virtuálního počítače Azure.  

>[!CAUTION] 
Při přihlášení žádná hesla přístup pro konzolu, pokud příkazy spuštění konzoly obsahovat nebo uložení hesla, tajné klíče, uživatelská jména nebo jakoukoli jinou formou z identifikovatelné osobní informace (PII), ty se zapíše do Diagnostika spouštění virtuálního počítače protokoly, společně s všechny ostatní viditelné text, v rámci implementace konzole sériového portu přejděte zpátky funkce. Tyto protokoly jsou cyklické a pouze uživatelé, kteří mají oprávnění ke čtení pro účet úložiště diagnostiky mít přístup k nim, ale doporučujeme následující osvědčený postup pomocí vzdálené plochy pro všechno, co, která může zahrnovat tajných klíčů nebo PII. 

### <a name="concurrent-usage"></a>Souběžné používání
Pokud je uživatel připojený k konzoly sériového portu a jiný uživatel úspěšně požaduje přístup k tomuto virtuálnímu počítači stejný, bude první uživatel odpojen a druhý uživatel připojen způsobem podobají první uživatel stálého a nechá fyzické konzoly a nový uživatel uložený.

>[!CAUTION] 
To znamená, že uživatel, který odpojí nebude odhlásit! Schopnost Vynutit odhlášení při odpojení (prostřednictvím SIGHUP nebo podobné mechanismus) je stále v plánovaná. Pro systém Windows není automatické vypršení časového limitu povolené ve Speciální konzoly pro správu, ale pro Linux můžete nakonfigurovat nastavení limitu terminálu. 


## <a name="accessing-serial-console-for-windows"></a>Přístup k konzoly sériového portu pro Windows 
Bude mít novější bitové kopie systému Windows Server na platformě Azure [Speciální konzoly pro správu](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) ve výchozím nastavení povolené (Speciální konzoly pro správu). Speciální konzoly pro správu je podporována na serveru verze systému Windows, ale není k dispozici v klientských verzích (například Windows 10, Windows 8 nebo Windows 7). Chcete-li povolit konzoly sériového portu pro virtuální počítače s Windows, které jsou vytvořené s použitím Feb2018 nebo nižší bitové kopie použijte následující kroky: 

1. Připojení k virtuálnímu počítači přes vzdálenou plochu Windows
2. Z příkazového řádku pro správu spusťte následující příkazy 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Restartujte systém pro konzolu Speciální konzoly pro správu, aby byl povolen

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Pokud potřebné Speciální konzoly pro správu se dá nastavit do režimu offline, i 

1. Připojte disk systému windows, který chcete speciální konzoly pro správu, které jsou nakonfigurované pro jako datový disk do stávajícího virtuálního počítače. 
2. Z příkazového řádku pro správu spusťte následující příkazy 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Jak zjistím, zda je povoleno speciální konzoly pro správu, nebo ne 

Pokud [Speciální konzoly pro správu] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) není povoleno konzole sériového portu nezobrazí řádku Speciální konzoly pro správu. V některých případech může zobrazit informace o stavu virtuálního počítače nebo je prázdný.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Povolení spouštěcí nabídky, které se zobrazí v konzole sériového portu 

Pokud potřebujete aktivovat Windows spouštěcí zavaděč zobrazí výzvu pro zobrazení v konzole sériového portu následující další možnosti můžete přidat do spouštěcí zavaděč systému Windows.

1. Připojení k virtuálnímu počítači přes vzdálenou plochu Windows
2. Z příkazového řádku pro správu spusťte následující příkazy 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Restartování systému pro spouštěcí nabídky povolení

> [!NOTE] 
> Na tento bod podpora pro funkce klíče není povolená, pokud požadujete rozšířené možnosti spuštění pomocí nástroje bcdedit/set {aktuální} onetimeadvancedoptions na, najdete v části [nástroje bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) další podrobnosti

## <a name="windows-commands---cmd"></a>Příkazy prostředí Windows - CMD 

Tato část obsahuje příklady příkazů pro provádění běžných úloh ve scénářích, kde budete muset použít speciální konzoly pro správu přístup k virtuálnímu počítači, například když potřebujete RDP řešení potíží s připojením.

Speciální konzoly pro správu byl zahrnut ve všech verzích Windows od Windows Server 2003, ale ve výchozím nastavení vypnutá. Speciální konzoly pro správu závisí na `sacdrv.sys` ovladač jádra `Special Administration Console Helper` služby (`sacsvr`) a `sacsess.exe` procesu. Další informace najdete v tématu [nouzové správy služeb nástroje a nastavení](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

Speciální konzoly pro správu můžete připojit k vaší běžícím operačním systému prostřednictvím sériového portu. Při spuštění CMD z Speciální konzoly pro správu, `sacsess.exe` spustí `cmd.exe` v rámci vaší běžícím operačním systému. Uvidíte, že v úloze správce, pokud jste protokolu RDP pro virtuální počítač ve stejné času jste připojeni k Speciální konzoly pro správu pomocí funkce konzoly sériového portu. CMD přístup přes Speciální konzoly pro správu je stejný `cmd.exe` používat při připojení prostřednictvím protokolu RDP. Stejné příkazy a nástroje jsou k dispozici, včetně možnosti z této instance CMD spusťte PowerShell. Prostředí Windows Recovery Environment (WinRE) v tomto Speciální konzoly pro správu je vám umožní správu spuštěné operačním systému, kde WinRE se spustí do jiné, minimální OS, je hlavní rozdíl mezi Speciální konzoly pro správu. Když virtuální počítače Azure přístup WinRE, s funkcí konzoly sériového portu nepodporují virtuální počítače Azure lze spravovat prostřednictvím Speciální konzoly pro správu.

Protože speciální konzoly pro správu je omezený na 80 x 24 vyrovnávací s žádné přejděte zpět, přidejte `| more` příkazy zobrazení výstupu jedné stránky v čase. Použití `<spacebar>` zobrazíte na další stránku nebo `<enter>` zobrazíte další řádek.  

`SHIFT+INSERT` je-li vložit zástupce pro okna konzoly sériového portu.

Z důvodu Speciální konzoly pro správu je omezená vyrovnávací paměti delší příkazy snadněji typu se v místním textovém editoru a jeho vložení do speciální konzoly pro správu.

### <a name="view-and-edit-windows-registry-settings"></a>Zobrazení a úpravy nastavení registru systému Windows
#### <a name="verify-rdp-is-enabled"></a>Ověřte, zda že je povoleno protokolu RDP
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Druhý klíč (v rámci \Policies) bude existovat, pouze pokud je nakonfigurované nastavení zásad skupiny relevantní.

#### <a name="enable-rdp"></a>Povolení protokolu RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

Druhý klíč (v rámci \Policies) by třeba, pouze pokud nakonfiguroval nastavení zásad relevantní skupiny. Hodnota bude přepsaná v další aktualizaci zásad skupiny, pokud je nakonfigurovaný v zásadách skupiny.

### <a name="manage-windows-services"></a>Spravovat služby systému Windows

#### <a name="view-service-state"></a>Zobrazení stavu služby
`sc query termservice`
####  <a name="view-service-logon-account"></a>Zobrazení služby přihlašovací účet
`sc qc termservice`
#### <a name="set-service-logon-account"></a>Nastavte přihlašovací účet služby 
`sc config termservice obj= "NT Authority\NetworkService"`

Mezeru po požadované symbolem rovná se.
#### <a name="set-service-start-type"></a>Typ spouštění služby sady
`sc config termservice start= demand` 

Mezeru po požadované symbolem rovná se. Spuštění možné hodnoty patří `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
#### <a name="set-service-dependencies"></a>Závislosti služeb sady
`sc config termservice depend= RPCSS`

Mezeru po požadované symbolem rovná se.
#### <a name="start-service"></a>Spuštění služby
`net start termservice`

nebo

`sc start termservice`
#### <a name="stop-service"></a>Zastavit službu
`net stop termservice`

nebo

`sc stop termservice`
### <a name="manage-networking-features"></a>Spravovat funkce sítě
#### <a name="show-nic-properties"></a>Zobrazit vlastnosti síťový adaptér
`netsh interface show interface` 
#### <a name="show-ip-properties"></a>Zobrazit vlastnosti IP adresy
`netsh interface ip show config`
#### <a name="show-ipsec-configuration"></a>Zobrazit konfigurace protokolu IPSec
`netsh nap client show configuration`  
#### <a name="enable-nic"></a>Povolit seskupování
`netsh interface set interface name="<interface name>" admin=enabled`
#### <a name="set-nic-to-use-dhcp"></a>Nastavit síťový adaptér pro používání protokolu DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Virtuální počítače Azure musí být vždy nakonfigurovaný v hostovaný operační systém pro používání protokolu DHCP k získání IP adresy. Nastavení statické IP Azure stále používá protokol DHCP přidělit statickou IP adresu virtuálního počítače.
#### <a name="ping"></a>Ping
`ping 8.8.8.8` 
#### <a name="port-ping"></a>Port ping  
Nainstalovat klient služby telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Test připojení

`telnet bing.com 80`

Chcete-li odebrat klient služby telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Pokud ve výchozím nastavení je omezené na metody, která je k dispozici v systému Windows, může být prostředí PowerShell je vhodnější pro testování připojení k portu. Najdete v části prostředí PowerShell následující příklady.
#### <a name="test-dns-name-resolution"></a>Test překladu názvů DNS
`nslookup bing.com`
#### <a name="show-windows-firewall-rule"></a>Zobrazit pravidla brány Windows Firewall
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
#### <a name="disable-windows-firewall"></a>Zakázání brány Windows Firewall
`netsh advfirewall set allprofiles state off`

Tento příkaz můžete použít při řešení potíží s dočasně vyloučení brány Windows Firewall. Ho bude možné povolit v dalším restartování nebo když jste enaable pomocí níže uvedeného příkazu. Nedojde k zastavení služby brány Windows Firewall (MPSSVC) nebo služby základní filtrování modul BFE () jako způsob, jak vyloučení brány Windows Firewall. Zastavení MPSSVC nebo BFE, bude mít za následek všechny připojení blokován.
#### <a name="enable-windows-firewall"></a>Povolit bránu Windows Firewall
`netsh advfirewall set allprofiles state on`
### <a name="manage-users-and-groups"></a>Spravovat uživatele a skupiny
#### <a name="create-local-user-account"></a>Vytvořte místní uživatelský účet
`net user /add <username> <password>`
#### <a name="add-local-user-to-local-group"></a>Přidání místního uživatele do místní skupiny
`net localgroup Administrators <username> /add`
#### <a name="verify-user-account-is-enabled"></a>Ověřte, že uživatelský účet je povolená.
`net user <username> | find /i "active"`

Virtuální počítače Azure vytvořené z zobecněný bitové kopie bude mít účet místního správce přejmenován na název zadaný při zřizování virtuálních počítačů. Takže obvykle nebudou `Administrator`.
#### <a name="enable-user-account"></a>Povolení uživatelského účtu
`net user <username> /active:yes`  
#### <a name="view-user-account-properties"></a>Zobrazení vlastností uživatelského účtu
`net user <username>`

Příklad řádky týkající se z účtu místního správce:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

#### <a name="view-local-groups"></a>Zobrazení místních skupin
`net localgroup`
### <a name="manage-the-windows-event-log"></a>Správa protokolu událostí systému Windows
#### <a name="query-event-log-errors"></a>Chyby v protokolu událostí dotazu
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Změna `/c:10` na požadovaný počet událostí vrátí nebo přesunout ho vrátit všechny události odpovídající filtru.
#### <a name="query-event-log-by-event-id"></a>Protokol událostí dotazu podle ID události
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Protokol událostí dotazu podle ID události a zprostředkovatele
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Dotaz protokolu událostí podle ID události a zprostředkovatele za posledních 24 hodin
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Použití `604800000` hledat zpět 7 dní místo 24 hodin.
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Dotaz protokolu událostí podle ID událostí, zprostředkovatele a EventData v posledních 7 dnů
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Zobrazení nebo odebrání nainstalovaných aplikací
#### <a name="list-installed-applications"></a>Výpis nainstalovaných aplikací
`wmic product get Name,InstallDate | sort /r | more`

`sort /r` Řazení sestupně podle nainstalovat datum, aby ji snadno zjistit, co byla nedávno nainstalovali. Použití `<spacebar>` přejdete na další stránku výstupu, nebo `<enter>` posunut jeden řádek.
#### <a name="uninstall-an-application"></a>Odinstalovat aplikaci
`wmic path win32_product where name="<name>" call uninstall`

Nahraďte `<name>` s názvem vrátila ve výše uvedeném příkazu pro aplikaci, kterou chcete odebrat.

### <a name="file-system-management"></a>Správa systému souborů
#### <a name="get-file-version"></a>Získání verze souboru
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Tento příklad vrátí verzi souboru virtuální síťový adaptér ovladače, který je netvsc.sys, netvsc63.sys nebo netvsc60.sys v závislosti na verzi Windows.
#### <a name="scan-for-system-file-corruption"></a>Vyhledat poškození systému souborů
`sfc /scannow`

Viz také [oprava bitové kopie systému Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
#### <a name="scan-for-system-file-corruption"></a>Vyhledat poškození systému souborů
`dism /online /cleanup-image /scanhealth`

Viz také [oprava bitové kopie systému Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
#### <a name="export-file-permissions-to-text-file"></a>Exportovat do textového souboru oprávnění k souboru
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
#### <a name="save-file-permissions-to-acl-file"></a>Uložit oprávnění k souboru do seznamu ACL souboru
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
#### <a name="restore-file-permissions-from-acl-file"></a>Obnovit oprávnění k souboru ze seznamu ACL souboru
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Cesta při použití `/restore` musí být nadřazená složka složky, které jste zadali při použití `/save`. V tomto příkladu `\RSA` nadřazeným objektem `\MachineKeys` složka zadaná v `/save` výše uvedeném příkladu.
#### <a name="take-ntfs-ownership-of-a-folder"></a>Převzetí vlastnictví složky systému souborů NTFS
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
#### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Udělení oprávnění systému souborů NTFS k rekurzivnímu složky
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
### <a name="manage-devices"></a>Správa zařízení
#### <a name="remove-non-present-pnp-devices"></a>Odeberte zařízení PNP není přítomen
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
### <a name="manage-group-policy"></a>Správa zásad skupiny
#### <a name="force-group-policy-update"></a>Vynutit aktualizaci zásad skupiny
`gpupdate /force /wait:-1`
### <a name="miscellaneous-tasks"></a>Různé úlohy
#### <a name="show-os-version"></a>Zobrazit verzi operačního systému
`ver`

nebo 

`wmic os get caption,version,buildnumber /format:list`

nebo 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
#### <a name="view-os-install-date"></a>Datum instalace operačního systému zobrazení
`systeminfo | find /i "original"`

nebo 

`wmic os get installdate`
#### <a name="view-last-boot-time"></a>Zobrazení posledního spuštění
`systeminfo | find /i "system boot time"`
#### <a name="view-time-zone"></a>Zobrazení časové pásmo
`systeminfo | find /i "time zone"`

nebo

`wmic timezone get caption,standardname /format:list`
#### <a name="restart-windows"></a>Restartování systému Windows
`shutdown /r /t 0`

Přidání `/f` vynutí spuštěné aplikace okno zavřít bez upozornění uživatele.
#### <a name="detect-safe-mode-boot"></a>Zjištění nouzovém režimu spouštění
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Příkazy prostředí Windows – prostředí PowerShell

Pokud chcete spustit prostředí PowerShell v Speciální konzoly pro správu, po dosažení příkazový řádek, zadejte:

`powershell <enter>`

>[!CAUTION]
Odebere modul PSReadLine z relace prostředí PowerShell před spuštěním další příkazy prostředí PowerShell. Je známý problém kde mohou být znaky navíc zavedeny do textu vložené ze schránky, pokud PSReadLine běží v relaci prostředí PowerShell v Speciální konzoly pro správu.

Nejdřív zkontrolujte, pokud je načtena PSReadLine. Načte se ve výchozím nastavení v systému Windows Server 2016, Windows 10 a novějších verzích systému Windows. Jenom je přítomen v dřívějších verzích Windows Pokud měl byla ručně nainstalovaná. 

Pokud tento příkaz vrátí řádek s žádný výstup, pak nebyl načten modulem a můžete pokračovat pomocí relace prostředí PowerShell v Speciální konzoly pro správu jako normální.

`get-module psreadline`

Pokud výše uvedeném příkazu vrátí verze modulu PSReadLine, spusťte následující příkaz, který ji uvolnit. Tento příkaz odstraňte nebo k odinstalaci modulu, ho pouze uvolnění provede z aktuální relace prostředí PowerShell.

`remove-module psreadline`

### <a name="view-and-edit-windows-registry-settings"></a>Zobrazení a úpravy nastavení registru systému Windows
#### <a name="verify-rdp-is-enabled"></a>Ověřte, zda že je povoleno protokolu RDP
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Druhý klíč (v rámci \Policies) bude existovat, pouze pokud je nakonfigurované nastavení zásad skupiny relevantní.
#### <a name="enable-rdp"></a>Povolení protokolu RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Druhý klíč (v rámci \Policies) by třeba, pouze pokud nakonfiguroval nastavení zásad relevantní skupiny. Hodnota bude přepsaná v další aktualizaci zásad skupiny, pokud je nakonfigurovaný v zásadách skupiny.
### <a name="manage-windows-services"></a>Spravovat služby systému Windows
#### <a name="view-service-details"></a>Zobrazit podrobnosti služby
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` je možné použít, ale neobsahuje přihlašovací účet služby. `Get-WmiObject win32-service` nepodporuje.
#### <a name="set-service-logon-account"></a>Nastavte přihlašovací účet služby
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Při použití účtu služby jiné než `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`, nebo `LocalSystem`, zadejte heslo k účtu jako poslední (osmého) argument za název účtu.
#### <a name="set-service-startup-type"></a>Nastavení typu spouštění služby
`set-service termservice -startuptype Manual`

`Set-service` přijímá `Automatic`, `Manual`, nebo `Disabled` pro typ spouštění.
#### <a name="set-service-dependencies"></a>Závislosti služeb sady
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
#### <a name="start-service"></a>Spuštění služby
`start-service termservice`
#### <a name="stop-service"></a>Zastavit službu
`stop-service termservice`
### <a name="manage-networking-features"></a>Spravovat funkce sítě
#### <a name="show-nic-properties"></a>Zobrazit vlastnosti síťový adaptér
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

nebo 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` je k dispozici v 2012 +, 2008 R2 používat `Get-WmiObject`.
#### <a name="show-ip-properties"></a>Zobrazit vlastnosti IP adresy
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
#### <a name="enable-nic"></a>Povolit seskupování
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

nebo

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` je k dispozici v 2012 +, 2008 R2 používat `Get-WmiObject`.
#### <a name="set-nic-to-use-dhcp"></a>Nastavit síťový adaptér pro používání protokolu DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` je k dispozici na 2012 +. 2008 R2 pomocí `Get-WmiObject`. Virtuální počítače Azure musí být vždy nakonfigurovaný v hostovaný operační systém pro používání protokolu DHCP k získání IP adresy. Nastavení statické IP Azure stále používá protokol DHCP umožnit IP k virtuálnímu počítači.
#### <a name="ping"></a>Ping
`test-netconnection`

nebo

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` bez žádné parametry se pokusí odeslat příkaz ping `internetbeacon.msedge.net`. Je k dispozici na 2012 +. 2008 R2 pomocí `Get-WmiObject` jako v druhém příkladu.
#### <a name="port-ping"></a>Port Ping
`test-netconnection -ComputerName bing.com -Port 80`

nebo

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` je k dispozici na 2012 +. Pro použití 2008 R2 `Net.Sockets.TcpClient`
#### <a name="test-dns-name-resolution"></a>Test překladu názvů DNS
`resolve-dnsname bing.com` 

nebo 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` je k dispozici na 2012 +. 2008 R2 pomocí `System.Net.DNS`.
#### <a name="show-windows-firewall-rule-by-name"></a>Pravidlo brány firewall systému Windows zobrazit podle názvu
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
#### <a name="show-windows-firewall-rule-by-port"></a>Zobrazit pravidlo brány firewall systému Windows, port
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

nebo

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` je k dispozici na 2012 +. 2008 R2 pomocí `hnetcfg.fwpolicy2` objektu COM. 
#### <a name="disable-windows-firewall"></a>Zakázání brány Windows firewall
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` je k dispozici na 2012 +. 2008 R2 pomocí `netsh advfirewall` jako odkazované v oddíle CMD, který je uveden výše.
### <a name="manage-users-and-groups"></a>Spravovat uživatele a skupiny
#### <a name="create-local-user-account"></a>Vytvořte místní uživatelský účet
`new-localuser <name>`
#### <a name="verify-user-account-is-enabled"></a>Ověřte, že uživatelský účet je povolená.
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

nebo 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` je k dispozici na 2012 +. 2008 R2 pomocí `Get-WmiObject`. Tento příklad ukazuje předdefinovaný účet místního správce, který má vždy SID `S-1-5-21-*-500`. Virtuální počítače Azure vytvořené z zobecněný bitové kopie bude mít účet místního správce přejmenován na název zadaný při zřizování virtuálních počítačů. Takže obvykle nebudou `Administrator`.
#### <a name="add-local-user-to-local-group"></a>Přidání místního uživatele do místní skupiny
`add-localgroupmember -group Administrators -member <username>`
#### <a name="enable-local-user-account"></a>Povolit místní uživatelský účet
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Tento příklad povolí předdefinovaný účet místního správce, který má vždy SID `S-1-5-21-*-500`. Virtuální počítače Azure vytvořené z zobecněný bitové kopie bude mít účet místního správce přejmenován na název zadaný při zřizování virtuálních počítačů. Takže obvykle nebudou `Administrator`.
#### <a name="view-user-account-properties"></a>Zobrazení vlastností uživatelského účtu
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

nebo 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` je k dispozici na 2012 +. 2008 R2 pomocí `Get-WmiObject`. Tento příklad ukazuje předdefinovaný účet místního správce, který má vždy SID `S-1-5-21-*-500`.
#### <a name="view-local-groups"></a>Zobrazení místních skupin
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` je k dispozici na 2012 +. 2008 R2 pomocí `Get-WmiObject`.
### <a name="manage-the-windows-event-log"></a>Správa protokolu událostí systému Windows
#### <a name="query-event-log-errors"></a>Chyby v protokolu událostí dotazu
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Změna `/c:10` na požadovaný počet událostí vrátí nebo přesunout ho vrátit všechny události odpovídající filtru.
#### <a name="query-event-log-by-event-id"></a>Protokol událostí dotazu podle ID události
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Protokol událostí dotazu podle ID události a zprostředkovatele
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Dotaz protokolu událostí podle ID události a zprostředkovatele za posledních 24 hodin
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Použití `604800000` hledat zpět 7 dní místo 24 hodin. |
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Dotaz protokolu událostí podle ID událostí, zprostředkovatele a EventData v posledních 7 dnů
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Zobrazení nebo odebrání nainstalovaných aplikací
#### <a name="list-installed-software"></a>Výpis nainstalovaných softwaru
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
#### <a name="uninstall-software"></a>Odinstalace softwaru
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
### <a name="file-system-management"></a>Správa systému souborů
#### <a name="get-file-version"></a>Získání verze souboru
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Tento příklad vrátí verzi souboru virtuální ovladače NIC, která se nazývá netvsc.sys, netvsc63.sys nebo netvsc60.sys v závislosti na verzi Windows.
#### <a name="download-and-extract-file"></a>Stažení a extrakci souborů
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Tento příklad vytvoří `c:\bin` složky, poté stáhne a extrahuje Sysinternals sada nástrojů do `c:\bin`.
### <a name="miscellaneous-tasks"></a>Různé úlohy
#### <a name="show-os-version"></a>Zobrazit verzi operačního systému
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
#### <a name="view-os-install-date"></a>Datum instalace operačního systému zobrazení
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
#### <a name="view-last-boot-time"></a>Zobrazení posledního spuštění
`(get-wmiobject win32_operatingsystem).lastbootuptime`
#### <a name="view-windows-uptime"></a>Zobrazit doba provozu systému Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Vrátí provozu jako `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, například `49:16:48:00.00`. 
#### <a name="restart-windows"></a>Restartování systému Windows
`restart-computer`

Přidání `-force` vynutí spuštěné aplikace okno zavřít bez upozornění uživatele.
### <a name="instance-metadata"></a>Instance metadat

V rámci svého virtuálního počítače Azure, chcete-li zobrazit podrobnosti, například osType, umístění, vmSize, vmId, název, název skupiny prostředků, subscriptionId, privateIpAddress a publicIpAddress se můžete dotazovat Azure instance metadata z.

Připojení k síti v pořádku hosta, dotazování instance metadata vyžaduje, protože ji provede volání REST prostřednictvím Azure hostitele ve službě metadata instance. Proto pokud budete moci dotazu instance metadata, která sdělí, že je schopen komunikovat přes síť do služby Azure hostovaná Host.

Další informace najdete v tématu [služby Azure Instance metadat](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

#### <a name="instance-metadata"></a>Instance metadat
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
#### <a name="os-type-instance-metadata"></a>Typ operačního systému (Instance Metadata)
`$im.Compute.osType`
#### <a name="location-instance-metadata"></a>Umístění (Instance Metadata)
`$im.Compute.Location`
#### <a name="size-instance-metadata"></a>Velikost (Instance Metadata)
`$im.Compute.vmSize`
#### <a name="vm-id-instance-metadata"></a>ID virtuálního počítače (Instance Metadata)
`$im.Compute.vmId`
#### <a name="vm-name-instance-metadata"></a>Název virtuálního počítače (Instance Metadata)
`$im.Compute.name`
#### <a name="resource-group-name-instance-metadata"></a>Název skupiny prostředků (Instance Metadata)
`$im.Compute.resourceGroupName`
#### <a name="subscription-id-instance-metadata"></a>ID předplatného (Instance Metadata)
`$im.Compute.subscriptionId`
#### <a name="tags-instance-metadata"></a>Značky (Instance Metadata)
`$im.Compute.tags`
#### <a name="placement-group-id-instance-metadata"></a>ID skupiny umístění (Instance Metadata)
`$im.Compute.placementGroupId`
#### <a name="platform-fault-domain-instance-metadata"></a>Doména selhání platformy (Instance Metadata)
`$im.Compute.platformFaultDomain`
#### <a name="platform-update-domain-instance-metadata"></a>Doména platformy aktualizace (Instance Metadata)
`$im.Compute.platformUpdateDomain`
#### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 privátní IP adresa (Instance Metadata)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
#### <a name="ipv4-public-ip-address-instance-metadata"></a>Veřejná IP adresa IPv4 (Instance Metadata)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
#### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Adresa podsítě IPv4 / předpony (Instance Metadata)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
#### <a name="ipv6-ip-address-instance-metadata"></a>IP adresa IPv6 (Instance Metadata)
`$im.network.interface.ipv6.ipAddress`
#### <a name="mac-address-instance-metadata"></a>Adresa MAC (Instance Metadata)
`$im.network.interface.macAddress`

## <a name="errors"></a>Chyby
Nejčastější chyby jsou přechodné ve své podstatě a opakování pokusu o připojení k adrese tyto. Níže uvedená tabulka obsahuje seznam chyb a zmírnění 

Chyba                            |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Nelze načíst nastavení diagnostiky spouštění pro '<VMNAME>'. Pokud chcete používat konzole sériového portu, zkontrolujte, zda že tento Diagnostika spouštění je povolený pro tento virtuální počítač. | Zkontrolujte, zda má virtuální počítač [spouštění diagnostiky](boot-diagnostics.md) povolena. 
Virtuální počítač je v zastaveném stavu deallocated. Spusťte virtuální počítač a opakujte pokus o připojení konzoly sériového portu. | Virtuální počítač musí být ve stavu spuštěna přístup ke konzole sériového portu
Nemáte potřebná oprávnění k použití této konzoly sériového portu virtuálního počítače. Zajistěte, abyste měli aspoň oprávnění role Přispěvatel virtuálních počítačů.| Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. V tématu [přístup k požadavkům](#prerequisites) podrobnosti
Nelze určit skupinu prostředků pro účet úložiště diagnostiky spouštění se<STORAGEACCOUNTNAME>'. Ověřte, zda je povoleno spuštění diagnostiky pro tento virtuální počítač a máte přístup k tomuto účtu úložiště. | Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. V tématu [přístup k požadavkům](#prerequisites) podrobnosti

## <a name="known-issues"></a>Známé problémy 
Jak jsme jsou stále ve fázích preview pro přístup ke konzole sériového portu, jsme pracují prostřednictvím některé známé problémy, níže je seznam těchto s možných řešení 

Problém                           |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Neexistuje žádná možnost pomocí konzoly sériového portu sady instance škálování pro virtuální počítač | V době preview přístup ke konzole sériového portu pro instancí sady škálování virtuálního počítače není podporován.
Stiskne zadejte po Banner informující o připojení nezobrazuje protokolu v řádku | [Stiskne zadejte se nic nestane.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Jenom informace o stavu se zobrazí při připojení k virtuální počítač s Windows| [Signály stavu Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
Nelze zadat v Speciální konzoly pro správu řádku, pokud je povoleno ladění jádra | Protokolu RDP pro virtuální počítač a spusťte `bcdedit /debug {current} off` z příkazového řádku se zvýšenými oprávněními. Pokud nemůžete RDP můžete místo toho připojit disk operačního systému k jiným virtuálním Počítačem Azure a upravit ho při připojené jako datový disk pomocí `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, pak Prohodit zpět na disk.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy 
**Q. Jak lze odeslat zpětnou vazbu?**

A. Poskytnutí zpětné vazby jako problém přechodem na https://aka.ms/serialconsolefeedback. Případně méně (upřednostňované) odeslat zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo v kategorii virtuálního počítače http://feedback.azure.com 

**Q. Zobrazí chybová zpráva "existující konzoly má konfliktní typ operačního systému"Systém Windows"pomocí požadovaný typ operačního systému Linux?**

A. Jedná se o známý problém tento problém lze vyřešit jednoduše otevřít [prostředí cloudu Azure](https://docs.microsoft.com/azure/cloud-shell/overview) v režimu bash a zkuste to znovu.

**Q. Nejste schopni přistupovat konzole sériového portu, kde můžete soubor případu podpory?**

A. Tato funkce preview se zabývá prostřednictvím Azure Preview podmínky. Podpora pro tento nejlépe vyřeší prostřednictvím kanálů uvedených výše. 

## <a name="next-steps"></a>Další postup
* Je taky dostupná ke konzole sériového portu [Linux](../linux/serial-console.md) virtuální počítače
* Další informace o [bootdiagnostics](boot-diagnostics.md)
