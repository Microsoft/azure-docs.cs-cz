---
title: Řešení potíží s připojením ke službě RDP pro virtuální počítače Azure podle ID události | Microsoft Docs
description: Pomocí ID událostí můžete řešit různé problémy, které zabraňují připojení protokolu RDP (Remote Desktop Protocol) k virtuálnímu počítači Azure (VM).
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
ms.openlocfilehash: 507cd6cfe9f251dbc304b579d634ff986b001264
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088593"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Řešení potíží s připojením RDP virtuálních počítačů Azure podle ID události 

Tento článek vysvětluje, jak pomocí ID událostí řešit problémy, které brání připojení protokolu RDP (Remote Desktop Protocol) k virtuálnímu počítači Azure (VM).

## <a name="symptoms"></a>Příznaky

Zkusíte použít relaci protokolu RDP (Remote Desktop Protocol) pro připojení k virtuálnímu počítači Azure. Po zadání přihlašovacích údajů dojde k chybě připojení a zobrazí se následující chybová zpráva:

**Tento počítač se nemůže připojit ke vzdálenému počítači. Zkuste se znovu připojit. Pokud potíže potrvají, obraťte se na vlastníka vzdáleného počítače nebo správce vaší sítě.**

Pokud chcete tento problém vyřešit, zkontrolujte protokoly událostí na virtuálním počítači a podívejte se na následující scénáře.

## <a name="before-you-troubleshoot"></a>Před odstraňováním potíží

### <a name="create-a-backup-snapshot"></a>Vytvoření snímku zálohy

Chcete-li vytvořit záložní snímek, postupujte podle kroků v části [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Vzdálené připojení k virtuálnímu počítači

Pokud se chcete vzdáleně připojit k virtuálnímu počítači, použijte jednu z metod, [jak pomocí nástrojů Remote Tools řešit problémy s virtuálními počítači Azure](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Scénář 1

### <a name="event-logs"></a>Protokoly událostí

V instanci CMD spusťte následující příkazy, abyste zkontrolovali, jestli se do systémového protokolu v posledních 24 hodinách protokoluje událost 1058 nebo událost 1057:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Název protokolu:**      Souborů <br />
**Zdroj:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *čas* <br />
**ID události:**      1058 <br />
**Kategorie úlohy:** NTato <br />
**Úroveň:**         Chyba <br />
**Klíčová slova:**      Standardním <br />
**Uživatel:**          NENÍ K DISPOZICI <br />
**Počítač:**      *počítač* <br />
**Popis:** Hostitel relace VPmu serveru se nepovedlo nahradit certifikát podepsaný svým podpisem, který se používá pro Hostitel relace VP ověřování serveru v připojeních TLS. K příslušnému kódu stavu byl přístup odepřen.

**Název protokolu:**      Souborů <br />
**Zdroj:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *čas* <br />
**ID události:**      1058 <br />
**Kategorie úlohy:** NTato <br />
**Úroveň:**         Chyba <br />
**Klíčová slova:**      Standardním <br />
**Uživatel:**          NENÍ K DISPOZICI <br />
**Počítač:**      *počítač* <br />
**Popis:** Serveru hostitele relace VP se nepodařilo vytvořit nový certifikát podepsaný svým držitelem, který se má použít pro ověřování serveru hostitele relace VP v připojeních TLS. příslušný stavový kód již objekt existuje.

**Název protokolu:**      Souborů <br />
**Zdroj:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *čas* <br />
**ID události:**      1057 <br />
**Kategorie úlohy:** NTato <br />
**Úroveň:**         Chyba <br />
**Klíčová slova:**      Standardním <br />
**Uživatel:**          NENÍ K DISPOZICI <br />
**Počítač:**      *počítač* <br />
**Popis:** Serveru Hostitel relace VP se nepodařilo vytvořit nový certifikát podepsaný svým jménem, který se použije k ověřování Hostitel relace VP serveru v připojeních TLS. Příslušný stavový kód byl sada klíčů neexistuje.

Můžete také vyhledat chyby zprostředkovatele SCHANNEL 36872 a 36870 spuštěním následujících příkazů:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Název protokolu:**      Souborů <br />
**Zdroj:**        Schannel <br />
**Datum:**          – <br />
**ID události:**      36870 <br />
**Kategorie úlohy:** NTato <br />
**Úroveň:**         Chyba <br />
**Klíčov**       <br />
**Uživatel:**          SOUBORŮ <br />
**Počítač:**      *počítač* <br />
**Popis:** Při pokusu o přístup k privátnímu klíči přihlašovacích údajů serveru TLS došlo k závažné chybě. Kód chyby vrácený kryptografickým modulem je 0x8009030D.  <br />
Stav interní chyby je 10001.

### <a name="cause"></a>Příčina
K tomuto problému dochází, protože k místním šifrovacím klíčům RSA ve složce MachineKeys na virtuálním počítači nelze přistup. K tomuto problému může dojít z některého z následujících důvodů:

1. Nesprávná konfigurace oprávnění ve složce MachineKeys nebo v souborech RSA.

2. Klíč RSA je poškozený nebo chybí.

### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, musíte nastavit správná oprávnění pro certifikát RDP pomocí těchto kroků.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Udělení oprávnění ke složce MachineKeys

1. Pomocí následujícího obsahu vytvořte skript:

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

2.  Spusťte tento skript pro resetování oprávnění ke složce MachineKey a k resetování souborů RSA na výchozí hodnoty.

3.  Zkuste znovu získat přístup k virtuálnímu počítači.

Po spuštění skriptu můžete vyhledat následující soubory, u kterých dochází k problémům s oprávněními:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Prodloužit platnost certifikátu podepsaného svým držitelem (RDP)

Pokud se problém opakuje, spusťte následující skript, abyste se ujistili, že se certifikát podepsaný svým držitelem protokolu RDP obnoví:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Pokud certifikát nemůžete obnovit, zkuste certifikát odstranit pomocí těchto kroků:

1. Na jiném virtuálním počítači ve stejné virtuální síti otevřete pole **Spustit** , zadejte **MMC**a potom stiskněte **OK**. 

2. V nabídce **soubor** vyberte **Přidat nebo odebrat modul snap-in**.

3. V seznamu **dostupné moduly snap-in** vyberte **certifikáty**a pak vyberte **Přidat**.

4. Vyberte položku **účet počítače**a potom vyberte možnost **Další**.

5. Vyberte **jiný počítač**a pak přidejte IP adresu virtuálního počítače, který obsahuje problémy.
   >[!Note]
   >Zkuste použít interní síť, abyste se vyhnuli používání virtuální IP adresy.

6. Vyberte **Dokončit**a pak vyberte **OK**.

   ![Vybrat počítač](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Rozbalte certifikáty, přejděte do složky Remote Desktop\Certificates, klikněte pravým tlačítkem na certifikát a pak vyberte **Odstranit**.

8. Restartujte službu Vzdálená plocha Configuration:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Pokud v tuto chvíli aktualizujete Store z konzoly MMC, certifikát se znovu zobrazí. 

Pokuste se získat přístup k virtuálnímu počítači pomocí protokolu RDP znovu.

#### <a name="update-tlsssl-certificate"></a>Aktualizace certifikátu TLS/SSL

Pokud nastavíte virtuální počítač tak, aby používal certifikát TLS/SSL, spusťte následující příkaz, který tento kryptografický otisk získá. Potom zkontrolujte, zda se jedná o stejný kryptografický otisk certifikátu:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Pokud ne, změňte kryptografický otisk:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Můžete se také pokusit odstranit klíč, aby protokol RDP používal certifikát podepsaný svým držitelem pro protokol RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Scénář 2

### <a name="event-log"></a>Protokol událostí

V instanci CMD spusťte následující příkazy, abyste zkontrolovali, jestli se do systémového protokolu v posledních 24 hodinách protokoluje událost chyby SCHANNEL 36871:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Název protokolu:**      Souborů <br />
**Zdroj:**        Schannel <br />
**Datum:**          – <br />
**ID události:**      36871 <br />
**Kategorie úlohy:** NTato <br />
**Úroveň:**         Chyba <br />
**Klíčov**       <br />
**Uživatel:**          SOUBORŮ <br />
**Počítač:**      *počítač* <br />
**Popis:** Při vytváření přihlašovacích údajů serveru TLS došlo k závažné chybě. Stav interní chyby je 10013.
 
### <a name="cause"></a>Příčina

K tomuto problému dochází v důsledku zásad zabezpečení. Pokud jsou starší verze TLS (například 1,0) zakázané, přístup k protokolu RDP se nezdařil.

### <a name="resolution"></a>Řešení

Protokol RDP používá jako výchozí protokol TLS 1,0. Protokol se ale může změnit na TLS 1,1, což je nový standard.

Pokud chcete tento problém vyřešit, přečtěte si téma [řešení chyb ověřování při připojení k virtuálnímu počítači Azure pomocí protokolu RDP](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Scénář 3

Pokud máte na virtuálním počítači nainstalovanou roli **Zprostředkovatel připojení k vzdálené ploše** , podívejte se, jestli za posledních 24 hodin spadá událost 2056 nebo 1296. V instanci CMD spusťte následující příkazy: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Název protokolu:**      Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Zdroj:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Datum:**          *čas* <br />
**ID události:**      2056 <br />
**Kategorie úlohy:** (109) <br />
**Úroveň:**         Chyba <br />
**Klíčov**       <br />
**Uživatel:**          SÍŤOVÁ SLUŽBA <br />
**Počítač:**      *plně kvalifikovaný název domény počítače* <br />
**Popis:** Nepovedlo se najít popis události s ID 2056 ze zdrojového kódu Microsoft-Windows-TerminalServices-SessionBroker. Buď není v místním počítači nainstalována komponenta, která vyvolává tuto událost, nebo je instalace poškozena. Součást můžete nainstalovat nebo opravit v místním počítači. <br />
Pokud událost vznikla v jiném počítači, informace o zobrazení byly uloženy spolu s událostí. <br />
V rámci této události byly zahrnuty následující informace: <br />
NULL <br />
NULL <br />
Přihlášení k databázi se nezdařilo.

**Název protokolu:**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**Zdroj:**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Datum:**          *čas* <br />
**ID události:**      1296 <br />
**Kategorie úlohy:** (104) <br />
**Úroveň:**         Chyba <br />
**Klíčov**       <br />
**Uživatel:**          SÍŤOVÁ SLUŽBA <br />
**Počítač:**      *plně kvalifikovaný název domény počítače* <br />
**Popis:** Nepovedlo se najít popis události s ID 1296 ze zdroje Microsoft-Windows-TerminalServices-SessionBroker-Client. Buď není v místním počítači nainstalována komponenta, která vyvolává tuto událost, nebo je instalace poškozena. Součást můžete nainstalovat nebo opravit v místním počítači.
Pokud událost vznikla v jiném počítači, informace o zobrazení byly uloženy spolu s událostí.
V rámci této události byly zahrnuty následující informace:  <br />
*text* <br />
*text* <br />
Zprostředkovatel připojení k vzdálené ploše není připravená na komunikaci RPC.

### <a name="cause"></a>Příčina

K tomuto problému dochází, protože se změnil název hostitele Zprostředkovatel připojení k vzdálené ploše serveru, což není podporovaná změna. 

Název hostitele má záznamy a závislosti v interní databázi Windows, kterou vyžaduje farma služby Vzdálená plocha, aby mohla fungovat. Změna názvu hostitele po sestavení farmy způsobuje mnoho chyb a může způsobit, že server služby broker přestane fungovat.

### <a name="resolution"></a>Řešení 

Chcete-li tento problém vyřešit, je třeba přeinstalovat roli Zprostředkovatel připojení k vzdálené ploše a interní databázi systému Windows.

## <a name="next-steps"></a>Další kroky

[Události Schannel](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn786445(v=ws.11))

[Zprostředkovatel zabezpečení Schannel – technický přehled](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn786429(v=ws.11))

[Protokol RDP selže s ID události 1058 & události 36870 s certifikátem Hostitel relace vzdálené plochy & komunikaci SSL](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)

[Schannel 36872 nebo Schannel 36870 na řadiči domény](/archive/blogs/instan/schannel-36872-or-schannel-36870-on-a-domain-controller)

[ID události 1058 – ověřování a šifrování služby Vzdálená plocha](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee890862(v=ws.10))
