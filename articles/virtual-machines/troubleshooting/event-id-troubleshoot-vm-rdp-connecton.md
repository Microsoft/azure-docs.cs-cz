---
title: Poradce při potížích s připojením RDP virtuálního počítače Azure podle ID události | Dokumenty společnosti Microsoft
description: Pomocí ID událostí můžete řešit různé problémy, které brání připojení protokolu RDP (RdP) k virtuálnímu počítači (VM) azure.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 2073d5f91b26cd2ae53e3291a6d1dad4d711b66d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437059"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Řešení potíží s připojením RDP virtuálních počítačů Azure podle ID události 

Tento článek vysvětluje, jak pomocí ID událostí řešit problémy, které brání připojení protokolu rdp (RdP) k virtuálnímu počítači (VM) Azure.

## <a name="symptoms"></a>Příznaky

Pokusíte se použít relaci protokolu vzdálené plochy (RDP) pro připojení k virtuálnímu počítači Azure. Po zadání pověření se připojení nezdaří a zobrazí se následující chybová zpráva:

**Tento počítač se nemůže připojit ke vzdálenému počítači. Pokud problém přetrvává, obraťte se na vlastníka vzdáleného počítače nebo správce sítě.**

Chcete-li tento problém vyřešit, zkontrolujte protokoly událostí na virtuálním počítači a pak se podívejte na následující scénáře.

## <a name="before-you-troubleshoot"></a>Před odstraňováním potíží

### <a name="create-a-backup-snapshot"></a>Vytvoření záložního snímku

Chcete-li vytvořit snímek zálohy, postupujte podle kroků v [snímek disku](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Vzdálené připojení k virtuálnímu virtuálnímu připojení

Chcete-li se vzdáleně připojit k virtuálnímu počítači, použijte jednu z metod v [tématu Použití vzdálených nástrojů k řešení problémů s virtuálním počítačem Azure](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Scénář 1

### <a name="event-logs"></a>Protokoly událostí

V instanci CMD spusťte následující příkazy a zkontrolujte, zda je událost 1058 nebo událost 1057 zaznamenána do systémového protokolu během posledních 24 hodin:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Název protokolu:**      Systému <br />
**Zdroj:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *čas* <br />
**ID události:** 1058 <br />
**Kategorie úkolu:** Žádný <br />
**Úroveň:**         Chyba <br />
**Klíčová slova:**      Klasické <br />
**Uživatel:**          N/a <br />
**Počítač:**      *počítač* <br />
**Popis:** Hostitelský server relací VP nenahradil certifikát podepsaný vlastními podpisy, jehož platnost vypršela a který byl použit pro ověřování hostitelského serveru relací VP u připojení TLS. Příslušný stavový kód byl Přístup byl odepřen.

**Název protokolu:**      Systému <br />
**Zdroj:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *čas* <br />
**ID události:** 1058 <br />
**Kategorie úkolu:** Žádný <br />
**Úroveň:**         Chyba <br />
**Klíčová slova:**      Klasické <br />
**Uživatel:**          N/a <br />
**Počítač:**      *počítač* <br />
**Popis:** Hostitelskému serveru relací VP se nepodařilo vytvořit nový certifikát podepsaný svým držitelem, který bude použit pro ověřování hostitelského serveru relací VP u připojení TLS, příslušný stavový kód již existuje.

**Název protokolu:**      Systému <br />
**Zdroj:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *čas* <br />
**ID události:** 1057 <br />
**Kategorie úkolu:** Žádný <br />
**Úroveň:**         Chyba <br />
**Klíčová slova:**      Klasické <br />
**Uživatel:**          N/a <br />
**Počítač:**      *počítač* <br />
**Popis:** Hostitelskému serveru relací VP se nepodařilo vytvořit nový certifikát podepsaný vlastním podpisem, který bude použit pro ověřování hostitelského serveru relací VP u připojení TLS. Příslušný stavový kód byl Keyset neexistuje

Můžete také zkontrolovat chybové události SCHANNEL 36872 a 36870 spuštěním následujících příkazů:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Název protokolu:**      Systému <br />
**Zdroj:**        Schannel <br />
**Datum:** — <br />
**ID události:** 36870 <br />
**Kategorie úkolu:** Žádný <br />
**Úroveň:**         Chyba <br />
**Klíčová slova:**       <br />
**Uživatel:**          Systému <br />
**Počítač:**      *počítač* <br />
**Popis:** Při pokusu o přístup k soukromému klíči pověření serveru TLS došlo k závažné chybě. Kód chyby vrácený z kryptografického modulu je 0x8009030D.  <br />
Stav vnitřní chyby je 10001.

### <a name="cause"></a>Příčina
K tomuto problému dochází, protože místní rsa šifrovací klíče ve složce MachineKeys na virtuálním počítači nelze přistupovat. K tomuto problému může dojít z jednoho z následujících důvodů:

1. Nesprávná konfigurace oprávnění ve složce Machinekeys nebo v souborech RSA.

2. Poškozený nebo chybějící klíč RSA.

### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, je třeba nastavit správná oprávnění k certifikátu RDP pomocí těchto kroků.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Udělení oprávnění složce MachineKeys

1. Vytvořte skript pomocí následujícího obsahu:

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

2.  Spusťte tento skript, chcete-li obnovit oprávnění složky MachineKey a obnovit soubory RSA na výchozí hodnoty.

3.  Zkuste znovu získat přístup k virtuálnímu virtuálnímu mněmu.

Po spuštění skriptu můžete zkontrolovat následující soubory, u kterých dochází k problémům s oprávněními:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Obnovit certifikát podepsaný pod svým držitelem RDP

Pokud problém přetrvává, spusťte následující skript a ujistěte se, že je obnoven certifikát podepsaný pod svým držitelem RDP:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Pokud certifikát nemůžete obnovit, pokuste se certifikát odstranit následujícím postupem:

1. Na jiném virtuálním počítači ve stejné virtuální síti otevřete pole **Spustit,** zadejte **mmc**a stiskněte **OK**. 

2. V nabídce **Soubor** vyberte **Přidat nebo odebrat modul snap-in**.

3. V seznamu **Dostupné moduly snap-in** vyberte **Certifikáty**a pak vyberte **Přidat**.

4. Vyberte **účet Počítače**a pak vyberte **Další**.

5. Vyberte **jiný počítač**a přidejte IP adresu virtuálního počítače, který má problémy.
   >[!Note]
   >Zkuste použít interní síť, abyste se vyhnuli použití virtuální IP adresy.

6. Vyberte **Dokončit**a pak vyberte **OK**.

   ![Vybrat počítač](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Rozbalte certifikáty, přejděte do složky Vzdálená plocha\Certifikáty, klikněte pravým tlačítkem myši na certifikát a vyberte **příkaz Odstranit**.

8. Restartujte službu Konfigurace vzdálené plochy:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >V tomto okamžiku, pokud aktualizujete úložiště z mmc, certifikát se znovu zobrazí. 

Zkuste znovu získat přístup k virtuálnímu virtuálnímu virtuálnímu bodu pomocí funkce RDP.

#### <a name="update-tlsssl-certificate"></a>Aktualizace certifikátu TLS/SSL

Pokud nastavíte virtuální ho virtuálního virtuálního provozu používat certifikát TLS/SSL, spusťte následující příkaz pro získání kryptografického otisku. Pak zkontrolujte, zda je stejný jako kryptografický otisk certifikátu:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Pokud tomu tak není, změňte otisk palce:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Můžete se také pokusit odstranit klíč tak, aby rdp používá certifikát podepsaný svým držitelem pro RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Scénář 2

### <a name="event-log"></a>Protokol událostí

V instanci CMD spusťte následující příkazy a zkontrolujte, zda je událost chyby SCHANNEL 36871 zaznamenána do systémového protokolu během posledních 24 hodin:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Název protokolu:**      Systému <br />
**Zdroj:**        Schannel <br />
**Datum:** — <br />
**ID události:** 36871 <br />
**Kategorie úkolu:** Žádný <br />
**Úroveň:**         Chyba <br />
**Klíčová slova:**       <br />
**Uživatel:**          Systému <br />
**Počítač:**      *počítač* <br />
**Popis:** Při vytváření pověření serveru TLS došlo k závažné chybě. Stav vnitřní chyby je 10013.
 
### <a name="cause"></a>Příčina

Tento problém je způsoben zásadami zabezpečení. Pokud jsou zakázány starší verze tls (například 1.0), přístup k přístupu k přístupu k přístupu k verzi RDP se nezdaří.

### <a name="resolution"></a>Řešení

Protokol RDP používá jako výchozí protokol protokol TLS 1.0. Protokol však může být změněn na TLS 1.1, což je nový standard.

Informace o řešení tohoto problému [najdete v tématu Poradce při řešení chyb ověřování při připojení k virtuálnímu počítači Azure pomocí protokolu RDP.](troubleshoot-authentication-error-rdp-vm.md#tls-version)

## <a name="scenario-3"></a>Scénář 3

Pokud jste nainstalovali roli **Zprostředkovatel připojení ke vzdálené ploše** na virtuálním počítači, zkontrolujte, zda je událost 2056 nebo událost 1296 za posledních 24 hodin. V instanci CMD spusťte následující příkazy: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Název protokolu:**      Microsoft-Windows-TerminalServices-SessionBroker/Provozní <br />
**Zdroj:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Datum:**          *čas* <br />
**ID akce:** 2056 <br />
**Kategorie úkolů:** (109) <br />
**Úroveň:**         Chyba <br />
**Klíčová slova:**       <br />
**Uživatel:**          SÍŤOVÁ SLUŽBA <br />
**Počítač:**      *počítač fqdn* <br />
**Popis:** Popis ID události 2056 ze zdroje Microsoft-Windows-TerminalServices-SessionBroker nebyl nalezen. Součást, která vyvolává tuto událost, není nainstalována v místním počítači nebo je instalace poškozena. Součást můžete nainstalovat nebo opravit v místním počítači. <br />
Pokud událost vznikla v jiném počítači, musely být informace o zobrazení uloženy spolu s událostí. <br />
K události byly zahrnuty následující informace: <br />
NULL <br />
NULL <br />
Přihlášení k databázi se nezdařilo.

**Název protokolu:**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**Zdroj:**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Datum:**          *čas* <br />
**ID události:** 1296 <br />
**Kategorie úkolů:** (104) <br />
**Úroveň:**         Chyba <br />
**Klíčová slova:**       <br />
**Uživatel:**          SÍŤOVÁ SLUŽBA <br />
**Počítač:**      *počítač fqdn* <br />
**Popis:** Popis ID události 1296 ze zdroje Microsoft-Windows-TerminalServices-SessionBroker-Client nebyl nalezen. Součást, která vyvolává tuto událost, není nainstalována v místním počítači nebo je instalace poškozena. Součást můžete nainstalovat nebo opravit v místním počítači.
Pokud událost vznikla v jiném počítači, musely být informace o zobrazení uloženy spolu s událostí.
K události byly zahrnuty následující informace:  <br />
*Text* <br />
*Text* <br />
Služba Zprostředkovatel připojení ke vzdálené ploše není připraven pro komunikaci vzdáleného volání procedur.

### <a name="cause"></a>Příčina

K tomuto problému dochází, protože název hostitele serveru služby Zprostředkovatel připojení ke vzdálené ploše je změněn, což není podporovaná změna. 

Název hostitele obsahuje položky a závislosti na interní databázi systému Windows, kterou vyžaduje farma služby Vzdálená plocha, aby bylo možné pracovat. Změna názvu hostitele po farmě je již vytvořena způsobí mnoho chyb a může způsobit makléřského serveru přestat pracovat.

### <a name="resolution"></a>Řešení 

Chcete-li tento problém vyřešit, role Zprostředkovatel připojení ke vzdálené ploše a interní databáze systému Windows je nutné přeinstalovat.

## <a name="next-steps"></a>Další kroky

[Události Kanálu](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Zprostředkovatel zabezpečení Schannel – technický přehled](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[Rdp se nezdaří s ID události 1058 & událost 36870 s certifikátem hostitele relací vzdálené plochy & ssl komunikace](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Schannel 36872 nebo Schannel 36870 na řadiči domény](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[ID události 1058 – ověřování a šifrování služby Vzdálená plocha](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

