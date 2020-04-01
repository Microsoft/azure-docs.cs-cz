---
title: Poradce při potížích s chybami ověřování při připojení k virtuálnímu počítači Azure pomocí protokolu RDP | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit chyby ověřování, ke kterým dochází při připojení k virtuálnímu počítači (VM) pomocí protokolu RDP (Remote Desktop Protocol).
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
ms.openlocfilehash: 03356c0b4a93f4befdbc529523e58642137a8887
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420820"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Řešení chyb ověřování při připojování k virtuálnímu počítači Azure pomocí protokolu RDP

Tento článek vám pomůže vyřešit chyby ověřování, ke kterým dochází při připojení k virtuálnímu počítači (VM) pomocí připojení protokolu RDP (RdP).

## <a name="symptoms"></a>Příznaky

Zachytíte snímek obrazovky virtuálního počítače Azure, který zobrazuje úvodní obrazovku a označuje, že operační systém běží. Však při pokusu o připojení k virtuálnímu počítači pomocí připojení ke vzdálené ploše, zobrazí se jedna z následujících chybových zpráv.

### <a name="error-message-1"></a>Chybová zpráva 1

**Došlo k chybě ověřování. Místní bezpečnostní úřad nelze kontaktovat.**

### <a name="error-message-2"></a>Chybová zpráva 2

**Vzdálený počítač, ke kterému se pokoušíte připojit, vyžaduje ověřování na úrovni sítě (NLA), ale řadič domény systému Windows nelze kontaktovat za účelem provedení seznamu NLA. Pokud jste správcem vzdáleného počítače, můžete zakázat službu NLA pomocí možností na kartě Vzdálené v dialogovém okně Vlastnosti systému.**

### <a name="error-message-3-generic-connection-error"></a>Chybová zpráva 3 (obecná chyba připojení)

**Tento počítač se nemůže připojit ke vzdálenému počítači. Pokud problém přetrvává, obraťte se na vlastníka vzdáleného počítače nebo správce sítě.**

## <a name="cause"></a>Příčina

Existuje několik důvodů, proč nla může blokovat přístup RDP k virtuálnímu počítači.

### <a name="cause-1"></a>Příčina 1

Virtuální ho virtuálního zařízení nemůže komunikovat s řadičem domény (DC). Tento problém může zabránit relaci RDP v přístupu k virtuálnímu virtuálnímu serveru pomocí pověření domény. Stále byste se však mohli přihlásit pomocí pověření místního správce. K tomuto problému může dojít v následujících situacích:

1. Kanál zabezpečení služby Active Directory mezi tímto virtuálním serverem a řadičem domény je přerušený.

2. Virtuální město má starou kopii hesla účtu a řadič domény má novější kopii.

3. Řadič domény, ke kterému se tento virtuální server připojuje, není v pořádku.

### <a name="cause-2"></a>Příčina 2

Úroveň šifrování virtuálního počítače je vyšší než ta, kterou používá klientský počítač.

### <a name="cause-3"></a>Příčina 3

Protokoly TLS 1.0, 1.1 nebo 1.2 (server) jsou ve virtuálním počítači zakázány.

### <a name="cause-4"></a>Příčina 4

Virtuální počítač byl nastaven zakázat přihlášení pomocí pověření domény a místní úřad zabezpečení (LSA) je nastaven nesprávně.

### <a name="cause-5"></a>Příčina 5

Virtuální ho virtuálního zařízení bylo nastaveno tak, aby přijímalo pouze připojení algoritmu kompatibilní s federálním standardem zpracování informací (FIPS). To se obvykle provádí pomocí zásad služby Active Directory. Jedná se o vzácnou konfiguraci, ale fips lze vynutit pouze pro připojení ke vzdálené ploše.

## <a name="before-you-troubleshoot"></a>Před odstraňováním potíží

### <a name="create-a-backup-snapshot"></a>Vytvoření záložního snímku

Chcete-li vytvořit snímek zálohy, postupujte podle kroků v [snímek disku](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Vzdálené připojení k virtuálnímu virtuálnímu připojení

Chcete-li se vzdáleně připojit k virtuálnímu počítači , použijte jednu z metod v [tématu Použití vzdálených nástrojů k řešení problémů s virtuálním počítačem Azure](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Služba klienta zásad skupiny

Pokud se jedná o virtuální ms přilehlý k doméně, nejprve zastavte službu Klient zásad skupiny, abyste zabránili přepsání změn zásadami služby Active Directory. Provedete to spuštěním následujícího příkazu:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Po opevnění problému obnovte možnost tohoto virtuálního aplikace kontaktovat doménu a načíst z domény nejnovější seznam skupin. Chcete-li to provést, spusťte následující příkazy:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Pokud je změna vrácena, znamená to, že problém způsobuje zásada služby Active Directory. 

### <a name="workaround"></a>Alternativní řešení

Chcete-li tento problém vyřešit, spusťte v příkazovém okně následující příkazy a zakažte nla:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Potom restartujte virtuální počítač.

Chcete-li znovu povolit nla, spusťte následující příkaz a restartujte virtuální počítač:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Řešení potíží

### <a name="for-domain-joined-vms"></a>Pro virtuální servery přispojené s doménou

Chcete-li tento problém vyřešit, nejprve zkontrolujte, zda virtuální ho virtuálního uživatele můžete připojit k řadiči domény a zda řadič domény má stav "v pořádku" a může zpracovávat požadavky z virtuálního soudu.

>[!Note] 
>Chcete-li otestovat stav řadiče domény, můžete použít jiný virtuální počítač na stejné virtuální síti a podsíti, které sdílejí stejný přihlašovací server.

Připojte se k virtuálnímu virtuálnímu zařízení, který má problém pomocí konzoly Serial console, vzdálené CMD nebo vzdálené ho prostředí PowerShell, podle kroků v části "Připojení k virtuálnímu zařízení vzdáleně".

Chcete-li zjistit, ke kterému řadiči domény se virtuální hotel připojuje, spusťte v konzole následující příkaz: 

```cmd
set | find /i "LOGONSERVER"
```

Potom zkontrolujte stav zabezpečeného kanálu mezi virtuálním serverem a řadičem domény. Chcete-li to provést, spusťte následující příkaz v instanci prostředí PowerShell se zvýšenými oprávněními. Tento příkaz vrátí logický příznak, který označuje, zda je zabezpečený kanál aktivní:

```powershell
Test-ComputerSecureChannel -verbose
```

Pokud je kanál přerušený, opravte jej následujícím příkazem:

```powershell
Test-ComputerSecureChannel -repair
```

Ujistěte se, že heslo účtu počítače ve službě Active Directory je aktualizováno na virtuálním počítači a řadiči domény:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Pokud je komunikace mezi řadičem domény a virtuálním počítačem dobrá, ale řadič domény není dostatečně v pořádku, aby otevřel relaci PROTOKOLU RDP, můžete se pokusit restartovat řadič domény.

Pokud předchozí příkazy nevyřešily problém s komunikací s doménou, můžete se k této doméně znovu připojit k tomuto virtuálnímu virtuálnímu jevu. Postupujte přitom takto:

1. Vytvořte skript s názvem Unjoin.ps1 pomocí následujícího obsahu a pak nasadit skript jako rozšíření vlastního skriptu na webu Azure:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Tento skript bere virtuální počítač z domény násilně a restartuje jej o 10 sekund později. Potom je třeba vyčistit objekt Computer na straně domény.

2.  Po dokončení čištění se znovu připojte k tomuto virtuálnímu virtuálnímu mněmu k doméně. Chcete-li to provést, vytvořte skript s názvem JoinDomain.ps1 pomocí následujícího obsahu a pak nasadit skript jako rozšíření vlastního skriptu na webu Azure Portal: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >To připojí virtuální počítač v doméně pomocí zadaných pověření.

Pokud je kanál služby Active Directory v pořádku, heslo počítače se aktualizuje a řadič domény pracuje podle očekávání, vyzkoušejte následující kroky.

Pokud problém přetrvává, zkontrolujte, zda je pověření domény zakázáno. Chcete-li to provést, otevřete okno příkazového řádku se zvýšenými oprávněními a spusťte následující příkaz, abyste zjistili, zda je virtuální počítač nastavený na zakázání účtů domény pro přihlášení k virtuálnímu počítači:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Pokud je klíč nastaven na **1**, znamená to, že server byl nastaven tak, aby nepovoloval pověření domény. Změňte tento klíč na **0**.

### <a name="for-standalone-vms"></a>Pro samostatné virtuální aplikace

#### <a name="check-minencryptionlevel"></a>Zkontrolovat úroveň MinEncryptionLevel

V instanci CMD spusťte následující příkaz pro dotaz na hodnotu registru **MinEncryptionLevel:**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Na základě hodnoty registru postupujte takto:

* 4 (FIPS): Přejít na [kontrolu FIPs kompatibilní algoritmy připojení](#fips-compliant).

* 3 (128bitové šifrování): Spuštěním následujícího příkazu nastavte závažnost na **2:**

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (Nejvyšší možné šifrování, jak je dáno klientem): Můžete se pokusit nastavit šifrování na minimální hodnotu **1** spuštěním následujícího příkazu:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Restartujte virtuální počítač tak, aby změny v registru se projeví.

#### <a name="tls-version"></a>Verze TLS

V závislosti na systému používá protokol RDP protokol TLS 1.0, 1.1 nebo 1.2 (server). Chcete-li zjistit, jak jsou tyto protokoly nastaveny na virtuálním počítači, otevřete instanci CMD a spusťte následující příkazy:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Pokud vrácené hodnoty nejsou všechny **1**, znamená to, že protokol je zakázán. Chcete-li povolit tyto protokoly, spusťte následující příkazy:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

U jiných verzí protokolu můžete spustit následující příkazy:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Získejte Verzi X.x s Verzí SSH/TLS z protokolů hostovaného operačního systému na chybách SCHANNEL.

#### <a name="check-fips-compliant-algorithms-connections"></a><a name="fips-compliant"></a>Zkontrolujte připojení algoritmů kompatibilních s FIPs

Vzdálenou plochu lze vynutit tak, aby používala pouze připojení algoritmů kompatibilních s fipsy. To lze nastavit pomocí klíče registru. Chcete-li to provést, otevřete okno příkazového řádku se zvýšenými oprávněními a potom se zadejte dotaz na následující klávesy:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Pokud příkaz vrátí **hodnotu 1**, změňte hodnotu registru na **hodnotu 0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Zkontrolujte, která je aktuální MinEncryptionLevel na virtuálním počítači:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Pokud příkaz vrátí **hodnotu 4**, změňte hodnotu registru na **2.**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Restartujte virtuální počítač tak, aby změny v registru se projeví.

## <a name="next-steps"></a>Další kroky

[Metoda SetEncryptionLevel třídy Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Konfigurace úrovní ověřování a šifrování serveru](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting třída](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
