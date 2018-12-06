---
title: Řešení problémů s připojením RDP virtuálního počítače Azure podle ID události | Dokumentace Microsoftu
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 4c783c70217a84bbe5ccf15accc4a2bec0b7cca8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959678"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Řešení potíží s připojením RDP virtuálních počítačů Azure podle ID události 

Tento článek vysvětluje, jak použít ID událostí k řešení potíží, které brání v připojení ke vzdálené ploše protocol (RDP) do virtuálního počítače (virtuální počítač Azure).

## <a name="symptoms"></a>Příznaky

Pokusíte použít relaci vzdálené plochy (RDP) protokol pro připojení k virtuálnímu počítači Azure. Až budete zadávat svoje přihlašovací údaje, připojení se nezdaří a zobrazí následující chybová zpráva:

**Tento počítač se nemůže připojit ke vzdálenému počítači. Zkuste se připojit znovu, pokud se problém opakuje, obraťte se na vlastníka vzdáleném počítači nebo správce sítě.**

Chcete-li tento problém vyřešit, zkontrolujte protokoly událostí na virtuální počítač a přečtěte si informace v následujících případech.

## <a name="before-you-troubleshoot"></a>Předtím, než je řešit

### <a name="create-a-backup-snapshot"></a>Vytvoření snímku zálohy

Pokud chcete vytvořit snímek zálohy, postupujte podle kroků v [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Vzdálené připojení k virtuálnímu počítači

Pro vzdálené připojení k virtuálnímu počítači, použijte jednu z metod v [použití nástrojů remote tools pro řešení potíží s virtuálním počítači Azure](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Scénář 1

### <a name="event-logs"></a>Protokoly událostí

V instanci CMD spusťte následující příkazy ke kontrole, jestli událost 1058 nebo 1057 se zaznamenají do protokolu systému za posledních 24 hodin:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Název protokolu:** systému <br />
**Zdroj:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:***čas* <br />
**ID události:** 1058 <br />
**Kategorie úkolu:** None <br />
**Úroveň:** chyba <br />
**Klíčová slova:** Classic <br />
**Uživatel:** není k dispozici <br />
**Počítač:***počítače* <br />
**Popis:** Server hostitele relace VP se nepodařilo nahradit neplatné vlastní podepsané certifikátu používaného pro ověřování serveru hostitele relace VP na připojení SSL. Příslušný kód stavu byl odepření přístupu.

**Název protokolu:** systému <br />
**Zdroj:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:***čas* <br />
**ID události:** 1058 <br />
**Kategorie úkolu:** None <br />
**Úroveň:** chyba <br />
**Klíčová slova:** Classic <br />
**Uživatel:** není k dispozici <br />
**Počítač:***počítače* <br />
**Popis:** server hostitele relace VP se nepodařilo vytvořit nový certifikát podepsaný svým držitelem pro ověřování serveru hostitele relace VP na připojení SSL, příslušný kód stavu byl objekt již existuje.

**Název protokolu:** systému <br />
**Zdroj:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:***čas* <br />
**ID události:** 1057 <br />
**Kategorie úkolu:** None <br />
**Úroveň:** chyba <br />
**Klíčová slova:** Classic <br />
**Uživatel:** není k dispozici <br />
**Počítač:***počítače* <br />
**Popis:** Server hostitele relace VP se nepodařilo vytvořit novou vlastní podepsaný certifikát, který se použije pro ověřování serveru hostitele relace VP na připojení SSL. Příslušný kód stavu byl kurzory neexistuje.

Můžete také vyhledat SCHANNEL chybové události 36872 a 36870 spuštěním následujících příkazů:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Název protokolu:** systému <br />
**Zdroj:** Schannel <br />
**Datum:** – <br />
**ID události:** 36870 <br />
**Kategorie úkolu:** None <br />
**Úroveň:** chyba <br />
**Klíčová slova:**       <br />
**Uživatel:** systému <br />
**Počítač:***počítače* <br />
**Popis:** při pokusu přistoupit k soukromému klíči pověření SSL serveru došlo k závažné chybě. Kód chyby vrácený kryptografickým modulem je 0x8009030D.  <br />
Stav vnitřní chyba je 10001.

### <a name="cause"></a>Příčina
K tomuto problému dochází, protože nelze získat přístup k místní šifrovací klíče RSA ve složce MachineKeys na virtuálním počítači. Tomuto problému může dojít z některého z následujících důvodů:

1. Konfigurace nesprávné oprávnění ve složce Machinekeys nebo soubory RSA.

2. Poškozené nebo chybí klíče RSA.

### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, budete muset nastavit správná oprávnění u certifikátu RDP pomocí těchto kroků.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Udělit oprávnění ke složce MachineKeys

1. Vytvoření skriptu můžete použít následující obsah:

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  Spusťte tento skript resetování oprávnění MachineKey složky a soubory RSA resetovat na výchozí hodnoty.

3.  Došlo k pokusu o přístup k virtuálnímu počítači znovu.

Po spuštění skriptu, můžete zkontrolovat následující soubory, které dochází k problémům s oprávněními:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Prodloužit platnost certifikátu podepsaného svým držitelem protokolu RDP

Pokud se problém nevyřeší, spusťte následující skript, abyste měli jistotu, že se obnovuje certifikát podepsaný svým držitelem protokol RDP:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Pokud chcete certifikát nelze obnovit, zkuste odstranit certifikát pomocí těchto kroků:

1. Na jiného virtuálního počítače ve stejné virtuální síti, otevřete **spustit** zadejte **konzoly mmc**a potom stiskněte klávesu **OK**. 

2. Na **souboru** nabídce vyberte možnost **Přidat/odebrat modul Snap-in**.

3. V **modul snap in k dispozici** seznamu vyberte **certifikáty**a pak vyberte **přidat**.

4. Vyberte **účet počítače**a pak vyberte **Další**.

5. Vyberte **jiného počítače**a pak přidat IP adresu virtuálního počítače, který má problémy.
   >[!Note]
   >Zkuste použít interní síti, aby pomocí virtuální IP adresy.

6. Vyberte **Dokončit**a pak vyberte **OK**.

   ![Vyberte počítač](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Rozbalte certifikáty, přejděte do složky vzdálené Desktop\Certificates, klikněte pravým tlačítkem na certifikát a potom vyberte **odstranit**.

8. Restartujte službu Konfigurace vzdálené plochy:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >V tomto okamžiku Pokud aktualizujete úložiště z konzoly mmc, zobrazí se certifikát. 

Došlo k pokusu o přístup k virtuálnímu počítači pomocí RDP znovu.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Aktualizace certifikátu vrstvy SSL (Secure Sockets)

Pokud nastavíte virtuální počítač pro použití certifikátu SSL, spuštěním následujícího příkazu získejte kryptografický otisk. Zkontrolujte, jestli je stejný jako kryptografický otisk certifikátu:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Pokud tomu tak není, změňte jeho kryptografický otisk:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Také můžete zkusit odstranit klíč tak, aby protokol RDP používá certifikát podepsaný svým držitelem pro protokol RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Scénář 2

### <a name="event-log"></a>Protokol událostí

V instanci CMD spusťte následující příkazy ke kontrole, jestli se událost chyby zprostředkovatele SCHANNEL 36871 protokoluje v systémovém protokolu během posledních 24 hodin:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Název protokolu:** systému <br />
**Zdroj:** Schannel <br />
**Datum:** – <br />
**ID události:** 36871 <br />
**Kategorie úkolu:** None <br />
**Úroveň:** chyba <br />
**Klíčová slova:**       <br />
**Uživatel:** systému <br />
**Počítač:***počítače* <br />
**Popis:** při vytváření pověření serveru TLS došlo k závažné chybě. Stav vnitřní chyba je 10013.
 
### <a name="cause"></a>Příčina

Tento problém je způsoben zásady zabezpečení. Když jsou zakázány starší verze protokolu TLS (třeba 1.0), selže přístup protokolu RDP.

### <a name="resolution"></a>Řešení

Protokol RDP používá jako výchozí protokol TLS 1.0. Ale protokol může být změněn na protokoly TLS 1.1, což je novým standardem.

Chcete-li tento problém vyřešit, najdete v článku [řešení chyb ověřování při připojení k virtuálnímu počítači Azure pomocí RDP](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Scénář 3

Pokud jste nainstalovali **Zprostředkovatel připojení ke vzdálené ploše** role na virtuálním počítači, zkontrolujte, jestli je událost 2056 nebo 1296 během posledních 24 hodin. V instanci CMD spusťte následující příkazy: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Název protokolu:** Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Zdroj:** Microsoft-Windows-TerminalServices-SessionBroker <br />
**Datum:***čas* <br />
**ID události:** 2056 <br />
**Kategorie úkolu:** (109) <br />
**Úroveň:** chyba <br />
**Klíčová slova:**       <br />
**Uživatel:** síťové služby <br />
**Počítač:***plně kvalifikovaný název domény počítače* <br />
**Popis:** popis 2056 ID události ze zdroje Microsoft-Windows-TerminalServices-SessionBroker nebyl nalezen. Součást, která vyvolá tuto událost není nainstalována na místním počítači nebo že je poškozená instalace. Můžete nainstalovat nebo opravit součásti v místním počítači. <br />
Pokud událost pochází z jiného počítače, informace o zobrazení museli uložit s událostí. <br />
Tyto informace je obsažena v události: <br />
NULL <br />
NULL <br />
Přihlášení k databázi se nezdařilo.

**Název protokolu:** Microsoft-Windows-TerminalServices-SessionBroker – klient/Operational <br />
**Zdroj:** Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Datum:***čas* <br />
**ID události:** 1296 <br />
**Kategorie úkolu:** (104) <br />
**Úroveň:** chyba <br />
**Klíčová slova:**       <br />
**Uživatel:** síťové služby <br />
**Počítač:***plně kvalifikovaný název domény počítače* <br />
**Popis:** popis 1296 ID události ze zdroje Microsoft-Windows-TerminalServices-SessionBroker – klient nebyl nalezen. Součást, která vyvolá tuto událost není nainstalována na místním počítači nebo že je poškozená instalace. Můžete nainstalovat nebo opravit součásti v místním počítači.
Pokud událost pochází z jiného počítače, informace o zobrazení museli uložit s událostí.
Tyto informace je obsažena v události:  <br />
*Text* <br />
*Text* <br />
Zprostředkovatel připojení ke vzdálené ploše není připraven na komunikaci RPC.

### <a name="cause"></a>Příčina

K tomuto problému dochází, protože se změní název hostitele serveru zprostředkovatele připojení k vzdálené ploše, což není podporované změny. 

Název hostitele má položky a závislosti v interní databázi Windows, která je vyžadována farmou služby vzdálené plochy, aby bylo možné pracovat. Změnou názvu hostitele, jakmile již vytvořen farmy způsobí mnoho chyb a může způsobit, že server Service broker přestane fungovat.

### <a name="resolution"></a>Řešení 

Chcete-li vyřešit tento problém, musíte přeinstalovat role Zprostředkovatel připojení ke vzdálené ploše a interní databáze Windows.

## <a name="next-steps"></a>Další kroky

[Události zprostředkovatele Schannel](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Technický přehled zprostředkovatele zabezpečení Schannel SSP](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[Protokol RDP se nezdaří s ID události 1058 & události 36870 s certifikát hostitele relace vzdálené plochy a komunikace SSL](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Schannel 36872 nebo zprostředkovatele Schannel 36870 na řadiči domény](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[ID události 1058 – Služba Vzdálená plocha ověřování a šifrování](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

