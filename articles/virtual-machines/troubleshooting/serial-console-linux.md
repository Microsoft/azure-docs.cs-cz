---
title: Sériová konzola Azure pro Linux | Microsoft Docs
description: Bi-Directional sériové konzole pro Azure Virtual Machines a Virtual Machine Scale Sets pomocí příkladu Linux.
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
ms.openlocfilehash: 9a31a22a5b037162198f594d9bcf35c91a0a4654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306867"
---
# <a name="azure-serial-console-for-linux"></a>Azure Serial Console for Linux

Konzola sériového portu v Azure Portal poskytuje přístup k textové konzole pro virtuální počítače se systémem Linux a instance sady škálování virtuálních počítačů. Toto sériové připojení se připojuje k sériovému portu ttyS0 virtuálního počítače nebo instance sady škálování virtuálních počítačů a poskytuje přístup k němu nezávisle na stavu sítě nebo operačního systému. Ke konzole sériového přístupu se dá přistupovat jenom pomocí Azure Portal a je povolená jenom pro uživatele, kteří mají roli přístupu přispěvatel nebo vyšší, k virtuálnímu počítači nebo sadě škálování virtuálních počítačů.

Sériová konzola funguje stejným způsobem pro virtuální počítače a instance sady škálování virtuálních počítačů. V tomto dokumentu budou všechny zmínky k virtuálním počítačům implicitně zahrnovat instance sady škálování virtuálních počítačů, pokud není uvedeno jinak.

Konzola sériového portu je všeobecně dostupná v globálních oblastech Azure a ve verzi Public Preview v Azure Government. Ještě není k dispozici v cloudu Azure Čína.

Dokumentaci k sériové konzole pro Windows najdete v tématu [sériová Konzola pro Windows](./serial-console-windows.md).

> [!NOTE]
> Sériová konzola je aktuálně nekompatibilní se spravovaným účtem úložiště diagnostiky spouštění. Pokud chcete použít sériovou konzolu, ujistěte se, že používáte vlastní účet úložiště.

## <a name="prerequisites"></a>Požadavky

- Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí používat model nasazení správy prostředků. Klasická nasazení nejsou podporovaná.

- Váš účet, který používá sériová konzola, musí mít [roli Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) pro virtuální počítač a účet úložiště [diagnostiky spouštění](boot-diagnostics.md) .

- Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí mít uživatele založené na heslech. Můžete ho vytvořit pomocí funkce [resetovat heslo](../extensions/vmaccess.md#reset-password) rozšíření pro přístup k virtuálním počítačům. V části **Podpora a řešení potíží** vyberte **resetovat heslo** .

- Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí mít povolenou [diagnostiku spouštění](boot-diagnostics.md) .

    ![Nastavení diagnostiky spouštění](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Nastavení specifická pro distribuce systému Linux najdete v tématu [dostupnost distribuce sériová konzola Linux](#serial-console-linux-distribution-availability).

- U virtuálního počítače nebo instance sady škálování virtuálních počítačů musí být nakonfigurovaná pro sériové výstup `ttys0` . Toto je výchozí nastavení pro Image Azure, ale u vlastních imagí je budete chtít dvakrát ověřit. Podrobnosti [níže](#custom-linux-images).


> [!NOTE]
> Konzola sériového portu vyžaduje, aby místní uživatel s nakonfigurovaným heslem. Virtuální počítače nebo sady škálování virtuálních počítačů, které jsou nakonfigurované jenom pomocí veřejného klíče SSH, se nebudou moct přihlásit ke konzole sériového portu. Chcete-li vytvořit místního uživatele s heslem, použijte [rozšíření VMAccess](../extensions/vmaccess.md), které je k dispozici na portálu, a vyberte možnost **resetovat heslo** v Azure Portal a vytvořte místního uživatele s heslem.
> Můžete také resetovat heslo správce v účtu [pomocí grub ke spuštění do režimu jednoho uživatele](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Dostupnost distribuce systému Linux v sériové konzole
Aby mohla konzola sériového fungování fungovat správně, musí být hostovaný operační systém nakonfigurovaný na čtení a zápis zpráv konzoly do sériového portu. U nejdůležitějších [distribucí systému Azure Linux](../linux/endorsed-distros.md) je ve výchozím nastavení nakonfigurována sériová konzola. Výběr **sériová konzola** v části **Podpora a řešení potíží** v Azure Portal poskytuje přístup ke konzole sériového portu.

> [!NOTE]
> Pokud nevidíte cokoli v konzole sériového portu, ujistěte se, že je na vašem VIRTUÁLNÍm počítači povolená Diagnostika spouštění. Při stisknutí této akce budou často opravovat problémy, **které se v** konzole sériového umístění nezobrazí.

Distribuce      | Přístup k sériové konzole
:-----------|:---------------------
Red Hat Enterprise Linux    | Ve výchozím nastavení je povolený přístup Sériová konzola.
CentOS      | Ve výchozím nastavení je povolený přístup Sériová konzola.
Debian      | Ve výchozím nastavení je povolený přístup Sériová konzola.
Ubuntu      | Ve výchozím nastavení je povolený přístup Sériová konzola.
CoreOS      | Ve výchozím nastavení je povolený přístup Sériová konzola.
SUSE        | Novější image SLES dostupné v Azure mají ve výchozím nastavení povolený přístup pomocí sériové konzoly. Pokud používáte starší verze (10 nebo starší) služby SLES v Azure, přečtěte si [článek znalostní báze](https://www.novell.com/support/kb/doc.php?id=3456486) , ve kterém můžete povolit sériovou konzolu.
Oracle Linux        | Ve výchozím nastavení je povolený přístup Sériová konzola.

### <a name="custom-linux-images"></a>Vlastní image Linuxu
Pokud chcete pro vlastní image virtuálního počítače se systémem Linux povolit konzolu sériového prostředí, povolte přístup k konzole v souboru */etc/inittab* pro spuštění terminálu `ttyS0` . Například: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. V ttyS0 možná budete muset vytvořit také Getty. To lze provést pomocí `systemctl start serial-getty@ttyS0.service` .

Budete také chtít přidat ttyS0 jako cíl pro sériový výstup. Další informace o konfiguraci vlastní image pro práci s konzolou sériového rozhraní najdete v tématu Obecné požadavky na systém při [vytváření a nahrání virtuálního pevného disku pro Linux v Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Pokud vytváříte vlastní jádro, zvažte povolení těchto příznaků jádra: `CONFIG_SERIAL_8250=y` a `CONFIG_MAGIC_SYSRQ_SERIAL=y` . Konfigurační soubor se obvykle nachází v cestě */boot/* .

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Běžné scénáře přístupu ke konzole sériového portu

Scénář          | Akce v konzole sériového portu
:------------------|:-----------------------------------------
Poškozený soubor *FSTAB* | Pokračujte stisknutím klávesy **ENTER** a opravte soubor *FSTAB* pomocí textového editoru. Je možné, že budete muset být v režimu jednoho uživatele. Další informace najdete v části "sériová konzola" o [tom, jak opravit problémy fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) a [pomocí konzoly sériového přístupu získat přístup k grub a jednomu uživatelskému režimu](serial-console-grub-single-user-mode.md).
Nesprávná pravidla brány firewall |  Pokud jste nakonfigurovali softwaru iptables k blokování připojení SSH, můžete pomocí sériové konzoly pracovat s VIRTUÁLNÍm počítačem bez nutnosti SSH. Další podrobnosti najdete na [stránce softwaru iptables Man](https://linux.die.net/man/8/iptables).<br>Podobně pokud brána firewall blokuje přístup přes SSH, můžete k virtuálnímu počítači přistupovat pomocí sériové konzoly a znovu nakonfigurovat bránu firewall. Další podrobnosti najdete v [dokumentaci k bráně firewall](https://firewalld.org/documentation/).
Poškození nebo ověření systému souborů | Další informace o řešení potíží s poškozenými systémy souborů pomocí sériové konzoly najdete v části věnované sériovým konzolám [virtuálního počítače Azure Linux](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) .
Problémy s konfigurací SSH | Přejděte do konzoly sériového portu a změňte nastavení. Sériová konzola lze použít bez ohledu na konfiguraci SSH virtuálního počítače, protože nevyžaduje, aby virtuální počítač fungoval s připojením k síti. Průvodce odstraňováním potíží je k dispozici v tématu [řešení potíží s připojením SSH k virtuálnímu počítači Azure Linux, u kterého došlo k chybě, nebo je zamítnutá](./troubleshoot-ssh-connection.md). Další podrobnosti najdete v [podrobnějších postupech při řešení potíží s nástrojem SSH pro problémy s připojením k virtuálnímu počítači Linux v Azure](./detailed-troubleshoot-ssh-connection.md) .
Interakce s nástrojem pro spouštění | Restartujte virtuální počítač v okně sériové konzoly, abyste měli přístup k GRUB na svém VIRTUÁLNÍm počítači se systémem Linux. Další podrobnosti a informace týkající se distribuce najdete v tématu [použití sériové konzoly pro přístup k grub a jednomu uživatelskému režimu](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Zakázání sériové konzoly

Ve výchozím nastavení mají všechny odběry povolený přístup pomocí sériové konzoly. Službu sériového portu můžete zakázat buď na úrovni předplatného, nebo na úrovni sady virtuálních počítačů nebo virtuálních počítačů. Podrobné pokyny najdete v tématu [povolení a zakázání služby Azure Serial Console](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Sériová konzola zabezpečení

### <a name="access-security"></a>Přístup k zabezpečení
Přístup ke konzole sériového portu je omezený na uživatele, kteří mají roli přístupu [přispěvatele virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebo vyšší k virtuálnímu počítači. Pokud váš Azure Active Directory tenant vyžaduje vícefaktorové ověřování (MFA), bude mít přístup ke konzole sériového portu taky MFA, protože přístup ke konzole sériového prostředí probíhá přes [Azure Portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Všechna data, která se odesílají zpátky a zpátky, se šifrují na lince.

### <a name="audit-logs"></a>Protokoly auditu
Veškerý přístup ke konzole sériového portu je aktuálně přihlášený k protokolům [diagnostiky spouštění](./boot-diagnostics.md) virtuálního počítače. Přístup k těmto protokolům jsou vlastněné a řízené správcem virtuálního počítače Azure.

> [!CAUTION]
> Do protokolu nejsou protokolována žádná přístupová hesla pro konzolu. Pokud se ale v konzole spouštějí příkazy, které obsahují nebo využívají hesla, tajné klíče, uživatelská jména nebo jakékoli jiné formy osobních údajů (PII), zapíší se do protokolů diagnostiky spouštění virtuálního počítače. Budou zapsány spolu s veškerým jiným viditelným textem jako součást implementace funkce Posun zpět pomocí sériové konzoly. Tyto protokoly jsou cyklické a k nim mají přístup jenom jednotlivci s oprávněním ke čtení k účtu úložiště diagnostiky. Pokud zadáváte nějaké příkazy pro práci s daty, které obsahují tajná data nebo PII, doporučujeme použít SSH, pokud není bezpodmínečně nutné použít sériová konzola.

### <a name="concurrent-usage"></a>Souběžné využití
Pokud je uživatel připojen ke konzole sériového portu a jiný uživatel úspěšně požaduje přístup ke stejnému virtuálnímu počítači, bude první uživatel odpojen a druhý uživatel připojen ke stejné relaci.

> [!CAUTION]
> To znamená, že odpojený uživatel nebude odhlášen. Možnost vymáhat odhlášení po odpojení (pomocí SIGHUP nebo podobného mechanismu) je stále v plánu. Pro systém Windows je povolen automatický časový limit ve speciální konzole pro správu (SAC); pro Linux ale můžete nakonfigurovat nastavení časový limit terminálu. Chcete-li to provést, přidejte `export TMOUT=600` do souboru *.bash_profile* nebo *. profil* pro uživatele, který používáte k přihlášení do konzoly. Toto nastavení vyprší časový limit relace po 10 minutách.

## <a name="accessibility"></a>Usnadnění
Přístupnost je klíčovým fokusem pro konzolu sériového rozhraní Azure. Za tímto účelem jsme zajistili, že sériová konzola je plně přístupná.

### <a name="keyboard-navigation"></a>Procházení pomocí klávesnice
Pomocí klávesy **TAB** na klávesnici přejděte v rozhraní sériové konzoly z Azure Portal. Vaše poloha se zvýrazní na obrazovce. Pokud chcete opustit okno konzoly sériového portu, stiskněte na své klávesnici **kombinaci kláves CTRL** + **F6** .

### <a name="use-serial-console-with-a-screen-reader"></a>Použití sériové konzoly se čtečkou obrazovky
Konzola sériového rozhraní obsahuje integrovanou podporu čtečky obrazovky. Navigace se zapnutým čtečkou obrazovky umožní, aby se text ALT pro aktuálně vybrané tlačítko načetl nahlasem pro čtečku obrazovky.

## <a name="known-issues"></a>Známé problémy
O některých problémech se používá konzola sériového portu a operační systém virtuálního počítače. Tady je seznam těchto problémů a kroky pro zmírnění rizik pro virtuální počítače se systémem Linux. Tyto problémy a omezení rizik platí pro instance virtuálních počítačů i instancí sady škálování virtuálních počítačů. Pokud se neshodují s chybou, kterou vidíte, přečtěte si část běžné chyby služby sériové konzoly při [běžných chybách sériové konzoly](./serial-console-errors.md).

Problém                           |   Omezení rizik
:---------------------------------|:--------------------------------------------|
Stisknutí klávesy **ENTER** po nápisu připojení nezpůsobí zobrazení výzvy k přihlášení. | GRUB možná není správně nakonfigurovaný. Spusťte následující příkazy: `grub2-mkconfig -o /etc/grub2-efi.cfg` a/nebo `grub2-mkconfig -o /etc/grub2.cfg` . Další informace najdete v tématu o tom, že [ENTER nedělá nic](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). K tomuto problému může dojít, pokud máte spuštěný vlastní virtuální počítač, zesílené zařízení nebo GRUB config, které způsobí, že se Linux nepřipojí k sériovému portu.
Text Sériová konzola zabírá pouze část velikosti obrazovky (často po použití textového editoru). | Sériové konzoly nepodporují vyjednávání o velikosti okna ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), což znamená, že nebude k dispozici žádný signál SIGWINCH k aktualizaci velikosti obrazovky a virtuální počítač nebude mít žádné znalosti o velikosti terminálu. Nainstalujte xterm nebo podobný nástroj, který vám poskytne `resize` příkaz, a pak spusťte `resize` .
Vložení dlouhých řetězců nefunguje. | Konzola sériového portu omezuje délku řetězců vložených do terminálu na 2048 znaků, aby se zabránilo přetížení šířky pásma sériového portu.
Nestabilní vstup klávesnice v obrázcích SLES BYOS. Vstup z klávesnice je jenom zřídka rozpoznaný. | Jedná se o problém s balíčkem Plymouth. Plymouth by se nemělo spouštět v Azure, protože nepotřebujete úvodní obrazovku a Plymouth brání možnosti platformy používat sériovou konzolu. Odeberte Plymouth pomocí `sudo zypper remove plymouth` a pak restartujte počítač. Případně můžete upravit řádek jádra vaší konfigurace GRUB připojením `plymouth.enable=0` ke konci řádku. To můžete provést [úpravou spouštěcí položky při spuštění](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)nebo úpravou GRUB_CMDLINE_LINUXho řádku v `/etc/default/grub` , opětovným sestavením grub `grub2-mkconfig -o /boot/grub2/grub.cfg` a následným restartováním.


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Č. Jak mohu odeslat zpětnou vazbu?**

A. Poskytněte zpětnou vazbu vytvořením problému GitHubu na adrese  https://aka.ms/serialconsolefeedback . Případně (méně upřednostňovaná) můžete odeslat zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo v kategorii virtuálního počítače v https://feedback.azure.com .

**Č. Podporuje sériová konzola kopírování/vkládání?**

A. Ano. Pomocí **kombinace kláves CTRL** + **+ SHIFT**+ Shift + + **C** **Ctrl** + **Shift** + **V** zkopírujte a vložte do terminálu.

**Č. Můžu místo připojení SSH použít sériovou konzolu?**

A. I když se může zdát, že by toto použití mohlo být technicky možné, má se tato konzola používat primárně jako nástroj pro řešení potíží v situacích, kdy připojení přes SSH není možné. K použití konzoly sériového rozhraní jako náhrady SSH doporučujeme z následujících důvodů:

- Konzola sériového portu nemá jako SSH větší šířku pásma. Vzhledem k tomu, že se jedná o textové připojení, je obtížné pracovat s velkým grafickým rozhraním.
- Přístup k Sériová konzola je aktuálně možný jenom pomocí uživatelského jména a hesla. Vzhledem k tomu, že klíče SSH jsou mnohem bezpečnější než kombinace uživatelského jména a hesla, z hlediska zabezpečení přihlášení doporučujeme protokol SSH přes sériovou konzolu.

**Č. Kdo může povolit nebo zakázat sériovou konzolu pro moje předplatné?**

A. Pokud chcete povolit nebo zakázat sériovou konzolu na úrovni předplatného, musíte mít oprávnění k zápisu do předplatného. Role, které mají oprávnění k zápisu, zahrnují role správce nebo vlastníka. Vlastní role můžou mít taky oprávnění k zápisu.

**Č. Kdo má přístup ke konzole sériového portu pro virtuální počítač/sadu škálování virtuálního počítače?**

A. Pro přístup ke konzole sériového portu musíte mít roli Přispěvatel virtuálních počítačů nebo vyšší pro virtuální počítač nebo sadu škálování virtuálního počítače.

**Č. Moje konzola sériového zobrazení nezobrazuje vše, co mám dělat?**

A. Bitová kopie je pro přístup k sériové konzole nejspíš nesprávně nakonfigurovaná. Informace o konfiguraci image pro povolení služby sériového prostředí najdete v tématu [dostupnost distribuce sériová konzola Linux](#serial-console-linux-distribution-availability).

**Č. Je k dispozici sériová Konzola pro sady škálování virtuálních počítačů?**

A. Ano, je! Další informace najdete v tématu [sériová Konzola pro Virtual Machine Scale Sets](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**Č. Když nastavil (a) jsem virtuální počítač nebo sadu škálování virtuálního počítače jenom pomocí ověřování pomocí klíče SSH, můžu stále používat sériovou konzolu pro připojení k virtuálnímu počítači nebo instanci sady škálování virtuálního počítače?**

A. Ano. Vzhledem k tomu, že konzola sériového portu nevyžaduje klíče SSH, stačí nastavit kombinaci uživatelského jména a hesla. Můžete to udělat tak, že v Azure Portal vyberete **resetovat heslo** a pomocí těchto přihlašovacích údajů se přihlásíte ke konzole sériového portu.

## <a name="next-steps"></a>Další kroky
* Použijte konzolu sériového [přístupu pro přístup k grub a jednomu uživatelskému režimu](serial-console-grub-single-user-mode.md).
* Použijte konzolu sériového rozhraní pro [volání NMI a SysRq](serial-console-nmi-sysrq.md).
* Naučte se používat konzolu sériového portu k [Povolení GRUB v různých distribuce](serial-console-grub-proactive-configuration.md) .
* Pro [virtuální počítače s Windows](./serial-console-windows.md)je dostupná taky konzola sériového portu.
* Přečtěte si další informace o [diagnostice spouštění](boot-diagnostics.md).
