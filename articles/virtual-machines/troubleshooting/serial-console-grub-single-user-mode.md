---
title: Azure konzoly sériového portu GRUB a režimu jednoho uživatele | Dokumentace Microsoftu
description: Pomocí konzoly sériového portu pro grub ve službě Azure virtual machines.
services: virtual-machines-linux
documentationcenter: ''
author: alsin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 47a97d842822ed3d6c8c1583808552c1b2d1d53e
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413399"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Použít pro přístup k GRUB a Jednouživatelský režim konzoly sériového portu
Režim jednoho uživatele je minimální prostředí s minimálním funkce. Může být užitečné pro prozkoumat problémy s spouštěcí nebo problémů se sítí a méně services může běžet na pozadí, v závislosti na runlevel, systém souborů nemusí i připojit automaticky. To je užitečné v situacích, například poškozený systém souborů, porušení fstab prošetření nebo síťového připojení (nesprávná iptables konfigurace).

Některé distribuce automaticky vyřadí je v jednouživatelském režimu nebo nouzového režimu Pokud je virtuální počítač nelze spustit. Jiné, ale vyžadují další nastavení, před jejich lze přetáhnout je do režimu jednoho uživatele nebo nouzové situace to automaticky.

Můžete zajistit, že GRUB je povoleno na virtuálním počítači mohli do režimu jednoho uživatele přístup. V závislosti na vaší distribuce může být Ujistěte se, že je povoleno GRUB nějakou práci navíc instalační program. 


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Přístup pro Red Hat Enterprise Linux (RHEL)
RHEL vyřadí je do režimu jednoho uživatele automaticky pokud ji nemůže normálně. Pokud jste nenastavili kořenový přístup pro režimu jednoho uživatele, ale nebudou mít kořenové heslo a nebude moci přihlásit. Existuje alternativní řešení (viz "Ručním zadáním režimu jednoho uživatele" níže), ale návrh je zpočátku nastavený přístup uživatele root.

### <a name="grub-access-in-rhel"></a>GRUB přístup v RHEL
RHEL se dodává s GRUB povolené úprav. Pokud chcete zadat GRUB, restartu virtuálního počítače s `sudo reboot` a stisknutím libovolné klávesy. Zobrazí se obrazovka GRUB zobrazí.

> Poznámka: Red Hat také poskytuje dokumentaci pro spuštění v režimu zachránit, nouzovou režim, režim ladění a resetování kořenového hesla. [Chcete-li k němu přístup, klikněte sem](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Nastavit kořenový přístup pro režim jednoho uživatele v RHEL
Režim jednoho uživatele v RHEL vyžaduje kořenového uživatele na povoleno, který je ve výchozím nastavení zakázané. Pokud máte třeba povolit režim jednoho uživatele, použijte následující pokyny:

1. Přihlaste se k systému Red Hat pomocí protokolu SSH
1. Přepnout do kořenového adresáře
1. Povolit heslo uživatele root 
    * `passwd root` (nastavte silné kořenové heslo)
1. Ujistěte se, že uživatel root mohou přihlašovat pouze prostřednictvím ttyS0
    * `edit /etc/ssh/sshd_config` a ujistěte se, že PermitRootLogIn je nastavena na no
    * `edit /etc/securetty file` Povolit přihlášení prostřednictvím ttyS0 jenom 

Teď Pokud systému se spustí do režimu jednoho uživatele můžete přihlásit pomocí hesla kořenového.

Případně zobrazí výzvu režimu jednoho uživatele RHEL 7.4 + nebo 6.9 + můžete povolit v GRUB, naleznete v tématu pokyny [zde](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ručně zadejte režimu jednoho uživatele v RHEL
Pokud jste nastavili GRUB a kořenový přístup pomocí výše uvedených pokynů pak můžete zadat režimu jednoho uživatele v následujících pokynech:

1. Při restartování virtuálního počítače zadejte GRUB stisknutím klávesy "Esc.
1. V GRUB stiskněte "e", chcete-li upravit vybraný operační systém, které chcete spustit (obvykle na první řádek)
1. Vyhledejte řádek jádra – v Azure, tím se spustí s `linux16`
1. Stiskněte kombinaci kláves Ctrl + E, přejděte na konec řádku
1. Přidejte na konec řádku následující: `systemd.unit=rescue.target`
    * Tím se spustí do režimu jednoho uživatele. Pokud chcete použít nouzový režim, přidejte `systemd.unit=emergency.target` na konci řádku namísto `systemd.unit=rescue.target`
1. Stisknutím kláves Ctrl + X ukončit a restartovat počítač s použité nastavení
1. Zobrazí se výzva k zadání hesla správce bylo možné do režimu jednoho uživatele – jedná se o stejné heslo, které jste vytvořili ve výše uvedených pokynů    

    ![](/media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Přejít do režimu jednoho uživatele bez kořenového účtu povoleno v RHEL
Pokud není projít výše uvedené kroky a povolit uživatel root, můžete stále resetovat kořenové heslo. Pomocí následujících pokynů:

> Poznámka: Pokud používáte SELinux, zkontrolujte, zda jste udělali další kroky popsané v dokumentaci k systému Red Hat [tady](https://aka.ms/rhel7grubterminal) při resetování kořenového hesla.

1. Při restartování virtuálního počítače zadejte GRUB stisknutím klávesy "Esc.
1. V GRUB stiskněte "e", chcete-li upravit vybraný operační systém, které chcete spustit (obvykle na první řádek)
1. Vyhledejte řádek jádra – v Azure, tím se spustí s `linux16`
1. Přidat `rd.break` do konce řádku a zajistit, že je mezera před `rd.break` (viz následující příklad)
    - Tímto se přeruší spuštění procesu před řízení je předáno z `initramfs` k `systemd`, jak je popsáno v dokumentaci k systému Red Hat [tady](https://aka.ms/rhel7rootpassword).
1. Stisknutím kláves Ctrl + X ukončit a restartovat počítač s použité nastavení
1. Po spuštění, se zahodí v nouzovém režimu systémem souborů jen pro čtení. Zadejte `mount -o remount,rw /sysroot` do prostředí pro opětovné připojení systému souborů kořenové pomocí oprávnění ke čtení/zápisu
1. Po spuštění do režimu jednoho uživatele, zadejte v `chroot /sysroot` pro přepnutí do `sysroot` jailbreak
1. Teď jste root. Můžete resetovat heslo kořenového s `passwd` a pak postupujte podle pokynů výše do režimu jednoho uživatele. Typ `reboot -f` restartovat po dokončení.

![](/media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Poznámka: Systémem pomocí výše uvedených pokynů vyřadí můžete do nouzový prostředí, tak můžete také provádět úlohy, jako jsou úpravy `fstab`. Obecně přijímané návrh je však k resetování kořenového hesla, který budete používat do režimu jednoho uživatele. 


## <a name="access-for-centos"></a>Přístup pro CentOS
Mnohem jako je Red Hat Enterprise Linux, vyžaduje režimu jednoho uživatele v CentOS GRUB a uživatel root, aby byla povolená. 

### <a name="grub-access-in-centos"></a>GRUB přístup v CentOS
CentOS součástí GRUB povolené úprav. Pokud chcete zadat GRUB, restartu virtuálního počítače s `sudo reboot` a stisknutím libovolné klávesy. Zobrazí se obrazovka GRUB zobrazí.

### <a name="single-user-mode-in-centos"></a>Režimu jednoho uživatele v CentOS
Postupujte podle pokynů pro RHEL výše pro povolení režimu jednoho uživatele v CentOS.

## <a name="access-for-ubuntu"></a>Přístup k Ubuntu 
Image Ubuntu nevyžadují kořenové heslo. Pokud systém spustí do režimu jednoho uživatele, můžete bez další přihlašovací údaje. 

### <a name="grub-access-in-ubuntu"></a>GRUB přístup v Ubuntu
Pro přístup k GRUB, stiskněte a podržte "Esc", a spouští virtuální počítač.

### <a name="single-user-mode-in-ubuntu"></a>Režimu jednoho uživatele v Ubuntu
Ubuntu vyřadí je do režimu jednoho uživatele automaticky pokud ji nemůže normálně. Ruční zadání režimu jednoho uživatele, použijte následující pokyny:

1. Z GRUB stiskněte "e", chcete-li upravit vaše spouštěcí položku (položky Ubuntu)
1. Vyhledejte řádek, který začíná `linux`, zkuste najít `ro`
1. Přidat `single` po `ro`, zajistit, že je mezera před a po ní `single`
1. Stisknutím kláves Ctrl + X restartujte s těmito nastaveními a do režimu jednoho uživatele

## <a name="access-for-coreos"></a>Přístup pro CoreOS
Režim jednoho uživatele v CoreOS vyžaduje GRUB, aby byla povolená. 

### <a name="grub-access-in-coreos"></a>GRUB přístup v CoreOS
Pro přístup k GRUB, stisknutím libovolné klávesy, když váš virtuální počítač spouští.

### <a name="single-user-mode-in-coreos"></a>Režimu jednoho uživatele v CoreOS
CoreOS vyřadí je do režimu jednoho uživatele automaticky pokud ji nemůže normálně. Ruční zadání režimu jednoho uživatele, použijte následující pokyny:
1. Z GRUB stiskněte "e", chcete-li upravit spouštěcí položka
1. Vyhledejte řádek, který začíná `linux$`. Mělo by být 2, zapouzdřené v různých if/else klauzule
1. Připojit `coreos.autologin=ttyS0` konec obě `linux$` řádky
1. Stisknutím kláves Ctrl + X restartujte s těmito nastaveními a do režimu jednoho uživatele

## <a name="access-for-suse-sles"></a>Přístup pro SUSE SLES
Novější imagí z rozhraní SLES 12 SP3 + povolit přístup prostřednictvím konzoly sériového portu v případě, že systém se spustí v nouzovém režimu. 

### <a name="grub-access-in-suse-sles"></a>GRUB přístup v SUSE SLES
GRUB přístup v SLES vyžaduje spouštěcí zavaděč konfigurace přes YaST. Chcete-li to provést, postupujte podle těchto pokynů:

1. SSH do SLES virtuální počítač a spusťte `sudo yast bootloader`. Použití `tab` klíč, `enter` klíč a klávesy se šipkami Procházet v nabídce. 
1. Přejděte do `Kernel Parameters`a zkontrolujte `Use serial console`. 
1. Přidat `serial --unit=0 --speed=9600 --parity=no` do konzoly argumentů.

1. Stiskněte klávesu F10 nastavení uložte a zavřete
1. Pokud chcete zadat GRUB, restartu virtuálního počítače a stisknutím libovolné klávesy během sekvence spouštění aby GRUB zůstat na obrazovku
    - Výchozí hodnota časového limitu pro GRUB je 1s. Tuto hodnotu můžete změnit pomocí změny `GRUB_TIMEOUT` proměnné v `/etc/default/grub`

![](/media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Režimu jednoho uživatele v SUSE SLES
Můžete se automaticky vloží do nouzový shell Pokud SLES nemůže normálně. Ruční zadání nouzový prostředí, použijte následující pokyny:

1. Z GRUB stiskněte "e", chcete-li upravit vaše spouštěcí položku (položky SLES)
1. Vyhledejte řádek jádra, který se spustí s `linux`
1. Připojit `systemd.unit=emergency.target` na konec řádku
1. Stisknutím kláves Ctrl + X restartujte s těmito nastaveními a zadejte nouzový prostředí
> Všimněte si, že se vloží do nouzový prostředí s _jen pro čtení_ systému souborů. Pokud chcete provádět žádné úpravy všech souborů, musíte pro opětovné připojení systému souborů pomocí oprávnění pro čtení i zápis. Chcete-li to provést, zadejte `mount -o remount,rw /` do prostředí

## <a name="access-for-oracle-linux"></a>Přístup pro Oracle Linux
Podobně jako je Red Hat Enterprise Linux, režimu jednoho uživatele v Oracle Linuxu vyžaduje GRUB a uživatel root, aby byla povolená. 

### <a name="grub-access-in-oracle-linux"></a>GRUB přístup v Oracle Linuxu
Oracle Linux se dodává s GRUB povolené úprav. Pokud chcete zadat GRUB, restartu virtuálního počítače s `sudo reboot` a stisknutím klávesy "Esc". Zobrazí se obrazovka GRUB zobrazí.

### <a name="single-user-mode-in-oracle-linux"></a>Režimu jednoho uživatele v Oracle Linuxu
Postupujte podle pokynů pro RHEL výše pro povolení režimu jednoho uživatele v Oracle Linuxu.

## <a name="next-steps"></a>Další postup
* Na stránce dokumentace s Linuxem hlavní konzoly sériového portu se nachází [tady](serial-console-linux.md).
* Použití konzoly sériového portu pro [NMI a SysRq volání](serial-console-nmi-sysrq.md)
* Je taky dostupná ke konzole sériového portu [Windows](serial-console-windows.md) virtuálních počítačů
* Další informace o [Diagnostika spouštění](boot-diagnostics.md)