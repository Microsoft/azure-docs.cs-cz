---
title: Konzola sériového prostředí Azure pro GRUB a režim single user | Microsoft Docs
description: Použití sériové konzoly pro grub na virtuálních počítačích Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 656bc8329d6273695e4da24a7e7d13c9df6a1080
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846607"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Použití sériové konzoly pro přístup k GRUB a jednomu uživatelskému režimu
GRUB je celkový jednotný zaváděcí program pro spouštění, který je pravděpodobně první věcí, kterou se zobrazí při spuštění virtuálního počítače. Vzhledem k tomu, že se zobrazuje před spuštěním operačního systému, není přístupná přes SSH. Z GRUB můžete změnit konfiguraci spouštění tak, aby se spouštěla do režimu jednoho uživatele, mimo jiné.

Režim jednoho uživatele je minimální prostředí s minimální funkčností. Může být užitečné při prověřování potíží se spouštěním, problémů systému souborů nebo problémů se sítí. Méně služeb může běžet na pozadí a v závislosti na runlevel nemusí být systém souborů ani automaticky připojen.

Režim jednoho uživatele je také užitečný v situacích, kdy je možné virtuální počítač pro přihlášení přijmout jenom klíče SSH. V takovém případě můžete použít jeden uživatelský režim k vytvoření účtu s ověřováním hesla. Všimněte si, že služba sériové konzoly umožní přístup pouze uživatelům s úrovní přístupu na úrovni přispěvatele nebo vyšší pro přístup ke konzole sériového virtuálního počítače.

Pokud chcete přejít do režimu Single User, budete při spouštění virtuálního počítače muset zadat GRUB a upravit konfiguraci spouštění v GRUB. Podrobné pokyny k zadávání GRUB najdete níže. Obecně platí, že k restartování virtuálního počítače můžete použít tlačítko restartovat v rámci konzoly sériového portu VM a zobrazit GRUB, pokud je váš virtuální počítač nakonfigurovaný tak, aby zobrazoval GRUB.

![Tlačítko pro restartování sériové konzoly Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Obecný přístup GRUB
Pokud chcete získat přístup k GRUB, bude potřeba restartovat virtuální počítač, když zůstane okno sériové konzoly otevřené. Některé distribuce budou vyžadovat vstup z klávesnice pro zobrazení GRUB, zatímco ostatní budou automaticky zobrazovat GRUB po dobu několika sekund a umožnit vstupu klávesnice uživatele zrušit časový limit.

Abyste měli jistotu, že je ve vašem VIRTUÁLNÍm počítači povolený GRUB, bude mít přístup k režimu Single User. V závislosti na vaší distribuce se může stát, že některá nastavení zajistí, že GRUB je povolený. Informace specifické pro distribuce jsou k dispozici níže a na [tomto odkazu](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Restartování virtuálního počítače pro přístup k GRUB v sériových konzolách
VIRTUÁLNÍ počítač můžete restartovat v rámci konzoly sériového portu tak, že přejdete na tlačítko napájení a kliknete na restartovat virtuální počítač. Tím se iniciuje restartování virtuálního počítače a v Azure Portal se zobrazí oznámení týkající se restartování.
Restartování virtuálního počítače můžete provést také pomocí příkazu SysRq `'b'` , pokud je povolený [SysRq](./serial-console-nmi-sysrq.md) . Při restartu se naučíte, co je potřeba od GRUB, podle pokynů pro konkrétní distribuce.

![Restart sériové konzoly Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Přístup do obecného režimu Single User
V situacích, kdy jste nenakonfigurovali účet s ověřováním hesla, může být potřeba ruční přístup k režimu Single User. Konfiguraci GRUB je potřeba upravit tak, aby se ručně zadal režim single user. Až to uděláte, přečtěte si téma použití jednotného uživatelského režimu k resetování nebo přidání hesla pro další pokyny.

V případech, kdy se virtuální počítač nemůže spustit, bude distribuce často automaticky přetahovat do režimu jednoho uživatele nebo nouzového režimu. Jiní uživatelé ale vyžadují další nastavení, než se budou moct odpustit do režimu jednoho uživatele nebo do nouzového režimu (třeba při nastavení kořenového hesla).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Resetování nebo přidání hesla pomocí režimu Single User
Až budete v režimu jednoho uživatele, přidejte nového uživatele s sudo oprávněními následujícím způsobem:
1. Spustit `useradd <username>` pro přidání uživatele
1. Spuštění `sudo usermod -a -G sudo <username>` pro udělení oprávnění nového uživatele root
1. Použijte `passwd <username>` k nastavení hesla pro nového uživatele. Pak se budete moct přihlásit jako nový uživatel.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Přístup pro Red Hat Enterprise Linux (RHEL)
RHEL se automaticky přetáhne do režimu jednoho uživatele, pokud se nedokáže normálně spustit. Pokud jste ale nestavili kořenový přístup pro režim single user, nebudete mít kořenové heslo a nebude se moct přihlásit. Existuje alternativní řešení (Další informace najdete v části Ruční zadání jednoho uživatelského režimu), ale v návrhu je zpočátku nastaveno oprávnění root Access.

### <a name="grub-access-in-rhel"></a>Přístup k GRUB v RHEL
RHEL se dodává s povoleným GRUBem. Pokud chcete zadat grub, restartujte virtuální počítač `sudo reboot` pomocí a stiskněte libovolnou klávesu. Uvidíte, že se zobrazí obrazovka GRUB.

> Poznámka: Red Hat taky poskytuje dokumentaci pro spouštění do záchranného režimu, nouzového režimu, režimu ladění a resetování kořenového hesla. [Pro přístup k němu klikněte sem](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Nastavení kořenového přístupu pro režim single user v RHEL
Režim jednoho uživatele v RHEL vyžaduje, aby byl povolený kořenový uživatel, který je ve výchozím nastavení zakázaný. Pokud potřebujete povolit režim jednoho uživatele, postupujte podle následujících pokynů:

1. Přihlášení k systému Red Hat přes SSH
1. Přepnout na kořen
1. Povolit heslo pro uživatele root
    * `passwd root`(nastavit silné kořenové heslo)
1. Zajistěte, aby se uživatel root mohl přihlásit jenom přes ttyS0.
    * `edit /etc/ssh/sshd_config`a ujistěte se, že je PermitRootLogIn nastavené na ne
    * `edit /etc/securetty file`povolení pouze přihlášení prostřednictvím ttyS0

Pokud se teď systém spustí do režimu jednoho uživatele, můžete se přihlásit pomocí kořenového hesla.

Případně pro RHEL 7.4 + nebo \ \ uživatel + můžete v GRUBch dotazech povolit jeden uživatelský režim, viz pokyny [zde](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single) .

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ruční zadání režimu Single User v RHEL
Pokud jste nastavili GRUB a root Access s výše uvedenými pokyny, můžete zadat jeden uživatelský režim s následujícími pokyny:

1. Stiskněte klávesu ESC a restartujte virtuální počítač, aby bylo možné zadat GRUB.
1. V GRUB stisknutím klávesy e upravíte vybraný operační systém, který chcete spustit (obvykle první řádek).
1. V Azure najdete řádek jádra – v Azure se spustí`linux16`
1. Stisknutím kombinace kláves CTRL + E přejdete na konec řádku.
1. Na konec řádku přidejte následující:`systemd.unit=rescue.target`
    * Spustí se v režimu jednoho uživatele. Pokud chcete použít nouzový režim, přidejte `systemd.unit=emergency.target` na konec řádku místo`systemd.unit=rescue.target`
1. Stisknutím kombinace kláves CTRL + X ukončete a restartujte s použitým nastavením.
1. Až budete moct zadat režim single user, budete vyzváni k zadání hesla správce – toto je stejné heslo, které jste vytvořili v předchozích pokynech.

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Zadat režim single user bez povoleného kořenového účtu v RHEL
Pokud jste neprošli výše uvedenými kroky pro povolení kořenového uživatele, můžete resetovat své kořenové heslo. Použijte následující pokyny:

> Poznámka: Pokud používáte SELinux, ujistěte se, že jste v dokumentaci k Red Hat popsali další kroky popsané v [tématu](https://aka.ms/rhel7grubterminal) resetování kořenového hesla.

1. Stiskněte klávesu ESC a restartujte virtuální počítač, aby bylo možné zadat GRUB.
1. V GRUB stisknutím klávesy e upravíte vybraný operační systém, který chcete spustit (obvykle první řádek).
1. V Azure najdete řádek jádra – v Azure se spustí`linux16`
1. Přidejte `rd.break` na konec řádku a ujistěte se, že je mezera před `rd.break` (viz příklad níže).
    - Tím dojde k přerušení procesu spouštění před předáním řízení z `initramfs` do `systemd`, jak je popsáno v dokumentaci k Red Hat [zde](https://aka.ms/rhel7rootpassword).
1. Stisknutím kombinace kláves CTRL + X ukončete a restartujte s použitým nastavením.
1. Po spuštění systému se systém souborů jen pro čtení přeruší do nouzového režimu. Do `mount -o remount,rw /sysroot` prostředí zadejte příkaz pro opětovné připojení kořenového systému souborů s oprávněním ke čtení a zápisu.
1. Po spuštění do režimu jednoho uživatele zadejte `chroot /sysroot` `sysroot` do jailbreak.
1. Nyní jste kořen. Můžete resetovat své kořenové heslo pomocí `passwd` a pak pomocí výše uvedených pokynů zadat režim single user. Po `reboot -f` dokončení zadejte a restartujte počítač.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Poznámka: Spuštění prostřednictvím výše uvedených pokynů vás přeruší do nouzového prostředí, takže můžete provádět i úlohy, jako je `fstab`například úpravy. Obecně přijatý návrh je však resetování kořenového hesla a jeho použití k zadání režimu Single User.


## <a name="access-for-centos"></a>Přístup pro CentOS
Podobně jako Red Hat Enterprise Linux, jeden uživatelský režim v CentOS vyžaduje GRUB a kořenový uživatel, který se má povolit.

### <a name="grub-access-in-centos"></a>Přístup k GRUB v CentOS
CentOS se dodává s povoleným GRUBem. Pokud chcete zadat grub, restartujte virtuální počítač `sudo reboot` pomocí a stiskněte libovolnou klávesu. Uvidíte, že se zobrazí obrazovka GRUB.

### <a name="single-user-mode-in-centos"></a>Režim single user v CentOS
Podle pokynů pro RHEL výše povolte v CentOS režim single user.

## <a name="access-for-ubuntu"></a>Přístup pro Ubuntu
Image Ubuntu nevyžadují kořenové heslo. Pokud se systém spustí do režimu jednoho uživatele, můžete ho použít bez dalších přihlašovacích údajů.

### <a name="grub-access-in-ubuntu"></a>Přístup k GRUB v Ubuntu
Pokud chcete získat přístup k GRUB, stiskněte a podržte při spouštění virtuálního počítače klávesu ESC.

Ve výchozím nastavení nemusí Ubuntu image automaticky zobrazovat obrazovku GRUB. Můžete to změnit pomocí následujících pokynů:
1. Otevřete `/etc/default/grub.d/50-cloudimg-settings.cfg` v textovém editoru podle vašeho výběru.
1. `GRUB_TIMEOUT` Změňte hodnotu na nenulovou hodnotu.
1. Otevřete `/etc/default/grub` v textovém editoru podle vašeho výběru.
1. Odkomentovat `GRUB_HIDDEN_TIMEOUT=1` řádek
1. Ujistěte se, že existuje řádek, který říká`GRUB_TIMEOUT_STYLE=menu`
1. Spustit `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Režim single user v Ubuntu
Ubuntu se automaticky přetáhne do režimu jednoho uživatele, pokud se nedokáže normálně spustit. Chcete-li ručně zadat režim jednoho uživatele, postupujte podle následujících pokynů:

1. Z GRUB stisknutím klávesy e upravíte položku Boot (položka Ubuntu).
1. Vyhledejte řádek, který začíná `linux`, a pak vyhledejte`ro`
1. Přidat `single` po`ro`a zajistit mezeru před a za`single`
1. Stisknutím kombinace kláves CTRL + X restartujte s těmito nastaveními a zadejte režim single user.

### <a name="using-grub-to-invoke-bash-in-ubuntu"></a>Použití GRUB k vyvolání bash v Ubuntu
Můžou nastat situace (například zapomenuté kořenové heslo), kde stále možná nebudete mít přístup k jednomu uživatelskému režimu na VIRTUÁLNÍm počítači s Ubuntu, a to po vyzkoušení výše uvedených pokynů. V jádru můžete také sdělit, aby spouštěla/bin/bash jako init, nikoli systémovou inicializaci, která vám poskytne bash prostředí a umožní údržbu systému. Použijte následující pokyny:

1. Z GRUB stisknutím klávesy e upravíte položku Boot (položka Ubuntu).
1. Vyhledejte řádek, který začíná `linux`, a pak vyhledejte`ro`
1. Nahradit `ro``rw init=/bin/bash`
    - Tím se váš systém souborů připojí jako pro čtení i zápis a jako proces inicializace použije/bin/bash.
1. Stisknutím kombinace kláves CTRL + X restartujete s těmito nastaveními.

## <a name="access-for-coreos"></a>Přístup pro CoreOS
Režim single user v CoreOS vyžaduje, aby byl povolený GRUB.

### <a name="grub-access-in-coreos"></a>Přístup k GRUB v CoreOS
Pokud chcete získat přístup k GRUB, při spouštění virtuálního počítače stiskněte libovolnou klávesu.

### <a name="single-user-mode-in-coreos"></a>Režim single user v CoreOS
CoreOS se automaticky přetáhne do režimu jednoho uživatele, pokud se nedokáže normálně spustit. Chcete-li ručně zadat režim jednoho uživatele, postupujte podle následujících pokynů:
1. Z GRUB stisknutím klávesy e upravíte položku Boot.
1. Vyhledejte řádek, který začíná `linux$`. V klauzulích if/else by měl být zapouzdřený 2.
1. Připojit `coreos.autologin=ttyS0` ke konci obou `linux$` řádků
1. Stisknutím kombinace kláves CTRL + X restartujte s těmito nastaveními a zadejte režim single user.

## <a name="access-for-suse-sles"></a>Přístup pro SUSE SLES
Novější obrázky SLES 12 SP3 + umožňují přístup prostřednictvím konzoly sériového režimu pro případ, že se systém spustí do nouzového režimu.

### <a name="grub-access-in-suse-sles"></a>GRUB přístup v SUSE SLES
GRUB přístup v SLES vyžaduje konfiguraci zaváděcího programu pro spouštění prostřednictvím YaST. Postupujte přitom podle těchto pokynů:

1. Spusťte na svém VIRTUÁLNÍm počítači s `sudo yast bootloader`SLES SSH a spusťte příkaz. K procházení nabídky použijte `enter` klávesu, klávesu a klávesy se šipkami. `tab`
1. Přejděte na `Kernel Parameters`a ověřte `Use serial console`.
1. Přidat `serial --unit=0 --speed=9600 --parity=no` k argumentům konzoly

1. Stisknutím klávesy F10 uložte nastavení a ukončete akci.
1. Pokud chcete zadat GRUB, restartujte virtuální počítač a stiskněte libovolný klíč během spouštěcí sekvence, abyste mohli GRUB zůstat na obrazovce.
    - Výchozí časový limit pro GRUB je 1. To můžete změnit změnou `GRUB_TIMEOUT` proměnné v`/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Režim single user v SUSE SLES
V případě, že se SLES nemůže normálně spustit, bude automaticky vyřazeno z nouzového prostředí. Chcete-li ručně zadat nouzové prostředí, postupujte podle následujících pokynů:

1. Z GRUB stisknutím klávesy e upravíte položku Boot (položka SLES).
1. Vyhledejte řádek jádra – bude začínat`linux`
1. Připojit `systemd.unit=emergency.target` ke konci řádku
1. Stisknutím kombinace kláves CTRL + X restartujte s těmito nastaveními a zadejte nouzové prostředí.
   > Všimněte si, že se do nouzového prostředí přestanou používat systém souborů _jen pro čtení_ . Pokud chcete provádět úpravy všech souborů, bude nutné znovu připojit systém souborů s oprávněním pro čtení i zápis. Pokud to chcete provést, `mount -o remount,rw /` zadejte do prostředí shell.

## <a name="access-for-oracle-linux"></a>Přístup pro Oracle Linux
Podobně jako Red Hat Enterprise Linux, jeden uživatelský režim v Oracle Linux vyžaduje GRUB a kořenový uživatel, který má být povolen.

### <a name="grub-access-in-oracle-linux"></a>GRUB přístup v Oracle Linux
Oracle Linux se dodává s GRUB, které jsou povolené. Pokud chcete zadat grub, restartujte virtuální počítač `sudo reboot` pomocí a stiskněte klávesu ESC. Uvidíte, že se zobrazí obrazovka GRUB. Pokud nevidíte grub, ujistěte se, že hodnota `GRUB_TERMINAL` řádku obsahuje "sériovou konzolu", například:. `GRUB_TERMINAL="serial console"`

### <a name="single-user-mode-in-oracle-linux"></a>Režim jednoho uživatele v Oracle Linux
Postupujte podle pokynů pro RHEL výše a povolte jeden uživatelský režim v Oracle Linux.

## <a name="next-steps"></a>Další postup
* Hlavní stránka dokumentace pro Linux na sériové konzole se nachází [tady](serial-console-linux.md).
* Naučte se používat sériovou konzolu k [Povolení GRUB v různých distribuce](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/) .
* Použití sériové konzoly pro [volání NMI a SysRq](serial-console-nmi-sysrq.md)
* Pro virtuální počítače s [Windows](serial-console-windows.md) je dostupná taky konzola sériového portu.
* Další informace o [diagnostice spouštění](boot-diagnostics.md)
