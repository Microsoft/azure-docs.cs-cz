---
title: Sériová konzola Azure pro Linux | Microsoft Docs
description: Obousměrná sériová Konzola pro Azure Virtual Machines a Virtual Machine Scale Sets.
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
ms.openlocfilehash: abee04afca45a2d6f558858b4490c8be1f37a2f8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451286"
---
# <a name="azure-serial-console-for-linux"></a>Sériová konzola Azure pro Linux

Konzola sériového portu v Azure Portal poskytuje přístup k textové konzole pro virtuální počítače se systémem Linux a instance sady škálování virtuálních počítačů. Toto sériové připojení se připojuje k sériovému portu ttyS0 virtuálního počítače nebo instance sady škálování virtuálních počítačů a poskytuje přístup k němu nezávisle na stavu sítě nebo operačního systému. Ke konzole sériového přístupu se dá přistupovat jenom pomocí Azure Portal a je povolená jenom pro uživatele, kteří mají roli přístupu přispěvatel nebo vyšší, k virtuálnímu počítači nebo sadě škálování virtuálních počítačů.

Sériová konzola funguje stejným způsobem pro virtuální počítače a instance sady škálování virtuálních počítačů. V tomto dokumentu budou všechny zmínky k virtuálním počítačům implicitně zahrnovat instance sady škálování virtuálních počítačů, pokud není uvedeno jinak.

Dokumentaci k sériové konzole pro Windows najdete v tématu [sériová Konzola pro Windows](../windows/serial-console.md).

> [!NOTE]
> Konzola sériového portu je všeobecně dostupná v globálních oblastech Azure a ve veřejné verzi Preview v Azure Government. Ještě není k dispozici v cloudu Azure Čína.


## <a name="prerequisites"></a>Požadavky

- Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí používat model nasazení správy prostředků. Klasická nasazení nejsou podporovány.

- Váš účet, který používá sériová konzola, musí mít [roli Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) pro virtuální počítač a účet úložiště [diagnostiky spouštění](boot-diagnostics.md) .

- Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí mít uživatele založené na heslech. Můžete si ho vytvořit pomocí [resetovat heslo](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkce rozšíření přístupu virtuálních počítačů. Vyberte **resetovat heslo** z **podpora a řešení potíží** oddílu.

- Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí mít povolenou [diagnostiku spouštění](boot-diagnostics.md) .

    ![Nastavení diagnostiky spouštění](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Nastavení specifická pro distribuce systému Linux najdete v tématu [dostupnost distribuce sériová konzola Linux](#serial-console-linux-distribution-availability).

- Pro sériové výstupy v `ttys0`musí být nakonfigurovaná instance virtuálního počítače nebo sady škálování virtuálního počítače. Toto je výchozí nastavení pro Image Azure, ale u vlastních imagí je budete chtít dvakrát ověřit. Podrobnosti [níže](#custom-linux-images).


> [!NOTE]
> Konzola sériového portu vyžaduje, aby místní uživatel s nakonfigurovaným heslem. Virtuální počítače nebo sady škálování virtuálních počítačů, které jsou nakonfigurované jenom pomocí veřejného klíče SSH, se nebudou moct přihlásit ke konzole sériového portu. Chcete-li vytvořit místního uživatele s heslem, použijte [rozšíření VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), které je k dispozici na portálu, a vyberte možnost **resetovat heslo** v Azure Portal a vytvořte místního uživatele s heslem.
> Můžete také resetovat heslo správce v účtu [pomocí grub ke spuštění do režimu jednoho uživatele](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Dostupnost distribuce systému Linux v sériové konzole
Aby mohla konzola sériového fungování fungovat správně, musí být hostovaný operační systém nakonfigurovaný na čtení a zápis zpráv konzoly do sériového portu. U nejdůležitějších [distribucí systému Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) je ve výchozím nastavení nakonfigurována sériová konzola. Výběr **sériová konzola** v části **Podpora a řešení potíží** v Azure Portal poskytuje přístup ke konzole sériového portu.

> [!NOTE]
> Pokud nevidíte cokoli v konzole sériového portu, ujistěte se, že je na vašem VIRTUÁLNÍm počítači povolená Diagnostika spouštění. Při stisknutí této akce budou často opravovat problémy, **které se v** konzole sériového umístění nezobrazí.

Distribuce      | Přístup k sériové konzole
:-----------|:---------------------
Red Hat Enterprise Linux    | Ve výchozím nastavení je povolený přístup Sériová konzola.
CentOS      | Ve výchozím nastavení je povolený přístup Sériová konzola.
Ubuntu      | Ve výchozím nastavení je povolený přístup Sériová konzola.
CoreOS      | Ve výchozím nastavení je povolený přístup Sériová konzola.
SUSE        | Novější image SLES dostupné v Azure mají ve výchozím nastavení povolený přístup pomocí sériové konzoly. Pokud používáte starší verze (10 nebo starší) služby SLES v Azure, přečtěte si [článek znalostní báze](https://www.novell.com/support/kb/doc.php?id=3456486) , ve kterém můžete povolit sériovou konzolu.
Oracle Linux        | Ve výchozím nastavení je povolený přístup Sériová konzola.

### <a name="custom-linux-images"></a>Vlastní image Linux
Pokud chcete pro vlastní image virtuálního počítače se systémem Linux povolit konzolu sériového prostředí, povolte přístup k konzole v souboru */etc/inittab* a spusťte terminál na `ttyS0`. Například: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. V ttyS0 možná budete muset vytvořit také Getty. To se dá udělat `systemctl start serial-getty@ttyS0.service`.

Budete také chtít přidat ttyS0 jako cíl pro sériový výstup. Další informace o konfiguraci vlastní image pro práci s konzolou sériového rozhraní najdete v tématu Obecné požadavky na systém při [vytváření a nahrání virtuálního pevného disku pro Linux v Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Pokud vytváříte vlastní jádro, zvažte povolení těchto příznaků jádra: `CONFIG_SERIAL_8250=y` a `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Konfigurační soubor se obvykle nachází v cestě */boot/* .

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Běžné scénáře přístupu ke konzole sériového portu

Scénář          | Akce v konzole sériového portu
:------------------|:-----------------------------------------
Poškozený soubor *FSTAB* | Pokračujte stisknutím klávesy **ENTER** a opravte soubor *FSTAB* pomocí textového editoru. Je možné, že budete muset být v režimu jednoho uživatele. Další informace najdete v části "sériová konzola" o [tom, jak opravit problémy fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) a [pomocí konzoly sériového přístupu získat přístup k grub a jednomu uživatelskému režimu](serial-console-grub-single-user-mode.md).
Pravidla brány firewall na nesprávný |  Pokud jste nakonfigurovali softwaru iptables k blokování připojení SSH, můžete pomocí sériové konzoly pracovat s VIRTUÁLNÍm počítačem bez nutnosti SSH. Další podrobnosti najdete na [stránce softwaru iptables Man](https://linux.die.net/man/8/iptables).<br>Podobně pokud brána firewall blokuje přístup přes SSH, můžete k virtuálnímu počítači přistupovat pomocí sériové konzoly a znovu nakonfigurovat bránu firewall. Další podrobnosti najdete v [dokumentaci k bráně firewall](https://firewalld.org/documentation/).
Poškození systému souborů a vrácení | Další informace o řešení potíží s poškozenými systémy souborů pomocí sériové konzoly najdete v části věnované sériovým konzolám [virtuálního počítače Azure Linux](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) .
Problémy s konfigurací SSH | Přístup ke konzole sériového portu a změnit nastavení. Sériová konzola lze použít bez ohledu na konfiguraci SSH virtuálního počítače, protože nevyžaduje, aby virtuální počítač fungoval s připojením k síti. Průvodce odstraňováním potíží je k dispozici v tématu [řešení potíží s připojením SSH k virtuálnímu počítači Azure Linux, u kterého došlo k chybě, nebo je zamítnutá](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Další podrobnosti najdete v [podrobnějších postupech při řešení potíží s nástrojem SSH pro problémy s připojením k virtuálnímu počítači Linux v Azure](./detailed-troubleshoot-ssh-connection.md) .
Interakce s zaváděcího programu pro spouštění | Restartujte virtuální počítač v okně sériové konzoly, abyste měli přístup k GRUB na svém VIRTUÁLNÍm počítači se systémem Linux. Další podrobnosti a informace týkající se distribuce najdete v tématu [použití sériové konzoly pro přístup k grub a jednomu uživatelskému režimu](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Zakázání sériové konzoly

Ve výchozím nastavení mají všechny odběry povolený přístup pomocí sériové konzoly. Službu sériového portu můžete zakázat buď na úrovni předplatného, nebo na úrovni sady virtuálních počítačů nebo virtuálních počítačů. Podrobné pokyny najdete v tématu [povolení a zakázání služby Azure Serial Console](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Zabezpečení konzoly sériového portu

### <a name="access-security"></a>Zabezpečení přístupu
Přístup ke konzole sériového portu je omezená na uživatele, kteří mají roli přístup z [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebo vyšší, k virtuálnímu počítači. Pokud váš tenant Azure Active Directory vyžaduje vícefaktorové ověřování (MFA), je přístup ke konzole sériového portu bude také nutné vícefaktorové ověřování, protože přístup ke konzole sériového portu, je prostřednictvím [webu Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Přenosu se šifrují všechna data, která se odešle vpřed a zpět.

### <a name="audit-logs"></a>Protokoly auditu
Veškerý přístup ke konzole sériového portu je aktuálně přihlášen [Diagnostika spouštění](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) protokoly virtuálního počítače. Přístup k tyto protokoly jsou vlastněné a řídí správce virtuálních počítačů Azure.

> [!CAUTION]
> Žádná hesla přístup pro konzolu jsou protokolovány. Nicméně pokud příkazy se spouští v rámci konzoly obsahovat nebo výstup hesla, tajné kódy, uživatelská jména nebo jakoukoli jinou formu identifikovatelné osobní údaje (PII), ty se zapíšou do protokolů diagnostiky spouštění virtuálního počítače. Se zapíšou spolu s všechny ostatní viditelného textu, jako součást provádění konzoly sériového portu přejděte zpět funkce. Tyto protokoly jsou cyklické a přístup k nim mají pouze uživatelé, kteří mají oprávnění ke čtení pro účet úložiště diagnostiky. Pokud zadáváte nějaké příkazy pro práci s daty, které obsahují tajná data nebo PII, doporučujeme použít SSH, pokud není bezpodmínečně nutné použít sériová konzola.

### <a name="concurrent-usage"></a>Souběžné používání
Pokud je uživatel připojen ke konzole sériového portu a jiný uživatel úspěšně požaduje přístup k tomuto virtuálnímu počítači stejný, bude první uživatel odpojen a druhý uživatel se připojil do stejné relace.

> [!CAUTION]
> To znamená, že odpojený uživatel nebude odhlášen. Možnost vymáhat odhlášení po odpojení (pomocí SIGHUP nebo podobného mechanismu) je stále v plánu. Pro systém Windows je povolen automatický časový limit ve speciální konzole pro správu (SAC); pro Linux ale můžete nakonfigurovat nastavení časový limit terminálu. Provedete to tak, že přidáte `export TMOUT=600` do souboru *. bash_profile* nebo *. profil* pro uživatele, který používáte k přihlášení do konzoly. Toto nastavení vyprší časový limit relace po 10 minutách.

## <a name="accessibility"></a>Přístupnost
Přístupnost je klíčovým fokusem pro konzolu sériového rozhraní Azure. Za tímto účelem jsme zajistili, že sériová konzola je plně přístupná.

### <a name="keyboard-navigation"></a>Procházení pomocí klávesnice
Použití **kartu** kláves na klávesnici a přejděte v konzole sériového portu rozhraní na webu Azure Portal. Vaše poloha budou zvýrazněny na obrazovce. Pokud chcete nechat fokus z okna konzoly sériového portu, stiskněte klávesu **Ctrl**+**F6** na klávesnici.

### <a name="use-serial-console-with-a-screen-reader"></a>Použití sériové konzoly se čtečkou obrazovky
Konzole sériového portu je integrované podpoře čtečky obrazovky. Navigace pomocí čtečky obrazovky zapnuté vám umožní alternativní text pro aktuálně vybrané tlačítko nahlas číst čtečka obrazovky.

## <a name="known-issues"></a>Známé problémy
O některých problémech se používá konzola sériového portu a operační systém virtuálního počítače. Tady je seznam těchto problémů a kroky pro zmírnění rizik pro virtuální počítače se systémem Linux. Tyto problémy a omezení rizik platí pro instance virtuálních počítačů i instancí sady škálování virtuálních počítačů. Pokud se neshodují s chybou, kterou vidíte, přečtěte si část běžné chyby služby sériové konzoly při [běžných chybách sériové konzoly](./serial-console-errors.md).

Problém                           |   Omezení rizik
:---------------------------------|:--------------------------------------------|
Stisknutím klávesy **Enter** po připojení banner nezpůsobí výzvě přihlášení má být zobrazen. | Další informace najdete v tématu [Hitting zadejte nemá žádný účinek,](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). K tomuto problému může dojít, pokud máte spuštěný vlastní virtuální počítač, zesílené zařízení nebo GRUB config, které způsobí, že se Linux nepřipojí k sériovému portu.
Text Sériová konzola zabírá pouze část velikosti obrazovky (často po použití textového editoru). | Sériové konzoly nepodporují vyjednávání o velikosti okna ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), což znamená, že nebude k dispozici žádný signál SIGWINCH k aktualizaci velikosti obrazovky a virtuální počítač nebude mít žádné znalosti o velikosti terminálu. Nainstalujte xterm nebo podobný nástroj, který vám poskytne příkaz `resize` a potom spusťte `resize`.
Vkládání dlouhé řetězce nebude fungovat. | Konzole sériového portu omezení délky řetězce do terminálu na 2 048 znaků, aby se zabránilo přetížení šířky pásma sériového portu.
Nestabilní vstup klávesnice v obrázcích SLES BYOS. Vstup z klávesnice je jenom zřídka rozpoznaný. | Jedná se o problém s balíčkem Plymouth. Plymouth by se nemělo spouštět v Azure, protože nepotřebujete úvodní obrazovku a Plymouth brání možnosti platformy používat sériovou konzolu. Odeberte Plymouth pomocí `sudo zypper remove plymouth` a pak restartujte počítač. Případně můžete upravit řádek jádra GRUB config tak, že na konec řádku připojíte `plymouth.enable=0`. To můžete provést [úpravou spouštěcí položky při spuštění](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)nebo úpravou GRUB_CMDLINE_LINUXho řádku v `/etc/default/grub`, opětovným sestavením GRUB pomocí `grub2-mkconfig -o /boot/grub2/grub.cfg`a následným restartováním.


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Jak mohu odeslat zpětnou vazbu?**

A. Poskytněte zpětnou vazbu vytvořením problému GitHubu na adrese https://aka.ms/serialconsolefeedback. Můžete také (méně upřednostňované), můžete odeslat zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo v kategorii virtuální počítač https://feedback.azure.com.

**Otázka: podporuje konzola sériového kopírování/vkládání?**

A. Ano. Použití **Ctrl**+**Shift**+**C** a **Ctrl**+**Shift** + **V** zkopírovat a vložit do terminálu.

**Otázka: můžu použít sériovou konzolu místo připojení SSH?**

A. I když se může zdát, že by toto použití mohlo být technicky možné, má se tato konzola používat primárně jako nástroj pro řešení potíží v situacích, kdy připojení přes SSH není možné. K použití konzoly sériového rozhraní jako náhrady SSH doporučujeme z následujících důvodů:

- Konzola sériového portu nemá jako SSH větší šířku pásma. Vzhledem k tomu, že se jedná o textové připojení, je obtížné pracovat s velkým grafickým rozhraním.
- Přístup k Sériová konzola je aktuálně možný jenom pomocí uživatelského jména a hesla. Vzhledem k tomu, že klíče SSH jsou mnohem bezpečnější než kombinace uživatelského jména a hesla, z hlediska zabezpečení přihlášení doporučujeme protokol SSH přes sériovou konzolu.

**Otázka. kdo může povolit nebo zakázat sériová Konzola pro moje předplatné?**

A. K povolení nebo zakázání konzole sériového portu na úrovni celé předplatné, musí mít oprávnění k zápisu do předplatného. Role, které mají oprávnění k zápisu zahrnují role správce nebo vlastníka. Vlastní role můžete také mít oprávnění k zápisu.

**Dotaz, který má přístup ke konzole sériového portu pro virtuální počítač/sadu škálování virtuálního počítače?**

A. Pro přístup ke konzole sériového portu musíte mít roli Přispěvatel virtuálních počítačů nebo vyšší pro virtuální počítač nebo sadu škálování virtuálního počítače.

**Otázka. moje konzola sériového zobrazení nezobrazuje vše, co mám dělat?**

A. Vaše image je pravděpodobně nesprávně nakonfigurované pro přístup ke konzole sériového portu. Informace o konfiguraci image pro povolení služby sériového prostředí najdete v tématu [dostupnost distribuce sériová konzola Linux](#serial-console-linux-distribution-availability).

**Otázka. je k dispozici konzola sériového portu pro služby Virtual Machine Scale Sets?**

A. Ano, je! Další informace najdete v tématu [sériová Konzola pro Virtual Machine Scale Sets](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**Dotaz. Pokud mám virtuální počítač nebo sadu škálování virtuálního počítače nastavili jenom pomocí ověřování pomocí protokolu SSH, můžu stále používat sériovou konzolu pro připojení k virtuálnímu počítači nebo instanci sady škálování virtuálního počítače?**

A. Ano. Vzhledem k tomu, že konzola sériového portu nevyžaduje klíče SSH, stačí nastavit kombinaci uživatelského jména a hesla. Můžete to udělat tak, že v Azure Portal vyberete **resetovat heslo** a pomocí těchto přihlašovacích údajů se přihlásíte ke konzole sériového portu.

## <a name="next-steps"></a>Další kroky
* Použijte konzolu sériového [přístupu pro přístup k grub a jednomu uživatelskému režimu](serial-console-grub-single-user-mode.md).
* Použijte konzolu sériového rozhraní pro [volání NMI a SysRq](serial-console-nmi-sysrq.md).
* Naučte se používat konzolu sériového portu k [Povolení GRUB v různých distribuce](serial-console-grub-proactive-configuration.md) .
* Pro [virtuální počítače s Windows](../windows/serial-console.md)je dostupná taky konzola sériového portu.
* Další informace o [Diagnostika spouštění](boot-diagnostics.md).

