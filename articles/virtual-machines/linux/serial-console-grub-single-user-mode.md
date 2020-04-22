---
title: Konzola Azure Serial Console pro grub a režim jednoho uživatele
description: Použití sériové konzoly pro grub ve virtuálních počítačích Azure.
services: virtual-machines-linux
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 2aa7110ab4e52fdc5c3804bd27be5f41081fb435
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758503"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Použití sériové konzoly pro přístup ke GRUB a režimu jednoho uživatele
GRUB je GRand Unified Bootloader. Z GRUB můžete mimo jiné upravit konfiguraci spouštění tak, aby se spustila do režimu jednoho uživatele.

Režim jednoho uživatele je minimální prostředí s minimální funkčností. Může být užitečné pro zkoumání problémů se spuštěním, problémy se systémem souborů nebo problémy se sítí. Na pozadí může běžet méně služeb a v závislosti na úrovni runlevelu nemusí být souborový systém ani automaticky připojen.

Režim jednoho uživatele je také užitečné v situacích, kdy virtuální počítač může být nakonfigurován pouze pro přijetí klíčů SSH pro přihlášení. V takovém případě můžete použít režim jednoho uživatele k vytvoření účtu s ověřováním hesla.

Chcete-li vstoupit do režimu jednoho uživatele, budete muset zadat GRUB, když se váš virtuální počítač spouštění, a upravit konfiguraci spouštění v GRUB. To může být provedeno s konzolou sériového zařízení virtuálního zařízení.

## <a name="general-grub-access"></a>Obecný přístup ke grubu
Chcete-li získat přístup ke společnosti GRUB, budete muset restartovat virtuální počítač při zachování otevřeného okna konzoly sériové konzole. Některé distribuce budou vyžadovat vstup z klávesnice pro zobrazení GRUB, zatímco jiné automaticky zobrazí GRUB na několik sekund a umožní uživateli vstup z klávesnice zrušit časový čas. 

Budete chtít zajistit, že GRUB je povolena na vašem virtuálním počítači, aby bylo možné získat přístup k režimu jednoho uživatele. V závislosti na distro, může být nějaké instalační práce, aby zajistily, že GRUB je povolena. Informace specifické pro distro jsou k dispozici níže.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>Restartujte virtuální počítač pro přístup ke ke ke drubům v konzole Serial Console
Restartování virtuálního počítače s otevřeným předlohou konzoly lze `'b'` provést pomocí příkazu SysRq, pokud je povoleno [SysRq,](./serial-console-nmi-sysrq.md) nebo kliknutím na tlačítko Restartovat v okně Přehled (otevřete virtuální počítač v nové záložce prohlížeče pro restartování bez zavření okna sériové konzole). Postupujte podle pokynů pro distro-specifické níže se dozvíte, co očekávat od GRUB při restartu.

## <a name="general-single-user-mode-access"></a>Obecný přístup k režimu jednoho uživatele
V situacích, kdy jste nenakonfigurovali účet s ověřováním heslem, může být nutný ruční přístup k režimu jednoho uživatele. Budete muset upravit konfiguraci GRUB pro ruční vstup do režimu jednoho uživatele. Jakmile to uděláte, přečtěte si článek Obnovení nebo přidání hesla pomocí režimu jednoho uživatele.

V případech, kdy virtuální hod nelze spustit, distribuce se často automaticky kapky vás do režimu jednoho uživatele nebo nouzového režimu. Jiné však vyžadují další nastavení, než vás mohou automaticky přetáhnout do režimu jednoho uživatele nebo nouzového režimu (například nastavení kořenového hesla).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Resetování nebo přidání hesla pomocí režimu jednoho uživatele
Jakmile jste v režimu jednoho uživatele, postupujte takto, abyste přidali nového uživatele s oprávněními sudo:
1. Spuštění `useradd <username>` pro přidání uživatele
1. Spuštění `sudo usermod -a -G sudo <username>` pro udělení oprávnění kořenového adresáře nového uživatele
1. Slouží `passwd <username>` k nastavení hesla pro nového uživatele. Poté se budete moci přihlásit jako nový uživatel

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Přístup pro Red Hat Enterprise Linux (RHEL)
RHEL vás automaticky upustí do režimu jednoho uživatele, pokud se nemůže normálně spustit. Pokud jste však nenastavili root přístup pro režim jednoho uživatele, nebudete mít kořenové heslo a nebudete se moci přihlásit. Existuje řešení (viz "Ruční zadávání režimu jednoho uživatele" níže), ale návrh je nejprve nastavit root přístup.

### <a name="grub-access-in-rhel"></a>GRUB přístup v RHEL
RHEL je dodáván s grub povoleno po vybalení z krabice. Chcete-li zadat GRUB, `sudo reboot` restartujte virtuální počítač a stiskněte libovolnou klávesu. Zobrazí se obrazovka GRUB.

> Poznámka: Red Hat také poskytuje dokumentaci pro spuštění do záchranného režimu, nouzového režimu, režimu ladění a resetování kořenového hesla. [Klikněte zde pro přístup k němu](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Nastavení root přístupu pro režim jednoho uživatele v RHEL
Režim jednoho uživatele v rhel vyžaduje, aby byl povolen kořenový uživatel, který je ve výchozím nastavení zakázán. Pokud potřebujete povolit režim jednoho uživatele, postupujte podle následujících pokynů:

1. Přihlaste se do systému Red Hat přes SSH
1. Přepnout do kořenového adresáře
1. Povolení hesla pro kořenového uživatele 
    * `passwd root`(nastavit silné kořenové heslo)
1. Ujistěte se, že uživatel kořenového adresáře se může přihlásit pouze přes ttyS0
    * `edit /etc/ssh/sshd_config`a ujistěte se, že PermitRootLogIn není nastaven na
    * `edit /etc/securetty file`povolit přihlášení pouze přes ttyS0 

Nyní, když se systém spustí do režimu jednoho uživatele, můžete se přihlásit pomocí kořenového hesla.

Alternativně pro RHEL 7.4+ nebo 6.9+ můžete povolit režim jednoho uživatele v grubových výzvách, viz pokyny [zde](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ruční zadání režimu jednoho uživatele v rhel
Pokud jste nastavili GRUB a root přístup s výše uvedenými pokyny, můžete vstoupit do režimu jednoho uživatele s následujícími pokyny:

1. Stiskněte 'Esc' při restartování virtuálního virtuálního mě pro vstup do GRUB
1. V GRUB, stiskněte 'e' upravit vybraný operační systém, který chcete spustit do (obvykle první řádek)
1. Najít jádro linky - v Azure, to bude začínat`linux16`
1. Stisknutím kláves Ctrl + E přejdete na konec řádku.
1. Na konec řádku přidejte toto:`systemd.unit=rescue.target`
    * Tím se spustíte do režimu jednoho uživatele. Chcete-li použít nouzový režim, přidejte `systemd.unit=emergency.target` na konec linky namísto`systemd.unit=rescue.target`
1. Stisknutím kláves Ctrl + X ukončíte a restartujte počítač s použitým nastavením.
1. Před vstupem do režimu jednoho uživatele budete vyzváni k zadání hesla správce – toto je stejné heslo, které jste vytvořili ve výše uvedených pokynech.    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Zadat režim jednoho uživatele bez povoleného kořenového účtu v RHEL
Pokud jste neprošli výše uvedenými kroky, abyste povolili uživatele root, můžete stále obnovit kořenové heslo. Postupujte podle následujících pokynů:

> Poznámka: Pokud používáte SELinux, ujistěte se, že jste při resetování kořenového hesla provedli další kroky popsané [v](https://aka.ms/rhel7grubterminal) dokumentaci k Red Hatu.

1. Stiskněte 'Esc' při restartování virtuálního virtuálního mě pro vstup do GRUB
1. V GRUB, stiskněte 'e' upravit vybraný operační systém, který chcete spustit do (obvykle první řádek)
1. Najít jádro linky - v Azure, to bude začínat`linux16`
1. Přidejte `rd.break` na konec řádku a ujistěte se, že před ní je mezera `rd.break` (viz příklad níže)
    - Tím se přeruší proces spouštění `systemd`před předáním ovládacího prvku do `initramfs` položky , jak je popsáno v dokumentaci k Red [Hatzde](https://aka.ms/rhel7rootpassword).
1. Stisknutím kláves Ctrl + X ukončíte a restartujte počítač s použitým nastavením.
1. Po spuštění budete propuštěni do nouzového režimu se souborovým systémem jen pro čtení. Vstupdo `mount -o remount,rw /sysroot` prostředí pro opětovné připojení kořenového systému souborů s oprávněními ke čtení a zápisu
1. Jakmile se spustíte do režimu jednoho uživatele, zadejte `chroot /sysroot` přepnout do `sysroot` vězení
1. Teď jsi kořen. Můžete obnovit root heslo `passwd` s a pak pomocí výše uvedených pokynů pro vstup do režimu jednoho uživatele. Po `reboot -f` dokončení zadejte restartování.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Poznámka: Běh přes výše uvedené pokyny klesne vás do nouzového shellu, takže můžete také provádět úkoly, jako je editace `fstab`. Obecně přijímaný návrh je však obnovit kořenové heslo a použít ho k zadání režimu jednoho uživatele. 


## <a name="access-for-centos"></a>Přístup pro CentOS
Stejně jako Red Hat Enterprise Linux, režim jednoho uživatele v CentOS vyžaduje GRUB a uživatele root, které mají být povoleny. 

### <a name="grub-access-in-centos"></a>GRUB přístup v CentOS
CentOS je dodáván s GRUB povoleno po vybalení z krabice. Chcete-li zadat GRUB, `sudo reboot` restartujte virtuální počítač a stiskněte libovolnou klávesu. Zobrazí se obrazovka GRUB.

### <a name="single-user-mode-in-centos"></a>Režim jednoho uživatele v CentOS
Podle výše uvedených pokynů pro RHEL povolte režim jednoho uživatele v CentOS.

## <a name="access-for-ubuntu"></a>Přístup pro Ubuntu 
Obrázky Ubuntu nevyžadují kořenové heslo. Pokud se systém spustí do režimu jednoho uživatele, můžete jej použít bez dalších pověření. 

### <a name="grub-access-in-ubuntu"></a>GRUB přístup v Ubuntu
Chcete-li získat přístup ke grubu, stiskněte a podržte "Esc", zatímco se virtuální hotel spouštějí. 

Ve výchozím nastavení se obrázky Ubuntu automaticky nezobrazí obrazovku GRUB. To lze změnit pomocí následujících pokynů:
1. Otevření `/etc/default/grub.d/50-cloudimg-settings.cfg` v textovém editoru dle vašeho výběru
1. Změna `GRUB_TIMEOUT` hodnoty na nenulovou hodnotu
1. Otevření `/etc/default/grub` v textovém editoru dle vašeho výběru
1. Zakomentujte `GRUB_HIDDEN_TIMEOUT=1` řádek
1. Spusťte `sudo update-grub`.

### <a name="single-user-mode-in-ubuntu"></a>Režim jednoho uživatele v Ubuntu
Ubuntu vás automaticky upustí do režimu jednoho uživatele, pokud se nemůže normálně spustit. Chcete-li ručně přejít režim jednoho uživatele, postupujte podle následujících pokynů:

1. Z GRUB, stiskněte 'e' pro úpravu boot entry (vstup Ubuntu)
1. Podívejte se na řádek, který začíná `linux`, pak se podívejte na`ro`
1. `single` Přidejte `ro`po , ujistěte se, že je prostor před a po`single`
1. Stisknutím kláves Ctrl + X restartujte s těmito nastaveními a přejděte do režimu jednoho uživatele

## <a name="access-for-coreos"></a>Přístup pro CoreOS
Režim jednoho uživatele v CoreOS vyžaduje grub, který má být povolen. 

### <a name="grub-access-in-coreos"></a>Přístup ke grubům v CoreOS
Chcete-li získat přístup ke společnosti GRUB, stiskněte libovolnou klávesu, když se virtuální počítač spouštějí.

### <a name="single-user-mode-in-coreos"></a>Režim jednoho uživatele v CoreOS
CoreOS vás automaticky upustí do režimu jednoho uživatele, pokud se nemůže normálně spustit. Chcete-li ručně přejít režim jednoho uživatele, postupujte podle následujících pokynů:
1. Z GRUB, stiskněte 'e' pro úpravu boot entry
1. Vyhledejte řádek, který `linux$`začíná na . Měly by existovat 2, zapouzdřené v různých if/ else klauzule
1. Připojit `coreos.autologin=ttyS0` na konec obou `linux$` řádků
1. Stisknutím kláves Ctrl + X restartujte s těmito nastaveními a přejděte do režimu jednoho uživatele

## <a name="access-for-suse-sles"></a>Přístup pro SUSE SLES
Novější snímky sles 12 SP3+ umožňují přístup přes sériovou konzoli v případě, že se systém spustí do nouzového režimu. 

### <a name="grub-access-in-suse-sles"></a>GRUB přístup v SUSE SLES
GRUB přístup v SLES vyžaduje zavaděč konfiguraci přes YaST. Chcete-li to provést, postupujte podle následujících pokynů:

1. ssh do vašeho SLES `sudo yast bootloader`VM a spustit . Pomocí `tab` kláves, `enter` kláves a kláves se šipkami procházet menu. 
1. Přejděte `Kernel Parameters`na `Use serial console`a zkontrolujte . 
1. Přidat `serial --unit=0 --speed=9600 --parity=no` do argumentů konzoly

1. Stisknutím klávesy F10 uložte nastavení a ukončete
1. Chcete-li zadat GRUB, restartujte virtuální počítač a stiskněte libovolnou klávesu během spouštěcí sekvence, aby GRUB zůstal na obrazovce
    - Výchozí časový limit pro GRUB je 1s. Tuto možnost můžete upravit `GRUB_TIMEOUT` změnou proměnné`/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Režim jednoho uživatele ve sles SUSE
Pokud sles nelze spustit normálně, budete automaticky vypuštěni do nouzového prostředí. Chcete-li ručně zadat nouzový granát, postupujte podle následujících pokynů:

1. Z GRUB, stiskněte 'e' pro úpravu boot entry (SLES položka)
1. Podívejte se na jádro linky bude začínat`linux`
1. Připojit `systemd.unit=emergency.target` na konec řádku
1. Stisknutím kláves Ctrl + X restartujte s těmito nastaveními a zadejte nouzové prostředí
   > Všimněte si, že budete vynecháni do nouzového prostředí se souborovým systémem _jen pro čtení._ Pokud chcete provést nějaké úpravy všech souborů, budete muset znovu připojit souborový systém s oprávněními pro čtení a zápis. Chcete-li to `mount -o remount,rw /` provést, zadejte do prostředí

## <a name="access-for-oracle-linux"></a>Přístup pro Oracle Linux
Stejně jako Red Hat Enterprise Linux, režim jednoho uživatele v Oracle Linux vyžaduje GRUB a uživatele root, které mají být povoleny. 

### <a name="grub-access-in-oracle-linux"></a>Přístup ke společnosti GRUB v systému Oracle Linux
Oracle Linux je dodáván s grub povoleno po vybalení z krabice. Chcete-li zadat GRUB, `sudo reboot` restartujte virtuální počítač s a stiskněte tlačítko "Esc". Zobrazí se obrazovka GRUB.

### <a name="single-user-mode-in-oracle-linux"></a>Režim jednoho uživatele v systému Oracle Linux
Podle výše uvedených pokynů pro rhel povolte režim jednoho uživatele v oracle linuxu.

## <a name="next-steps"></a>Další kroky
* Hlavní stránka dokumentace sériové konzole Linux je [umístěna zde](serial-console.md).
* Použití konzoly Serial Console pro [volání NMI a SysRq](serial-console-nmi-sysrq.md)
* Konzola Serial Console je k dispozici také pro virtuální servery [windows.](../windows/serial-console.md)
* Další informace o [diagnostice spouštění](boot-diagnostics.md)
