---
title: Služba Azure Serial Console pro grub a režim pro jednoho uživatele | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak používat serial console pro GRUB ve virtuálních počítačích Azure.
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
ms.openlocfilehash: 06cb3fe5d551ddfc95fcbd37cd9620adebd825c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883923"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Použití konzoly Serial Console pro přístup k režimu GRUB a režimu pro jednoho uživatele
GRand Unified Bootloader (GRUB) je pravděpodobně první věc, kterou vidíte při spuštění virtuálního počítače (VM). Vzhledem k tomu, že se zobrazí před spuštěním operačního systému, GRUB není přístupný přes SSH. V GRUB můžete mimo jiné upravit konfiguraci spouštění tak, aby se spustila do režimu jednoho uživatele.

Režim jednoho uživatele je minimální prostředí s minimální funkčností. Může být užitečné pro zkoumání problémů se spuštěním, problémy se systémem souborů nebo problémy se sítí. Na pozadí může běžet méně služeb a v závislosti na úrovni runlevelu nemusí být systém souborů ani automaticky připojen.

Režim jednoho uživatele je také užitečné v situacích, kdy váš virtuální počítač může být nakonfigurován tak, aby přijímat pouze SSH klíče pro přihlášení. V takovém případě můžete použít režim jednoho uživatele k vytvoření účtu s ověřováním hesla. 

> [!NOTE]
> Služba Sériová konzola umožňuje přístup k sériové konzoli virtuálního počítačů pouze uživatelům s oprávněními na úrovni *přispěvatele* nebo vyšším.

Chcete-li vstoupit do režimu jednoho uživatele, zadejte GRUB při spuštění virtuálního počítače a upravte konfiguraci spouštění v GRUB. Viz podrobné pokyny pro zadání GRUB v další části. Obecně platí, že pokud váš virtuální počítač byl nakonfigurovaný pro zobrazení GRUB, můžete použít tlačítko restart v rámci konzole sériového počítače k restartování virtuálního počítače a zobrazení GRUB.

![Tlačítko Restart sériové konzole Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Obecný přístup ke grubu
Chcete-li získat přístup ke vzdálenému virtuálnímu počítači, restartujte virtuální počítač, když je otevřené podokno sériové konzole. Některé distribuce vyžadují vstup z klávesnice pro zobrazení GRUB a jiné automaticky zobrazit GRUB na několik sekund, aby vstup z klávesnice uživatele zrušit časový čas.

Chcete-li mít přístup k režimu jednoho uživatele, chcete zajistit, že GRUB je povolena na vašem virtuálním počítači. V závislosti na vaší distribuci mohou být některé instalační práce nezbytné k zajištění, že grub je povolen. Informace o konkrétní distribuci najdete v další části a na naší [stránce Podpora Linuxu v Azure.](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Restartujte virtuální počítač pro přístup ke ke ke drubům v konzole Serial Console
Virtuální počítač v konzole Serial Console můžete restartovat tak, že najedete na tlačítko **Restartovat** a pak vyberete **restartovat virtuální počítač**. V dolní části podokna se zobrazí oznámení o restartování.

Virtuální počítač můžete také restartovat spuštěním příkazu SysRq "b", pokud je povolen [sysRq.](./serial-console-nmi-sysrq.md) Chcete-li se dozvědět, co očekávat od GRUB při restartování, naleznete v pokynech pro konkrétní distribuci v dalších částech.

![Restart sériové konzoly Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Obecný přístup k režimu pro jednoho uživatele
Pokud jste nenakonfigurovali účet s ověřováním heslem, může být nutné získat ruční přístup k režimu pro jednoho uživatele. Upravte konfiguraci GRUB tak, aby ručně přecškovala do režimu jednoho uživatele. Další pokyny naleznete v části "Pomocí režimu pro jednoho uživatele obnovte nebo přidejte heslo".

Pokud se virtuální ms nedaří spustit, distribuce vás často automaticky přepne do režimu jednoho uživatele nebo nouzového režimu. Jiné distribuce však vyžadují další nastavení, například nastavení kořenového hesla, než vás mohou automaticky přeřadit do režimu jednoho uživatele nebo nouzového režimu.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Obnovení nebo přidání hesla pomocí režimu jednoho uživatele
Až budete v režimu pro jednoho uživatele, přidejte nového uživatele s oprávněními sudo následujícím způsobem:
1. Spusťte `useradd <username>` přidání uživatele.
1. Spusťte `sudo usermod -a -G sudo <username>` udělení oprávnění kořenového adresáře nového uživatele.
1. Slouží `passwd <username>` k nastavení hesla pro nového uživatele. Poté se můžete přihlásit jako nový uživatel.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Přístup pro Red Hat Enterprise Linux (RHEL)
Pokud rhel nelze spustit normálně, to kapky vás do režimu jednoho uživatele automaticky. Pokud jste však root access pro režim pro jednoho uživatele nenastavili, nemáte kořenové heslo a nemůžete se přihlásit. Existuje řešení (viz část "Ručně zadejte režim jednoho uživatele v RHEL"), ale doporučujeme nastavit přístup k kořenům zpočátku.

### <a name="grub-access-in-rhel"></a>GRUB přístup v RHEL
RHEL je dodáván s grub povoleno po vybalení z krabice. Chcete-li zadat GRUB, restartujte virtuální počítač spuštěním `sudo reboot`a stiskněte libovolnou klávesu. Měla by být zobrazena podokno GRUB. Pokud tomu tak není, ujistěte se, že v`/etc/default/grub`souboru GRUB jsou přítomny následující řádky ( ):

**Pro RHEL 8**

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
> Red Hat také poskytuje dokumentaci pro spuštění do záchranného režimu, nouzového režimu nebo režimu ladění a pro resetování kořenového hesla. Pokyny naleznete v [tématu Úpravy nabídky terminálu během spuštění](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Nastavení root přístupu pro režim jednoho uživatele v RHEL
Kořenový uživatel je ve výchozím nastavení zakázán. Režim jednoho uživatele v rhel vyžaduje, aby byl povolen kořenový uživatel. Pokud potřebujete povolit režim pro jednoho uživatele, postupujte podle následujících pokynů:

1. Přihlaste se do systému Red Hat přes SSH.
1. Přepněte do kořenového adresáře.
1. Povolte heslo pro kořenového uživatele následujícím způsobem:
    * Spustit `passwd root` (nastavit silné kořenové heslo).
1. Ujistěte se, že kořenový uživatel může přihlásit pouze přes ttyS0 tímto následujícím způsobem:  
    a. Spusťte `edit /etc/ssh/sshd_config`a ujistěte se, `no`že PermitRootLogIn je nastavena na .  
    b. Spuštěním `edit /etc/securetty file` povolíte přihlášení pouze prostřednictvím ttyS0.

Nyní, pokud se systém spustí do režimu jednoho uživatele, můžete se přihlásit pomocí kořenového hesla.

Případně pro RHEL 7.4+ nebo 6.9+, chcete-li povolit režim jednoho uživatele v grubových výzvách, viz [Spuštění do režimu jednoho uživatele](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ruční zadání režimu jednoho uživatele v rhel
Pokud jste nastavili přístup ke kořenům GRUB a root pomocí předchozích pokynů, můžete vstoupit do režimu jednoho uživatele následujícím způsobem:

1. Chcete-li zadat GRUB, stiskněte při restartování virtuálního počítače klávesu Esc.
1. V grubu stisknutím klávesy E upravte operační systém, do kterého chcete spustit. Operační systém je obvykle uveden na prvním řádku.
1. Najděte řádek jádra. V Azure začíná na *linux16*.
1. Stisknutím kláves Ctrl+E přejdete na konec řádku.
1. Na konci řádku přidejte *systemd.unit=rescue.target*.
    
    Tato akce vás spustí do režimu jednoho uživatele. Pokud chcete použít nouzový režim, přidejte *systemd.unit=emergency.target* na konec linky (místo *systemd.unit=rescue.target).*

1. Stisknutím kláves Ctrl+X ukončete a restartujte počítač s použitým nastavením.

   Před vstupem do režimu jednoho uživatele budete vyzváni k zadání hesla správce. Toto heslo je heslo, které jste vytvořili v předchozích pokynech.

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Zadat režim jednoho uživatele bez povoleného kořenového účtu v RHEL
Pokud jste uživatele root nepovolili podle předchozích pokynů, můžete kořenové heslo obnovit následujícím způsobem:

> [!NOTE]
> Pokud používáte SELinux, při resetování kořenového hesla postupujte podle dalších kroků popsaných v [dokumentaci k Red Hat](https://aka.ms/rhel7grubterminal).

1. Chcete-li zadat GRUB, stiskněte při restartování virtuálního počítače klávesu Esc.

1. V grubu stisknutím klávesy E upravte operační systém, do kterého chcete spustit. Operační systém je obvykle uveden na prvním řádku.
1. Najděte řádek jádra. V Azure začíná na *linux16*.
1. Na konci řádku přidejte *rd.break* na konec řádku. Ponechte mezeru mezi řádkem jádra a *souborem rd.break*.

    Tato akce přeruší proces spouštění `initramfs` `systemd`před předáním ovládacího prvku do položky , jak je popsáno v [dokumentaci k redhat](https://aka.ms/rhel7rootpassword).
1. Stisknutím kláves Ctrl+X ukončete a restartujte počítač s použitým nastavením.

   Po restartování se dostanete do nouzového režimu se souborovým systémem jen pro čtení. 
   
1. Ve skořepině zadejte, `mount -o remount,rw /sysroot` chcete-li znovu připojit kořenový systém souborů s oprávněními ke čtení a zápisu.
1. Po spuštění do režimu jednoho `chroot /sysroot` uživatele, `sysroot` zadejte přepnout do vězení.
1. Teď jsi u kořene. Kořenové heslo můžete obnovit `passwd` zadáním a následným zadáním předchozích pokynů do režimu jednoho uživatele. 
1. Po dokončení zadejte `reboot -f` restart.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> Spuštění matné předchozí instrukce kapky vás do nouzového prostředí, `fstab`takže můžete také provádět úkoly, jako je například úpravy . Obvykle však doporučujeme resetovat kořenové heslo a použít ho k přepání režimu pro jednoho uživatele.

## <a name="access-for-centos"></a>Přístup pro CentOS
Stejně jako Red Hat Enterprise Linux, režim jednoho uživatele v CentOS vyžaduje GRUB a uživatele root, které mají být povoleny.

### <a name="grub-access-in-centos"></a>GRUB přístup v CentOS
CentOS je dodáván s GRUB povoleno po vybalení z krabice. Chcete-li zadat GRUB, restartujte virtuální počítač zadáním `sudo reboot`a stiskněte libovolnou klávesu. Tato akce zobrazí podokno GRUB.

### <a name="single-user-mode-in-centos"></a>Režim pro jednoho uživatele v CentOS
Chcete-li povolit režim jednoho uživatele v CentOS, postupujte podle dřívějších pokynů pro RHEL.

## <a name="access-for-ubuntu"></a>Přístup pro Ubuntu
Obrázky Ubuntu nevyžadují kořenové heslo. Pokud se systém spustí do režimu jednoho uživatele, můžete jej použít bez dalších pověření.

### <a name="grub-access-in-ubuntu"></a>GRUB přístup v Ubuntu
Chcete-li získat přístup ke grubu, stiskněte a podržte Esc při zavádění virtuálního aplikace.

Ve výchozím nastavení nemusí obrazy Ubuntu automaticky zobrazovat podokno GRUB. Nastavení můžete změnit následujícím způsobem:
1. V textovém editoru otevřete soubor */etc/default/grub.d/50-cloudimg-settings.cfg.*

1. Změňte `GRUB_TIMEOUT` hodnotu na nenulovou hodnotu.
1. V textovém editoru otevřete */etc/default/grub*.
1. Zakomentujte `GRUB_HIDDEN_TIMEOUT=1` řádek.
1. Ujistěte se, `GRUB_TIMEOUT_STYLE=menu` že je tam fronta.
1. Spusťte `sudo update-grub`.

### <a name="single-user-mode-in-ubuntu"></a>Režim pro jednoho uživatele v Ubuntu
Pokud Ubuntu nemůže zavést normálně, automaticky vás upustí do režimu jednoho uživatele. Chcete-li zadat režim jednoho uživatele ručně, postupujte takto:

1. V GRUB, stiskněte Tlačítko E upravit boot entry (vstup Ubuntu).
1. Podívejte se na řádek, který začíná *linux*, a pak se podívejte *na ro*.
1. Přidejte *jeden* po *ro*, ujistěte se, že je prostor před a po *single*.
1. Stisknutím kláves Ctrl+X restartujte s těmito nastaveními a přejděte do režimu jednoho uživatele.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Použijte GRUB vyvolat bash v Ubuntu
Poté, co jste vyzkoušeli předchozí pokyny, může nastat situace (například zapomenuté root heslo), kdy stále nemůžete přistupovat k režimu jednoho uživatele ve vašem virtuálním počítači Ubuntu. Můžete také říct, jádro `/bin/bash` spustit jako init, spíše než systém init. Tato akce vám dává bash shell a umožňuje údržbu systému. Postupujte podle následujících pokynů:

1. V GRUB, stiskněte Tlačítko E upravit boot entry (vstup Ubuntu).

1. Podívejte se na řádek, který začíná *linux*, a pak se podívejte *na ro*.
1. Nahradit *ro* *rw init = / bin / bash*.

    Tato akce připojí systém souborů jako čtení `/bin/bash` a zápis a používá jako proces init.
1. Stisknutím kláves Ctrl+X restartujte s těmito nastaveními.

## <a name="access-for-coreos"></a>Přístup pro CoreOS
Režim jednoho uživatele v CoreOS vyžaduje grub, který má být povolen.

### <a name="grub-access-in-coreos"></a>Přístup ke grubům v CoreOS
Chcete-li získat přístup ke společnosti GRUB, stiskněte libovolnou klávesu při zavádění virtuálního počítače.

### <a name="single-user-mode-in-coreos"></a>Režim pro jednoho uživatele v CoreOS
Pokud coreos nelze spustit normálně, to kapky vás do režimu jednoho uživatele automaticky. Chcete-li zadat režim jednoho uživatele ručně, postupujte takto:

1. V grubu upravte položku zaváděcího zařízení stisknutím klávesy E.

1. Podívejte se na řádek, který začíná *linux $*. Měly by existovat dvě instance řádku, každý zapouzdřený v *jiném, pokud ... jinak.*
1. Připojit *coreos.autologin=ttyS0* na konec každého *linux$* řádku.
1. Stisknutím kláves Ctrl+X restartujte s těmito nastaveními a přejděte do režimu jednoho uživatele.

## <a name="access-for-suse-sles"></a>Přístup pro SUSE SLES
Novější obrázky sles 12 SP3+ umožňují přístup přes sériovou konzoli, pokud se systém spustí do nouzového režimu.

### <a name="grub-access-in-suse-sles"></a>GRUB přístup v SUSE SLES
GRUB přístup v SLES vyžaduje konfiguraci zavaděče přes YaST. Chcete-li vytvořit konfiguraci, postupujte takto:

1. Pomocí SSH se přihlaste k virtuálnímu `sudo yast bootloader`počítači SLES a spusťte . Stiskněte klávesu Tab, stiskněte Enter a potom pomocí kláves se šipkami procházejte nabídku.

1. Přejděte na **Parametry jádra**a zaškrtněte políčko **Použít konzolu sériového portu.**
1. Přidejte `serial --unit=0 --speed=9600 --parity=no` do **konzoly** argumenty.
1. Stisknutím klávesy F10 uložte nastavení a ukončete.
1. Chcete-li zadat GRUB, restartujte virtuální počítač a stiskněte libovolnou klávesu během spouštěcí sekvence, aby se podokno GRUB zobrazovalo.

    Výchozí časový limit pro GRUB je **1s**. Toto nastavení můžete upravit `GRUB_TIMEOUT` změnou proměnné v souboru */etc/default/grub.*

![Inicializace konfigurace zavaděče](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Režim pro jednoho uživatele ve sles SUSE
Pokud sles nelze spustit normálně, jste automaticky spadl do nouzového prostředí. Chcete-li nouzovou střelu zadat ručně, postupujte takto:

1. V grubu upravte položku spouštění (položku SLES) stisknutím klávesy E.

1. Podívejte se na jádro linky, která začíná *s linuxem*.
1. Append *systemd.unit=emergency.target* na konec řádku jádra.
1. Stisknutím kombinace kláves Ctrl+X restartujte s těmito nastaveními a přejděte do nouzového prostředí.

   > [!NOTE]
   > Tato akce vás přenese do nouzového prostředí se systémem souborů jen pro čtení. Chcete-li upravit všechny soubory, znovu připojte systém souborů s oprávněními pro čtení a zápis. Chcete-li tak `mount -o remount,rw /` učinit, zadejte do prostředí.

## <a name="access-for-oracle-linux"></a>Přístup pro Oracle Linux
Podobně jako Red Hat Enterprise Linux vyžaduje režim jednoho uživatele v Oracle Linuxu grub a uživatele root.

### <a name="grub-access-in-oracle-linux"></a>Přístup ke společnosti GRUB v systému Oracle Linux
Oracle Linux je dodáván s grub povoleno po vybalení z krabice. Chcete-li zadat GRUB, restartujte virtuální počítač spuštěním `sudo reboot`a stiskněte klávesu Esc. Tato akce zobrazí podokno GRUB. Pokud se podokno GRUB nezobrazí, ujistěte `GRUB_TERMINAL` se, že hodnota řádku `GRUB_TERMINAL="serial console"`obsahuje *sériovou konzolu* (tj. ). Znovu vytvořit `grub2-mkconfig -o /boot/grub/grub.cfg`GRUB s .

### <a name="single-user-mode-in-oracle-linux"></a>Režim jednoho uživatele v systému Oracle Linux
Chcete-li v systému Oracle Linux povolit režim jednoho uživatele, postupujte podle dřívějších pokynů pro funkci RHEL.

## <a name="next-steps"></a>Další kroky
Další informace o konzole Serial Console najdete v tématu:
* [Dokumentace k konzolové konzoli Linux](serial-console-linux.md)
* [Povolení grubu v různých distribucích pomocí konzoly Serial Console](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Použití konzoly Serial Console pro volání NMI a SysRq](serial-console-nmi-sysrq.md)
* [Sériová konzola pro virtuální servery Windows](serial-console-windows.md)
* [Diagnostika spouštění](boot-diagnostics.md)
