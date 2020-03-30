---
title: Při napojovat připojení RDP k virtuálním počítačům Azure dojde k vnitřní chybě | Dokumenty společnosti Microsoft
description: Přečtěte si, jak řešit interní chyby RDP v Microsoft Azure.| Dokumenty společnosti Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 8046e4f42db50db15c840a13b95ae1f3620a8c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266920"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Při pokusu o připojení k virtuálnímu počítači Azure přes Vzdálenou plochu dojde k vnitřní chybě

Tento článek popisuje chybu, ke které může dojít při pokusu o připojení k virtuálnímu počítači (VM) v Microsoft Azure.


## <a name="symptoms"></a>Příznaky

K virtuálnímu počítači Azure se nelze připojit pomocí protokolu rdp (remote desktop). Připojení se zasekne v části Konfigurace vzdáleného připojení nebo se zobrazí následující chybová zpráva:

- Vnitřní chyba RDP
- Došlo k vnitřní chybě.
- Tento počítač nelze připojit ke vzdálenému počítači. Zkuste se připojit znovu. Pokud problém přetrvává, obraťte se na vlastníka vzdáleného počítače nebo správce sítě.


## <a name="cause"></a>Příčina

K tomuto problému může dojít z následujících důvodů:

- K místním šifrovacím klíčům RSA nelze získat přístup.
- Protokol TLS je zakázán.
- Certifikát je poškozen nebo vypršela jeho platnost.

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte snímek disku operačního systému ovlivněného virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](../windows/snapshot-copy-managed-disk.md).

K řešení tohoto problému použijte konzolu Serial Console nebo [opravte virtuální počítače offline](#repair-the-vm-offline) připojením disku operačního systému virtuálního počítače k virtuálnímu virtuálnímu počítače pro obnovení.


### <a name="use-serial-control"></a>Použití sériového řízení

Připojte se k [konzoli Serial Console a otevřete instanci prostředí PowerShell](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Pokud konzola Serial Console není na vašem virtuálním počítači povolená, přejděte do části [oprava virtuálního počítače offline.](#repair-the-vm-offline)

#### <a name="step-1-check-the-rdp-port"></a>Krok: 1 Kontrola portu RDP

1. V instanci prostředí PowerShell použijte [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) ke kontrole, zda port 8080 používá jiné aplikace:

        Netstat -anob |more
2. Pokud Termservice.exe používá port 8080, přejděte ke kroku 2. Pokud jiný než Termservice.exe používá port 8080, postupujte takto:

    1. Zastavte službu pro aplikaci, která používá službu 3389:

            Stop-Service -Name <ServiceName> -Force

    2. Spusťte terminálovou službu:

            Start-Service -Name Termservice

2. Pokud aplikaci nelze zastavit nebo pokud se vás tato metoda netýká, změňte port pro RDP:

    1. Změna portu:

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Nastavte bránu firewall pro nový port:

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Aktualizujte skupinu zabezpečení sítě pro nový port](../../virtual-network/security-overview.md) v portu RDP portálu Azure.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Krok 2: Nastavení správných oprávnění pro certifikát podepsaný pod svým podpisem rdp

1.  V instanci prostředí PowerShell spusťte následující příkazy jeden po druhém a obnovte certifikát podepsaný pod svým držitelem rdp:

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Pokud nelze obnovit certifikát pomocí této metody, zkuste obnovit certifikát podepsaný pod svým držitelem protokolu RDP vzdáleně:

    1. Z funkčního virtuálního počítače, který má připojení k virtuálnímu počítače, který má potíže, zadejte **mmc** do pole **Spustit** a otevřete konzolu MMC.
    2. V nabídce **Soubor** vyberte **Přidat nebo odebrat modul snap-in**, vyberte **Certifikáty**a pak vyberte **Přidat**.
    3. Vyberte **Účty počítače**, vyberte **Jiný počítač**a přidejte adresu IP problémového virtuálního počítače.
    4. Přejděte do složky **Vzdálená plocha\Certifikáty,** klepněte pravým tlačítkem myši na certifikát a vyberte **příkaz Odstranit**.
    5. V instanci prostředí PowerShell ze konzoly Serial Console restartujte službu Konfigurace vzdálené plochy:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Obnovte oprávnění pro složku MachineKeys.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. Restartujte virtuální počítač a zkuste spustit připojení ke vzdálené ploše k virtuálnímu počítači. Pokud k chybě stále dochází, přejděte k dalšímu kroku.

#### <a name="step-3-enable-all-supported-tls-versions"></a>Krok 3: Povolení všech podporovaných verzí TLS

Klient RDP používá jako výchozí protokol protokol TLS 1.0. To však lze změnit na TLS 1.1, který se stal novým standardem. Pokud je tls 1.1 na virtuálním počítači zakázán, připojení se nezdaří.
1.  V instanci CMD povolte protokol TLS:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Chcete-li zabránit přepsání změn zásadami služby AD, dočasně zastavte aktualizaci zásad skupiny:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Restartujte virtuální počítač tak, aby se změny projevily. Pokud je problém vyřešen, spusťte následující příkaz a znovu povolte zásady skupiny:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Pokud je změna vrácena, znamená to, že ve vaší doméně společnosti existuje zásada služby Active Directory. Je třeba změnit tuto zásadu, aby se zabránilo tento problém z opakování.

### <a name="repair-the-vm-offline"></a>Oprava virtuálního virtuálního montu offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojení disku operačního systému k virtuálnímu virtuálnímu počítače pro obnovení

1. [Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Po připojení disku operačního systému k virtuálnímu počítače pro obnovení zkontrolujte, zda je disk v konzole pro správu disků označen jako **online.** Poznamenejte si písmeno jednotky, které je přiřazeno k připojenému disku operačního systému.
3. Spusťte připojení vzdálené plochy k virtuálnímu počítači pro obnovení.

#### <a name="enable-dump-log-and-serial-console"></a>Povolení protokolu výpisu a sériové konzoly

Chcete-li povolit protokol s výpisem stavu paměti a konzolu Serial Console, spusťte následující skript.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními **(Spustit jako správce**).
2. Spusťte tento skript:

    V tomto skriptu předpokládáme, že písmeno jednotky, které je přiřazeno k připojenému disku operačního systému, je F. Nahraďte toto písmeno jednotky příslušnou hodnotou pro váš virtuální počítač.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Obnovení oprávnění pro složku MachineKeys

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními **(Spustit jako správce**).
2. Spusťte následující skript. V tomto skriptu předpokládáme, že písmeno jednotky, které je přiřazeno k připojenému disku operačního systému, je F. Nahraďte toto písmeno jednotky příslušnou hodnotou pro váš virtuální počítač.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Povolení všech podporovaných verzí TLS

1.  Otevřete relaci příkazového řádku se zvýšenými oprávněními **(Spustit jako správce)** a spusťte následující příkazy. Následující skript předpokládá, že písmeno ovladače je přiřazeno k připojenému disku operačního systému F. Nahraďte toto písmeno jednotky příslušnou hodnotou pro virtuální počítač.
2.  Zkontrolujte, který tls je povolen:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Pokud klíč neexistuje nebo jeho hodnota je **0**, povolte protokol spuštěním následujících skriptů:

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  Povolit nla:

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [Odpojte disk operačního systému a znovu vytvořte virtuální ho dispoziční](../windows/troubleshoot-recovery-disks-portal.md)program a zkontrolujte, zda byl problém vyřešen.





