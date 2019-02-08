---
title: Řešení potíží se selháním instalace služby Mobility nabízených oznámení při povolení replikace pro zotavení po havárii | Dokumentace Microsoftu
description: Řešení chyb při instalaci služby Mobility, při povolení replikace pro zotavení po havárii
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 02/07/2019
ms.openlocfilehash: 3de5996f574bf076b856a4d0cf7e18d77b1a9e5d
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895682"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Řešení potíží s nabízenou instalací služby Mobility

Instalace služby Mobility je klíče krokem při povolení replikace. Úspěch tento krok závisí výhradně na splnění požadavků a práci s podporovanou konfigurací. Většina běžných chyb, které se zabývají během instalace služby Mobility se z důvodu:

* [Přihlašovací údaje nebo oprávnění chyby](#credentials-check-errorid-95107--95108)
* [Neúspěšná přihlášení](#login-failures-errorid-95519-95520-95521-95522)
* [Chyby připojení](#connectivity-failure-errorid-95117--97118)
* [Chyby sdílení souborů a tiskáren](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Chyby rozhraní WMI](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nepodporovaný operační systémy](#unsupported-operating-systems)
* [Nepodporované konfigurace spuštění](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Selhání instalace VSS](#vss-installation-failures)
* [Název zařízení v konfiguraci GRUB, takže nemusíte zařízení UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Svazek LVM](#lvm-support-from-920-version)
* [Restartovat upozornění](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Při povolení replikace Azure Site Recovery se pokusí vložit instalace agenta služby mobility na virtuálním počítači. Jako součást tohoto konfiguračního serveru pokusí připojit k virtuálnímu počítači a zkopírujte agenta. Pokud chcete povolit úspěšnou instalaci, postupujte podrobné pokyny k odstraňování uvedena níže.

## <a name="credentials-check-errorid-95107--95108"></a>Zkontrolujte přihlašovací údaje (ID chyby: 95107 & 95108)

* Ověřte, jestli uživatelský účet vybrána při povolení replikace **platný, přesné**.
* Azure Site Recovery vyžaduje **KOŘENOVÉ** účet nebo uživatelský účet s **oprávnění správce** k provedení nabízené instalace. V opačném nabízené instalace se zablokuje na zdrojovém počítači.
  * Pro Windows (**chyba 95107**), ověření, pokud uživatelský účet nemá přístup pro správu, místní nebo doméně na zdrojovém počítači.
  * Pokud nepoužíváte účet domény, je nutné zakázat vzdálené řízení přístupu uživatele v místním počítači.
    * Zakázání vzdálené řízení přístupu uživatele, pod klíčem registru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System přidáte novou hodnotu DWORD: LocalAccountTokenFilterPolicy. Nastavte hodnotu na 1. K provedení tohoto kroku, spusťte následující příkaz z příkazového řádku:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Pro Linux (**chyba 95108**), je nutné vybrat kořenový účet pro úspěšnou instalaci agenta mobility. Kromě toho by měl běžet služba SFTP. Pokud chcete povolit SFTP subsystému a ověřování heslem v souboru sshd_config:
    1. Přihlaste se jako uživatel root.
    2. Přejděte k souboru /etc/ssh/sshd_config, vyhledejte řádek, který začíná PasswordAuthentication.
    3. Zrušte na řádku komentář a změňte hodnotu na Ano.
    4. Vyhledejte řádek, který začíná subsystému a zrušte komentář na řádku.
    5. Restartujte službu sshd.

Pokud chcete upravit přihlašovací údaje účtu pro vybrané uživatele, postupujte podle pokynů [tady](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Selhání dostatečná oprávnění (ID chyby: 95517)

Když uživatel zvolili k instalaci agenta mobility nemá oprávnění správce, konfigurační server/horizontální navýšení kapacity procesového serveru nebude moci kopírovat softwaru agenta mobility do zdrojového počítače. Tato chyba je tedy výsledkem chyba odepření přístupu. Ujistěte se, že uživatelský účet má oprávnění správce.

Pokud chcete upravit přihlašovací údaje účtu pro vybrané uživatele, postupujte podle pokynů [tady](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Selhání dostatečná oprávnění (ID chyby: 95518)

Když zařízení vztah důvěryhodnosti domény mezi primární doménou a pracovní stanice se nezdaří při pokusu o přihlášení ke zdrojovému počítači, instalace agenta mobility se nezdaří s id chyby 95518. Ano Ujistěte se, že uživatelský účet použitý k instalaci agenta mobility má oprávnění správce k přihlášení pomocí primární domény zdrojového počítače.

Pokud chcete upravit přihlašovací údaje účtu pro vybrané uživatele, postupujte podle pokynů [tady](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Neúspěšná přihlášení (ID chyby: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Přihlašovací údaje uživatelského účtu se zakázaly. (ID chyby: 95519)

Uživatelský účet vybrána při povolení replikace se zakázalo. Chcete-li uživatelský účet, najdete v článku [tady](https://aka.ms/enable_login_user) nebo spusťte následující příkaz tak, že nahradíte text *uživatelské jméno* s skutečné uživatelské jméno.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Přihlašovací údaje uzamčen kvůli více neúspěšných pokusů o přihlášení (ID chyby: 95520)

Více neúspěšných opakování úsilí pro přístup k počítači dojde k uzamčení uživatelského účtu. Selhání možné příčiny:

* Při konfiguraci nastavení zadané přihlašovací údaje jsou nesprávné nebo
* Uživatelský účet vybrána při povolení replikace není správné

Ano, měnit přihlašovací údaje zvolí podle pokynů [tady](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) a zkuste operaci zopakovat za nějakou dobu.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Přihlašovací servery nejsou k dispozici na zdrojovém počítači (ID chyby: 95521)

K této chybě dochází, pokud nejsou k dispozici na zdrojovém počítači přihlašovací servery. Nedostupnost přihlašovací servery povede k selhání žádosti o přihlášení, a proto není možné nainstalovat agenta mobility. Pro úspěšné přihlášení Ujistěte se, že přihlašovací servery jsou k dispozici na zdrojovém počítači a spuštění služby přihlašování. Podrobné pokyny, klikněte na tlačítko [tady](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Na zdrojovém počítači neběží služba přihlášení (ID chyby: 95522)

Přihlášení služby na zdrojovém počítači není spuštěná a způsobila chybu při žádosti o přihlášení. Proto nelze nainstalovat agenta mobility. Pokud chcete vyřešit, ujistěte se, že je pro úspěšné přihlášení přihlašovací služba běží na zdrojovém počítači. Spustit službu přihlášení, spusťte příkaz "net start přihlášení" z příkazového řádku nebo službu "NetLogon" spustit Správce úloh.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Chyba připojení (ID chyby: 95117 & 97118)**

Konfigurační server / procesový server horizontální navýšení kapacity se pokusí připojit k zdrojový virtuální počítač pro instalaci agenta Mobility. Tato chyba nastane, pokud zdrojový počítač není dosažitelný kvůli problémům se síťovým připojením. Pokud chcete vyřešit,

* Ujistěte se, že můžete provádět na příkaz ping zdrojový počítač z konfiguračního serveru. Pokud jste vybrali horizontální navýšení kapacity procesového serveru při povolení replikace, ujistěte se, že můžete provádět na příkaz ping zdrojový počítač z procesového serveru.
  * Ze zdrojového serveru počítače příkazového řádku pomocí Telnetu odešlete zprávu ping konfigurační server / horizontální navýšení kapacity procesového serveru s portem https (135), jak vidíte níže, jestli jsou všechny problémy se síťovým připojením nebo brány firewall portu blokující problémy.

     `telnet <CS/ scale-out PS IP address> <135>`
* Kromě toho **virtuálního počítače s Linuxem**,
  * Zkontrolujte, jestli jsou nainstalovaná nejnovější balíčky openssh, openssh-server a openssl.
  * Zkontrolujte a ujistěte se, že Secure Shell (SSH) zapnutá a běží na portu 22.
  * By měl běžet služba SFTP. Pokud chcete povolit ověřování SFTP subsystému a heslo v souboru sshd_config
    * Přihlaste se jako uživatel root.
    * Přejděte k souboru /etc/ssh/sshd_config, vyhledejte řádek, který začíná PasswordAuthentication.
    * Zrušte na řádku komentář a změňte hodnotu na hodnotu Ano
    * Vyhledejte řádek, který začíná subsystému a zrušte na řádku komentář
    * Restartujte službu sshd.
* Pokus o připojení může mít se nezdařilo, pokud neexistuje žádná správná odpověď po určitou dobu nebo navázané připojení se nezdařila, protože připojený hostitel se nepodařilo odpovědět.
* Připojení/sítě nebo domény může být problém související s. Může být také způsobena název DNS, které řeší problém nebo problém vyčerpání portů TCP. Zkontrolujte, jestli jsou ve vaší doméně všech známých problémů.

## <a name="connectivity-failure-errorid-95523"></a>Chyba připojení (ID chyby: 95523)

Tato chyba nastane, pokud síť, ve kterém se nachází zdrojový počítač nebyl nalezen nebo mohla být odstraněna nebo již není k dispozici. Tím zajistíte, že existuje síť je jediný způsob, jak tuto chybu napravíme.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Kontrola služby Sdílení souborů a tiskáren (ID chyby: 95105 & 95106)

Po kontrole připojení Ujistěte se, že je ve vašem virtuálním počítači povolit služby Sdílení souborů a tiskáren. Tato nastavení jsou nutné ke kopírování agenta Mobility na zdrojový počítač.

Pro **windows 2008 R2 a v předchozích verzích**,

* Povolit sdílení souborů a tiskáren přes bránu Windows Firewall
  * Otevřete ovládací panely -> systém a zabezpečení -> Brána Windows Firewall. -> v levém podokně, klikněte na tlačítko Upřesnit nastavení -> klikněte ve stromu konzoly na příchozí pravidla.
  * Vyhledejte pravidla a sdílení tiskáren (NB-Session-In) a soubor sdílení souborů a tiskáren (SMB-In). Pro každé pravidlo, klikněte pravým tlačítkem na pravidlo a pak klikněte na tlačítko **Povolit pravidlo**.
* Povolit sdílení pomocí zásad skupiny souborů
  * Přejít na začátek zadejte gpmc.msc a hledání.
  * V navigačním podokně otevřete následující složky: Zásady místního počítače, konfigurace uživatele, šablony pro správu, součásti Windows a sdílení v síti.
  * V podokně podrobností klikněte dvakrát na **zabránit uživatelům ve sdílení souborů v rámci jejich profilu**. Zakázání nastavení zásad skupiny a povolení uživatele umožňují sdílet soubory, klikněte na možnost zakázáno. Klikněte na tlačítko OK uložte provedené změny. Další informace, klikněte na tlačítko [tady](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Pro **novější verze**, postupujte podle pokynů uvedených [tady](vmware-azure-install-mobility-service.md) umožňující sdílení souborů a tiskáren.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Kontrola konfigurace Windows Management Instrumentation (WMI) (kód chyby: 95103)

Po kontrole služby souborů a tiskáren, povolení služby WMI pro privátní, veřejná a domény profily přes bránu firewall. Tato nastavení jsou nutné k dokončení vzdálené spuštění na zdrojovém počítači. Pokud chcete povolit,

* Přejděte do ovládacích panelů, klikněte na tlačítko zabezpečení a pak klikněte na možnost Brána Windows Firewall.
* Klikněte na změnit nastavení a potom klikněte na kartu výjimky.
* V okně Výjimky zaškrtněte políčko pro Windows Management Instrumentation (WMI) pro povolení provozu služby WMI přes bránu firewall. 

Můžete také povolit provoz WMI přes bránu firewall na příkazovém řádku. Pomocí následujícího příkazu `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Další články pro řešení problémů WMI nelze nalézt v následujících článcích.

* [Základní testování služby WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Řešení problémů WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Poradce při potížích se skripty WMI a služby rozhraní WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Nepodporovaný operační systémy

Další nejčastější příčinou selhání může být způsobeno nepodporovaný operační systém. Ujistěte se, že používáte podporovanou verzi operačního systému nebo jádra pro úspěšnou instalaci služby Mobility. Vyhněte se použití privátní opravy.
Chcete-li zobrazit seznam operační systémy a verze jádra, které Azure Site Recovery podporuje, najdete v našich [dokument matice podpory](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nepodporované konfigurace spouštěcího disku (ID chyby: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Spouštěcí a systémové oddíly nebo svazky nejsou stejném disku (ID chyby: 95309)

Před 9.20 verze, spouštěcí a systémové oddíly nebo svazky na různých discích was má nepodporovanou konfiguraci. Z [9.20 verze](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), tato konfigurace je podporovaná. Tato podpora používali nejnovější verzi.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Spouštěcí disk není k dispozici (ID chyby: 95310)

Virtuální počítač bez spouštěcí disk nejde chránit. To je zajistit hladký průběh obnovení virtuálního počítače během operace převzetí služeb při selhání. Neexistence spouštěcí disk má za následek selhání ke spuštění počítače po převzetí služeb při selhání. Ujistěte se, že virtuální počítač obsahuje spouštěcí disk a operaci opakujte. Všimněte si také, že se nepodporuje více spouštěcích disků na stejném počítači.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Na zdrojovém počítači k dispozici více spouštěcích disků (ID chyby: 95311)

Virtuální počítač s více spouštěcí disk není [podporované konfigurace](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systémový oddíl na více discích (ID chyby: 95313)

Dříve než ve verzi 9.20 kořenovém oddílu nebo svazku rozdělený na několik disků se má nepodporovanou konfiguraci. Z [9.20 verze](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), tato konfigurace je podporovaná. Tato podpora používali nejnovější verzi.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Povolení ochrany se nezdařila, protože podle konfigurace GRUB místo UUID název zařízení (ID chyby: 95320)

**Možné příčiny:** </br>
Konfigurační soubory GRUB ("/ boot/grub/menu.lst", "/ boot/grub/grub.cfg", "/ boot/grub2/grub.cfg" nebo "/ etc/výchozí/grub") může obsahovat hodnotu pro parametry **kořenové** a **obnovit** jako zařízení skutečné názvy místo UUID. Site Recovery určuje UUID přístup podle názvu zařízení můžou změnit napříč restartování virtuálního počítače, protože virtuální počítač nemusí přijde up se stejným názvem na převzetí služeb při selhání, což vede k problémům. Příklad: </br>


- Následující řádek je ze souboru GRUB **/boot/grub2/grub.cfg**. <br>
*Linux /boot/vmlinuz-3.12.49-11-default **root = / dev/sda2** ${extra_cmdline} **obnovit = / dev/sda1** splash = silent bezobslužný showopts*


- Následující řádek je ze souboru GRUB **/boot/grub/menu.lst**
*jádra /boot/vmlinuz-3.0.101-63-default **root = / dev/sda2** **obnovit = / dev/sda1 ** splash = tiché crashkernel = 256M-:128M showopts vga = 0x314*

Pokud zjistíte tučné řetězec výše, GRUB má skutečné zařízení názvy parametrů "root" a "obnovit" místo UUID.
 
**Jak vyřešit:**<br>
Názvy zařízení mělo být nahrazeno s odpovídající identifikátor UUID.<br>


1. Najít identifikátor UUID zařízení spuštěním příkazu "blkid <device name>". Příklad:<br>
```
blkid /dev/sda1
/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
blkid /dev/sda2 
/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
```

2. Nyní nahraďte její identifikátor UUID v následujícím formátu název zařízení "kořenový = UUID =<UUID>". Například, pokud jsme nahradit názvy zařízení UUID pro kořenové a pokračovat v parametru zmíněné v souborech "/ boot/grub2/grub.cfg", "/ boot/grub2/grub.cfg" nebo "/ etc/výchozí/grub: pak řádků v souborech bude vypadat takto. <br>
*jádra /boot/vmlinuz-3.0.101-63-default **kořenové = UUID = 62927e85 f7ba-40bc-9993-cc1feeb191e4** **obnovit = UUID = 6f614b44 433b-431b-9ca1-4dd2f6f74f6b** splash = tiché crashkernel = 256M-:128M showopts vga = 0x314*
3. Opětné spuštění ochrany znovu

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Nainstalovat službu Mobility dokončeno s upozorněním restartování (ID chyby: 95265 & 95266)

Službu mobility Site Recovery má spoustu součástí, z nichž jeden je volána ovladač filtru. Ovladač filtru získá načtou do systémové paměti pouze po jednom restartování systému. To znamená, že opravy ovladač filtru pouze se dají realizovat nový ovladač filtru je načtena; které může dojít pouze v době restartování systému.

**Mějte prosím na paměti** , toto je upozornění a stávající replikaci budou fungovat i po nová aktualizace agenta. Je možné restartovat, kdykoli budete chtít získat výhody nový ovladač filtru, ale pokud nemáte restartovat než také starý filtru ovladačů udržuje o práci. Ano, po aktualizaci bez restartování, kromě ovladač filtru **získá realizované výhody další vylepšení a oprav ve službě mobility**. Takže i když doporučuje, není to povinné restartování po každém upgradu. Informace o tom, kdy je povinný restart, klikněte na tlačítko [tady](https://aka.ms/v2a_asr_reboot).

> [!TIP]
>Doporučených postupech ohledně plánování upgrady období údržby, najdete v tématu [tady](https://aka.ms/v2a_asr_upgrade_practice).

## <a name="lvm-support-from-920-version"></a>Podpora LVM z 9.20 verze

Dříve než ve verzi 9.20 LVM je podporován pouze datových disků. Boot by měl být na disku oddílu a nesmí být svazku LVM.

Z [9.20 verze](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), [disk s operačním systémem na LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) je podporována. Tato podpora používali nejnovější verzi.

## <a name="insufficient-space-errorid-95524"></a>Nedostatek místa (ID chyby: 95524)

Při kopírování agenta Mobility na zdrojový počítač se vyžaduje aspoň 100 MB volného místa. Ano Ujistěte se, že zdrojový počítač má požadované volné místo a zkuste operaci zopakovat.

## <a name="vss-installation-failures"></a>Selhání instalace služby VSS

Instalace služby VSS je součástí instalace agenta Mobility. Tato služba se používá při generování body obnovení konzistentní vzhledem k aplikaci. K selhání během instalace služby VSS může dojít z několika důvodů. Chcete-li zjistit přesný chyby, přečtěte si **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. V následující části jsou zvýrazněny několik běžných chyb a kroků pro řešení.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Došlo k chybě VSS-2147023170 [0x800706BE] - ukončovací kód 511

Tento problém většinou dochází, když antivirový software blokuje provoz služby Azure Site Recovery. Chcete-li vyřešit,

1. Vyloučit všechny složky, které jsou uvedené [tady](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Postupujte podle pokynů publikovaných poskytovatelem antivirový program odblokujete registrace knihovny DLL ve Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Došlo k chybě VSS 7 [0x7] - ukončovací kód 511

Toto je chyba za běhu a příčinou je nedostatek paměti k instalaci aplikace VSS. Nezapomeňte zvětšete místo na disku pro úspěšné dokončení této operace.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Došlo k chybě VSS-2147023824 [0x80070430] - ukončovací kód. 517

Tato chyba nastane, pokud je služba Azure Site Recovery VSS Provider [označená k odstranění](https://msdn.microsoft.com/en-us/library/ms838153.aspx). Pokus nainstalovat VSS na zdrojovém počítači ručně spuštěním následující příkazový řádek

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Došlo k chybě VSS-2147023841 [0x8007041F] - ukončovací kód 512

Tato chyba nastane, pokud je databáze služby Azure Site Recovery VSS Provider [uzamčen](https://msdn.microsoft.com/en-us/library/ms833798.aspx). Pokus nainstalovat VSS na zdrojovém počítači ručně spuštěním následující příkazový řádek

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>VSS ukončovací kód 806

Tato chyba nastane, pokud uživatelský účet použitý k instalaci nemá oprávnění k provedení příkazu CSScript. Zadejte potřebná oprávnění pro uživatelský účet pro spuštění skriptu a operaci opakujte.

### <a name="other-vss-errors"></a>Další chyby VSS.

Pokus nainstalovat poskytovatele služby VSS na zdrojovém počítači ručně spuštěním následující příkazový řádek

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="next-steps"></a>Další postup

[Zjistěte, jak](vmware-azure-tutorial.md) nastavit zotavení po havárii pro virtuální počítače VMware.
