---
title: Řešení chyb ověřování při připojení k virtuálnímu počítači Azure pomocí protokolu RDP | Dokumentace Microsoftu
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
ms.openlocfilehash: 47d3b827099d3a4a7520ac66765d2928795b6e49
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967976"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Řešení chyb ověřování při připojení k virtuálnímu počítači Azure pomocí protokolu RDP

Tento článek vám pomohou vyřešit chyby s ověřováním, ke kterým dochází při použití protokolu RDP (Remote Desktop) připojení pro připojení k Azure virtuální počítač (VM).

## <a name="symptoms"></a>Příznaky

Můžete zachytit snímek obrazovky virtuálního počítače Azure, který se zobrazí na úvodní obrazovce a naznačuje, že je spuštěn operační systém. Ale při pokusu o připojení k virtuálnímu počítači pomocí připojení ke vzdálené ploše, obdržíte jednu z následujících chybových zpráv.

### <a name="error-message-1"></a>Chybová zpráva 1

**Došlo k chybě ověřování. Místní úřad zabezpečení nelze kontaktovat.**

### <a name="error-message-2"></a>Chybová zpráva 2

**Vzdálený počítač, který se pokoušíte připojit, vyžaduje ověřování na úrovni sítě (NLA), ale nelze provádět NLA kontaktovat řadiče domény Windows. Pokud jste správce na vzdáleném počítači, můžete zakázat NLA pomocí možnosti na kartě Vzdálená dialogovém okně Vlastnosti systému.**

### <a name="error-message-3-generic-connection-error"></a>Chybová zpráva 3 (obecné připojení. Chyba)

**Tento počítač se nemůže připojit ke vzdálenému počítači. Zkuste se připojit znovu, pokud se problém opakuje, obraťte se na vlastníka vzdáleném počítači nebo správce sítě.**

## <a name="cause"></a>Příčina

Existuje několik důvodů, proč může NLA blokuje přístup protokolu RDP k virtuálnímu počítači.

### <a name="cause-1"></a>Příčiny 1

Virtuální počítač nemůže komunikovat s řadičem domény (DC). Tento problém může bránit relaci RDP v přístupu k virtuálnímu počítači pomocí přihlašovacích údajů domény. Ale bude stále budete moci přihlásit pomocí přihlašovacích údajů místního správce. Tomuto problému může dojít v následujících situacích:

1. Active Directory zabezpečený kanál mezi tento virtuální počítač a řadič domény je přerušeno.

2. Virtuální počítač má stará kopie heslo k účtu a řadič domény má kopii novější.

3. Řadič domény, který tento virtuální počítač se připojuje k není v pořádku.

### <a name="cause-2"></a>Příčiny 2

Úroveň šifrování virtuálního počítače je vyšší než ten, který se používá v klientském počítači.

### <a name="cause-3"></a>Příčina 3

Protokoly TLS 1.0, 1.1 nebo 1.2 (server) jsou zakázány ve virtuálním počítači.

### <a name="cause-4"></a>Příčina 4

Zakázat přihlášení pomocí přihlašovacích údajů do domény nastavil virtuální počítač a místního úřadu zabezpečení (LSA) správně nastavená.

### <a name="cause-5"></a>Příčina 5

Virtuální počítač byl nastaven tak, aby přijímal jenom federální informace o zpracování Standard (FIPS)-algoritmus kompatibilní připojení. To se obvykle provádí pomocí zásad služby Active Directory. Toto je vzácné konfigurace, ale je možné vynutit standard FIPS pro připojení ke vzdálené ploše pouze.

## <a name="before-you-troubleshoot"></a>Předtím, než je řešit

### <a name="create-a-backup-snapshot"></a>Vytvoření snímku zálohy

Pokud chcete vytvořit snímek zálohy, postupujte podle kroků v [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Vzdálené připojení k virtuálnímu počítači

Pro vzdálené připojení k virtuálnímu počítači, použijte jednu z metod v [použití nástrojů remote tools pro řešení potíží s virtuálním počítači Azure](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Služba Klient zásad skupiny

Pokud je virtuální počítač připojený k doméně, nejprve zastavte službu Klient zásad skupiny tak, aby se zabránilo žádné zásady služby Active Directory přepisovat změny. Chcete-li to provést, spusťte následující příkaz:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Až bude problém vyřešen, obnovení možnost tento virtuální počítač kontaktovat doménu k načtení nejnovějších zásad skupiny z domény. Chcete-li to provést, spusťte následující příkazy:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Pokud se vrátí zpět změny, znamená to, že zásady služby Active Directory je příčinou problému. 

### <a name="workaround"></a>Alternativní řešení

Chcete-li tento problém vyřešit, spusťte následující příkazy v příkazovém okně Zakázat NLA:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Restartujte virtuální počítač.

Opětovné povolení NLA, spusťte následující příkaz a potom restartujte virtuální počítač:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Řešení potíží

### <a name="for-domain-joined-vms"></a>Pro virtuální počítače připojené k doméně

Při řešení tohoto problému, nejprve zkontrolujte, zda virtuální počítač může připojit k řadiči domény a zda řadič domény je ve stavu "v pořádku" a může zpracovávat požadavky z virtuálního počítače.

>[!Note] 
>Otestovat stav řadiče domény, použijete k jinému virtuálnímu počítači ve stejné virtuální sítě a podsítě, které sdílejí stejný přihlašovací server.

Připojit k virtuálnímu počítači, který má problém s použitím konzoly sériového portu, vzdálené CMD nebo vzdáleného prostředí PowerShell podle pokynů v části "Vzdálené připojení k virtuálnímu počítači".

Pokud chcete zjistit, které řadič domény, virtuální počítač se připojuje k, spusťte následující příkaz v konzole: 

```cmd
set | find /i "LOGONSERVER"
```

Zkontrolujte stav zabezpečený kanál mezi tímto a řadič domény. Chcete-li to provést, spusťte následující příkaz v instanci prostředí PowerShell se zvýšenými oprávněními. Tento příkaz vrátí příznak logické hodnoty označující, zda je zabezpečený kanál aktivní:

```powershell
Test-ComputerSecureChannel -verbose
```

Pokud kanál není funkční, spusťte následující příkaz k opravě ho:

```powershell
Test-ComputerSecureChannel -repair
```

Ujistěte se, že je na virtuálním počítači a řadič domény aktualizovat heslo účtu počítače ve službě Active Directory:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Pokud komunikace mezi řadiči domény a virtuální počítač je dobrá, ale řadič domény není v dobrém stavu dostatečně otevřete relaci RDP, můžete zkusit restartovat řadič domény.

Pokud výše uvedených příkazů nevyřešilo problém komunikace k doméně, můžete připojit tento virtuální počítač k doméně. Postupujte přitom takto:

1. Vytvořit skript, který je pojmenován Unjoin.ps1 pomocí následující obsah a pak skript nasadit jako rozšíření vlastních skriptů na webu Azure portal:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Tento skript má virtuální počítač nuceně mimo doménu a restartuje je 10 sekund později. Pak bude nutné vyčistit objekt počítače na straně domény.

2.  Po dokončení vyčištění připojte tento virtuální počítač k doméně. K tomuto účelu vytvořte skript, který se jmenuje JoinDomain.ps1 pomocí následující obsah a skript nasadit jako rozšíření vlastních skriptů na webu Azure portal: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Připojí se virtuální počítač v doméně pomocí zadaných přihlašovacích údajů.

Pokud kanál služby Active Directory je v pořádku, heslo počítače se aktualizuje a řadiče domény funguje podle očekávání, vyzkoušejte následující kroky.

Pokud se problém nevyřeší, zkontrolujte, jestli je zakázaná přihlašovacích údajů domény. Chcete-li to provést, otevřete okno příkazového řádku se zvýšenými oprávněními a spusťte následující příkaz k určení, zda je virtuální počítač nastavit zakázat účty domény pro přihlášení k virtuálnímu počítači:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Pokud klíč je nastavená na **1**, to znamená, že server byl nastaven nahoru nepovolení přihlašovacích údajů do domény. Změny tohoto klíče k **0**.

### <a name="for-standalone-vms"></a>U samostatných virtuálních počítačů

#### <a name="check-minencryptionlevel"></a>Zkontrolujte MinEncryptionLevel

V instanci CMD spusťte následující příkaz k dotazu **MinEncryptionLevel** hodnoty registru:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Podle hodnoty registru, postupujte takto:

* 4 (FIPS): přejděte na [připojení algoritmy odpovídající standardu FIPs zkontrolujte](#fips-compliant).

* 3 (128bitové šifrování): nastavit závažnost **2** spuštěním následujícího příkazu:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (nejvyšší šifrování nejbližším podle klienta): můžete nastavit šifrování na minimální hodnotu **1** spuštěním následujícího příkazu:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Restartujte virtuální počítač tak, aby se projevily změny v registru.

#### <a name="tls-version"></a>Verze protokolu TLS

V závislosti na systému protokol RDP používá protokol TLS 1.0, 1.1 nebo 1.2 (server). K dotazování, jak se tyto protokoly jsou nastavené na virtuálním počítači, spusťte instanci CMD a pak spusťte následující příkazy:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Pokud vrácených hodnot nejsou všechny **1**, to znamená, že je zakázaný protokol. Pokud chcete povolit tyto protokoly, spusťte následující příkazy:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

U ostatních verzí protokolu můžete spuštěním následujících příkazů:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Získání verze x.x SSH/TLS od protokoly operačního systému hosta na chyby zprostředkovatele SCHANNEL.

#### <a name="fips-compliant"></a> Zkontrolujte připojení algoritmy odpovídající standardu FIPs

Vzdálená plocha je možné vynutit pouze připojení algoritmus kompatibilní se standardem FIPs. To lze nastavit pomocí klíče registru. Chcete-li to provést, otevřete okno příkazového řádku se zvýšenými oprávněními a potom zadejte dotaz následující klíče:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Pokud příkaz vrátí **1**, změňte hodnotu registru na **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Kontrola, což je aktuální MinEncryptionLevel na virtuálním počítači:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Pokud příkaz vrátí **4**, změňte hodnotu registru na **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Restartujte virtuální počítač tak, aby se projevily změny v registru.

## <a name="next-steps"></a>Další postup

[Metoda SetEncryptionLevel Třída Win32_TSGeneralSetting třídy](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Konfigurace úrovně šifrování a ověřování serveru](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Třída Win32_TSGeneralSetting třídy](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
