---
title: Řešení potíží s nabízenou instalací služby mobility pomocí Azure Site Recovery
description: Řešení chyb při instalaci služby mobility Services při povolování replikace pro zotavení po havárii pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 62c8240a4d2e50aa3b584f322baf7d2ee217c6d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98127868"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Řešení potíží s nabízenou instalací služby mobility

Instalace služby mobility je klíčovým krokem pro povolení replikace. Úspěch tohoto kroku závisí na požadavcích na schůzku a na práci s podporovanými konfiguracemi. Mezi nejběžnější chyby, které byste mohli při instalaci služby mobility využít:

* [Chyby přihlašovacích údajů/oprávnění](#credentials-check-errorid-95107--95108)
* [Neúspěšná přihlášení](#login-failures-errorid-95519-95520-95521-95522)
* [Chyby připojení](#connectivity-failure-errorid-95117--97118)
* [Chyby sdílení souborů a tiskáren](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Selhání rozhraní WMI (Windows Management Instrumentation) (WMI)](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nepodporované operační systémy](#unsupported-operating-systems)
* [Nepodporované konfigurace spouštění](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Selhání instalace služby Stínová kopie svazku (VSS)](#vss-installation-failures)
* [Název zařízení v konfiguraci GRUB namísto UUID zařízení](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Svazek LVM (Logical Volume Manager)](#lvm-support-from-920-version)
* [Upozornění na restartování](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Když povolíte replikaci, Azure Site Recovery se pokusí nainstalovat agenta služby mobility na virtuální počítač (VM). V rámci tohoto procesu se konfigurační server pokusí připojit k virtuálnímu počítači a zkopírovat agenta. Pokud chcete povolit úspěšnou instalaci, postupujte podle podrobných pokynů k řešení potíží.

## <a name="credentials-check-errorid-95107--95108"></a>Ověření přihlašovacích údajů (ErrorID: 95107 & 95108)

Ověřte, zda je uživatelský účet vybraný během povolování replikace platný a přesný. Pro provedení nabízené instalace vyžaduje Azure Site Recovery **kořenový** účet nebo uživatelský účet s **oprávněními správce** . V opačném případě bude nabízená instalace na zdrojovém počítači blokovaná.

V případě systému Windows (**chyba 95107**) ověřte, zda uživatelský účet má na zdrojovém počítači oprávnění správce, a to buď pomocí místního účtu, nebo doménového účtu. Pokud nepoužíváte doménový účet, je nutné zakázat řízení přístupu vzdáleného uživatele v místním počítači.

* Ruční přidání klíče registru, který zakazuje vzdálené řízení přístupu uživatele:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Přidat nový `DWORD` : `LocalAccountTokenFilterPolicy`
  * Nastavte hodnotu na `1`

* Chcete-li přidat klíč registru, spusťte z příkazového řádku následující příkaz:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Pro Linux (**chyba 95108**) musíte zvolit **kořenový** účet pro úspěšnou instalaci agenta služby mobility. Kromě toho by měly být spuštěny služby SSH protokol FTP (File Transfer Protocol) (SFTP). Povolení subsystému SFTP a ověřování hesla v souboru _sshd_config_ :

1. Přihlaste se jako uživatel **root**.
1. Přejít na _/etc/ssh/sshd_config soubor_, Najděte řádek, který začíná na `PasswordAuthentication` .
1. Odkomentujte řádek a změňte hodnotu na `yes` .
1. Najděte řádek, který začíná `Subsystem` a odkomentujte řádek.
1. Restartujte `sshd` službu.

Pokud chcete upravit pověření zvoleného uživatelského účtu, postupujte podle [těchto pokynů](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Selhání nedostatečných oprávnění (ErrorID: 95517)

Pokud uživatel, který zvolil instalaci agenta mobility, nemá oprávnění správce, konfigurační server nebo procesový Server se škálováním na více instancí nebude moct zkopírovat software agenta mobility na zdrojový počítač. Tato chyba je výsledkem chyby odepření přístupu. Ujistěte se, že uživatelský účet má oprávnění správce.

Pokud chcete upravit pověření zvoleného uživatelského účtu, postupujte podle [těchto pokynů](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Selhání nedostatečných oprávnění (ErrorID: 95518)

Pokud se při pokusu o přihlášení ke zdrojovému počítači dojde k chybě při navazování vztahu důvěryhodnosti domény mezi primární doménou a pracovní stanicí, instalace agenta mobility se nezdařila s ID chyby 95518. Ujistěte se, že uživatelský účet použitý k instalaci agenta mobility má oprávnění správce pro přihlášení k primární doméně zdrojového počítače.

Pokud chcete upravit pověření zvoleného uživatelského účtu, postupujte podle [těchto pokynů](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Neúspěšná přihlášení (ErrorID: 95519, 95520, 95521, 95522)

Tato část popisuje přihlašovací údaje a chybové zprávy přihlášení.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Přihlašovací údaje uživatelského účtu jsou zakázané (ErrorID: 95519).

Uživatelský účet vybraný během povolování replikace byl zakázán. Pokud chcete uživatelský účet povolit, přečtěte si [Tento článek](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) nebo spusťte následující příkaz, který nahradí textové _uživatelské_ jméno skutečným uživatelským jménem.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Přihlašovací údaje byly uzamčeny kvůli několika neúspěšným pokusům o přihlášení (ErrorID: 95520).

Několik neúspěšných pokusů o přístup k počítači bude uzamknout uživatelský účet. Příčinou chyby může být:

* Přihlašovací údaje zadané během nastavení konfigurace nejsou správné.
* Uživatelský účet vybraný během povolování replikace je nesprávný.

Podle [těchto pokynů](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) upravte přihlašovací údaje a zkuste operaci zopakovat.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>Na zdrojovém počítači nejsou k dispozici přihlašovací servery (ErrorID: 95521).

K této chybě dochází, když přihlašovací servery nejsou na zdrojovém počítači k dispozici. Pokud nejsou přihlašovací servery dostupné, přihlašovací požadavky selžou a Agent mobility nejde nainstalovat. Po úspěšném přihlášení zajistěte, aby byly na zdrojovém počítači k dispozici přihlašovací servery, a spusťte službu Netlogon. Další informace najdete v tématu [scénáře přihlášení do systému Windows](/windows-server/security/windows-authentication/windows-logon-scenarios).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Přihlašovací služba není na zdrojovém počítači spuštěná (ErrorID: 95522).

Přihlašovací služba není na zdrojovém počítači spuštěná a způsobila selhání žádosti o přihlášení. Agenta mobility nejde nainstalovat. Pokud chcete chybu vyřešit, použijte jednu z následujících metod ke spuštění `Netlogon` služby na zdrojovém počítači:

* Chcete-li spustit `Netlogon` službu z příkazového řádku, spusťte příkaz `net start Netlogon` .
* Ve Správci úloh spusťte `Netlogon` službu.

## <a name="connectivity-failure-errorid-95117--97118"></a>Chyba připojení (ErrorID: 95117 & 97118)

Konfigurační server/procesový Server se škálováním na více instancí se pokusí připojit ke zdrojovému virtuálnímu počítači a nainstalovat agenta mobility. K této chybě dochází, pokud zdrojový počítač není dostupný, protože došlo k problémům se síťovým připojením.

Řešení této chyby:

* Ověřte, že uživatelský účet má na zdrojovém počítači oprávnění správce, a to pomocí místního účtu nebo účtu domény. Pokud nepoužíváte doménový účet, je nutné zakázat řízení přístupu vzdáleného uživatele v místním počítači.
  * Ruční přidání klíče registru, který zakazuje vzdálené řízení přístupu uživatele:
    * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
    * Přidat nový `DWORD` : `LocalAccountTokenFilterPolicy`
    * Nastavte hodnotu na `1`
  * Chcete-li přidat klíč registru, spusťte z příkazového řádku následující příkaz:

    `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

* Ujistěte se, že na zdrojovém počítači můžete testovat z konfiguračního serveru. Pokud jste během povolování replikace zvolili procesový Server se škálováním na více instancí, ujistěte se, že na zdrojovém počítači můžete testovat z procesového serveru příkaz k otestování.

* Ujistěte se, že je ve vašem virtuálním počítači povolená služba sdílení souborů a tiskáren. Podívejte se na [Tento postup.](vmware-azure-troubleshoot-push-install.md#file-and-printer-sharing-services-check-errorid-95105--95106)

* Ujistěte se, že je ve vašem virtuálním počítači povolená služba WMI. Podívejte se na [Tento postup.](vmware-azure-troubleshoot-push-install.md#windows-management-instrumentation-wmi-configuration-check-error-code-95103)

* Ujistěte se, že síťové sdílené složky na virtuálním počítači jsou dostupné z procesového serveru. Podívejte se na [Tento postup.](vmware-azure-troubleshoot-push-install.md#check-access-for-network-shared-folders-on-source-machine-errorid-9510595523)

* Na příkazovém řádku počítač zdrojového serveru použijte příkaz `Telnet` k otestování konfiguračního serveru nebo procesového serveru se škálováním na více instancí na portu HTTPS 135, jak je znázorněno v následujícím příkazu. Tento příkaz zkontroluje, jestli nedochází k problémům se síťovým připojením nebo blokování portů brány firewall.

  `telnet <CS/ scale-out PS IP address> <135>`

* Pro virtuální počítače se systémem Linux navíc:
  * Ověřte, jestli jsou nainstalované nejnovější OpenSSH, OpenSSH server a balíčky OpenSSL.
  * Zkontrolujte a zajistěte, aby byl povolený Secure Shell (SSH) a běžel na portu 22.
  * Služby SFTP by měly být spuštěné. Postup povolení subsystému SFTP a ověřování hesla v souboru _sshd_config_ :

    1. Přihlaste se jako uživatel **root**.
    1. Přejít na _/etc/ssh/sshd_config_ soubor, Najděte řádek, který začíná na `PasswordAuthentication` .
    1. Odkomentujte řádek a změňte hodnotu na `yes` .
    1. Najděte řádek, který začíná `Subsystem` a odkomentujte řádek.
    1. Restartujte `sshd` službu.

* Pokus o připojení se nezdařil, pokud po určitém časovém intervalu neexistují žádné správné odpovědi nebo navázáno připojení selhalo, protože se nepovedlo odpovědět připojeného hostitele.
* Může se jednat o problém související s připojením/sítí nebo doménou. Důvodem může být taky to, že název DNS řeší problém s vyčerpáním portů TCP. Ověřte, zda jsou ve vaší doméně žádné známé problémy.

## <a name="connectivity-failure-errorid-95523"></a>Chyba připojení (ErrorID: 95523)

K této chybě dojde, pokud se síť, která se nachází na zdrojovém počítači, nenašla, je možná Odstraněná nebo už není dostupná. Jediným způsobem, jak chybu vyřešit, je zajistit, aby síť existovala.

## <a name="check-access-for-network-shared-folders-on-source-machine-errorid-9510595523"></a>Zkontroluje přístup pro síťové sdílené složky na zdrojovém počítači (ErrorID: 95105, 95523).

Ověřte, jestli jsou sdílené síťové složky na virtuálním počítači dostupné ze procesového serveru (PS) vzdáleně pomocí zadaných přihlašovacích údajů. Pro potvrzení přístupu: 

1. Přihlaste se k počítači procesového serveru.
2. Otevřete Průzkumníka souborů. Do adresního řádku zadejte `\\<SOURCE-MACHINE-IP>\C$` a klikněte na ENTER.

    ![Otevřít složku v PS](./media/vmware-azure-troubleshoot-push-install/open-folder-process-server.PNG)

3. Průzkumník souborů zobrazí výzvu k zadání přihlašovacích údajů. Zadejte uživatelské jméno a heslo a klikněte na OK. <br><br/>

    ![Zadat přihlašovací údaje](./media/vmware-azure-troubleshoot-push-install/provide-credentials.PNG)

    >[!NOTE]
    > Pokud je zdrojový počítač připojený k doméně, zadejte název domény společně s uživatelským jménem `<domainName>\<username>` . Pokud je zdrojový počítač v pracovní skupině, zadejte jenom uživatelské jméno.

4. Pokud je připojení úspěšné, složky zdrojového počítače se budou na procesovém serveru zobrazovat vzdáleně.

    ![Viditelné složky ze zdrojového počítače](./media/vmware-azure-troubleshoot-push-install/visible-folders-from-source.png)

Pokud připojení neproběhne úspěšně, zkontrolujte prosím, jestli jsou splněné všechny požadavky.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Ověření služby sdílení souborů a tiskáren (ErrorID: 95105 & 95106)

Po kontrole připojení zkontrolujte, jestli je ve vašem virtuálním počítači povolená služba sdílení souborů a tiskáren. Tato nastavení jsou nutná ke zkopírování agenta mobility na zdrojový počítač.

Pro **Windows 2008 R2 a starší verze**:

* Pokud chcete povolit sdílení souborů a tiskáren přes bránu Windows Firewall,
  1. Otevřete **Ovládací panely**  >  **systém a zabezpečení**  >  **brány Windows Firewall**. V levém podokně vyberte možnost **Rozšířená nastavení**  >  **příchozí pravidla** ve stromu konzoly.
  1. Vyhledejte pravidla sdílení souborů a tiskáren (NB-Session-in) a sdílení souborů a tiskáren (SMB-in).
  1. U každého pravidla klikněte pravým tlačítkem na pravidlo a pak klikněte na **Povolit pravidlo**.

* Povolení sdílení souborů s Zásady skupiny:
  1. Klikněte na **Start**, zadejte `gpmc.msc` a vyhledejte.
  1. V navigačním podokně otevřete následující složky: Konfigurace uživatele **zásad místního počítače**  >    >  **šablony pro správu**  >  sdílení sítě **součásti systému Windows**  >  .
  1. V podokně podrobností poklikejte na **zabránit uživatelům v sdílení souborů v rámci svého profilu**.

     Chcete-li zakázat nastavení Zásady skupiny a povolit uživatelům sdílení souborů, vyberte možnost **zakázáno**.

  1. Výběrem **OK** uložte změny.

  Další informace najdete v tématu [Povolení nebo zakázání sdílení souborů s Zásady skupiny](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Pokud chcete povolit sdílení souborů a tiskáren pro novější verze systému Windows nebo Linux, postupujte podle pokynů v části [instalace služby mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů](vmware-azure-install-mobility-service.md) .

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Rozhraní WMI (Windows Management Instrumentation) (WMI) – ověření konfigurace (kód chyby: 95103)

Po kontrole souborové a tiskové služby povolte službu WMI pro privátní, veřejné a doménové profily přes bránu firewall. Tato nastavení jsou nutná k dokončení vzdáleného spuštění na zdrojovém počítači.

Povolení rozhraní WMI:

1. Otevřete **Ovládací panely**  >  **zabezpečení** a vyberte **Brána Windows Firewall**.
1. Vyberte **změnit nastavení** a pak vyberte kartu **výjimky** .
1. V okně **výjimky** zaškrtněte políčko pro rozhraní WMI (Windows Management Instrumentation) (WMI), aby se povolil provoz rozhraní WMI přes bránu firewall.

Přenos rozhraní WMI můžete povolit také pomocí brány firewall z příkazového řádku pomocí následujícího příkazu:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Další články týkající se řešení potíží s rozhraním WMI najdete v následujících článcích.

* [Základní testování služby WMI](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)
* [Řešení potíží s rozhraním WMI](/windows/win32/wmisdk/wmi-troubleshooting)
* [Řešení potíží se skripty WMI a službami WMI](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Nepodporované operační systémy

Dalším běžným důvodem k selhání může být nepodporovaný operační systém. Pro úspěšnou instalaci služby mobility použijte podporovaný operační systém a verzi jádra. Vyhněte se používání privátních oprav.

Pokud chcete zobrazit seznam operačních systémů a verzí jádra podporovaných nástrojem Azure Site Recovery, přečtěte si [dokument s maticí podpory](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nepodporované konfigurace spouštěcího disku (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Spouštěcí a systémové oddíly/svazky nejsou na stejném disku (ErrorID: 95309).

Před verzí 9,20 byly spouštěcí a systémové oddíly/svazky na různých discích nepodporovanou konfigurací. Od [verze 9,20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)je tato konfigurace podporovaná.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>Spouštěcí disk není k dispozici (ErrorID: 95310)

Virtuální počítač bez spouštěcího disku není možné chránit. Spouštěcí disk zajišťuje během operace převzetí služeb při selhání bezproblémové obnovení virtuálního počítače. Při nepřítomnosti spouštěcího disku dojde k selhání při spuštění počítače po převzetí služeb při selhání. Zajistěte, aby virtuální počítač obsahoval spouštěcí disk, a operaci opakujte. Několik spouštěcích disků ve stejném počítači není navíc podporováno.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Na zdrojovém počítači existuje několik spouštěcích disků (ErrorID: 95311).

Virtuální počítač s několika spouštěcími disky není [podporovaná konfigurace](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systémový oddíl na více discích (ErrorID: 95313)

Před verzí 9,20 byl kořenový oddíl nebo nastavení svazku na více discích nepodporovanou konfigurací. Od [verze 9,20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)je tato konfigurace podporovaná.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Ochranu se nepovedlo zapnout, protože název zařízení uvedený v konfiguraci GRUB místo UUID (ErrorID: 95320).

### <a name="possible-cause"></a>Možná příčina

Konfigurační soubory Grand Unified zaváděcího programu (GRUB) (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_ nebo _/etc/default/grub_) můžou obsahovat hodnotu pro **kořen** parametrů a **obnovit** je jako skutečné názvy zařízení místo univerzálně jedinečného identifikátoru (UUID). Site Recovery zmocňuje přístup k identifikátoru UUID, protože názvy zařízení se můžou v rámci restartování virtuálního počítače změnit. Například virtuální počítač nemusí být online se stejným názvem při převzetí služeb při selhání a bude mít za následek problémy.

Například:

- Následující řádek je ze souboru GRUB _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Následující řádek je ze souboru GRUB _/boot/grub/menu.lst_:

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> GRUB řádky obsahují skutečné názvy zařízení pro **kořen** parametrů a **obnovení** , nikoli UUID.

### <a name="how-to-fix"></a>Jak opravit

Názvy zařízení je potřeba nahradit odpovídajícími identifikátory UUID.

1. Vyhledá UUID zařízení provedením příkazu `blkid \<device name>` .

   Například:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Nyní nahraďte název zařízení identifikátorem UUID ve formátu, jako je například `root=UUID=\<UUID>` . Pokud například nahradíte názvy zařízení identifikátorem UUID pro kořen a parametr Resume uvedené v souborech _/boot/grub2/grub.cfg_, _/boot/grub2/grub.cfg_ nebo _/etc/default/grub_ , řádky v souborech vypadají jako na následujícím řádku:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Restartujte ochranu.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Instalace služby mobility byla dokončena s upozorněním na restartování (ErrorID: 95265 & 95266)

Služba Site Recovery mobility má mnoho komponent, z nichž jedna se nazývá ovladač filtru. Ovladač filtru je načten do systémové paměti pouze během restartování systému. Opravy ovladačů filtru je možné realizovat pouze v případě, že je v době restartování systému načten nový ovladač filtru.

> [!IMPORTANT]
> Toto je upozornění a stávající replikace bude fungovat i po novém aktualizace agenta. Můžete se rozhodnout, že budete chtít kdykoli restartovat, pokud chcete získat výhody nového ovladače filtru, ale pokud ho nerestartujete, původní ovladač filtru zůstane funkční. Takže po aktualizaci bez restartování, s výjimkou ovladače filtru, jsou **realizované i výhody dalších vylepšení a oprav v rámci služby mobility**. I když se to doporučuje, není po každém upgradu nutné restartovat počítač. Informace o tom, kdy je restartování povinné, nastavíte tak, že v části aktualizace služby v Azure Site Recovery zadáte možnost [restartovat po upgradu služby mobility](service-updates-how-to.md#reboot-after-mobility-service-upgrade) .

> [!TIP]
>Osvědčené postupy při plánování upgradu během časového období údržby najdete v tématu [Podpora nejnovějšího operačního systému/jádra](service-updates-how-to.md#support-for-latest-operating-systemskernels) v části aktualizace služby v Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>Podpora LVM z verze 9,20

Před verzí 9,20 byl pro datové disky podporován Správce logických svazků (LVM). `/boot`Oddíl by měl být na diskovém oddílu a ne na LVM svazek.

Od [verze 9,20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)se podporuje disk s [operačním systémem na LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) .

## <a name="insufficient-space-errorid-95524"></a>Nedostatek místa (ErrorID: 95524)

Po zkopírování agenta mobility na zdrojový počítač se vyžaduje aspoň 100 MB volného místa. Ujistěte se, že na zdrojovém počítači je požadované množství volného místa, a zkuste operaci zopakovat.

## <a name="low-system-resources"></a>Nedostatek systémových prostředků

Možné ID chyb zjištěné pro tento problém jsou 95572 a 95573. K tomuto problému dochází, když má systém nedostatek dostupné paměti a nemůže přidělit paměť pro instalaci služby mobility. Zajistěte, aby byla pro instalaci dokončena dostatek paměti, aby bylo možné pokračovat v jejím dokončení.

## <a name="vss-installation-failures"></a>Selhání instalace VSS

Instalace služby Stínová kopie svazku (VSS) je součástí instalace agenta mobility. Tato služba se v procesu používá ke generování bodů obnovení konzistentního vzhledem k aplikacím. Selhání při instalaci VSS může nastat z několika důvodů. Chcete-li identifikovat přesné chyby, podívejte se na _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_. Některé běžné chyby a postup řešení jsou zvýrazněny v následující části.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Chyba služby VSS – 2147023170 [0x800706BE] – ukončovací kód 511

Tento problém se nejčastěji zobrazuje, když antivirový software blokuje operace služby Azure Site Recovery Services.

Řešení tohoto problému:

1. Zkontrolujte seznam [vyloučení složky z antivirového programu](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Podle pokynů publikovaných vaším poskytovatelem antivirového programu odblokujte registraci knihovny DLL ve Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Chyba služby VSS 7 [0x7] – ukončovací kód 511

Tato chyba je chyba modulu runtime, která je způsobena tím, že k instalaci služby VSS není dostatek paměti. Zvětšete místo na disku pro úspěšné dokončení této operace.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Chyba služby VSS – 2147023824 [0x80070430] – ukončovací kód 517

K této chybě dochází, když je Azure Site Recovery služba poskytovatele VSS [označena k odstranění](/previous-versions/ms838153(v=msdn.10)). Pokuste se ručně nainstalovat službu VSS na zdrojový počítač spuštěním následujícího příkazu:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Chyba služby VSS – 2147023841 [0x8007041F] – ukončovací kód 512

K této chybě dojde, pokud je Azure Site Recovery databáze služby poskytovatele VSS [uzamčena](/previous-versions/ms833798(v=msdn.10)). Pokuste se ručně nainstalovat VSS na zdrojový počítač spuštěním následujícího příkazu z příkazového řádku:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

V případě selhání ověřte, zda je v **počátečním** stavu nějaký antivirový program nebo jiné služby zablokované. Proces ve **výchozím** stavu může zachovat zámek u databázových služeb. Dojde k selhání při instalaci poskytovatele služby VSS. Zajistěte, aby ve **výchozím** stavu nebyla žádná služba, a pak zkuste operaci zopakovat.

### <a name="vss-exit-code-806"></a>Ukončovací kód VSS 806

K této chybě dochází, pokud uživatelský účet použitý k instalaci nemá oprávnění ke spuštění `CSScript` příkazu. Poskytněte uživatelskému účtu potřebná oprávnění ke spuštění skriptu a operaci opakujte.

### <a name="other-vss-errors"></a>Další chyby VSS

Pokuste se na zdrojový počítač ručně nainstalovat službu poskytovatele VSS spuštěním následujícího příkazu z příkazového řádku:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>Chyba služby VSS – 0x8004E00F

K této chybě obvykle dochází během instalace agenta mobility z důvodu problémů v nástroji `DCOM` a `DCOM` je v kritickém stavu.

K určení příčiny chyby použijte následující postup.

### <a name="examine-the-installation-logs"></a>Kontrola protokolů instalace

1. Otevřete Instalační protokol umístěný na adrese _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_.
2. Následující chyba indikuje tento problém:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Řešení tohoto problému:

Obraťte se na [tým platformy Microsoft Windows](https://aka.ms/Windows_Support) , kde získáte pomoc při řešení problému s modelem DCOM.

Po vyřešení problému modelu DCOM přeinstalujte Azure Site Recovery zprostředkovatele služby VSS ručně pomocí následujícího příkazu z příkazového řádku:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Pokud konzistence aplikací není pro vaše požadavky na zotavení po havárii zásadní, můžete instalaci poskytovatele služby VSS obejít.

Postup při obejít instalaci poskytovatele služby VSS Azure Site Recovery a ručně nainstalovat Azure Site Recovery po instalaci zprostředkovatele služby VSS:

1. Nainstalujte službu mobility. Instalace se nezdaří v kroku: **Konfigurace po instalaci**.
1. Postup při obejít instalaci stínové kopie svazku:
   1. Otevřete instalační adresář služby Azure Site Recovery mobility v umístění:

      _C:\Program Files (x86) \Microsoft Azure Site Recovery\agent_

   1. Přidejte Azure Site Recovery instalační skripty poskytovatele služby VSS _InMageVSSProvider_Install_ a _InMageVSSProvider_Uninstall. cmd_ , aby se vždy úspěšně zdařily, a to přidáním následujících řádků:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Proveďte ruční instalaci agenta mobility.
1. Po úspěšné instalaci a přechodu k dalšímu kroku **nakonfigurujte** a odeberte přidané řádky.
1. Chcete-li nainstalovat poskytovatele služby VSS, otevřete příkazový řádek jako správce a spusťte následující příkaz:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Ověřte, zda je zprostředkovatel služby Stínová kopie svazku (VSS) Azure Site Recovery nainstalován jako služba v rámci služeb systému Windows. Otevřete službu Component Service konzoly MMC a potvrďte, že je uveden poskytovatel služby Stínová kopie svazku.
1. Pokud se instalace poskytovatele služby VSS stále nezdařila, pracujte s technickou podporou pro řešení chyb oprávnění v rozhraní CAPI2 (Cryptographic Application Programming Interface).

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Instalace poskytovatele služby Stínová kopie svazku se nezdařila, protože Clusterová služba je povolena v počítači bez clusteru.

Tento problém způsobí selhání instalace agenta Azure Site Recovery mobility během instalace Azure Site Recovery poskytovatele služby VSS. Příčinou této chyby je, že došlo k potížím s tím `COM+` , že brání instalaci poskytovatele VSS.

### <a name="to-identify-the-issue"></a>Identifikace problému

V protokolu umístěném na konfiguračním serveru na adrese _C:\ProgramData\ASRSetupLogs\UploadedLogs \<date-time> UA_InstallLogFile. log_ najdete následující výjimku:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Řešení tohoto problému:

1. Ověřte, zda je tento počítač počítač bez clusteru a zda nejsou používány součásti clusteru.
1. Pokud se komponenty nepoužívají, odeberte z počítače součásti clusteru.

## <a name="drivers-are-missing-on-the-source-server"></a>Na zdrojovém serveru chybí ovladače.

Pokud se instalace agenta mobility nezdařila, zkontrolujte protokoly v části _C:\ProgramData\ASRSetupLogs_ a zjistěte, jestli některé z požadovaných ovladačů v některých sadách ovládacích prvků chybí.

Řešení tohoto problému:

1. Pomocí Editoru registru `regedit.msc` , jako je, otevřete registr.
1. Otevřete `HKEY_LOCAL_MACHINE\SYSTEM` uzel.
1. V `SYSTEM` uzlu vyhledejte sady ovládacích prvků.
1. Otevřete jednotlivé sady ovládacích prvků a ověřte, zda jsou k dispozici následující ovladače systému Windows:

   * Ovladač
   * VMBus
   * Storflt
   * Storvsc
   * Intelide

1. Přeinstalujte všechny chybějící ovladače.

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](vmware-azure-tutorial.md) o tom, jak nastavit zotavení po havárii pro virtuální počítače VMware.
