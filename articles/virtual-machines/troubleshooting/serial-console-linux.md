---
title: Azure Serial Console pro Linux | Dokumenty společnosti Microsoft
description: Obousměrná sériová konzola pro virtuální počítače Azure a škálovací sady virtuálních počítačů.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: b1f7708c9bd213e201ba4eb8837a191dca68ca9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167015"
---
# <a name="azure-serial-console-for-linux"></a>Azure Serial Console for Linux

Serial Console na portálu Azure poskytuje přístup k textové konzoli pro virtuální počítače (VMs) linuxa a škálovací sady virtuálních počítačů instance. Toto sériové připojení se připojuje k sériovému portu ttys0 instance škálovací sady virtuálního počítače nebo virtuálního počítače a poskytuje k němu přístup nezávisle na stavu sítě nebo operačního systému. K sériové konzoli lze přistupovat jenom pomocí portálu Azure portal a je povolena jenom pro ty uživatele, kteří mají přístupovou roli přispěvatele nebo vyšší pro škálovací sadu virtuálního počítače nebo virtuálního počítače.

Serial Console funguje stejným způsobem pro virtuální počítače a instance škálovací sady virtuálních strojů. V tomto dokumentu budou všechny zmínky o virtuálních počítačích implicitně zahrnovat instance škálovací sady virtuálních strojů, pokud není uvedeno jinak.

Dokumentace ke konzole Sériové houštiny pro systém Windows naleznete [v tématu Konzola sériové hospo- pro systém Windows](../windows/serial-console.md).

> [!NOTE]
> Serial Console je obecně dostupná v globálních oblastech Azure a ve verzi Public Preview v Azure Government. V cloudu Azure China ještě není dostupná.


## <a name="prerequisites"></a>Požadavky

- Instance škálovací sady virtuálních počítače nebo virtuálního počítače musí používat model nasazení správy prostředků. Klasická nasazení nejsou podporována.

- Váš účet, který používá sériovou konzolu, musí mít [roli přispěvatele virtuálního počítače](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) pro virtuální počítač a účet úložiště [diagnostiky spouštění](boot-diagnostics.md)

- Instance škálovací sady virtuálního počítače nebo virtuálního počítače musí mít uživatele založeného na heslech. Můžete vytvořit jeden s funkcí [resetování hesla](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) rozšíření přístupu k virtuálnímu počítače. V části **Podpora + řešení potíží** vyberte Obnovit **heslo.**

- Instance škálovací sady virtuálního počítače nebo virtuálního počítače musí mít [povolenou diagnostiku spouštění.](boot-diagnostics.md)

    ![Nastavení diagnostiky spouštění](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Nastavení specifická pro linuxové distribuce naleznete v [tématu Dostupnost distribuce Linuxu v sériové konzoli](#serial-console-linux-distribution-availability).

- Instance škálovací sady virtuálního počítače nebo virtuálního `ttys0`počítače musí být nakonfigurovaná pro sériový výstup na . Toto je výchozí pro image Azure, ale budete chtít zkontrolovat na vlastní image. Podrobnosti [níže](#custom-linux-images).


> [!NOTE]
> Sériová konzola vyžaduje místního uživatele s nakonfigurovaným heslem. Virtuální počítače nebo škálovací sady virtuálních strojů nakonfigurované jenom s veřejným klíčem SSH se nebudou moci přihlásit k sériové konzole. Chcete-li vytvořit místního uživatele s heslem, použijte [rozšíření VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), které je dostupné na portálu, výběrem **možnosti Resetovat heslo** na webu Azure portal a vytvořte místního uživatele s heslem.
> Můžete také obnovit heslo správce ve svém účtu [pomocí GRUB pro spuštění do režimu jednoho uživatele](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Dostupnost distribuce Linuxu pro sériovou konzolu
Aby konzola sériového zařízení fungovala správně, musí být hostovaný operační systém nakonfigurován tak, aby přijíme a zapisoval zprávy konzoly do sériového portu. Většina [schválených distribucí Azure Linuxu](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) má ve výchozím nastavení nakonfigurovanou konzolu. Výběr **sériové konzoly** v části **Podpora + řešení potíží** na portálu Azure poskytuje přístup k konzoli serial.

> [!NOTE]
> Pokud v konzole sériové konzole nic nevidíte, ujistěte se, že je na vašem virtuálním počítači povolena diagnostika spouštění. Bít **Enter** často řeší problémy, kdy se v sériové konzole nic nezobrazuje.

Distribuce      | Přístup k sériové konzole
:-----------|:---------------------
Red Hat Enterprise Linux    | Přístup ke sériové konzoli je ve výchozím nastavení povolen.
CentOS      | Přístup ke sériové konzoli je ve výchozím nastavení povolen.
Debian      | Přístup ke sériové konzoli je ve výchozím nastavení povolen.
Ubuntu      | Přístup ke sériové konzoli je ve výchozím nastavení povolen.
CoreOS      | Přístup ke sériové konzoli je ve výchozím nastavení povolen.
SUSE        | Novější image SLES dostupné v Azure mají ve výchozím nastavení povolený přístup ke konzole. Pokud používáte starší verze (10 nebo starší) SLES v Azure, najdete v [článku KB](https://www.novell.com/support/kb/doc.php?id=3456486) povolit sériovou konzolu.
Oracle Linux        | Přístup ke sériové konzoli je ve výchozím nastavení povolen.

### <a name="custom-linux-images"></a>Vlastní linuxové obrázky
Chcete-li povolit sériovou konzolu pro vlastní bitovou kopii virtuálního počítače s `ttyS0`Linuxem, povolte přístup ke konzoli v souboru */etc/inittab* a spusťte terminál na . Například: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Možná budete také muset plodit getty na ttyS0. To lze provést `systemctl start serial-getty@ttyS0.service`s .

Budete také chtít přidat ttys0 jako cíl pro sériový výstup. Další informace o konfiguraci vlastní bitové kopie pro práci se sériovou konzolou najdete v tématu obecné systémové požadavky na [webu Vytvoření a nahrání virtuálního pevného disku Linux v Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Pokud vytváříte vlastní jádro, zvažte povolení `CONFIG_SERIAL_8250=y` těchto `CONFIG_MAGIC_SYSRQ_SERIAL=y`příznaků jádra: a . Konfigurační soubor je obvykle umístěn v *cestě /boot/.*

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Běžné scénáře pro přístup k konzole Serial Console

Scénář          | Akce v konzole Serial Console
:------------------|:-----------------------------------------
Nefunkční soubor *FSTAB* | Stisknutím klávesy **Enter** pokračujte v opravě souboru *FSTAB* pomocí textového editoru. Možná budete muset být v režimu jednoho uživatele, aby tak učinily. Další informace naleznete v části sériové konzole [Jak opravit problémy fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) a [pomocí sériové konzoly pro přístup k GRUB a režimu jednoho uživatele](serial-console-grub-single-user-mode.md).
Nesprávná pravidla brány firewall |  Pokud jste nakonfigurovali iptables blokovat připojení SSH, můžete použít sériovou konzolu pro interakci s virtuálním počítačem bez nutnosti SSH. Více informací naleznete na [stránce iptables man .](https://linux.die.net/man/8/iptables)<br>Podobně pokud váš firewalld blokuje přístup SSH, můžete přistupovat k virtuálnímu počítači prostřednictvím sériové konzoly a překonfigurovat firewalld. Více informací naleznete v [dokumentaci k firewallu](https://firewalld.org/documentation/).
Poškození/kontrola souborového systému | Další podrobnosti o řešení potíží s poškozenými souborovými systémy pomocí sériové konzole naleznete v části sériové konzoly [v virtuálním počítači Azure Linux.](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck)
Problémy s konfigurací SSH | Získejte přístup k konzole sériového zařízení a změňte nastavení. Sériovou konzolu lze použít bez ohledu na konfiguraci SSH virtuálního počítače, protože nevyžaduje, aby virtuální počítače měl síťové připojení k dispozici. Průvodce odstraňováním potíží je k dispozici na [řešení potíží s připojením SSH k virtuálnímu počítači Azure Linux, který selže, chyby nebo je odmítnut](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Další podrobnosti jsou k dispozici na [podrobné kroky řešení potíží SSH pro problémy připojení k virtuálnímu počítači SN Linux v Azure](./detailed-troubleshoot-ssh-connection.md)
Interakce se zavaděcem | Restartujte virtuální počítač z okna sériové konzole pro přístup ke grubu na vašem virtuálním počítači s Linuxem. Další podrobnosti a informace specifické pro distro, naleznete [v tématu Použití konzoly sériového připojení pro přístup ke službě GRUB a režimu jednoho uživatele](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Zakázání konzoly Sériové houšti

Ve výchozím nastavení mají všechna předplatná povolen přístup ke konzole. Sériovou konzolu můžete zakázat na úrovni předplatného nebo na úrovni škálovací sady virtuálních zařízení/virtuálních strojů. Podrobné pokyny najdete v části [Povolit a zakázat konzolu Azure Serial Console](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Zabezpečení konzoly sériového portu

### <a name="access-security"></a>Zabezpečení přístupu
Přístup k konzole sériového zařízení je omezen na uživatele, kteří mají přístupovou roli [přispěvatele virtuálního počítače](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebo vyšší k virtuálnímu počítači. Pokud váš tenant Azure Active Directory vyžaduje vícefaktorové ověřování (MFA), bude přístup k sériové konzoli také potřebovat vícefaktorovou pomocí, protože přístup sériové konzole je prostřednictvím [portálu Azure](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Všechna data, která jsou odesílána tam a zpět, jsou šifrována na lince.

### <a name="audit-logs"></a>Protokoly auditu
Veškerý přístup ke sériové konzoli je aktuálně zaznamenán do protokolů [diagnostiky spouštění](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) virtuálního počítače. Přístup k těmto protokolům vlastní a řídí správce virtuálního počítače Azure.

> [!CAUTION]
> Nejsou zaznamenána žádná přístupová hesla pro konzolu. Pokud však příkazy spuštěné v rámci konzoly obsahují nebo výstup hesla, tajné klíče, uživatelská jména nebo jakoukoli jinou formu osobně identifikovatelné informace (PII), budou zapsány do protokolů diagnostiky spuštění virtuálního počítače. Budou napsány spolu se všemi ostatními viditelným textem jako součást implementace funkce posunu zpět sériové konzole. Tyto protokoly jsou cyklické a přístup k nim mají pouze osoby s oprávněním ke čtení pro účet úložiště diagnostiky. Pokud zadáváte data nebo příkazy, které obsahují tajné kódy nebo PII, doporučujeme používat SSH, pokud není sériová konzola nezbytně nutná.

### <a name="concurrent-usage"></a>Souběžné použití
Pokud je uživatel připojen k sériové konzole a jiný uživatel úspěšně požaduje přístup ke stejnému virtuálnímu počítači, bude první uživatel odpojen a druhý uživatel připojen ke stejné relaci.

> [!CAUTION]
> To znamená, že odpojený uživatel nebude odhlášen. Možnost vynutit odhlášení při odpojení (pomocí SIGHUP nebo podobného mechanismu) je stále na plánu. Pro windows je povolen automatický časový čas ve speciální konzoli pro správu (SAC); pro Linux však můžete nakonfigurovat nastavení časového času terminálu. Chcete-li tak `export TMOUT=600` učinit, přidejte do souboru *.bash_profile* nebo *.profile* uživatele, který se přihlašuje ke konzole. Toto nastavení bude časový majek relace po 10 minutách.

## <a name="accessibility"></a>Usnadnění
Přístupnost je klíčovým zaměřením pro konzolu Azure Serial Console. Za tímto účelem jsme zajistili, že sériová konzola je plně přístupná.

### <a name="keyboard-navigation"></a>Procházení pomocí klávesnice
Pomocí klávesy **Tab** na klávesnici můžete procházet rozhraní množiny na webu Azure Portal. Vaše poloha bude na obrazovce zvýrazněna. Chcete-li ponechat fokus okna sériové konzole, stiskněte na klávesnici **kombinaci kláves Ctrl**+**F6.**

### <a name="use-serial-console-with-a-screen-reader"></a>Použití konzoly Serial Console se čtečkou obrazovky
Sériová konzola má vestavěnou podporu čtečky obrazovky. Při procházení se zapnutou čtečkou obrazovky umožní, aby program pro čtení z obrazovky přečetl alternativní text aktuálně vybraného tlačítka.

## <a name="known-issues"></a>Známé problémy
Jsme si vědomi některých problémů se sériovou konzolou a operačním systémem virtuálního zařízení. Tady je seznam těchto problémů a kroky pro zmírnění pro virtuální počítače s Linuxem. Tyto problémy a skutečnosti snižující závažnost rizika platí pro virtuální počítače i instance škálovací sady virtuálních počítačů. Pokud se neshodují s chybou, kterou vidíte, přečtěte si běžné chyby služby sériové konzole na [běžných chybách konzoly sériové hospo-](./serial-console-errors.md)

Problém                           |   Omezení rizik
:---------------------------------|:--------------------------------------------|
Po zobrazení nápisu **Připojení** nezpůsobíte zobrazení výzvy k přihlášení. | GRUB nemusí být správně nakonfigurován. Spusťte následující `grub2-mkconfig -o /etc/grub2-efi.cfg` příkazy: `grub2-mkconfig -o /etc/grub2.cfg`a/nebo . Další informace naleznete v tématu [Bít zadejte neprovede žádné .](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) K tomuto problému může dojít, pokud používáte vlastní virtuální počítač, tvrzené zařízení nebo grub config, který způsobí, že Linux se nezdaří připojení k sériovému portu.
Text konzoly sériové hodu zabírá pouze část velikosti obrazovky (často po použití textového editoru). | Sériové konzoly nepodporují vyjednávání o velikosti okna[(RFC 1073),](https://www.ietf.org/rfc/rfc1073.txt)což znamená, že nebude odeslán žádný signál SIGWINCH, který by aktualizoval velikost obrazovky, a virtuální počítač nebude mít žádné znalosti o velikosti vašeho terminálu. Nainstalujte xterm nebo podobný nástroj, `resize` který vám `resize`poskytne příkaz, a poté spusťte .
Vkládání dlouhých strun nefunguje. | Konzola sériového systému omezuje délku řetězců vnesených do terminálu na 2048 znaků, aby nedošlo k přetížení šířky pásma sériového portu.
Nevyzpytatelný vstup z klávesnice v obrazech SLES BYOS. Vstup z klávesnice je rozpoznán pouze sporadicky. | Tohle je problém s plymouthovým balíčkem. Plymouth by neměl být spuštěn v Azure, protože nepotřebujete úvodní obrazovku a Plymouth narušuje schopnost platformy používat serial console. Odstraňte `sudo zypper remove plymouth` Plymouth s a restartujte počítač. Případně upravte řádek jádra konfigurace GRUB připojením `plymouth.enable=0` na konec řádku. Můžete to udělat [úpravou spouštěcí položky při startu](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles), nebo `/etc/default/grub`úpravou GRUB_CMDLINE_LINUX řádku `grub2-mkconfig -o /boot/grub2/grub.cfg`v , přestavba GRUB s a restartu.


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Jak mohu odeslat zpětnou vazbu?**

A. Poskytněte zpětnou vazbu vytvořením problému GitHub u . https://aka.ms/serialconsolefeedback Alternativně (méně preferované), můžete azserialhelp@microsoft.com odeslat zpětnou vazbu prostřednictvím nebo v kategorii virtuálních https://feedback.azure.comstrojů .

**Otázka: Podporuje konzola sériového zařízení kopírování a vkládání?**

A. Ano. Ke kopírování a vkládání do terminálu použijte **klávesy Ctrl**+**Shift**+**C** a **Ctrl**+**Shift**+**V.**

**Otázka: Mohu použít sériovou konzolu místo připojení SSH?**

A. I když se toto použití může zdát technicky možné, sériová konzola je určena především jako nástroj pro řešení potíží v situacích, kdy připojení přes SSH není možné. Doporučujeme nepoužívat sériovou konzolu jako náhradu SSH z následujících důvodů:

- Sériová konzola nemá takovou šířku pásma jako SSH. Vzhledem k tomu, že se jedná o připojení pouze pro text, je obtížné více interakcí s vysokým zatížením gui.
- Přístup ke konzole sériové ho je v současné době možný pouze pomocí uživatelského jména a hesla. Vzhledem k tomu, že klíče SSH jsou mnohem bezpečnější než kombinace uživatelského jména a hesla, z hlediska zabezpečení přihlášení doporučujeme SSH přes sériovou konzolu.

**Otázka: Kdo může povolit nebo zakázat sériovou konzolu pro mé předplatné?**

A. Chcete-li povolit nebo zakázat sériovou konzolu na úrovni předplatného, musíte mít oprávnění k zápisu k předplatnému. Role, které mají oprávnění k zápisu, zahrnují role správce nebo vlastníka. Vlastní role mohou mít také oprávnění k zápisu.

**Otázka: Kdo má přístup k konzoli sériové houštiny pro škálovací sadu virtuálních počítačů nebo virtuálních strojů?**

A. Chcete-li získat přístup k konzolové konzole, musíte mít roli přispěvatele virtuálního počítače nebo vyšší nebo vyšší pro škálovací sadu virtuálních počítačů nebo pro virtuální počítač.

**Otázka: Na konzole sériové konzole se nic nezobrazuje, co mám dělat?**

A. Obrázek je pravděpodobně nesprávně nakonfigurován pro přístup ke sériové konzoli. Informace o konfiguraci bitové kopie pro povolení sériové konzoly naleznete v [tématu Dostupnost distribuce linuxové konzoly v sériové konzole](#serial-console-linux-distribution-availability).

**Otázka: Je sériová konzola k dispozici pro škálovací sady virtuálních strojů?**

A. Ano, je! Viz [Sériová konzola pro škálovací sady virtuálních strojů](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**Otázka: Pokud nastavím škálovací sadu virtuálních počítačů nebo virtuálních strojů pomocí jenom ověřování pomocí klíče SSH, můžu se stále připojit k instanci škálovací sady virtuálních zařízení/virtuálních strojů?**

A. Ano. Vzhledem k tomu, že konzola sériové ho schrápění nevyžaduje klávesy SSH, stačí nastavit kombinaci uživatelského jména a hesla. Můžete tak učinit tak, že vyberete **resetovat heslo** na webu Azure portal a pomocí těchto přihlašovacích údajů se přihlásíte k sériové konzole.

## <a name="next-steps"></a>Další kroky
* Pomocí konzoly sériového portu [přemísíčte grub a režim u jednoho uživatele](serial-console-grub-single-user-mode.md).
* Použijte sériovou konzolu pro [volání NMI a SysRq](serial-console-nmi-sysrq.md).
* Naučte se používat sériovou konzolu k [povolení GRUB v různých distribucích](serial-console-grub-proactive-configuration.md)
* Sériová konzola je k dispozici také pro [virtuální servery windows](../windows/serial-console.md).
* Další informace o [diagnostice spuštění](boot-diagnostics.md).

