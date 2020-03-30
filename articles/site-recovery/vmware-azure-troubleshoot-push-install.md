---
title: Poradce při potížích s nabízenou instalací služby Mobility Service pomocí azure site recovery f
description: Poradce při potížích s instalací služby Mobility Services při povolení replikace pro zotavení po havárii pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 3646499ad2104566cb82f3f26c6b55d05f84dc7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953778"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Poradce při potížích s nabízenou instalací služby Mobility Service 

Instalace služby Mobility je klíčovým krokem během povolení replikace. Úspěch tohoto kroku závisí výhradně na splnění požadavků a práci s podporovanými konfiguracemi. Nejčastější chyby, kterým čelíte během instalace služby Mobility, jsou způsobeny:

* [Chyby pověření/oprávnění](#credentials-check-errorid-95107--95108)
* [Selhání přihlášení](#login-failures-errorid-95519-95520-95521-95522)
* [Chyby připojení](#connectivity-failure-errorid-95117--97118)
* [Chyby při sdílení souborů a tiskáren](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Selhání systému WMI](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nepodporované operační systémy](#unsupported-operating-systems)
* [Nepodporované konfigurace spuštění](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Selhání instalace VSS](#vss-installation-failures)
* [Název zařízení v konfiguraci GRUB namísto uuid zařízení](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Objem LVM](#lvm-support-from-920-version)
* [Upozornění na restartování](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Když povolíte replikaci, Azure Site Recovery se pokusí tlačit nainstalovat agenta služby mobility na vašem virtuálním počítači. V rámci tohoto konfiguračního serveru se pokusí připojit k virtuálnímu počítači a zkopírovat agenta. Chcete-li povolit úspěšnou instalaci, postupujte podle pokynů pro řešení potíží krok za krokem uvedených níže.

## <a name="credentials-check-errorid-95107--95108"></a>Kontrola pověření (ErrorID: 95107 & 95108)

* Ověřte, zda je uživatelský účet zvolený během povolit replikaci **platný a přesný**.
* Azure Site Recovery vyžaduje **ROOT** účet nebo uživatelský účet s **oprávněními správce** k provádění nabízené instalace. V opačném případě bude nabízená instalace blokována ve zdrojovém počítači.
  * V systému Windows (**chyba 95107**) ověřte, zda má uživatelský účet přístup pro správu, místní nebo doménový, ve zdrojovém počítači.
  * Pokud nepoužíváte účet domény, je třeba zakázat řízení vzdáleného přístupu uživatelů v místním počítači.
    * Chcete-li zakázat řízení vzdáleného přístupu uživatelů, přidejte v části HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System registry key nový dword: LocalAccountTokenFilterPolicy. Nastavte její hodnotu na 1. Chcete-li provést tento krok, spusťte následující příkaz z příkazového řádku:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Pro Linux (**chyba 95108**) musíte zvolit kořenový účet pro úspěšnou instalaci agenta mobility. Kromě toho by měly být spuštěny služby SFTP. Povolení ověřování podsystémem SFTP a hesla v souboru sshd_config:
    1. Přihlaste se jako uživatel root.
    2. Přejděte do souboru /etc/ssh/sshd_config, najděte řádek, který začíná ověřováním hesla.
    3. Odkomentujte řádek a změňte hodnotu na ano.
    4. Najděte řádek, který začíná subsystémem, a odkomentujte řádek.
    5. Restartujte službu sshd.

Chcete-li upravit přihlašovací údaje k vybranému uživatelskému účtu, postupujte [podle pokynů uvedených zde](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Selhání nedostatečných oprávnění (ErrorID: 95517)

Pokud uživatel zvolený k instalaci agenta mobility nemá oprávnění správce, nebude povoleno kopírovat software agenta mobility do zdrojového počítače. Takže tato chyba je výsledkem odepření přístupu selhání. Ujistěte se, že uživatelský účet má oprávnění správce.

Chcete-li upravit přihlašovací údaje k vybranému uživatelskému účtu, postupujte [podle pokynů uvedených zde](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Selhání nedostatečných oprávnění (ErrorID: 95518)

Pokud se při pokusu o přihlášení ke zdrojovému počítači nezdaří vytvoření vztahu důvěryhodnosti domény mezi primární doménou a pracovní stanicí, instalace agenta mobility se nezdaří s chybou ID 95518. Proto se ujistěte, že uživatelský účet používaný k instalaci agenta mobility má oprávnění správce k přihlášení prostřednictvím primární domény zdrojového počítače.

Chcete-li upravit přihlašovací údaje k vybranému uživatelskému účtu, postupujte [podle pokynů uvedených zde](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Selhání přihlášení (ErrorID: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Pověření uživatelského účtu byla zakázána (ErrorID: 95519)

Uživatelský účet zvolený během povolit replikaci byl zakázán. Chcete-li povolit uživatelský účet, podívejte se do [článku zde](https://aka.ms/enable_login_user) nebo spusťte následující příkaz nahrazením textového *uživatelského jména* skutečným uživatelským jménem.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Pověření uzamčena z důvodu několika neúspěšných pokusů o přihlášení (ErrorID: 95520)

Více neúspěšných pokusů o přístup k počítači uzamkne uživatelský účet. Selhání může být způsobeno:

* Pověření poskytnutá během instalace konfigurace jsou nesprávná nebo
* Uživatelský účet zvolený během povolení replikace je chybný.

Upravte tedy pověření zvolená podle [pokynů uvedených zde](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) a opakujte operaci po nějaké době.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Přihlašovací servery nejsou ve zdrojovém počítači k dispozici (ErrorID: 95521)

K této chybě dochází, pokud přihlašovací servery nejsou k dispozici ve zdrojovém počítači. Nedostupnost přihlašovacích serverů povede k selhání požadavku na přihlášení, a proto nelze nainstalovat agenta mobility. Chcete-li úspěšné přihlášení, ujistěte se, že přihlašovací servery jsou k dispozici ve zdrojovém počítači a spusťte přihlašovací službu. Podrobné pokyny naleznete v části KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) Err Msg: Aktuálně nejsou k dispozici žádné přihlašovací servery.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Přihlašovací služba není spuštěna ve zdrojovém počítači (ErrorID: 95522)

Přihlašovací služba není spuštěna ve zdrojovém počítači a způsobila selhání žádosti o přihlášení. Agent a mobilita tedy nelze nainstalovat. Chcete-li vyřešit, ujistěte se, že přihlašovací služba je spuštěna na zdrojovém počítači pro úspěšné přihlášení. Chcete-li spustit přihlašovací službu, spusťte příkaz "net start Logon" z příkazového řádku nebo spusťte službu NetLogon ze Správce úloh.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Selhání připojení (ErrorID: 95117 & 97118)**

Konfigurační server/ horizontální navýšení kapacity procesu server se pokusí připojit ke zdrojovému virtuálnímu počítače k instalaci agenta mobility. K této chybě dochází, pokud zdrojový počítač není dostupný z důvodu problémů s připojením k síti. Chcete-li vyřešit,

* Ujistěte se, že můžete ping zdrojového počítače z konfiguračního serveru. Pokud jste během povolování replikace zvolili horizontální navýšení kapacity, ujistěte se, že můžete pingovat zdrojový počítač ze serveru procesu.
  * Z příkazového řádku počítače zdrojového serveru použijte program Telnet k příkazu ping na konfigurační mši/ horizontální navýšení kapacity procesního serveru s portem https (135), jak je znázorněno níže, abyste zjistili, zda existují problémy s připojením k síti nebo problémy s blokováním portů brány firewall.

     `telnet <CS/ scale-out PS IP address> <135>`
* Navíc pro **virtuální počítač s Linuxem**,
  * Zkontrolujte, zda jsou nainstalovány nejnovější balíčky openssh, openssh-server a openssl.
  * Zkontrolujte a ujistěte se, že secure shell (SSH) je povolena a běží na portu 22.
  * Služby SFTP by měly být spuštěny. Chcete-li povolit ověřování podsystému SFTP a hesla v souboru sshd_config,
    * Přihlaste se jako uživatel root.
    * Přejděte do souboru /etc/ssh/sshd_config, najděte řádek, který začíná ověřováním hesla.
    * Odkomentujte řádek a změňte hodnotu na ano
    * Najděte řádek, který začíná subsystémem, a odkomentujte řádek
    * Restartujte službu sshd.
* Pokus o připojení mohl selhat, pokud po určité době neexistuje žádná správná odpověď nebo navázání připojení se nezdařilo, protože připojený hostitel neodpověděl.
* Může se jedná o problém související s připojením/sítí/doménou. Může to být také způsobeno problémem s překladem názvu DNS nebo problémem vyčerpání portu TCP. Zkontrolujte, zda ve vaší doméně nejsou nějaké takové známé problémy.

## <a name="connectivity-failure-errorid-95523"></a>Selhání připojení (ErrorID: 95523)

K této chybě dochází, pokud síť, ve které se zdrojový počítač nachází, není nalezena nebo byla odstraněna nebo již není k dispozici. Jediný způsob, jak chybu vyřešit, je zajistit, aby síť existovala.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Kontrola služeb sdílení souborů a tiskáren (ErrorID: 95105 & 95106)

Po kontrole připojení ověřte, jestli je ve virtuálním počítači povolena služba sdílení souborů a tiskáren. Tato nastavení jsou nutné ke kopírování agenta mobility do zdrojového počítače.

Pro **windows 2008 R2 a předchozí verze**,

* Chcete-li povolit sdílení souborů a tisku prostřednictvím brány Windows Firewall,
  * Otevřete ovládací panel -> System and Security -> Windows Firewall -> v levém podokně, klepněte na tlačítko Upřesnit nastavení -> ve stromu konzoly klepněte na tlačítko Upřesnit nastavení -> klepněte na položku Příchozí pravidla.
  * Vyhledejte pravidla Sdílení souborů a tiskáren (NB-Session-In) a Sdílení souborů a tiskáren (SMB-In). U každého pravidla klepněte pravým tlačítkem myši na pravidlo a potom klepněte na příkaz **Povolit pravidlo**.
* Chcete-li povolit sdílení souborů pomocí zásad skupiny,
  * Přejděte na úvodní obrazovku, zadejte příkaz gpmc.msc a vyhledejte.
  * V navigačním podokně otevřete následující složky: Zásady místního počítače, Konfigurace uživatele, Šablony pro správu, Součásti systému Windows a Sdílení sítě.
  * V podokně podrobností poklepejte na **položku Zabránit uživatelům ve sdílení souborů v jejich profilu**. Chcete-li zakázat nastavení zásad skupiny a povolit uživateli možnost sdílet soubory, klepněte na tlačítko Zakázáno. Klikněte na tlačítko OK a uložte změny. Další informace najdete [v tématu Povolení nebo zakázání sdílení souborů pomocí zásad skupiny](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Pro novější verze postupujte podle pokynů uvedených v [části Instalace služby Mobility pro zotavení po havárii virtuálních měn VMware a fyzických serverů](vmware-azure-install-mobility-service.md) a povolit sdílení souborů a tiskáren. **later versions**

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Kontrola konfigurace wmi (WMI) (Kód chyby: 95103)

Po kontrole služeb souborů a tiskáren povolte službu WMI pro soukromé, veřejné a doménové profily prostřednictvím brány firewall. Tato nastavení jsou nutné k dokončení vzdáleného spuštění ve zdrojovém počítači. Chcete-li povolit,

* Přejděte do Ovládacích panelů, klikněte na Zabezpečení a potom klikněte na bránu Windows Firewall.
* Klikněte na Změnit nastavení a potom klikněte na kartu Výjimky.
* V okně Výjimky zaškrtněte políčko u služby WMI (WMI) pro službu WMI, chcete-li povolit přenos služby WMI přes bránu firewall. 

Můžete také povolit přenos služby WMI prostřednictvím brány firewall na příkazovém řádku. Použití následujícího příkazu`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Další články o řešení potíží se službou WMI naleznete v následujících článcích.

* [Základní testování wmi](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Řešení potíží se službou WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Řešení problémů se skripty služby WMI a službami služby WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Nepodporované operační systémy

Dalším nejčastějším důvodem selhání může být kvůli nepodporovanému operačnímu systému. Ujistěte se, že jste na podporované verzi operačního systému/jádra pro úspěšnou instalaci služby Mobility. Vyhněte se použití soukromé opravy.
Seznam operačních systémů a verzí jádra podporovaných službou Azure Site Recovery najdete v našem [dokumentu matice podpory](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nepodporované konfigurace spouštěcího disku (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Spouštěcí a systémové oddíly / svazky nejsou stejný disk (ErrorID: 95309)

Před verzí 9.20 byla spouštěcí a systémová oddíly/ svazky na různých discích nepodporovanou konfigurací. Od [verze 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)je tato konfigurace podporována. Pro tuto podporu použijte nejnovější verzi.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Spouštěcí disk není k dispozici (ErrorID: 95310)

Virtuální počítač bez spouštěcího disku nelze chránit. To je zajistit hladké obnovení virtuálního počítače během operace převzetí služeb při selhání. Absence spouštěcího disku má za následek selhání spuštění počítače po převzetí služeb při selhání. Ujistěte se, že virtuální počítač obsahuje spouštěcí disk a opakujte operaci. Všimněte si také, že více spouštěcích disků ve stejném počítači není podporováno.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Více spouštěcích disků přítomných ve zdrojovém počítači (ErrorID: 95311)

Virtuální počítač s více spouštěcími disky není [podporovanou konfigurací](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systémový oddíl na více discích (ErrorID: 95313)

Před verzí 9.20 byl kořenový oddíl nebo svazek položený na více discích nepodporovanou konfigurací. Od [verze 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)je tato konfigurace podporována. Pro tuto podporu použijte nejnovější verzi.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Povolení ochrany se nezdařilo jako název zařízení uvedený v konfiguraci GRUB namísto UUID (ErrorID: 95320)

**Možná příčina:** </br>
Konfigurační soubory GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" nebo "/etc/default/grub") mohou obsahovat hodnotu **pro kořen** parametrů a **pokračovat** jako skutečné názvy zařízení namísto UUID. Obnovení webu nařizuje přístup UUID jako název zařízení se může změnit po restartování virtuálního počítače jako virtuální počítač nemusí přijít-up se stejným názvem na převzetí služeb při selhání, což má za následek problémy. Například: </br>


- Následující řádek je ze souboru GRUB **/boot/grub2/grub.cfg**. <br>
  *linux /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2** ${extra_cmdline} **resume=/dev/sda1** splash=tichý tichý showopts*


- Následující řádek je ze souboru GRUB **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Pokud budete dodržovat tučný řetězec výše, GRUB má skutečné názvy zařízení pro parametry "root" a "resume" místo UUID.
 
**Jak opravit:**<br>
Názvy zařízení je potřeba nahradit odpovídajícími identifikátory UUID.<br>


1. Najděte UUID zařízení provedením příkazu "blkid \<název zařízení>". Například:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Nyní nahraďte název zařízení jeho UUID ve formátu jako\<"root= UUID = UUID>". Například, pokud nahradíme názvy zařízení s UUID pro root a resume parametr uvedený výše v souborech "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" nebo "/etc/default/grub: pak řádky v souborech vypadají. <br>
   *jádro /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUI D=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=tichý crashkernel=256M-:128M showopts vga=0x314*
3. Znovu restartovat ochranu

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Instalace služby mobility dokončena s upozorněním k restartování (ErrorID: 95265 & 95266)

Služba mobility site recovery má mnoho součástí, z nichž jedna se nazývá ovladač filtru. Ovladač filtru se načte do systémové paměti pouze při restartování systému. To znamená, že opravy ovladačů filtru lze realizovat pouze při načtení nového ovladače filtru. což se může stát pouze v době restartu systému.

**Upozorňujeme,** že se jedná o upozornění a existující replikace bude fungovat i po aktualizaci nového agenta. Můžete se rozhodnout restartovat kdykolibudete chtít získat výhody nového ovladače filtru, ale pokud nechcete restartovat starý ovladač filtru pokračuje v práci. Takže po aktualizaci bez restartu, na rozdíl od ovladače filtru, **výhody dalších vylepšení a oprav v mobilitě služby dostane realizován**. Takže, i když doporučujeme, není povinné restartovat po každém upgradu. Informace o tom, kdy je restartování povinné, [nastavíte restartování zdrojového počítače po upgradu agenta mobility](https://aka.ms/v2a_asr_reboot) v aktualizacích služby v Azure Site Recovery.

> [!TIP]
>Doporučené postupy při plánování upgradů během okna údržby najdete v [tématu Podpora nejnovějších verzí operačního systému/jádra v aktualizacích služby](https://aka.ms/v2a_asr_upgrade_practice) v azure site recovery.

## <a name="lvm-support-from-920-version"></a>Podpora LVM od verze 9.20

Před verzí 9.20 byla lvm podporována pouze pro datové disky. /boot by měl být na disku oddílu a nesmí být svazek LVM.

Od [verze 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)je podporován [os disk na LVM.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) Pro tuto podporu použijte nejnovější verzi.

## <a name="insufficient-space-errorid-95524"></a>Nedostatek místa (ErrorID: 95524)

Při zkopírování agenta mobility do zdrojového počítače je vyžadováno alespoň 100 MB volného místa. Proto se ujistěte, že zdrojový počítač vyžaduje volné místo a opakujte operaci.

## <a name="vss-installation-failures"></a>Selhání instalace VSS

Součástí instalace agenta mobility je instalace služby Stínová kopie svazku (VSS). Tato služba se používá při generování bodů obnovení konzistentních vzhledem k aplikacím. K chybám během instalace vsystému VSS může dojít z několika důvodů. Chcete-li identifikovat přesné chyby, přečtěte si odkaz **na soubor c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. V následující části je zvýrazněno několik běžných chyb a kroky řešení.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Chyba VSS -2147023170 [0x800706BE] - ukončovací kód 511

Tento problém se většinou zobrazí, když antivirový software blokuje provoz služeb Azure Site Recovery. Řešení tohoto problému:

1. Vylučte všechny [zde](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)uvedené složky .
2. Chcete-li odblokovat registraci knihovny DLL v systému Windows, postupujte podle pokynů zveřejněných poskytovatelem antivirového programu.

### <a name="vss-error-7-0x7---exit-code-511"></a>Chyba VSS 7 [0x7] - ukončovací kód 511

Jedná se o chybu za běhu a je způsobena nedostatkem paměti k instalaci vss. Ujistěte se, že pro úspěšné dokončení této operace zvětšete místo na disku.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Chyba VSS -2147023824 [0x80070430] - ukončovací kód 517

K této chybě dochází, když je služba Zprostředkovatel obnovení webu Azure [označena k odstranění](https://msdn.microsoft.com/library/ms838153.aspx). Pokuste se nainstalovat službu VSS ručně do zdrojového počítače spuštěním následujícího příkazového řádku

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Chyba VSS -2147023841 [0x8007041F] - ukončovací kód 512

K této chybě dochází, když je [zamknutá](https://msdn.microsoft.com/library/ms833798.aspx)databáze služeb zprostředkovatele Azure Site Recovery . Pokuste se nainstalovat službu VSS ručně do zdrojového počítače spuštěním následujícího příkazového řádku

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

V případě selhání zkontrolujte, zda se nějaký antivirový program nebo jiné služby zasekly ve stavu "Spuštění". To by mohlo zachovat zámek na databázové služby. To povede k selhání při instalaci zprostředkovatele VSS. Ujistěte se, že žádná služba je ve stavu "Spuštění" a opakujte výše uvedené operace.

### <a name="vss-exit-code-806"></a>Ukončovací kód VSS 806

K této chybě dochází, pokud uživatelský účet použitý pro instalaci nemá oprávnění ke spuštění příkazu CSScript. Poskytněte uživateli potřebná oprávnění ke spuštění skriptu a opakování operace.

### <a name="other-vss-errors"></a>Další chyby VSS

Pokuste se nainstalovat službu poskytovatele Služby VSS ručně do zdrojového počítače spuštěním následujícího příkazového řádku

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>Chyba VSS - 0x8004E00F

K této chybě obvykle dochází během instalace agenta mobility z důvodu problémů v dcom a DCOM je v kritickém stavu.

K určení příčiny chyby použijte následující postup.

**Zkontrolujte protokoly instalace**

1. Otevřete protokol instalace umístěný na adrese c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. Přítomnost následující chyby označuje tento problém:

    Zrušení registrace existující aplikace...  Vytvoření katalogového objektu Získejte sbírku aplikací 

    Chyba:

    - Kód chyby: -2147164145 [0x8004E00F]
    - Kód ukončení: 802

Chcete-li tento problém vyřešit:

Obraťte se na [tým platformy Microsoft Windows](https://aka.ms/Windows_Support) získat pomoc při řešení problému DCOM.

Po vyřešení problému S COM přeinstalujte zprostředkovatele Azure Site Recovery VSS ručně pomocí následujícího příkazu:
 
**C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Pokud konzistence aplikace není důležitá pro vaše požadavky na zotavení po havárii, můžete obejít instalaci zprostředkovatele VSS. 

Pokud chcete obejít instalaci zprostředkovatele VSS pro obnovení webu Azure a ručně nainstalovat po instalaci zprostředkovatele Azure Site Recovery VSS zprostředkovatele webu:

1. Nainstalujte službu mobility. 
   > [!Note]
   > 
   > Instalace se nezdaří v kroku "Po instalaci konfigurace". 
2. Chcete-li obejít instalaci VSS:
   1. Otevřete instalační adresář služby Azure Site Recovery Service, který se nachází na adrese:
   
      C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   2. Upravte instalační skripty zprostředkovatele Azure Site Recovery VSS **nMageVSSProvider_Install** a **InMageVSSProvider_Uninstall.cmd** tak, aby byly vždy úspěšné přidáním následujících řádků:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Znovu spusťte instalaci agenta mobility ručně. 
4. Po dokončení instalace a přejde k dalšímu kroku **Konfigurace**, odeberte řádky, které jste přidali.
5. Chcete-li nainstalovat zprostředkovatele služby VSS, otevřete příkazový řádek jako správce a spusťte následující příkaz:
   
    **C:\Program Files (x86)\Microsoft Azure Site Recovery\agent> .\InMageVSSProvider_Install.cmd**

9. Ověřte, zda je zprostředkovatel ASR VSS nainstalován jako služba ve službách systému Windows, a otevřete konzolu MMC služby Komponenty a ověřte, zda je uveden zprostředkovatel ASR VSS.
10. Pokud instalace zprostředkovatele VSS nadále selhat, práce s CX vyřešit chyby oprávnění v CAPI2.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Instalace zprostředkovatele VSS se nezdaří z důvodu povolení clusterové služby v počítači bez clusteru

Tento problém způsobí, že instalace agenta obnovení webu Azure nezdaří během kroku instalace zprostředkovatele ASAzure Site RecoveryR VSS z důvodu problému s COM +, který brání instalaci zprostředkovatele VSS.
 
### <a name="to-identify-the-issue"></a>Chcete-li identifikovat problém

V protokolu umístěném na konfiguračním serveru c:\ProgramData\ASRSetupLogs\UploadedLogs\<datum a čas>UA_InstallLogFile.log naleznete následující výjimku:

Com+ nemohl mluvit s koordinátorem distribuovaných transakcí společnosti Microsoft (Výjimka z HRESULT: 0x8004E00F)

Chcete-li tento problém vyřešit:

1.  Ověřte, zda se jedná o počítač bez clusteru a zda nejsou používány součásti clusteru.
3.  Pokud součásti nejsou používány, odeberte součásti clusteru ze zařízení.

## <a name="drivers-are-missing-on-the-source-server"></a>Na zdrojovém serveru chybí ovladače.

Pokud se instalace agenta mobility nezdaří, zkontrolujte protokoly v části C:\ProgramData\ASRSetupLogs a zjistěte, zda v některých sadách ovládacích prvku chybí některé požadované ovladače.
 
Chcete-li tento problém vyřešit:
  
1. Pomocí editoru registru, například regedit.msc, otevřete registr.
2. Otevřete uzel HKEY_LOCAL_MACHINE\SYSTEM.
3. V uzlu SYSTEM vyhledejte ovládací sady ovládacích prvku.
4. Otevřete každou sadu ovládacích prvku a ověřte, zda jsou k dispozici následující ovladače systému Windows:

   - Atapi
   - Vmbus
   - Storflt (Fr.)
   - Storvsc (Stova
   - srozumitelný
 
Přeinstalujte všechny chybějící ovladače.

## <a name="next-steps"></a>Další kroky

[Přečtěte si, jak](vmware-azure-tutorial.md) nastavit zotavení po havárii pro virtuální virtuální měny VMware.
