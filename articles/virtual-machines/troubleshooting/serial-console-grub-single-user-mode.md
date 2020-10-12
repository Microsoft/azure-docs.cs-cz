---
title: Konzola sériového prostředí Azure pro GRUB a režim jednoho uživatele | Microsoft Docs
description: Tento článek popisuje, jak používat sériovou konzoli pro GRUB na virtuálních počítačích Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 5341cc62a7d02c3072df90becf893dec18427ac2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87439548"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Použití sériové konzoly pro přístup k GRUB a jednomu uživatelskému režimu
Při spouštění virtuálního počítače (VM) je pravděpodobně první věcí, kterou vidíte, při spuštění virtuálního počítače. Vzhledem k tomu, že se zobrazuje před spuštěním operačního systému, GRUB není přístupná přes SSH. V GRUB můžete upravit konfiguraci spouštění pro spuštění do režimu jednoho uživatele mimo jiné.

Režim jednoho uživatele je minimální prostředí s minimální funkčností. Může být užitečné při zkoumání potíží se spouštěním, problémů systému souborů nebo problémů se sítí. Méně služeb může běžet na pozadí a v závislosti na runlevel nemusí být systém souborů ani automaticky připojen.

Režim jednoho uživatele je také užitečný v situacích, kdy je možné nakonfigurovat virtuální počítač tak, aby přijímal jenom klíče SSH pro přihlášení. V takovém případě můžete použít režim jednoho uživatele k vytvoření účtu s ověřováním hesla.

> [!NOTE]
> Služba sériové konzoly umožňuje pouze uživatelům s úrovní *Přispěvatel* nebo vyšší oprávnění pro přístup ke konzole sériového virtuálního počítače.

Pokud chcete přejít do režimu jednoho uživatele, zadejte GRUB při spouštění virtuálního počítače a upravte konfiguraci spouštění v GRUB. Přečtěte si podrobné pokyny k zadávání GRUB v další části. Obecně platí, že pokud je váš virtuální počítač nakonfigurovaný tak, aby zobrazoval GRUB, můžete k restartování virtuálního počítače a zobrazení GRUB použít tlačítko restartovat v rámci sériové konzole virtuálního počítače.

![Tlačítko pro restartování sériové konzoly Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Obecný přístup GRUB
Pokud chcete získat přístup k GRUB, restartujte virtuální počítač a otevřete podokno sériová konzola. Některé distribuce vyžadují vstup z klávesnice k zobrazení GRUB a ostatní se automaticky zobrazují GRUB po dobu několika sekund, které umožňují vstupu klávesnice uživatele zrušit časový limit.

Aby bylo možné získat přístup k režimu jednoho uživatele, je třeba zajistit, aby byl na VIRTUÁLNÍm počítači povolený GRUB. V závislosti na vaší distribuci může být potřeba, aby se zajistilo, že bude GRUB povolená nějaká instalace. Informace specifické pro distribuci najdete v další části.

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Restartování virtuálního počítače pro přístup k GRUB v sériových konzolách
Virtuální počítač můžete restartovat v rámci sériové konzoly tak, že ho najedete myší na tlačítko **restartovat** a pak vyberete **restartovat virtuální počítač**. V dolní části podokna se zobrazí oznámení o restartování.

Můžete také restartovat virtuální počítač spuštěním příkazu "b", pokud je povolený [SysRq](./serial-console-nmi-sysrq.md) . Informace o tom, co se má při restartování GRUB očekávat, najdete v tématu pokyny pro distribuci v dalších částech.

![Restart sériové konzoly Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Obecný přístup k režimu jednoho uživatele
Pokud jste nenakonfigurovali účet s ověřováním hesla, budete možná potřebovat ruční přístup k režimu jednoho uživatele. Upravte konfiguraci GRUB tak, aby ručně zadala režim jednoho uživatele. Až to uděláte, přečtěte si část použití režimu jednoho uživatele k resetování nebo přidání hesla, kde najdete další pokyny.

Pokud se virtuální počítač nemůže spustit, distribuce se často automaticky odřadí do režimu jednoho uživatele nebo do nouzového režimu. Další distribuce ale vyžadují další nastavení, jako je například nastavení kořenového hesla, než bude možné automaticky odvolat do jednoho nebo nouzového režimu pro jednoho uživatele.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Použití režimu jednoho uživatele k resetování nebo přidání hesla
Až budete v režimu jednoho uživatele, přidejte nového uživatele s oprávněními sudo pomocí následujícího postupu:
1. Spusťte `useradd <username>` a přidejte uživatele.
1. Spusťte `sudo usermod -a -G sudo <username>` pro udělení oprávnění nového uživatele root.
1. Použijte `passwd <username>` k nastavení hesla pro nového uživatele. Pak se můžete přihlásit jako nový uživatel.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Přístup pro Red Hat Enterprise Linux (RHEL)
Pokud se RHEL nemůže spustit normálně, bude se automaticky přehlédnout do režimu jednoho uživatele. Pokud jste ale nenastavili kořenový přístup pro režim jednoho uživatele, nemáte kořenové heslo a nebudete se moct přihlásit. K dispozici je alternativní řešení (viz oddíl ruční zadání režimu jednoho uživatele v RHEL), ale doporučujeme, abyste nejdřív nastavili root Access.

### <a name="grub-access-in-rhel"></a>Přístup k GRUB v RHEL
RHEL se dodává s povoleným GRUBem. Pokud chcete zadat GRUB, restartujte virtuální počítač spuštěním `sudo reboot` a pak stiskněte libovolnou klávesu. Mělo by se zobrazit podokno GRUB. Pokud ne, ujistěte se, že v souboru GRUB () jsou k dispozici následující řádky `/etc/default/grub` :

**Pro RHEL 8**

>[!NOTE]
> Red Hat doporučuje pomocí Grubby nakonfigurovat parametry příkazového řádku jádra v RHEL 8 +. V současné době není možné aktualizovat parametry grub timeout a Terminal pomocí Grubby. Chcete-li upravit aktualizaci GRUB_CMDLINE_LINUX argument pro všechny položky spuštění, spusťte příkaz `grubby --update-kernel=ALL --args="console=ttyS0,115200 console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"` . Další podrobnosti jsou k dispozici [zde](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel).

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**Pro RHEL 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> Red Hat také poskytuje dokumentaci pro spouštění do záchranného režimu, nouzového režimu nebo režimu ladění a pro resetování kořenového hesla. Pokyny najdete v tématu [Úpravy nabídky terminálu během spouštění](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Nastavení kořenového přístupu pro režim jednoho uživatele v RHEL
Uživatel root je ve výchozím nastavení zakázaný. Režim jednoho uživatele v RHEL vyžaduje, aby byl povolený kořenový uživatel. Pokud potřebujete povolit režim jednoho uživatele, postupujte podle následujících pokynů:

1. Přihlaste se k systému Red Hat přes SSH.
1. Přepněte na kořen.
1. Pomocí následujícího postupu povolte heslo pro kořenového uživatele:
    * Spusťte `passwd root` (nastavte silné kořenové heslo).
1. Zajistěte, aby se kořenový uživatel mohl přihlásit jenom přes ttyS0, a to následujícím způsobem: a. Spusťte `edit /etc/ssh/sshd_config` příkaz a ujistěte se, že je PermitRootLogIn nastaveno na `no` .
    b. Spusťte příkaz `edit /etc/securetty file` pro povolení přihlášení pouze přes ttyS0.

Když se teď systém spustí do režimu jednoho uživatele, můžete se přihlásit pomocí kořenového hesla.

Případně můžete v případě RHEL 7.4 + nebo ID_spouštění + povolit režim jednoho uživatele v GRUBch dotazech, viz [spuštění do režimu jednoho uživatele](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ruční zadání režimu jednoho uživatele v RHEL
Pokud jste nastavili GRUB a root Access pomocí předchozích pokynů, můžete zadat režim s jedním uživatelem pomocí následujícího postupu:

1. Pokud chcete zadat GRUB, stiskněte klávesu ESC při restartování virtuálního počítače.
1. V GRUB klikněte na tlačítko E a upravte operační systém, který chcete spustit. Operační systém je obvykle uveden na prvním řádku.
1. Najděte řádek jádra. V Azure začíná na *linux16*.
1. Stisknutím kombinace kláves CTRL + E přejdete na konec řádku.
1. Na konci řádku přidejte *System. Unit = zachránit. Target*.

    Tato akce spustí režim jednoho uživatele. Pokud chcete použít nouzový režim, přidejte *System. Unit = Emergency. Target* do konce řádku (místo *System. Unit = zachránit. Target*).

1. Stisknutím kombinace kláves CTRL + X ukončete a restartujte s použitým nastavením.

   Než budete moct přejít do režimu jednoho uživatele, zobrazí se výzva k zadání hesla správce. Toto heslo je ten, který jste vytvořili v předchozím postupu.

    ![Animovaný obrázek znázorňující rozhraní příkazového řádku Uživatel vybere Server, vyhledá konec řádku jádra a pak zadá zadaný text.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Zadat režim jednoho uživatele bez povoleného kořenového účtu v RHEL
Pokud jste nepovolili kořenového uživatele podle předchozích pokynů, můžete resetovat kořenové heslo pomocí následujících kroků:

> [!NOTE]
> Pokud používáte SELinux, ujistěte se, že při resetování kořenového hesla budete postupovat podle dalších kroků popsaných v [dokumentaci k Red Hat](https://aka.ms/rhel7grubterminal).

1. Pokud chcete zadat GRUB, stiskněte klávesu ESC při restartování virtuálního počítače.

1. V GRUB klikněte na tlačítko E a upravte operační systém, který chcete spustit. Operační systém je obvykle uveden na prvním řádku.
1. Najděte řádek jádra. V Azure začíná na *linux16*.
1. Na konci řádku přidejte *Rd. Break* na konec řádku. Ponechte mezeru mezi řádkem jádra a *Rd. Break*.

    Tato akce přeruší proces spouštění před předáním řízení z `initramfs` do `systemd` , jak je popsáno v [dokumentaci k Red Hat](https://aka.ms/rhel7rootpassword).
1. Stisknutím kombinace kláves CTRL + X ukončete a restartujte s použitým nastavením.

   Po restartování počítače se systémem souborů, který je jen pro čtení, přestanete do nouzového režimu.

1. V prostředí zadejte pro opětovné `mount -o remount,rw /sysroot` připojení kořenového systému souborů s oprávněním ke čtení a zápisu.
1. Po spuštění do režimu jednoho uživatele `chroot /sysroot` Přepněte do `sysroot` jailbreaku.
1. Teď jste v kořenovém adresáři. Můžete resetovat své kořenové heslo tak, že zadáte `passwd` a potom použijete předchozí pokyny k zadání režimu jednoho uživatele.
1. Až budete hotovi, zadejte příkaz `reboot -f` k restartování.

![Animovaný obrázek znázorňující rozhraní příkazového řádku Uživatel vybere Server, vyhledá konec řádku jádra a zadá zadané příkazy.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> V předchozích pokynech se vám povedete do nouzového prostředí, abyste mohli provádět i úlohy, jako je například úpravy `fstab` . Doporučujeme ale obvykle, abyste obnovili své kořenové heslo a použili ho k zadání režimu pro jeden uživatel.

## <a name="access-for-centos"></a>Přístup pro CentOS
Podobně jako Red Hat Enterprise Linux, režim jednoho uživatele v CentOS vyžaduje GRUB a kořenový uživatel, který se má povolit.

### <a name="grub-access-in-centos"></a>Přístup k GRUB v CentOS
CentOS se dodává s povoleným GRUBem. Pokud chcete zadat GRUB, restartujte virtuální počítač tak, že zadáte `sudo reboot` a potom stisknete libovolnou klávesu. Tato akce zobrazí podokno GRUB.

### <a name="single-user-mode-in-centos"></a>Režim jednoho uživatele v CentOS
Pokud chcete povolit režim pro jednoho uživatele v CentOS, postupujte podle předchozích pokynů pro RHEL.

## <a name="access-for-ubuntu"></a>Přístup pro Ubuntu
Image Ubuntu nevyžadují kořenové heslo. Pokud se systém spustí do režimu jednoho uživatele, můžete ho použít bez dalších přihlašovacích údajů.

### <a name="grub-access-in-ubuntu"></a>Přístup k GRUB v Ubuntu
Pokud chcete získat přístup k GRUB, stiskněte a podržte klávesu ESC, zatímco se virtuální počítač spouští.

Ve výchozím nastavení nemusí Ubuntu obrázky automaticky zobrazovat podokno GRUB. Nastavení můžete změnit následujícím způsobem:
1. V textovém editoru otevřete soubor */etc/default/grub.d/50-cloudimg-Settings.cfg* .

1. Změňte `GRUB_TIMEOUT` hodnotu na nenulovou hodnotu.
1. V textovém editoru otevřete */etc/default/grub*.
1. Odkomentujte `GRUB_HIDDEN_TIMEOUT=1` řádek.
1. Zajistěte, aby byl `GRUB_TIMEOUT_STYLE=menu` řádek.
1. Spusťte příkaz `sudo update-grub`.

### <a name="single-user-mode-in-ubuntu"></a>Režim jednoho uživatele v Ubuntu
Pokud se Ubuntu nemůže spustit normálně, bude se automaticky přehlédnout do režimu jednoho uživatele. Chcete-li provést ruční zadání režimu jednoho uživatele, postupujte následovně:

1. Stisknutím klávesy E v GRUB upravíte položku Boot (položka Ubuntu).
1. Vyhledejte řádek, který začíná na *Linux*, a pak vyhledejte *ro*.
1. Přidejte *jednu* po *ro*a zajistěte, aby existovala mezera před a po *jednom*.
1. Stisknutím kombinace kláves CTRL + X restartujte s těmito nastaveními a zadejte režim jednoho uživatele.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Použití GRUB k vyvolání bash v Ubuntu
Po vyzkoušení předchozích pokynů může nastat situace (například zapomenuté kořenové heslo), kde stále nemůžete získat přístup k režimu jednoho uživatele ve vašem VIRTUÁLNÍm počítači s Ubuntu. Můžete také říct, aby jádro běželo `/bin/bash` jako init, nikoli jako systém inicializace. Tato akce poskytuje prostředí bash a umožňuje údržbu systému. Použijte následující pokyny:

1. Stisknutím klávesy E v GRUB upravíte položku Boot (položka Ubuntu).

1. Vyhledejte řádek, který začíná na *Linux*, a pak vyhledejte *ro*.
1. Nahraďte *ro* s *RW init =/bin/bash*.

    Tato akce připojí váš systém souborů jako čtení i zápis a použije `/bin/bash` jako proces inicializace.
1. Stisknutím kombinace kláves CTRL + X restartujte s tímto nastavením.

## <a name="access-for-coreos"></a>Přístup pro CoreOS
Režim jednoho uživatele v CoreOS vyžaduje, aby byl povolený GRUB.

### <a name="grub-access-in-coreos"></a>Přístup k GRUB v CoreOS
Pokud chcete získat přístup k GRUB, při spouštění virtuálního počítače stiskněte libovolnou klávesu.

### <a name="single-user-mode-in-coreos"></a>Režim jednoho uživatele v CoreOS
Pokud se CoreOS nemůže spustit normálně, bude se automaticky přehlédnout do režimu jednoho uživatele. Chcete-li provést ruční zadání režimu jednoho uživatele, postupujte následovně:

1. V GRUB stisknutím klávesy E upravte spouštěcí položku.

1. Vyhledejte řádek, který začíná na *Linux $*. Měly by existovat dvě instance řádku, z nichž každý je zapouzdřen v jiné *if... else* – klauzule.
1. Přidejte *CoreOS. autologin = ttyS0* na konec každé *Linux $* line.
1. Stisknutím kombinace kláves CTRL + X restartujte s těmito nastaveními a zadejte režim jednoho uživatele.

## <a name="access-for-suse-sles"></a>Přístup pro SUSE SLES
Novější obrázky z SLES 12 SP3 + povolují přístup přes konzolu sériového režimu, pokud se systém spustí do nouzového režimu.

### <a name="grub-access-in-suse-sles"></a>GRUB přístup v SUSE SLES
Přístup GRUB v SLES vyžaduje konfiguraci zaváděcího programu pro spouštění prostřednictvím YaST. Chcete-li vytvořit konfiguraci, postupujte následovně:

1. Pomocí SSH se přihlaste ke svému VIRTUÁLNÍmu počítači s SLES a pak spusťte `sudo yast bootloader` . Stiskněte klávesu TAB, stiskněte klávesu ENTER a potom v nabídce přejděte pomocí kláves se šipkami.

1. Přejít na **parametry jádra**a zaškrtněte políčko **použít konzolu sériového portu** .
1. Přidejte `serial --unit=0 --speed=9600 --parity=no` do argumentů **konzoly** .
1. Stisknutím klávesy F10 uložte nastavení a ukončete operaci.
1. Pokud chcete zadat GRUB, restartujte virtuální počítač a stisknutím libovolné klávesy během spouštěcí sekvence Nechejte podokno GRUB zobrazené.

    Výchozí časový limit pro GRUB je **1**. Toto nastavení můžete změnit změnou `GRUB_TIMEOUT` proměnné v souboru */etc/default/grub* .

![Inicializuje se konfigurace zaváděcího programu pro spouštění.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Režim jednoho uživatele v SUSE SLES
Pokud se SLES nedá spustit normálně, automaticky se přejdou do nouzového prostředí. Chcete-li zadat nouzové prostředí ručně, postupujte následovně:

1. Stisknutím klávesy E v GRUB upravíte položku Boot (položka SLES).

1. Vyhledejte řádek jádra, který začíná systémem *Linux*.
1. Připojit *systém. jednotka = nouzový. Target* na konec řádku jádra.
1. Stisknutím kombinace kláves CTRL + X restartujte s tímto nastavením a zadejte nouzové prostředí.

   > [!NOTE]
   > Tato akce vás zavede do nouzového prostředí pomocí systému souborů určeného jen pro čtení. Chcete-li upravit všechny soubory, znovu připojte systém souborů s oprávněním pro čtení i zápis. Provedete to tak, `mount -o remount,rw /` že v prostředí zadáte.

## <a name="access-for-oracle-linux"></a>Přístup pro Oracle Linux
Podobně jako Red Hat Enterprise Linux, režim jednoho uživatele v Oracle Linux vyžaduje GRUB a kořenový uživatel, který se má povolit.

### <a name="grub-access-in-oracle-linux"></a>GRUB přístup v Oracle Linux
Oracle Linux se dodává s GRUB, které jsou povolené. Pokud chcete zadat GRUB, restartujte virtuální počítač spuštěním `sudo reboot` a pak stiskněte klávesu ESC. Tato akce zobrazí podokno GRUB. Pokud se nezobrazuje podokno GRUB, ujistěte se, že hodnota `GRUB_TERMINAL` řádku obsahuje *sériovou konzoli* (tj `GRUB_TERMINAL="serial console"` .). Sestavte GRUB pomocí `grub2-mkconfig -o /boot/grub/grub.cfg` .

### <a name="single-user-mode-in-oracle-linux"></a>Režim jednoho uživatele v Oracle Linux
Pokud chcete povolit režim jednoho uživatele v Oracle Linux, postupujte podle předchozích pokynů pro RHEL.

## <a name="next-steps"></a>Další kroky
Další informace o sériové konzole najdete v těchto tématech:
* [Dokumentace k sériové konzole pro Linux](serial-console-linux.md)
* [Použití sériové konzoly k povolení GRUB v různých distribucích](http://linuxonazure.azurewebsites.net/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Použití sériové konzoly pro volání NMI a SysRq](serial-console-nmi-sysrq.md)
* [Sériová Konzola pro virtuální počítače s Windows](serial-console-windows.md)
* [Diagnostika spouštění](boot-diagnostics.md)
