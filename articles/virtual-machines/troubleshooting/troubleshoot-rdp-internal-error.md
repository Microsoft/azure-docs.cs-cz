---
title: Vnitřní chyba nastane, pokud vytvoříte připojení RDP k Azure Virtual Machines | Dokumentace Microsoftu
description: Informace o řešení potíží s RDP s interními chybami v Microsoft Azure. | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: dd75d5a3186bbb6ba82e2deb83a7e8429e32a3f2
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134518"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Dojde k interní chybě při pokusu o připojení k virtuálnímu počítači Azure přes vzdálenou plochu

Tento článek popisuje chybu, která může dojít při pokusu o připojení k virtuálnímu počítači (VM) v Microsoft Azure.
> [!NOTE]
> Azure nabízí dva různé modely nasazení pro vytváření a práci s prostředky: [nástroj Resource Manager a klasický režim](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Resource Manageru, který vám doporučujeme používat pro nová nasazení namísto modelu nasazení classic.

## <a name="symptoms"></a>Příznaky

Nelze se připojit k virtuálnímu počítači Azure s použitím remote desktop protocol (RDP). Připojení zablokuje v části "Konfigurace vzdálené", nebo se zobrazí následující chybová zpráva:

- Vnitřní chyba protokolu RDP
- Došlo k vnitřní chybě
- Tento počítač nelze připojení ke vzdálenému počítači. Zkuste se znovu připojit. Pokud potíže potrvají, obraťte se na vlastníka vzdáleném počítači nebo správce sítě


## <a name="cause"></a>Příčina

Tomuto problému může dojít z následujících důvodů:

- Nelze získat přístup k místní šifrovací klíče RSA.
- Protokol TLS je zakázaný.
- Certifikát je poškozený nebo vypršela platnost.

## <a name="solution"></a>Řešení

Předtím, než budete postupovat podle těchto kroků, vytvořte snímek disku s operačním systémem virtuálního počítače ovlivněný jako záložní. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).

Chcete-li tento problém vyřešit, použijte konzole sériového portu nebo [opravte virtuální počítač v režimu offline](#repair-the-vm-offline) připojením disku s operačním systémem virtuálního počítače na virtuální počítač pro obnovení.


### <a name="use-serial-control"></a>Použití sériového portu ovládacího prvku

Připojte se k [sériové konzoly a otevřené instance prostředí PowerShell](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Pokud konzole sériového portu není povolená na virtuálním počítači, přejděte [opravte virtuální počítač v režimu offline](#repair-the-vm-offline) části.

#### <a name="step-1-check-the-rdp-port"></a>Krok: Kontrola 1 RDP port

1. V instanci prostředí PowerShell, použijte [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) ke kontrole, jestli port 8080 je použít v jiných aplikacích:

        Netstat -anob |more
2. Pokud Termservice.exe používá 8080 port, přejděte ke kroku 2. Pokud jiná služba nebo aplikace než Termservice.exe používá 8080 port, postupujte podle těchto kroků:

    1. Zastavte službu pro aplikaci, která používá službu 3389:

        Stop-Service - Name <ServiceName>

    2. Spuštění Terminálové služby:

        Start-Service - Name inicializace

2. Pokud aplikace nelze zastavit, nebo pokud tato metoda na vás nemusí vztahovat, změňte port pro protokol RDP:

    1. Změna portu:

        Set-ItemProperty-cesta "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Tcp Server\WinStations\RDP"-název číslo_portu – hodnota <Hexportnumber>

        Stop-Service - Name inicializace Start-Service-Name inicializace

    2. Nastavení brány firewall pro nový port:

        Set-NetFirewallRule-"Name Vzdálená plocha-přesměrovač-v-TCP" - LocalPort < nový PORT (decimální) >

    3. [Aktualizovat skupinu zabezpečení sítě pro nový port](../../virtual-network/security-overview.md) v Azure portal RDP port.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Krok 2: Nastavení správná oprávnění u certifikátu podepsaného svým držitelem protokolu RDP

1.  V instanci prostředí PowerShell spusťte následující příkazy jeden po druhém prodloužit platnost certifikátu podepsaného svým držitelem protokol RDP:

        Import-Module PKI Set-Location Cert:\LocalMachine $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Pokud tímto způsobem nejde obnovit certifikát, pokusí se vzdáleně obnovit certifikát podepsaný svým držitelem protokol RDP:

    1. Funkčním virtuálním počítači, který má připojení k virtuálnímu počítači, který dochází k potížím, typ **konzoly mmc** v **spustit** otevřete okno konzoly Microsoft Management Console.
    2. Na **souboru** nabídce vyberte možnost **Přidat/odebrat modul Snap-in**vyberte **certifikáty**a pak vyberte **přidat**.
    3. Vyberte **účty počítačů**vyberte **jiného počítače**a pak přidat IP adresu problémový virtuální počítač.
    4. Přejděte na **vzdálené Desktop\Certificates** složky, klikněte pravým tlačítkem na certifikát a poté vyberte **odstranit**.
    5. V prostředí PowerShell instanci z konzoly sériového portu restartujte službu Konfigurace vzdálené plochy:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Resetování oprávnění ke složce MachineKeys.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt Restart-Service TermService -Force

4. Restartujte virtuální počítač a opakujte spuštění připojení vzdálené plochy k virtuálnímu počítači. Pokud chyba přetrvává, přejděte k dalšímu kroku.

Krok 3: Povolení všechny podporované verze TLS

Klienta protokolu RDP používá jako výchozí protokol TLS 1.0. To však můžete změnit na protokoly TLS 1.1, který se stal novým standardem. Pokud je zakázané protokolu TLS 1.1 na virtuálním počítači, připojení se nezdaří.
1.  V instanci CMD povolte protokol TLS:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Chcete-li zabránit přepsání změny zásad AD, zastavte dočasně aktualizace zásad skupiny:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Restartujte virtuální počítač tak, aby se změny projevily. Pokud se problém vyřeší, spusťte následující příkaz pro opětovné povolení zásad skupiny:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Pokud se vrátí zpět změny, znamená to, že se zásady služby Active Directory v doméně vaší společnosti. Budete muset změnit tyto zásady z opětovnému tomuto problému zabráníte tak.

### <a name="repair-the-vm-offline"></a>Opravte virtuální počítač v režimu Offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojte disk s operačním systémem pro virtuální počítač pro obnovení

1. [Připojte disk s operačním systémem pro virtuální počítač pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Po disk s operačním systémem je připojen k virtuální počítač pro obnovení, ujistěte se, že disk je označený jako **Online** v konzole Správa disků. Poznamenejte si písmeno jednotky, která je přiřazena připojeném disku s operačním systémem.
3. Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení.

#### <a name="enable-dump-log-and-serial-console"></a>Povolení protokolu výpisu stavu systému a konzoly sériového portu

Pokud chcete povolit protokol s výpisem paměti a konzoly sériového portu, spusťte následující skript.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (**spustit jako správce**).
2. Spusťte tento skript:

    V tomto skriptu předpokládáme, že je písmeno jednotky, která je přiřazena připojeném disku s operačním systémem F. nahradit toto písmeno jednotky s odpovídající hodnotou pro váš virtuální počítač.

    ```powershell
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

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Resetování oprávnění ke složce MachineKeys

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (**spustit jako správce**).
2. Spusťte následující skript. V tomto skriptu předpokládáme, že je písmeno jednotky, která je přiřazena připojeném disku s operačním systémem F. nahradit toto písmeno jednotky s odpovídající hodnotou pro váš virtuální počítač.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Povolit všechny podporované verze TLS

1.  Otevřete relaci příkazového řádku se zvýšenými oprávněními (**spustit jako správce**) a spustit následující příkazy. Následující skript předpokládá, že je přiřazeno písmeno ovladač připojeném disku s operačním systémem je F. nahradit toto písmeno jednotky s odpovídající hodnotou pro váš virtuální počítač.
2.  Kontrola, který je povolený protokol TLS:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Pokud klíč neexistuje, nebo je jeho hodnota **0**, povolte protokol spuštěním následujících skriptů:

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  Povolte NLA:

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [Odpojit disk s operačním systémem a znovu vytvořte virtuální počítač](../windows/troubleshoot-recovery-disks-portal.md)a potom zkontrolujte, zda byl problém vyřešen.





