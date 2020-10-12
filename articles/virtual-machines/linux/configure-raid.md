---
title: Konfigurace softwarového pole RAID na virtuálním počítači se systémem Linux
description: Naučte se používat mdadm ke konfiguraci RAID v systému Linux v Azure.
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 3471ccfa0899f73969c511dea283c2d0d7051af8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84659785"
---
# <a name="configure-software-raid-on-linux"></a>Konfigurace softwarového pole RAID v systému Linux
Běžným scénářem je použití softwarového pole RAID u virtuálních počítačů se systémem Linux v Azure, které slouží k zobrazení několika připojených datových disků jako jednoho zařízení RAID. To se obvykle dá využít ke zlepšení výkonu a k zajištění vyšší propustnosti v porovnání s použitím pouze jednoho disku.

## <a name="attaching-data-disks"></a>Připojování datových disků
Ke konfiguraci zařízení RAID potřebujete dva nebo více prázdných datových disků.  Hlavním důvodem pro vytvoření zařízení RAID je zvýšit výkon v/v disku.  Na základě požadavků na vstupně-výstupní operace se můžete rozhodnout připojit disky, které jsou uložené v naší službě Storage úrovně Standard, s až 500 IO/PS na disk nebo naše úložiště Premium s až 5000/s/PS na disk. Tento článek neposkytuje podrobné informace o tom, jak zřídit a připojit datové disky k virtuálnímu počítači se systémem Linux.  Podrobné pokyny k připojení prázdného datového disku k virtuálnímu počítači se systémem Linux v Azure najdete v článku o Microsoft Azureu [připojit disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

> [!IMPORTANT]
>Nekombinujte disky různých velikostí, takže by to vedlo k tomu, že raidset bude mít vliv na to, co nejpomalejší disk bude omezený. 

## <a name="install-the-mdadm-utility"></a>Instalace nástroje mdadm
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **CentOS & Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES a openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>Vytvoření oddílů disku
V tomto příkladu vytvoříme oddíl s jedním diskem na/dev/sdc. Nový diskový oddíl se bude jmenovat/dev/sdc1.

1. Začněte `fdisk` vytvářet oddíly.

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

1. Stisknutím klávesy n na příkazovém řádku vytvořte oddíl **n**ový:

    ```bash
    Command (m for help): n
    ```

1. Potom stisknutím klávesy p vytvořte oddíl **p**rimární:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Stisknutím klávesy 1 vyberete oddíl číslo 1:

    ```bash
    Partition number (1-4): 1
    ```

1. Vyberte počáteční bod nového oddílu nebo stisknutím klávesy `<enter>` potvrďte výchozí umístění oddílu na začátku volného místa na jednotce:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Vyberte velikost oddílu, například + 10G, a vytvořte oddíl 10 GB. Nebo stiskněte `<enter>` vytvořit jeden oddíl, který pokrývá celou jednotku:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. V dalším kroku Změňte ID a **t**identifikátor oddílu z výchozího ID 83 (Linux) na ID FD (auto RAID pro Linux):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Nakonec zapište tabulku oddílů na jednotku a ukončete nástroj Fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Vytvoření pole RAID
1. Následující příklad bude "prokládaný" (RAID Level 0) tři oddíly umístěné na třech samostatných datových discích (sdc1, sdd1, SDE1).  Po spuštění tohoto příkazu se vytvoří nové zařízení RAID s názvem **/dev/md127** . Všimněte si také, že pokud tyto datové disky dřív částečně doplňují jiné nepoužívané pole RAID, může být nutné přidat `--force` parametr do `mdadm` příkazu:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Vytvoření systému souborů na novém zařízení RAID
   
    **CentOS, Oracle Linux, SLES 12, openSUSE a Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    **SLES 11** – povolení Boot.MD a vytvoření mdadm. conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Po provedení těchto změn v systémech SUSE může být vyžadováno restartování. Tento krok není *vyžadován v* SLES 12.
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>Přidat nový systém souborů do/etc/fstab
> [!IMPORTANT]
> Nesprávná úprava souboru/etc/fstab by mohla vést k nespouštěcímu systému. Pokud si nejste jistí, podívejte se do dokumentace k distribuci, kde najdete informace o tom, jak soubor správně upravit. Doporučuje se také vytvořit zálohu souboru/etc/fstab před úpravou.

1. Vytvořte požadovaný přípojný bod pro nový systém souborů, například:

    ```bash
    sudo mkdir /data
    ```
1. Při úpravách/etc/fstab by měl být **identifikátor UUID** použit k odkazování systému souborů na místo názvu zařízení.  Pomocí `blkid` nástroje určete UUID pro nový systém souborů:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. V textovém editoru otevřete/etc/fstab a přidejte položku pro nový systém souborů, například:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Nebo na **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Pak uložte a zavřete adresáři/etc/fstab.

1. Otestujte správnost položky/etc/fstab:

    ```bash  
    sudo mount -a
    ```

    Pokud tento příkaz má za následek chybovou zprávu, zkontrolujte prosím syntaxi v souboru/etc/fstab.
   
    V dalším kroku spusťte `mount` příkaz, abyste zajistili, že je systém souborů připojený:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. Volitelné Parametry spuštění Failsafe
   
    **Konfigurace fstab**
   
    Mnoho distribucí zahrnuje buď `nobootwait` `nofail` parametry připojení, nebo, které mohou být přidány do souboru/etc/fstab. Tyto parametry umožňují selhání při připojování konkrétního systému souborů a umožňuje, aby se systém Linux spouštěl i v případě, že není schopen správně připojit systém souborů RAID. Další informace o těchto parametrech najdete v dokumentaci k distribuci.
   
    Příklad (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Spouštěcí parametry pro Linux**
   
    Kromě výše uvedených parametrů může parametr jádra " `bootdegraded=true` " umožňovat spuštění systému i v případě, že je RAID zjištěn jako poškozený nebo snížený, například pokud je datová jednotka z virtuálního počítače neúmyslně odebrána. Ve výchozím nastavení to může být způsobeno i nespouštěcím systémem.
   
    Postup správného úprav parametrů jádra najdete v dokumentaci k distribuci. Například v mnoha distribucích (CentOS, Oracle Linux, SLES 11) se tyto parametry dají do souboru přidat ručně `/boot/grub/menu.lst` .  V Ubuntu tento parametr lze přidat do `GRUB_CMDLINE_LINUX_DEFAULT` proměnné v "/etc/default/GRUB".


## <a name="trimunmap-support"></a>Podpora OŘEZÁVÁNÍ a odmapování
Některé jádro systému Linux podporují operace OŘEZÁVÁNÍ a odmapování, aby bylo možné zahodit nepoužívané bloky na disku. Tyto operace jsou primárně užitečné ve službě Storage úrovně Standard pro informování Azure o tom, že odstraněné stránky už nejsou platné a můžou být zahozeny. Vypuštění stránek může ušetřit náklady, pokud vytvoříte velké soubory a pak je odstraníte.

> [!NOTE]
> Pokud je velikost bloku dat pole nastavená na hodnotu menší než výchozí (512 KB), nemusí pole RAID vystavovat příkazy k zahození. Důvodem je to, že členitost mapování na hostiteli je také 512 KB. Pokud jste změnili velikost bloku dat pole prostřednictvím parametru mdadm `--chunk=` , může jádro ignorovat požadavky na ořezávání a oddálení.

Existují dva způsoby, jak na svém VIRTUÁLNÍm počítači se systémem Linux povolit podporu OŘEZÁVÁNÍ. V obvyklých případech si prostudujte doporučený postup:

- Použijte `discard` možnost připojení v `/etc/fstab` , například:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- V některých případech `discard` může mít možnost vliv na výkon. Alternativně můžete `fstrim` příkaz spustit ručně z příkazového řádku nebo ho přidat do crontab, aby se pravidelně spouštěl:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
