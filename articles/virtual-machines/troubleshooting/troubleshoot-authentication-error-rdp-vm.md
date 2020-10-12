---
title: Řešení chyb ověřování při připojení k virtuálnímu počítači Azure pomocí protokolu RDP | Microsoft Docs
description: Přečtěte si, jak řešit chyby ověřování, ke kterým dochází při použití protokol RDP (Remote Desktop Protocol) (RDP) pro připojení k virtuálnímu počítači Azure (VM).
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
ms.openlocfilehash: cc1ad3104596cc7ad4bb48f88e4c8312bc833371
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088559"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Řešení chyb ověřování při připojování k virtuálnímu počítači Azure pomocí protokolu RDP

Tento článek vám může pomoct při odstraňování chyb ověřování, ke kterým dochází při použití připojení protokol RDP (Remote Desktop Protocol) (RDP) pro připojení k virtuálnímu počítači Azure (VM).

## <a name="symptoms"></a>Příznaky

Zachytíte snímek obrazovky virtuálního počítače Azure, který zobrazuje úvodní obrazovku, a indikuje, že operační systém je spuštěný. Když se ale pokusíte připojit k virtuálnímu počítači pomocí Připojení ke vzdálené ploše, zobrazí se některá z následujících chybových zpráv.

### <a name="error-message-1"></a>Chybová zpráva 1

**Došlo k chybě ověřování. Místní autoritu zabezpečení nelze kontaktovat.**

### <a name="error-message-2"></a>Chybová zpráva 2

**Vzdálený počítač, ke kterému se pokoušíte připojit, vyžaduje ověřování na úrovni sítě (NLA), ale řadič domény systému Windows se nedá kontaktovat, aby se mohl provést NLA. Pokud jste správce na vzdáleném počítači, můžete NLA vypnout pomocí možností na kartě vzdálené v dialogovém okně Vlastnosti systému.**

### <a name="error-message-3-generic-connection-error"></a>Chybová zpráva 3 (chyba obecného připojení)

**Tento počítač se nemůže připojit ke vzdálenému počítači. Zkuste se znovu připojit. Pokud potíže potrvají, obraťte se na vlastníka vzdáleného počítače nebo správce vaší sítě.**

## <a name="cause"></a>Příčina

Existuje několik důvodů, proč může protokol NLA zablokovat přístup protokolu RDP k virtuálnímu počítači.

### <a name="cause-1"></a>Příčina 1

Virtuální počítač nemůže komunikovat s řadičem domény (DC). Tento problém by mohl zabránit relaci RDP v přístupu k virtuálnímu počítači pomocí přihlašovacích údajů domény. Budete se ale pořád moci přihlásit pomocí přihlašovacích údajů místního správce. K tomuto problému může dojít v následujících situacích:

1. Kanál zabezpečení služby Active Directory mezi tímto virtuálním počítačem a řadičem domény je poškozený.

2. Virtuální počítač má starou kopii hesla účtu a řadič domény má novější kopii.

3. Řadič domény, ke kterému se tento virtuální počítač připojuje, není v pořádku.

### <a name="cause-2"></a>Příčina 2

Úroveň šifrování virtuálního počítače je vyšší než ta, kterou používá klientský počítač.

### <a name="cause-3"></a>Příčina 3

Protokoly TLS 1,0, 1,1 nebo 1,2 (Server) jsou na virtuálním počítači zakázané.

### <a name="cause-4"></a>Příčina 4

Virtuální počítač byl nastaven tak, aby zakázal přihlášení pomocí přihlašovacích údajů domény, a místní úřad zabezpečení (LSA) se nastaví nesprávně.

### <a name="cause-5"></a>Příčina 5

Virtuální počítač byl nastavený tak, aby přijímal jenom připojení algoritmu standardu FIPS (Federal Information Processing Standard). To se obvykle provádí pomocí zásad služby Active Directory. Toto je vzácná konfigurace, ale Standard FIPS se dá vyhovět jenom pro připojení ke vzdálené ploše.

## <a name="before-you-troubleshoot"></a>Před odstraňováním potíží

### <a name="create-a-backup-snapshot"></a>Vytvoření snímku zálohy

Chcete-li vytvořit záložní snímek, postupujte podle kroků v části [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Vzdálené připojení k virtuálnímu počítači

Pokud se chcete vzdáleně připojit k virtuálnímu počítači, použijte jednu z metod, [jak pomocí nástrojů Remote Tools řešit problémy s virtuálními počítači Azure](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Klientská služba zásad skupiny

Pokud se jedná o virtuální počítač připojený k doméně, nejdřív zastavte službu klienta Zásady skupiny, aby nedošlo k přepsání změn zásad služby Active Directory. Provedete to spuštěním následujícího příkazu:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Po vyřešení problému obnovte schopnost tohoto virtuálního počítače kontaktovat doménu a získat nejnovější objekt zásad skupiny z domény. Uděláte to tak, že spustíte následující příkazy:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Pokud se změna vrátí, znamená to, že problém způsobuje zásada služby Active Directory. 

### <a name="workaround"></a>Alternativní řešení

Pokud chcete tento problém obejít, zakažte NLA spuštěním následujících příkazů v příkazovém okně:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Pak restartujte virtuální počítač.

Pokud chcete znovu povolit NLA, spusťte následující příkaz a restartujte virtuální počítač:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Řešení potíží

### <a name="for-domain-joined-vms"></a>Pro virtuální počítače připojené k doméně

Pokud chcete tento problém vyřešit, nejdřív ověřte, jestli se virtuální počítač může připojit k řadiči domény, a jestli má řadič domény stav "v pořádku" a může zpracovávat požadavky z virtuálního počítače.

>[!Note] 
>Pokud chcete otestovat stav řadiče domény, můžete použít jiný virtuální počítač ve stejné virtuální síti a podsíti, která sdílí stejný přihlašovací server.

Připojte se k virtuálnímu počítači, který má problém, pomocí Sériová konzola, vzdáleného příkazu nebo vzdáleného PowerShellu podle postupu v části připojení k virtuálnímu počítači vzdáleně.

Pokud chcete zjistit, ke kterému řadiči domény se virtuální počítač připojuje, spusťte v konzole následující příkaz: 

```cmd
set | find /i "LOGONSERVER"
```

Pak zkontrolujte stav zabezpečeného kanálu mezi virtuálním počítačem a řadičem domény. Provedete to tak, že v instanci PowerShellu se zvýšenými oprávněními spustíte následující příkaz. Tento příkaz vrátí logický příznak, který označuje, jestli je zabezpečený kanál aktivní:

```powershell
Test-ComputerSecureChannel -verbose
```

Pokud je kanál porušený, spusťte následující příkaz, který ho opravíte:

```powershell
Test-ComputerSecureChannel -repair
```

Ujistěte se, že je na VIRTUÁLNÍm počítači a řadiči domény aktualizované heslo účtu počítače ve službě Active Directory:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Pokud je komunikace mezi řadičem domény a virtuálním počítačem dobrá, ale řadič domény není dostatečně dobrý pro otevření relace RDP, můžete zkusit restartovat řadič domény.

Pokud předchozí příkazy neopravily problém komunikace s doménou, můžete tento virtuální počítač znovu připojit k doméně. Postupujte takto:

1. Pomocí následujícího obsahu vytvořte skript s názvem Unjoin.ps1 a potom tento skript nasaďte jako rozšíření vlastních skriptů na Azure Portal:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Tento skript vynutí, aby se virtuální počítač vypnul z domény nuceně a později se restartuje 10 sekund. Pak je nutné vyčistit objekt počítače na straně domény.

2.  Po dokončení vyčištění znovu připojte tento virtuální počítač k doméně. Provedete to tak, že vytvoříte skript s názvem JoinDomain.ps1 pomocí následujícího obsahu a potom tento skript nasadíte jako rozšíření vlastních skriptů na Azure Portal: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Tím se připojí virtuální počítač k doméně pomocí zadaných přihlašovacích údajů.

Pokud je kanál služby Active Directory v pořádku, heslo počítače se aktualizuje a řadič domény funguje podle očekávání, zkuste provést následující kroky.

Pokud se problém opakuje, ověřte, jestli jsou přihlašovací údaje domény zakázané. Provedete to tak, že otevřete okno příkazového řádku se zvýšenými oprávněními a pak spustíte následující příkaz, abyste zjistili, jestli je pro přihlášení k virtuálnímu počítači nastavený tento virtuální počítač. zakázání účtů domény:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Pokud je klíč nastavený na hodnotu **1**, znamená to, že server byl nastavený tak, aby nepovoloval přihlašovací údaje do domény. Změňte tento klíč na **0**.

### <a name="for-standalone-vms"></a>Pro samostatné virtuální počítače

#### <a name="check-minencryptionlevel"></a>Kontrolovat MinEncryptionLevel

V instanci CMD spusťte následující příkaz, který se dotazuje na hodnotu registru **MinEncryptionLevel** :

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Na základě hodnoty registru použijte následující postup:

* 4 (FIPS): Přejít na [kontrolu připojení algoritmů kompatibilních se standardem FIPS](#fips-compliant).

* 3 (128 bitů šifrování): závažnost nastavte na **2** spuštěním následujícího příkazu:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (nejvyšší možná šifrování, jak je Nadiktuj klient): můžete zkusit nastavit šifrování na minimální hodnotu **1** spuštěním následujícího příkazu:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Restartujte virtuální počítač, aby se změny v registru projevily.

#### <a name="tls-version"></a>Verze TLS

V závislosti na systému používá protokol RDP protokol TLS 1,0, 1,1 nebo 1,2 (Server). Pokud chcete zadat dotaz na to, jak se tyto protokoly nastavují na virtuálním počítači, otevřete instanci CMD a pak spusťte následující příkazy:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Pokud vrácené hodnoty nejsou všechny **1**, znamená to, že je protokol zakázaný. Chcete-li povolit tyto protokoly, spusťte následující příkazy:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Pro jiné verze protokolu můžete spustit následující příkazy:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Načte SSH/TLS verze x. x z protokolů hostovaného operačního systému na chyby zprostředkovatele SCHANNEL.

#### <a name="check-fips-compliant-algorithms-connections"></a><a name="fips-compliant"></a> Kontrolovat připojení algoritmů vyhovující standardu FIPs

Vzdálená plocha se dá vykonat pro použití jenom připojení algoritmů kompatibilních se standardem FIPs. Dá se nastavit pomocí klíče registru. Provedete to tak, že otevřete okno příkazového řádku se zvýšenými oprávněními a potom zadáte dotaz na následující klíče:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Pokud příkaz vrátí hodnotu **1**, změňte hodnotu registru na **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Ověřte, jestli je aktuální MinEncryptionLevel na virtuálním počítači:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Pokud příkaz vrátí hodnotu **4**, změňte hodnotu registru na **2** .

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Restartujte virtuální počítač, aby se změny v registru projevily.

## <a name="next-steps"></a>Další kroky

[Metoda SetEncryptionLevel třídy Win32_TSGeneralSetting](/windows/desktop/termserv/win32-tsgeneralsetting-setencryptionlevel)

[Konfigurace ověření serveru a úrovní šifrování](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting – třída](/windows/desktop/termserv/win32-tsgeneralsetting)
