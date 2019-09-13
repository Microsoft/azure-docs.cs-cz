---
title: Řešení selhání nabízené instalace služby mobility při povolování replikace pro zotavení po havárii | Microsoft Docs
description: Řešení chyb při instalaci služby mobility při povolování replikace pro zotavení po havárii
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 4aa18379962c289f5094795988a247f4c7e35df2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910638"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Řešení potíží s nabízenou instalací služby mobility

Instalace služby mobility je klíčový krok během povolování replikace. Úspěch tohoto kroku závisí výhradně na požadavcích na schůzku a na práci s podporovanými konfiguracemi. Mezi nejběžnější chyby, které čelíte během instalace služby mobility, patří:

* [Chyby přihlašovacích údajů/oprávnění](#credentials-check-errorid-95107--95108)
* [Neúspěšná přihlášení](#login-failures-errorid-95519-95520-95521-95522)
* [Chyby připojení](#connectivity-failure-errorid-95117--97118)
* [Chyby sdílení souborů a tiskáren](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Selhání WMI](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nepodporované operační systémy](#unsupported-operating-systems)
* [Nepodporované konfigurace spuštění](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Selhání instalace VSS](#vss-installation-failures)
* [Název zařízení v konfiguraci GRUB namísto UUID zařízení](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM svazek](#lvm-support-from-920-version)
* [Upozornění na restartování](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Když povolíte replikaci, Azure Site Recovery se pokusí nainstalovat agenta služby mobility na virtuálním počítači. V rámci této funkce se konfigurační server pokusí připojit k virtuálnímu počítači a zkopírovat agenta. Pokud chcete povolit úspěšnou instalaci, postupujte podle pokynů v části Postup řešení potíží uvedených níže.

## <a name="credentials-check-errorid-95107--95108"></a>Kontrolu přihlašovacích údajů (ErrorID: 95107 & 95108)

* Ověřte, zda je uživatelský účet vybraný během povolování replikace **platný, přesný**.
* Azure Site Recovery vyžaduje k provedení nabízené instalace **kořenový** účet nebo uživatelský účet s **oprávněními správce** . V opačném případě bude nabízená instalace na zdrojovém počítači blokovaná.
  * V případě systému Windows (**chyba 95107**) Zkontrolujte, zda uživatelský účet má na zdrojovém počítači oprávnění správce, a to buď místní, nebo doména.
  * Pokud nepoužíváte doménový účet, je nutné zakázat řízení přístupu vzdáleného uživatele v místním počítači.
    * Pokud chcete zakázat řízení přístupu ke vzdálenému uživateli, přidejte do klíče registru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System novou hodnotu DWORD: LocalAccountTokenFilterPolicy. Nastavte hodnotu na 1. Chcete-li provést tento krok, spusťte z příkazového řádku následující příkaz:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Pro Linux (**chyba 95108**) musíte zvolit kořenový účet pro úspěšnou instalaci agenta mobility. Navíc by měly být spuštěny služby SFTP. Postup povolení subsystému SFTP a ověřování hesla v souboru sshd_config:
    1. Přihlaste se jako kořenový adresář.
    2. Do souboru/etc/ssh/sshd_config Najděte řádek, který začíná na PasswordAuthentication.
    3. Odkomentujte řádek a změňte hodnotu na Ano.
    4. Vyhledejte řádek, který začíná na subsystému, a odkomentujte řádek.
    5. Restartujte službu sshd.

Pokud chcete změnit přihlašovací údaje zvoleného uživatelského účtu, postupujte podle pokynů uvedených [tady](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Selhání nedostatečných oprávnění (ErrorID: 95517)

Pokud uživatel, který zvolil instalaci agenta mobility, nemá oprávnění správce, konfigurační server nebo procesový Server se škálováním na více instancí nebude moct zkopírovat software agenta mobility na zdrojový počítač. Tato chyba je způsobena tím, že dojde k chybě odepření přístupu. Ujistěte se, že uživatelský účet má oprávnění správce.

Pokud chcete změnit přihlašovací údaje zvoleného uživatelského účtu, postupujte podle pokynů uvedených [tady](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Selhání nedostatečných oprávnění (ErrorID: 95518)

Když při pokusu o přihlášení ke zdrojovému počítači dojde k chybě při vyvazování vztahu důvěryhodnosti domény mezi primární doménou a pracovní stanicí, instalace agenta mobility se nezdařila s ID chyby 95518. Ujistěte se, že uživatelský účet použitý k instalaci agenta mobility má oprávnění správce pro přihlášení prostřednictvím primární domény zdrojového počítače.

Pokud chcete změnit přihlašovací údaje zvoleného uživatelského účtu, postupujte podle pokynů uvedených [tady](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Neúspěšné přihlášení (ErrorID: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Přihlašovací údaje uživatelského účtu jsou zakázané (ErrorID: 95519)

Uživatelský účet vybraný během povolování replikace byl zakázán. Pokud chcete uživatelský účet povolit, přečtěte si tento [](https://aka.ms/enable_login_user) článek nebo spusťte následující příkaz, který nahradí textové *uživatelské* jméno skutečným uživatelským jménem.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Pověření byla uzamčena z důvodu více nezdařených pokusů o přihlášení (ErrorID: 95520)

Několik neúspěšných pokusů o přístup k počítači bude uzamknout uživatelský účet. Příčinou chyby může být:

* Přihlašovací údaje zadané během nastavení konfigurace nejsou správné nebo
* Uživatelský účet vybraný během povolování replikace není správný.

Proto upravte pověření zvolená podle pokynů uvedených [tady](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) a po nějaké době operaci opakujte.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Na zdrojovém počítači nejsou k dispozici přihlašovací servery (ErrorID: 95521)

K této chybě dojde, pokud na zdrojovém počítači nejsou přihlašovací servery k dispozici. Nedostupnost přihlašovacích serverů povede k selhání žádosti o přihlášení, takže agenta mobility nejde nainstalovat. Pro úspěšné přihlášení zajistěte, aby byly na zdrojovém počítači k dispozici přihlašovací servery, a spusťte přihlašovací službu. Podrobné pokyny najdete v článku KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) Err Msg: V tuto chvíli nejsou k dispozici žádné přihlašovací servery.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Na zdrojovém počítači není spuštěná přihlašovací služba (ErrorID: 95522)

Přihlašovací služba není na zdrojovém počítači spuštěná a způsobila selhání žádosti o přihlášení. Proto nejde nainstalovat agenta mobility. Pokud chcete řešení vyřešit, ujistěte se, že je na zdrojovém počítači spuštěná přihlašovací služba pro úspěšné přihlášení. Přihlašovací službu spustíte spuštěním příkazu "net start Logon" z příkazového řádku nebo spuštěním služby NetLogon ze Správce úloh.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Chyba připojení (ErrorID: 95117 & 97118)**

Konfigurační server/procesový Server se škálováním na více instancí se pokusí připojit ke zdrojovému virtuálnímu počítači a nainstalovat agenta mobility. K této chybě dochází, pokud není zdrojový počítač dosažitelný z důvodu problémů s připojením k síti. Chcete-li vyřešit,

* Ujistěte se, že se na zdrojovém počítači můžete z konfiguračního serveru připojit pomocí testu. Pokud jste během povolování replikace zvolili procesový Server se škálováním na více instancí, ujistěte se, že máte možnost odeslat z procesového serveru svůj zdrojový počítač.
  * Z příkazového řádku počítač zdrojového serveru pomocí programu Telnet otestujte konfigurační server nebo procesový Server se škálováním na více instancí pomocí portu HTTPS (135), jak je uvedeno níže, a zjistěte, jestli nedochází k problémům se síťovým připojením nebo blokování portů brány firewall.

     `telnet <CS/ scale-out PS IP address> <135>`
* Pro **virtuální počítače se systémem Linux**navíc
  * Ověřte, jestli jsou nainstalované nejnovější balíčky OpenSSH, OpenSSH-server a OpenSSL.
  * Zkontrolujte a zajistěte, aby byl povolený Secure Shell (SSH) a běžel na portu 22.
  * Služby SFTP by měly být spuštěné. Pokud chcete povolit podsystém SFTP a ověřování hesla v souboru sshd_config,
    * Přihlaste se jako kořenový adresář.
    * Do souboru/etc/ssh/sshd_config Najděte řádek, který začíná na PasswordAuthentication.
    * Odkomentujte řádek a změňte hodnotu na Ano.
    * Najde řádek, který začíná na subsystému, a odkomentujte řádek.
    * Restartujte službu sshd.
* Pokus o připojení se nezdařil, pokud po určitém časovém intervalu neexistuje žádná řádná odpověď nebo navázání připojení selhalo, protože se nepovedlo odpovědět připojenému hostiteli.
* Může se jednat o problém související s připojením/sítí nebo doménou. Důvodem může být taky název DNS, který řeší problém nebo vyčerpání portů TCP. Ověřte, zda jsou ve vaší doméně žádné známé problémy.

## <a name="connectivity-failure-errorid-95523"></a>Chyba připojení (ErrorID: 95523)

K této chybě dojde, když síť, ve které se nachází zdrojový počítač, nebyla nalezena nebo byla odstraněna nebo již není k dispozici. Jediným způsobem, jak chybu vyřešit, je zajištění existence sítě.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Kontroluje se služba sdílení souborů a tiskáren (ErrorID: 95105 & 95106)

Po kontrole připojení zkontrolujte, jestli je ve vašem virtuálním počítači povolená služba sdílení souborů a tiskáren. Tato nastavení jsou nutná ke zkopírování agenta mobility do zdrojového počítače.

Pro **windows 2008 R2 a předchozí verze**

* Pokud chcete povolit sdílení souborů a tiskáren přes bránu Windows Firewall,
  * Otevřete ovládací panely – > systém a zabezpečení > bránu Windows Firewall – > v levém podokně klikněte na Upřesnit nastavení – > klikněte na příchozí pravidla ve stromu konzoly.
  * Vyhledejte pravidla sdílení souborů a tiskáren (NB-Session-in) a sdílení souborů a tiskáren (SMB-in). U každého pravidla klikněte pravým tlačítkem na pravidlo a pak klikněte na **Povolit pravidlo**.
* Pokud chcete povolit sdílení souborů s Zásady skupiny,
  * Vyberte Start, zadejte GPMC. msc a vyhledejte.
  * V navigačním podokně otevřete následující složky: Zásady místního počítače, konfigurace uživatele, Šablony pro správu, součásti systému Windows a sdílení sítě.
  * V podokně podrobností poklikejte na **zabránit uživatelům v sdílení souborů v rámci svého profilu**. Pokud chcete nastavení Zásady skupiny zakázat a povolit uživatelům sdílení souborů, klikněte na Disabled (zakázáno). Změny uložíte kliknutím na OK. Další informace najdete v tématu [Povolení nebo zakázání sdílení souborů s Zásady skupiny](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Pro **pozdější verze**postupujte podle pokynů uvedených v tématu [instalace služby mobility pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů](vmware-azure-install-mobility-service.md) a povolte sdílení souborů a tiskáren.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Rozhraní WMI (Windows Management Instrumentation) (WMI) – kontrolu konfigurace (kód chyby: 95103)

Po kontrole souborové a tiskové služby povolte službu WMI pro privátní, veřejné a doménové profily prostřednictvím brány firewall. Tato nastavení jsou nutná k dokončení vzdáleného spuštění na zdrojovém počítači. Chcete-li povolit,

* Přejděte na ovládací panely, klikněte na zabezpečení a pak klikněte na brána Windows Firewall.
* Klikněte na změnit nastavení a pak klikněte na kartu výjimky.
* V okně výjimky Zaškrtněte políčko pro rozhraní WMI (Windows Management Instrumentation) (WMI), aby se povolil provoz rozhraní WMI přes bránu firewall. 

Provoz rozhraní WMI můžete povolit také přes bránu firewall na příkazovém řádku. Použijte následující příkaz`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Další články týkající se řešení potíží s rozhraním WMI najdete v následujících článcích.

* [Základní testování služby WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Řešení potíží s rozhraním WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Řešení potíží se skripty WMI a službami WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Nepodporované operační systémy

Dalším nejběžnějším důvodem selhání může být nepodporovaný operační systém. Abyste mohli úspěšně nainstalovat službu mobility, ujistěte se, že jste v podporovaném operačním systému/verzi jádra. Vyhněte se použití soukromé opravy.
Pokud chcete zobrazit seznam operačních systémů a verzí jádra podporovaných nástrojem Azure Site Recovery, přečtěte si náš [dokument s maticí podpory](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nepodporované konfigurace spouštěcího disku (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Spouštěcí a systémové oddíly nebo svazky nejsou na stejném disku (ErrorID: 95309)

Před verzí 9,20 byla Nepodporovaná konfigurace spouštěcích a systémových oddílů/svazků na různých discích. Tato konfigurace je dostupná od [verze 9,20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery). Pro tuto podporu použijte nejnovější verzi.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Spouštěcí disk není k dispozici (ErrorID: 95310)

Virtuální počítač bez spouštěcího disku nelze chránit. K zajištění hladkého obnovení virtuálního počítače během operace převzetí služeb při selhání. Nepřítomnost spouštěcího disku způsobí selhání spuštění počítače po převzetí služeb při selhání. Zajistěte, aby virtuální počítač obsahoval spouštěcí disk, a operaci opakujte. Všimněte si také, že více spouštěcích disků ve stejném počítači není podporováno.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Na zdrojovém počítači existuje víc spouštěcích disků (ErrorID: 95311)

Virtuální počítač s několika spouštěcími disky není podporovanou [konfigurací](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systémový oddíl na více discích (ErrorID: 95313)

Před verzí 9,20 byl kořenový oddíl nebo svazek na více discích nepodporovanou konfigurací. Tato konfigurace je dostupná od [verze 9,20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery). Pro tuto podporu použijte nejnovější verzi.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Ochranu se nepovedlo zapnout, protože název zařízení uvedený v konfiguraci GRUB místo UUID (ErrorID: 95320)

**Možná příčina:** </br>
Konfigurační soubory GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" nebo "/etc/default/GRUB") můžou obsahovat hodnotu pro **kořen** parametrů a **obnovit** je jako skutečné názvy zařízení namísto UUID. Site Recovery přístup k identifikátorům UUID, protože název zařízení se může změnit v průběhu restartování virtuálního počítače, protože virtuální počítač nemusí při převzetí služeb při selhání přijít se stejným názvem, což způsobí problémy. Příklad: </br>


- Následující řádek je ze souboru GRUB **/boot/grub2/grub.cfg**. <br>
  *Linux/Boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **Resume =/dev/sda1** Úvod = tiché tiché showopts*


- Následující řádek je ze souboru GRUB **/boot/grub/menu.lst** 
  *jádra /boot/vmlinuz-3.0.101-63-default **root = / dev/sda2** **obnovit = / dev/sda1** splash = tiché crashkernel = 256M-:128M showopts vga = 0x314*

Pokud si vyberete tučný řetězec uvedený výše, GRUB má skutečné názvy zařízení pro parametry "root" a "Resume" místo UUID.
 
**Jak opravit:**<br>
Názvy zařízení by měly být nahrazeny odpovídajícím identifikátorem UUID.<br>


1. Vyhledejte UUID zařízení spuštěním příkazu "blkid \<Device Name >". Příklad:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Nyní nahraďte název zařízení identifikátorem UUID ve formátu jako "root = UUID =\<UUID >". Pokud například nahradíte názvy zařízení identifikátorem UUID pro kořen a parametr Resume uvedené výše v souborech "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" nebo "/etc/default/grub: řádky v souborech vypadají jako. <br>
   *jádro/Boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **Resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** úvodní = tichá crashkernel = 256M-: 128M showopts VGA = 0x314*
3. Znovu znovu spusťte ochranu.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Instalace služby mobility byla dokončena s upozorněním na restartování (ErrorID: 95265 & 95266)

Služba Site Recovery mobility má mnoho komponent, z nichž jedna se nazývá ovladač filtru. Ovladač filtru se načte do systémové paměti pouze v době restartování systému. To znamená, že opravy ovladače filtru lze realizovat pouze při načtení nového ovladače filtru. k tomu může dojít pouze v době restartování systému.

**Všimněte si** , že se jedná o upozornění a stávající replikace bude fungovat i po novém aktualizace agenta. Můžete se rozhodnout, že budete chtít kdykoli restartovat, abyste získali výhody nového ovladače filtru, ale Pokud nerestartujete původní ovladač filtru, zůstane v provozu. Takže po aktualizaci bez restartování od ovladače filtru jsou **realizované výhody dalších vylepšení a oprav ve službě mobility**. I když se to doporučuje, není po každém upgradu nutné restartovat počítač. Informace o tom, kdy je restartování povinné, nastavíte tak, že v části aktualizace služby v Azure Site Recovery zadáte [restart zdrojového počítače po upgradu agenta mobility](https://aka.ms/v2a_asr_reboot) .

> [!TIP]
>Osvědčené postupy při plánování upgradu během časového období údržby najdete v tématu [Podpora nejnovějších verzí operačního systému a jádra](https://aka.ms/v2a_asr_upgrade_practice) v článku aktualizace služby v Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>Podpora LVM z verze 9,20

Před verzí 9,20 byl LVM podporován pouze pro datové disky. /Boot by měl být na disku a nesmí být svazkem LVM.

Na LVM je podporovaná [verze 9,20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) [disku s operačním systémem](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) . Pro tuto podporu použijte nejnovější verzi.

## <a name="insufficient-space-errorid-95524"></a>Nedostatek místa (ErrorID: 95524)

Když se na zdrojový počítač zkopíruje agent mobility, vyžaduje se aspoň 100 MB volného místa. Ujistěte se, že na zdrojovém počítači je vyžadováno volné místo, a zkuste operaci zopakovat.

## <a name="vss-installation-failures"></a>Selhání instalace VSS

Instalace služby Stínová kopie svazku je součástí instalace agenta mobility. Tato služba se používá v procesu generování bodů obnovení konzistentních vzhledem k aplikacím. Selhání při instalaci VSS může nastat z několika důvodů. Chcete-li identifikovat přesné chyby, podívejte se na **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Několik běžných chyb a postup řešení je zvýrazněn v následující části.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Chyba služby VSS – 2147023170 [0x800706BE] – ukončovací kód 511

K tomuto problému dochází hlavně v případě, že antivirový software blokuje operace služby Azure Site Recovery Services. K vyřešení tohoto problému:

1. Vylučte všechny složky, které jsou [tady](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)uvedené.
2. Podle pokynů publikovaných poskytovatelem antivirové ochrany odblokujte registraci knihovny DLL ve Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Chyba služby VSS 7 [0x7] – ukončovací kód 511

Jedná se o chybu za běhu a příčinou je nedostatek paměti pro instalaci služby VSS. Zajistěte, aby bylo možné zvětšit místo na disku pro úspěšné dokončení této operace.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Chyba služby VSS – 2147023824 [0x80070430] – ukončovací kód 517

K této chybě dochází, když je Azure Site Recovery služba poskytovatele VSS [označena k odstranění](https://msdn.microsoft.com/library/ms838153.aspx). Pokuste se ručně nainstalovat VSS na zdrojový počítač spuštěním následujícího příkazového řádku.

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Chyba služby VSS – 2147023841 [0x8007041F] – ukončovací kód 512

K této chybě dojde, pokud je Azure Site Recovery databáze služby [](https://msdn.microsoft.com/library/ms833798.aspx)poskytovatele VSS uzamčena. Pokuste se ručně nainstalovat VSS na zdrojový počítač spuštěním následujícího příkazového řádku.

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

V případě selhání ověřte, zda je nějaký antivirový program nebo jiné služby zablokovaný ve stavu "spouštění". To může zachovat zámek u databázových služeb. Dojde k selhání při instalaci poskytovatele služby VSS. Zajistěte, aby ve stavu "spouštění" nejsou žádné služby, a operaci opakujte.

### <a name="vss-exit-code-806"></a>Ukončovací kód VSS 806

K této chybě dochází, pokud uživatelský účet použitý k instalaci nemá oprávnění ke spuštění příkazu CSScript. Poskytněte uživatelskému účtu potřebná oprávnění ke spuštění skriptu a operaci opakujte.

### <a name="other-vss-errors"></a>Další chyby VSS

Pokuste se ručně nainstalovat službu poskytovatele VSS na zdrojový počítač spuštěním následujícího příkazového řádku.

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>Chyba služby VSS – 0x8004E00F

K této chybě obvykle dochází během instalace agenta mobility, protože problémy v modelu DCOM a DCOM jsou v kritickém stavu.

K určení příčiny chyby použijte následující postup.

**Kontrola protokolů instalace**

1. Otevřete Instalační protokol umístěný na adrese c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. Následující chyba indikuje tento problém:

    Ruší se registrace existující aplikace...  Vytvoření objektu katalogu získání kolekce aplikací 

    CHYBA

    - Kód chyby:-2147164145 [0x8004E00F]
    - Ukončovací kód: 802

Problém vyřešíte takto:

Obraťte se na [tým platformy Microsoft Windows](https://aka.ms/Windows_Support) , kde získáte pomoc při řešení problému s modelem DCOM.

Po vyřešení problému modelu DCOM přeinstalujte Azure Site Recovery zprostředkovatele služby VSS ručně pomocí následujícího příkazu:
 
**C:\Program Files (x86) \Microsoft Azure Site Recovery\agent > "C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Pokud není konzistence aplikací zásadní pro požadavky na zotavení po havárii, můžete instalaci poskytovatele služby VSS obejít. 

Postup při obejít instalaci poskytovatele služby VSS Azure Site Recovery a ručně nainstalovat Azure Site Recovery po instalaci zprostředkovatele služby VSS:

1. Nainstalujte službu mobility. 
   > [!Note]
   > 
   > Instalace se nezdaří v kroku "po instalaci konfigurace". 
2. Postup při obejít instalaci stínové kopie svazku:
   1. Otevřete instalační adresář služby Azure Site Recovery mobility v umístění:
   
      C:\Program Files (x86) \Microsoft Azure Site Recovery\agent
   2. Upravte Azure Site Recovery instalační skripty poskytovatele služby VSS **nMageVSSProvider_Install** a **InMageVSSProvider_Uninstall. cmd** tak, aby se vždy úspěšně zdařily, a to přidáním následujících řádků:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Znovu spusťte ruční instalaci agenta mobility. 
4. Po úspěšné instalaci a přechodu k dalšímu kroku **nakonfigurujte**a odeberte přidané řádky.
5. Chcete-li nainstalovat poskytovatele služby VSS, otevřete příkazový řádek jako správce a spusťte následující příkaz:
   
    **C:\Program Files (x86) \Microsoft Azure Site Recovery\agent > .\InMageVSSProvider_Install.cmd**

9. Ověřte, že je zprostředkovatel VSS pro automatické obnovení systému nainstalovaný jako služba ve službách Windows, a otevřete konzolu MMC Service pro ověření, že je v seznamu uveden poskytovatel služby ASR.
10. Pokud se instalace poskytovatele služby VSS stále nezdařila, pracujte s CX pro řešení chyb oprávnění v CAPI2.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Instalace poskytovatele služby Stínová kopie svazku se nezdařila, protože Clusterová služba je povolena v počítači bez clusteru.

Tento problém způsobí selhání instalace agenta Azure Site Recovery mobility během instalace poskytovatele VSS ASAzure Site Recovery z důvodu problému s COM+, který brání instalaci poskytovatele služby VSS.
 
### <a name="to-identify-the-issue"></a>Identifikace problému

V protokolu, který se nachází na konfiguračním serveru\<v C:\ProgramData\ASRSetupLogs\UploadedLogs data a času > UA_InstallLogFile. log, se zobrazí následující výjimka:

COM+ se nepovedlo komunikovat se službou Microsoft DTC (Distributed Transaction Coordinator) (výjimka z HRESULT: 0x8004E00F)

Problém vyřešíte takto:

1.  Ověřte, zda je tento počítač počítač bez clusteru a zda nejsou používány součásti clusteru.
3.  Pokud se komponenty nepoužívají, odeberte z počítače součásti clusteru.

## <a name="drivers-are-missing-on-the-source-server"></a>Na zdrojovém serveru chybí ovladače.

Pokud se instalace agenta mobility nezdařila, zkontrolujte protokoly v části C:\ProgramData\ASRSetupLogs a zjistěte, jestli některé z požadovaných ovladačů v některých sadách ovládacích prvků chybí.
 
Problém vyřešíte takto:
  
1. Pomocí Editoru registru, jako je regedit. msc, otevřete registr.
2. Otevřete uzel HKEY_LOCAL_MACHINE\SYSTEM.
3. V uzlu systém vyhledejte sady ovládacích prvků.
4. Otevřete jednotlivé sady ovládacích prvků a ověřte, zda jsou k dispozici následující ovladače systému Windows:

   - ovladač
   - VMBus
   - storflt
   - storvsc
   - Intelide
 
Přeinstalujte všechny chybějící ovladače.

## <a name="next-steps"></a>Další postup

[Přečtěte si, jak](vmware-azure-tutorial.md) nastavit zotavení po havárii pro virtuální počítače VMware.
