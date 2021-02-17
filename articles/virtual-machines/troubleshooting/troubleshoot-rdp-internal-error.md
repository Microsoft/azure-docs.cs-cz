---
title: Při navázání připojení RDP k Azure Virtual Machines dojde k vnitřní chybě | Microsoft Docs
description: Přečtěte si, jak řešit potíže s interními chybami protokolu RDP v Microsoft Azure. | Microsoft Docs
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
ms.openlocfilehash: 5c8bd335832a950385f88f13dc31eb7f6159f831
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548127"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Při pokusu o připojení k virtuálnímu počítači Azure přes Vzdálenou plochu dojde k vnitřní chybě

Tento článek popisuje chybu, ke které může dojít při pokusu o připojení k virtuálnímu počítači v Microsoft Azure.


## <a name="symptoms"></a>Příznaky

K virtuálnímu počítači Azure se nemůžete připojit pomocí protokolu RDP (Remote Desktop Protocol). Připojení se zablokuje v části **Konfigurace vzdáleného** nebo se zobrazí tato chybová zpráva:

- Vnitřní chyba protokolu RDP
- Došlo k vnitřní chybě.
- Tento počítač nemůže být připojen ke vzdálenému počítači. Zkuste se znovu připojit. Pokud se problém opakuje, obraťte se na vlastníka vzdáleného počítače nebo správce vaší sítě.


## <a name="cause"></a>Příčina

K tomuto problému může dojít z následujících důvodů:

- Je možné, že virtuální počítač je napadený.
- K místním šifrovacím klíčům RSA nelze přistup.
- Protokol TLS je zakázán.
- Certifikát je poškozený nebo vypršel jeho platnost.

## <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, postupujte podle kroků v následujících částech. Než začnete, pořídit snímek disku s operačním systémem ovlivněného virtuálního počítače jako záložního. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

### <a name="check-rdp-security"></a>Ověřit zabezpečení RDP

Nejprve zkontrolujte, zda není skupina zabezpečení sítě pro port RDP 3389 zabezpečena (otevřít). Pokud není zabezpečený a zobrazuje se \* jako zdrojová IP adresa pro příchozí, omezte port RDP na IP adresu uživatele specifc a pak otestujte přístup RDP. Pokud se to nepovede, proveďte kroky v další části.

### <a name="use-serial-control"></a>Použití ovládacího prvku sériového portu

Použijte sériovou konzolu nebo [opravte virtuální počítač offline](#repair-the-vm-offline) připojením disku operačního systému virtuálního počítače k virtuálnímu počítači pro obnovení.

Začněte tím, že se připojíte ke [konzole sériového prostředí a otevřete instanci PowerShellu](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Pokud není na vašem VIRTUÁLNÍm počítači povolená konzola sériového prostředí, přečtěte si část [opravy offline virtuálního počítače](#repair-the-vm-offline) .

#### <a name="step-1-check-the-rdp-port"></a>Krok: 1 zkontrolování portu RDP

1. V instanci prostředí PowerShell ověřte pomocí příkazu [netstat](/windows-server/administration/windows-commands/netstat) , zda je port 3389 používán jinými aplikacemi:

    ```powershell
    Netstat -anob |more
    ```

2. Pokud Termservice.exe používá port 3389, pokračujte na krok 2. Pokud je na portu 3389 jiná služba nebo jiná aplikace než Termservice.exe, použijte následující postup:

    1. Zastavte službu pro aplikaci, která používá službu 3389:

        ```powershell
        Stop-Service -Name <ServiceName> -Force
        ```

    2. Spusťte Terminálovou službu:

        ```powershell
        Start-Service -Name Termservice
        ```

2. Pokud se aplikace nedá zastavit nebo pokud se vám tato metoda nevztahuje, změňte port pro RDP:

    1. Změňte port:

        ```powershell
        Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

        Stop-Service -Name Termservice -Force

        Start-Service -Name Termservice
        ```

    2. Nastavte bránu firewall pro nový port:

        ```powershell
        Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>
        ```

    3. [Aktualizujte skupinu zabezpečení sítě pro nový port](../../virtual-network/network-security-groups-overview.md) v portu Azure Portal RDP.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Krok 2: nastavení správných oprávnění pro certifikát podepsaný svým držitelem protokolu RDP

1. V instanci prostředí PowerShell spusťte následující příkazy jeden po jednom pro obnovení certifikátu podepsaného svým držitelem protokolu RDP:

    ```powershell
    Import-Module PKI

    Set-Location Cert:\LocalMachine 

    $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 

    Remove-Item -Path $RdpCertThumbprint

    Stop-Service -Name "SessionEnv"

    Start-Service -Name "SessionEnv"
    ```

2. Pokud nemůžete obnovit certifikát pomocí této metody, pokuste se vzdáleně obnovit certifikát podepsaný svým držitelem protokolu RDP:

    1. V pracovním počítači s připojením k virtuálnímu počítači, na kterém dochází k potížím, zadejte do pole **Spustit** **MMC** a otevřete konzolu Microsoft Management Console.
    2. V nabídce **soubor** vyberte **Přidat nebo odebrat modul snap-in**, vyberte **certifikáty** a pak vyberte **Přidat**.
    3. Vyberte **účty počítačů**, vyberte **jiný počítač** a pak přidejte IP adresu virtuálního počítače problému.
    4. Přejděte do složky **Remote Desktop\Certificates** , klikněte pravým tlačítkem na certifikát a pak vyberte **Odstranit**.
    5. V instanci prostředí PowerShell z konzoly sériového portu restartujte službu Vzdálená plocha konfigurace:

        ```powershell
        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"
        ```

3. Resetujte oprávnění pro složku MachineKeys.

    ```powershell
    remove-module psreadline 

    md c:\temp

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 

    takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 

    Restart-Service TermService -Force
    ```

4. Restartujte virtuální počítač a pak zkuste spustit připojení ke vzdálené ploše virtuálního počítače. Pokud k chybě stále dochází, pokračujte na další krok.

#### <a name="step-3-enable-all-supported-tls-versions"></a>Krok 3: povolení všech podporovaných verzí TLS

Klient RDP používá jako výchozí protokol TLS 1,0. To se ale dá změnit na TLS 1,1, který se stane novým standardem. Pokud je na virtuálním počítači zakázaný protokol TLS 1,1, připojení se nezdaří.

1. V instanci CMD Povolte protokol TLS:

    ```console
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
    ```

2. Chcete-li zabránit tomu, aby zásady služby AD přepsaly změny, zastavte aktualizaci zásad skupiny dočasně:

    ```console
    REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
    ```

3. Restartujte virtuální počítač, aby se změny projevily. Pokud se problém vyřeší, spusťte následující příkaz, kterým znovu povolíte zásady skupiny:

    ```console
    sc config gpsvc start= auto sc start gpsvc

    gpupdate /force
    ```

    Pokud se změna vrátí, znamená to, že je ve vaší doméně společnosti zásada služby Active Directory. Abyste se vyhnuli opětovnému výskytu tohoto problému, je nutné tuto zásadu změnit.

### <a name="repair-the-vm-offline"></a>Oprava virtuálního počítače v režimu offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojení disku s operačním systémem k virtuálnímu počítači pro obnovení

1. [Připojte disk s operačním systémem k virtuálnímu počítači pro obnovení](./troubleshoot-recovery-disks-portal-windows.md).
2. Po připojení disku s operačním systémem k virtuálnímu počítači pro obnovení se ujistěte, že je disk označený jako **online** v konzole pro správu disků. Poznamenejte si písmeno jednotky přiřazené k připojenému disku s operačním systémem.
3. Spusťte připojení ke vzdálené ploše virtuálního počítače pro obnovení.

#### <a name="enable-dump-log-and-serial-console"></a>Povolit protokol výpisu paměti a sériová konzola

Pokud chcete povolit protokol výpisu a sériovou konzolu, spusťte následující skript.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (**Spustit jako správce**).
2. Spusťte tento skript:

    V tomto skriptu předpokládáme, že písmeno jednotky přiřazené k připojenému disku s operačním systémem je F. nahraďte toto písmeno jednotky odpovídající hodnotou pro váš virtuální počítač.

    ```console
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

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Resetovat oprávnění pro složku MachineKeys

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (**Spustit jako správce**).
2. Spusťte následující skript. V tomto skriptu předpokládáme, že písmeno jednotky přiřazené k připojenému disku s operačním systémem je F. nahraďte toto písmeno jednotky odpovídající hodnotou pro váš virtuální počítač.

    ```console
    Md F:\temp

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt

    takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
    ```

#### <a name="enable-all-supported-tls-versions"></a>Povolit všechny podporované verze TLS

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (**Spustit jako správce**) a spusťte následující příkazy. Následující skript předpokládá, že je přiřazeno písmeno ovladače k připojenému disku s operačním systémem F. nahraďte toto písmeno jednotky odpovídající hodnotou pro váš virtuální počítač.
2. Ověřte, který protokol TLS je povolený:

    ```console
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO
    ```

3. Pokud klíč neexistuje nebo je jeho hodnota **0**, povolte protokol spuštěním následujících skriptů:

    ```console
    REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
    ```

4. Povolit NLA:

    ```console
    REM Enable NLA

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
    ```

5. [Odpojte disk s operačním systémem a znovu vytvořte virtuální počítač](./troubleshoot-recovery-disks-portal-windows.md)a potom zkontrolujte, jestli je problém vyřešený.
