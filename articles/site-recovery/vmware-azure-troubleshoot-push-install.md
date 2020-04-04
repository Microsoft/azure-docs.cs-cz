---
title: Poradce při potížích s nabízenou instalací služby Mobility Service pomocí azure site recovery
description: Poradce při potížích s instalací služby Mobility Services při povolení replikace pro zotavení po havárii pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 1afd931249d4dbeda2b4b25f822837e2a564f959
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656322"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Poradce při potížích s nabízenou instalací služby Mobility

Instalace služby Mobility je klíčovým krokem k povolení replikace. Úspěch tohoto kroku závisí na splnění předpokladů a práci s podporovanými konfiguracemi. Nejčastější chyby, kterým můžete čelit během instalace služby Mobility, jsou způsobeny:

* [Chyby pověření/oprávnění](#credentials-check-errorid-95107--95108)
* [Selhání přihlášení](#login-failures-errorid-95519-95520-95521-95522)
* [Chyby připojení](#connectivity-failure-errorid-95117--97118)
* [Chyby při sdílení souborů a tiskáren](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Selhání služby WMI (WMI) služby WMI](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nepodporované operační systémy](#unsupported-operating-systems)
* [Nepodporované konfigurace spouštění](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Selhání instalace služby Stínová kopie svazku (VSS)](#vss-installation-failures)
* [Název zařízení v konfiguraci GRUB namísto uuid zařízení](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Svazek Správce logických svazků (LVM)](#lvm-support-from-920-version)
* [Upozornění na restartování](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Když povolíte replikaci, Azure Site Recovery se pokusí nainstalovat agenta služby mobility na vašem virtuálním počítači (VM). V rámci tohoto procesu se konfigurační server pokusí připojit k virtuálnímu počítači a zkopírovat agenta. Chcete-li povolit úspěšnou instalaci, postupujte podle podrobných pokynů pro řešení potíží.

## <a name="credentials-check-errorid-95107--95108"></a>Kontrola pověření (ErrorID: 95107 & 95108)

Ověřte, zda je uživatelský účet zvolený během povolit replikaci platný a přesný. Azure Site Recovery vyžaduje **root** účet nebo uživatelský účet s **oprávněními správce** k provedení nabízené instalace. V opačném případě bude nabízená instalace blokována ve zdrojovém počítači.

V systému Windows (**chyba 95107**) ověřte, zda má uživatelský účet přístup správce ve zdrojovém počítači, a to buď s místním účtem, nebo s účtem domény. Pokud nepoužíváte účet domény, je třeba zakázat řízení vzdáleného přístupu uživatelů v místním počítači.

* Ruční přidání klíče registru, který zakáže vzdálený přístup uživatelů:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Přidejte `DWORD`nový :`LocalAccountTokenFilterPolicy`
  * Nastavte hodnotu na`1`

* Chcete-li přidat klíč registru, spusťte z příkazového řádku následující příkaz:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Pro Linux (**chyba 95108**) musíte zvolit **kořenový** účet pro úspěšnou instalaci agenta služby Mobility. Kromě toho by měly být spuštěny služby SSH File Transfer Protocol (SFTP). Povolení ověřování podsystémem SFTP a hesla v _souboru sshd_config:_

1. Přihlaste se jako uživatel **root**.
1. Přejděte na _soubor /etc/ssh/sshd_config_, vyhledejte řádek, který začíná písmenem `PasswordAuthentication`.
1. Odkomentujte řádek a změňte hodnotu na `yes`.
1. Najděte řádek, který `Subsystem`začíná na , a odkomentujte řádek.
1. Restartujte `sshd` službu.

Chcete-li změnit přihlašovací údaje zvoleného uživatelského účtu, postupujte [podle těchto pokynů](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Selhání nedostatečných oprávnění (ErrorID: 95517)

Pokud uživatel zvolený k instalaci agenta mobility nemá oprávnění správce, nebude konfigurační server/horizontální navýšení kapacity procesního serveru moci kopírovat software agenta mobility do zdrojového počítače. Tato chyba je výsledkem selhání odepřenpřístup. Ujistěte se, že uživatelský účet má oprávnění správce.

Chcete-li změnit přihlašovací údaje zvoleného uživatelského účtu, postupujte [podle těchto pokynů](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Selhání nedostatečných oprávnění (ErrorID: 95518)

Pokud se při pokusu o přihlášení ke zdrojovému počítači nezdaří vytvoření vztahu důvěryhodnosti domény mezi primární doménou a pracovní stanicí, instalace agenta mobility se nezdaří s chybou ID 95518. Ujistěte se, že uživatelský účet použitý k instalaci agenta mobility má oprávnění správce pro přihlášení prostřednictvím primární domény zdrojového počítače.

Chcete-li změnit přihlašovací údaje k vybranému uživatelskému účtu, postupujte [podle následujících pokynů](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Selhání přihlášení (ErrorID: 95519, 95520, 95521, 95522)

Tato část popisuje chybové zprávy pověření a přihlášení.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Pověření uživatelského účtu byla zakázána (ErrorID: 95519)

Uživatelský účet zvolený během povolení replikace byl zakázán. Chcete-li povolit uživatelský účet, podívejte se do [tohoto článku](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) nebo spusťte následující příkaz nahrazením _textového uživatelského jména_ skutečným uživatelským jménem.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Pověření uzamčena z důvodu několika neúspěšných pokusů o přihlášení (ErrorID: 95520)

Více neúspěšných pokusů o přístup k počítači uzamkne uživatelský účet. Selhání může být způsobeno:

* Pověření poskytnutá během instalace konfigurace jsou nesprávná.
* Uživatelský účet zvolený během povolit replikaci je chybný.

Upravte pověření zvolená podle [těchto pokynů](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) a opakujte operaci.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>Přihlašovací servery nejsou ve zdrojovém počítači k dispozici (ErrorID: 95521)

K této chybě dochází, pokud přihlašovací servery nejsou k dispozici ve zdrojovém počítači. Pokud přihlašovací servery nejsou k dispozici, požadavky na přihlášení se nezdaří a agent mobility nelze nainstalovat. Chcete-li se úspěšně přihlásit, ujistěte se, že přihlašovací servery jsou k dispozici ve zdrojovém počítači a spusťte službu Netlogon. Další informace naleznete v tématu [Windows Logon Scenarios](/windows-server/security/windows-authentication/windows-logon-scenarios).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Přihlašovací služba není spuštěna ve zdrojovém počítači (ErrorID: 95522)

Přihlašovací služba není spuštěna ve zdrojovém počítači a způsobila selhání žádosti o přihlášení. Agenta mobility nelze nainstalovat. Chcete-li chybu vyřešit, spusťte `Netlogon` službu ve zdrojovém počítači jedním z následujících způsobů:

* Chcete-li `Netlogon` službu spustit z příkazového řádku, spusťte příkaz `net start Netlogon`.
* Ve Správci úloh `Netlogon` spusťte službu.

## <a name="connectivity-failure-errorid-95117--97118"></a>Selhání připojení (ErrorID: 95117 & 97118)

Konfigurační server/horizontální navýšení kapacity procesu server se pokusí připojit ke zdrojovému virtuálnímu počítače k instalaci agenta mobility. K této chybě dochází, pokud zdrojový počítač není dostupný, protože existují problémy s připojením k síti.

Chcete-li chybu vyřešit:

* Ujistěte se, že můžete ping zdrojového počítače z konfiguračního serveru. Pokud jste během povolování replikace zvolili horizontální navýšení kapacity, ujistěte se, že můžete pingovat zdrojový počítač z procesního serveru.

* Z příkazového řádku počítače `Telnet` zdrojového serveru použijte příkaz ping na konfiguračním serveru nebo horizontálnínavýšení kapacity procesního serveru na portu HTTPS 135, jak je znázorněno v následujícím příkazu. Tento příkaz zkontroluje, zda existují problémy s připojením k síti nebo problémy s blokováním portů brány firewall.

  `telnet <CS/ scale-out PS IP address> <135>`

* Navíc pro virtuální počítač s Linuxem:
  * Zkontrolujte, zda jsou nainstalovány nejnovější balíčky OpenSSH, OpenSSH Server a OpenSSL.
  * Zkontrolujte a ujistěte se, že secure shell (SSH) je povolena a běží na portu 22.
  * Služby SFTP by měly být spuštěny. Povolení ověřování podsystémem SFTP a hesla v _souboru sshd_config:_

    1. Přihlaste se jako uživatel **root**.
    1. Přejděte do souboru _/etc/ssh/sshd_config,_ vyhledejte řádek, který začíná písmenem `PasswordAuthentication`.
    1. Odkomentujte řádek a změňte hodnotu na `yes`.
    1. Najděte řádek, který `Subsystem`začíná na , a odkomentujte řádek
    1. Restartujte `sshd` službu.

* Pokus o připojení mohl selhat, pokud po určité době neexistují žádné správné odpovědi nebo se nezdařilo navázání připojení, protože připojený hostitel neodpověděl.
* Může se jedná o problém související s připojením/sítí/doménou. Může to být také proto, že název DNS řeší problém nebo problém vyčerpání portu TCP. Zkontrolujte, zda ve vaší doméně nejsou nějaké takové známé problémy.

## <a name="connectivity-failure-errorid-95523"></a>Selhání připojení (ErrorID: 95523)

K této chybě dochází, pokud není nalezena síť, ve které se nachází zdrojový počítač, mohla být odstraněna nebo již není k dispozici. Jediným způsobem, jak chybu vyřešit, je zajistit, aby síť existovala.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Kontrola služeb sdílení souborů a tiskáren (ErrorID: 95105 & 95106)

Po kontrole připojení ověřte, jestli je ve virtuálním počítači povolena služba sdílení souborů a tiskáren. Tato nastavení jsou nutné ke zkopírování agenta mobility do zdrojového počítače.

Pro **systém Windows 2008 R2 a předchozí verze**:

* Chcete-li povolit sdílení souborů a tisku prostřednictvím brány Windows Firewall,
  1. Otevřete systém **Ovládacích panelů** > **a bránu** > **Windows Firewall**zabezpečení . V levém podokně vyberte **upřesnit nastavení** > **Příchozí pravidla ve** stromu konzoly.
  1. Vyhledejte pravidla Sdílení souborů a tiskáren (NB-Session-In) a Sdílení souborů a tiskáren (SMB-In).
  1. U každého pravidla klepněte pravým tlačítkem myši na pravidlo a potom klepněte na příkaz **Povolit pravidlo**.

* Povolení sdílení souborů pomocí zásad skupiny:
  1. Přejděte na `gpmc.msc` úvodní **obrazovku**, zadejte a vyhledejte.
  1. V navigačním podokně otevřete následující složky: **Local Computer Policy** > **User Configuration** > **Administrative Templates** > **Windows Components** > **Network Sharing**.
  1. V podokně podrobností poklepejte na **položku Zabránit uživatelům ve sdílení souborů v jejich profilu**.

     Chcete-li zakázat nastavení zásad skupiny a povolit uživateli možnost sdílení souborů, vyberte **možnost Zakázáno**.

  1. Výběrem **OK** uložte změny.

  Další informace najdete [v tématu Povolení nebo zakázání sdílení souborů pomocí zásad skupiny](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Chcete-li povolit sdílení souborů a tiskáren pro novější verze systému Windows nebo Linux, postupujte podle pokynů v [části Instalace služby Mobility pro zotavení virtuálních zařízení VMware a fyzických serverů v oblasti rychlého](vmware-azure-install-mobility-service.md) nasazení .

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Kontrola konfigurace wmi (WMI) (Kód chyby: 95103)

Po kontrole služeb souborů a tiskáren povolte službu WMI pro soukromé, veřejné a doménové profily prostřednictvím brány firewall. Tato nastavení jsou nutné k dokončení vzdáleného spuštění ve zdrojovém počítači.

Povolení wmi:

1. Přejděte na položku**Zabezpečení** **ovládacích panelů** > a vyberte **bránu Windows Firewall**.
1. Vyberte **Změnit nastavení** a pak vyberte kartu **Výjimky.**
1. V okně Výjimky zaškrtněte políčko u služby WMI (WMI) pro službu WMI, chcete-li povolit přenos **služby** WMI přes bránu firewall.

Můžete také povolit přenos služby WMI prostřednictvím brány firewall z příkazového řádku pomocí následujícího příkazu:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Další články o řešení potíží se službou WMI naleznete v následujících článcích.

* [Základní testování wmi](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Řešení potíží se službou WMI](/windows/win32/wmisdk/wmi-troubleshooting)
* [Řešení problémů se skripty služby WMI a službami služby WMI](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Nepodporované operační systémy

Dalším běžným důvodem selhání může být z důvodu nepodporovaného operačního systému. Pro úspěšnou instalaci služby Mobility použijte podporovaný operační systém a verzi jádra. Vyhněte se použití soukromých záplat.

Seznam operačních systémů a verzí jádra podporovaných službou Azure Site Recovery najdete v [dokumentu matice podpory](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nepodporované konfigurace spouštěcího disku (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Spouštěcí a systémové oddíly / svazky nejsou stejný disk (ErrorID: 95309)

Před verzí 9.20 byly spouštěcí a systémové oddíly/svazky na různých discích nepodporovanou konfigurací. Počínaje [verzí 9.20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)je tato konfigurace podporována.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>Spouštěcí disk není k dispozici (ErrorID: 95310)

Virtuální počítač bez spouštěcího disku nelze chránit. Spouštěcí disk zajišťuje hladké obnovení virtuálního počítače během operace převzetí služeb při selhání. Absence spouštěcího disku má za následek selhání spuštění počítače po převzetí služeb při selhání. Ujistěte se, že virtuální počítač obsahuje spouštěcí disk a opakujte operaci. Také více spouštěcídisky ve stejném počítači nejsou podporovány.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Více spouštěcích disků přítomných ve zdrojovém počítači (ErrorID: 95311)

Virtuální počítač s více spouštěcími disky není [podporovanou konfigurací](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systémový oddíl na více discích (ErrorID: 95313)

Před verzí 9.20 byl kořenový oddíl nebo nastavení svazku na více discích nepodporovanou konfigurací. Počínaje [verzí 9.20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)je tato konfigurace podporována.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Povolení ochrany se nezdařilo jako název zařízení uvedený v konfiguraci GRUB namísto UUID (ErrorID: 95320)

### <a name="possible-cause"></a>Možná příčina

Konfigurační soubory Grand Unified Bootloader (GRUB) (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_nebo _/etc/default/grub_) mohou obsahovat hodnotu pro **kořen** parametrů a **pokračovat** jako skutečné názvy zařízení namísto univerzálně jedinečného identifikátoru (UUID). Obnovení webu vyžaduje přístup UUID jako názvy zařízení může změnit po restartování virtuálního počítače. Například virtuální počítač nemusí být online se stejným názvem při převzetí služeb při selhání a to má za následek problémy.

Například:

- Následující řádek je ze souboru GRUB _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Následující řádek je ze souboru GRUB _/boot/grub/menu.lst_:

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> Řádky GRUB obsahují skutečné názvy zařízení pro **kořenové** parametry a **obnovení** spíše než UUID.

### <a name="how-to-fix"></a>Jak opravit

Názvy zařízení je potřeba nahradit odpovídajícími identifikátory UUID.

1. Najít UUID zařízení provedením příkazu `blkid \<device name>`.

   Například:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Nyní nahraďte název zařízení jeho UUID ve formátu jako `root=UUID=\<UUID>`. Například, pokud nahradíme názvy zařízení s UUID pro root a resume parametr uvedený v souborech _/boot/grub2/grub.cfg_, _/boot/grub2/grub.cfg_, nebo _/etc/default/grub_ pak řádky v souborech vypadají jako následující řádek:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Restartujte ochranu.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Instalace služby Mobility byla dokončena s upozorněním na restartování (ErrorID: 95265 & 95266)

Služba Site Recovery Mobility má mnoho součástí, z nichž jedna se nazývá ovladač filtru. Ovladač filtru je načten do systémové paměti pouze během restartování systému. Opravy ovladačů filtru lze realizovat pouze při načtení nového ovladače filtru při restartování systému.

> [!IMPORTANT]
> Toto je upozornění a existující replikace bude fungovat i po aktualizaci nového agenta. Můžete se rozhodnout restartovat kdykolibudete chtít získat výhody nového ovladače filtru, ale pokud nerestartujete, starý ovladač filtru pokračuje v práci. Takže po aktualizaci bez restartování, s výjimkou ovladače filtru, **výhody dalších vylepšení a oprav ve službě Mobility si realizované**. I když je doporučeno, není povinné restartovat po každém upgradu. Informace o tom, kdy je restartování povinné, nastavíte oddíl [Restartování po upgradu služby Mobility](service-updates-how-to.md#reboot-after-mobility-service-upgrade) v aktualizacích služby v azure site recovery.

> [!TIP]
>Doporučené postupy při plánování upgradů během okna údržby najdete v [tématu Podpora nejnovějších aktualizací operačního systému/jádra](service-updates-how-to.md#support-for-latest-operating-systemskernels) v části Aktualizace služby v azure site recovery.

## <a name="lvm-support-from-920-version"></a>Podpora LVM od verze 9.20

Před verzí 9.20 byl logický správce svazků (LVM) podporován pouze pro datové disky. Oddíl `/boot` by měl být na disku oddíla a nikoli svazek LVM.

Počínaje [verzí 9.20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)je podporován [disk operačního systému na LVM.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)

## <a name="insufficient-space-errorid-95524"></a>Nedostatek místa (ErrorID: 95524)

Při zkopírování agenta mobility do zdrojového počítače je vyžadováno alespoň 100 MB volného místa. Ujistěte se, že zdrojový počítač má požadované množství volného místa a opakujte operaci.

## <a name="vss-installation-failures"></a>Selhání instalace VSS

Instalace služby VSS (Volume Shadow Copy Service) je součástí instalace agenta mobility. Tato služba se používá v procesu ke generování konzistentní body obnovení aplikace. K chybám během instalace vsystému VSS může dojít z několika důvodů. Chcete-li identifikovat přesné chyby, přejdete na odkaz _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_. Některé běžné chyby a kroky řešení jsou zvýrazněny v následující části.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Chyba VSS -2147023170 [0x800706BE] - ukončovací kód 511

Tento problém se nejčastěji používá, když antivirový software blokuje provoz služeb Azure Site Recovery.

Řešení tohoto problému:

1. Prohlédněte si seznam [vyloučení složek z programu Antivirus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Chcete-li odblokovat registraci knihovny DLL v systému Windows, postupujte podle pokynů publikovaných poskytovatelem antivirového softwaru.

### <a name="vss-error-7-0x7---exit-code-511"></a>Chyba VSS 7 [0x7] - ukončovací kód 511

Tato chyba je chyba za běhu, která je způsobena, protože není dostatek paměti pro instalaci VSS. Zvětšete místo na disku pro úspěšné dokončení této operace.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Chyba VSS -2147023824 [0x80070430] - ukončovací kód 517

K této chybě dochází, když je služba Zprostředkovatel obnovení webu Azure [označena k odstranění](/previous-versions/ms838153(v=msdn.10)). Zkuste nainstalovat službu VSS ručně do zdrojového počítače spuštěním následujícího příkazu:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Chyba VSS -2147023841 [0x8007041F] - ukončovací kód 512

K této chybě dochází, když je [zamknutá](/previous-versions/ms833798(v=msdn.10))databáze služeb zprostředkovatele Azure Site Recovery . Pokuste se nainstalovat službu VSS ručně do zdrojového počítače spuštěním následujícího příkazu z příkazového řádku:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Pokud dojde k selhání, zkontrolujte, zda se antivirový program nebo jiné služby zasekly ve stavu **Spuštění.** Proces ve stavu **Spuštění** můžete zachovat zámek na databázové služby. To povede k selhání při instalaci zprostředkovatele VSS. Ujistěte se, že žádná služba je ve stavu **Spuštění** a opakujte výše uvedené operace.

### <a name="vss-exit-code-806"></a>Ukončovací kód VSS 806

K této chybě dochází, pokud uživatelský účet použitý pro `CSScript` instalaci nemá oprávnění ke spuštění příkazu. Poskytněte uživateli potřebná oprávnění ke spuštění skriptu a opakování operace.

### <a name="other-vss-errors"></a>Další chyby VSS

Pokuste se nainstalovat službu poskytovatele Služby VSS ručně do zdrojového počítače spuštěním následujícího příkazu z příkazového řádku:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>Chyba VSS - 0x8004E00F

K této chybě obvykle dochází během instalace agenta `DCOM` mobility `DCOM` z důvodu problémů v a je v kritickém stavu.

K určení příčiny chyby použijte následující postup.

### <a name="examine-the-installation-logs"></a>Zkontrolujte protokoly instalace

1. Otevřete protokol instalace umístěný na adrese _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_.
2. Přítomnost následující chyby označuje tento problém:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Chcete-li tento problém vyřešit:

Obraťte se na [tým platformy Microsoft Windows](https://aka.ms/Windows_Support) získat pomoc při řešení problému DCOM.

Po vyřešení problému S COM přeinstalujte zprostředkovatele Azure Site Recovery VSS ručně pomocí následujícího příkazu z příkazového řádku:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Pokud konzistence aplikace není důležitá pro vaše požadavky na zotavení po havárii, můžete obejít instalaci zprostředkovatele VSS.

Pokud chcete obejít instalaci zprostředkovatele VSS pro obnovení webu Azure a ručně nainstalovat po instalaci zprostředkovatele Azure Site Recovery VSS zprostředkovatele webu:

1. Nainstalujte službu Mobility. Instalace se nezdaří v kroku: **Konfigurace po instalaci**.
1. Chcete-li obejít instalaci VSS:
   1. Otevřete instalační adresář služby Azure Site Recovery Service, který se nachází na adrese:

      _C:\Program Files (x86)\Microsoft Azure Site Recovery\agent_

   1. Upravte instalační skripty zprostředkovatele Azure Site Recovery VSS _InMageVSSProvider_Install_ a _InMageVSSProvider_Uninstall.cmd_ tak, aby vždy uspěly přidáním následujících řádků:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Proveďte ruční instalaci agenta mobility.
1. Po dokončení instalace a přejde meze k dalšímu kroku **Konfigurace**, odeberte řádky, které jste přidali.
1. Chcete-li nainstalovat zprostředkovatele služby VSS, otevřete příkazový řádek jako správce a spusťte následující příkaz:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Ověřte, že zprostředkovatel VSS pro obnovení webu Azure je nainstalovaný jako služba ve službách Windows. Otevřete konzolu MMC služby komponent a ověřte, zda je uveden zprostředkovatel Služby VSS.
1. Pokud instalace zprostředkovatele VSS stále selhává, pracujte s technickou podporou na vyřešení chyb oprávnění v rozhraní CAPI2 (Cryptographic Application Programming Interface).

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Instalace zprostředkovatele VSS se nezdaří, protože clusterová služba je povolena v počítači bez clusteru.

Tento problém způsobí, že instalace agenta obnovení webu Azure nezdaří během instalace zprostředkovatele Obnovení webu Azure. Selhání je proto, že je `COM+` problém s, který zabraňuje instalaci zprostředkovatele VSS.

### <a name="to-identify-the-issue"></a>Chcete-li identifikovat problém

V protokolu umístěném na konfiguračním serveru _c:\ProgramData\ASRSetupLogs\UploadedLogs\<date-time>UA_InstallLogFile.log_ najdete následující výjimku:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Chcete-li tento problém vyřešit:

1. Ověřte, zda se jedná o počítač bez clusteru a zda nejsou používány součásti clusteru.
1. Pokud součásti nejsou používány, odeberte součásti clusteru ze počítače.

## <a name="drivers-are-missing-on-the-source-server"></a>Na zdrojovém serveru chybí ovladače.

Pokud se instalace agenta mobility nezdaří, zkontrolujte protokoly v části _C:\ProgramData\ASRSetupLogs_ a zjistěte, zda v některých sadách ovládacích prvku chybí některé požadované ovladače.

Chcete-li tento problém vyřešit:

1. Pomocí editoru registru, například `regedit.msc`, otevřete registr.
1. Otevřete `HKEY_LOCAL_MACHINE\SYSTEM` uzel.
1. V `SYSTEM` uzlu vyhledejte sady ovládacích prvku.
1. Otevřete každou sadu ovládacích prvku a ověřte, zda jsou k dispozici následující ovladače systému Windows:

   * Atapi
   * Vmbus
   * Storflt (Fr.)
   * Storvsc (Stova
   * Intelide

1. Přeinstalujte všechny chybějící ovladače.

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](vmware-azure-tutorial.md) o tom, jak nastavit zotavení po havárii pro virtuální virtuální měny VMware.
