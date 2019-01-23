---
title: Konfigurace softwarového pole RAID na virtuálním počítači s Linuxem | Dokumentace Microsoftu
description: Další informace o použití mdadm konfigurace diskového pole RAID v Linuxu v Azure.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.component: disks
ms.openlocfilehash: 3627891df429745c66bb85aed5182ff934268027
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475420"
---
# <a name="configure-software-raid-on-linux"></a>Konfigurace softwarového pole RAID v Linuxu
Je běžným scénářem pomocí softwarového pole RAID na virtuální počítače s Linuxem v Azure k dispozici více připojené datové disky jako jedno zařízení RAID. Obvykle to umožňuje zlepšit výkon a umožňuje lepší výkon ve srovnání s použitím pouze jedním diskem.

## <a name="attaching-data-disks"></a>Připojení spravovaných disků
Dva nebo více prázdných datových disků jsou potřebné ke konfiguraci zařízení RAID.  Hlavním důvodem pro vytváření zařízení RAID je pro zlepšení výkonu vstupně-výstupní operace disku.  Podle svých potřeb vstupně-výstupní operace, můžete připojit disků, které jsou uloženy v našich úložiště úrovně Standard s až 500 vstupně-výstupních operací/ps pro jeden disk nebo naše služby Premium storage s až 5000 vstupně-výstupních operací/ps na disk. Tento článek nepřešel do podrobností o tom, jak zřídit a připojit datové disky virtuálního počítače s Linuxem.  Přečtěte si článek Microsoft Azure [připojení disku](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podrobné pokyny o tom, jak připojit prázdný datový disk do virtuálního počítače s Linuxem v Azure.

## <a name="install-the-mdadm-utility"></a>Nainstalujte nástroj mdadm
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

## <a name="create-the-disk-partitions"></a>Vytváření oddílů disku
V tomto příkladu vytvoříme /dev/sdc oddíl jeden disk. Nový oddíl disku bude volána /dev/sdc1.

1. Spustit `fdisk` zahajte proces vytváření oddílů

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

1. Stisknutím klávesy n příkazového řádku k vytvoření **n**nový oddíl:

    ```bash
    Command (m for help): n
    ```

1. Dále stisknutím klávesy "p", chcete-li vytvořit **p**rimární oddílu:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Stisknutím klávesy "1" Vyberte číslo oddílu 1:

    ```bash
    Partition number (1-4): 1
    ```

1. Vyberte výchozí bod nového oddílu, nebo stisknutím klávesy `<enter>` přijměte výchozí umístění oddílu na začátku volného místa na disku:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Vyberte velikost oddílu, zadejte například "+10G" k vytvoření oddílu 10 GB. Také můžete stisknout klávesu `<enter>` vytvořit jeden oddíl, který zahrnuje celou jednotku:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. V dalším kroku změnit ID a **t**yp oddílu z výchozí ID "83" (Linux) s ID 'fd"(Linux raid automaticky):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. A konečně tabulka oddílů zápis na disk a ukončete fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Vytvoření pole RAID
1. Následující příklad se "stripe" (úroveň pole RAID 0) tři oddíly umístěné na tři samostatné datové disky (sdc1, sdd1, sde1).  Po spuštění tohoto příkazu nové zařízení RAID **/dev/md127** se vytvoří. Všimněte si také, že pokud těchto datových disků jsme dříve součástí jiného nefunkční pole RAID může být potřeba přidat `--force` parametr `mdadm` příkaz:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Vytvořit systém souborů v novém zařízení diskového pole RAID
   
    a. **CentOS, Oracle Linux, SLES 12, openSUSE a Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    b. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **SLES 11** – povolit boot.md a vytvořit mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Po provedení těchto změn v systémech SUSE může být nutný restart počítače. Tento krok je *není* vyžaduje na SLES 12.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Přidat nový systém souborů /etc/fstab
> [!IMPORTANT]
> Nesprávně úpravou souboru /etc/fstab by mohlo způsobit systém nelze spustit. Pokud nejste jisti, najdete v dokumentaci vaší distribuce pro informace o tom, jak správně upravit tento soubor. Doporučujeme také, že je záloha souboru /etc/fstab vytvořená před úpravou.

1. Vytvořte požadované přípojný bod pro nový systém souborů, například:

    ```bash
    sudo mkdir /data
    ```
1. Při úpravě /etc/fstab, **UUID** by měl být slouží k odkazování systému souborů, nikoli název zařízení.  Použití `blkid` nástroje k určení identifikátoru UUID pro nový systém souborů:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Otevřete /etc/fstab v textovém editoru a přidejte záznam pro nový systém souborů, například:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Nebo na **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Potom uložte a zavřete /etc/fstab.

1. Otestujte správnost položce/etc/fstab:

    ```bash  
    sudo mount -a
    ```

    Pokud tento příkaz vrátí chybovou zprávu, Zkontrolujte prosím syntaxi v souboru /etc/fstab.
   
    Další spuštění `mount` příkaz, kterým zajistíte, připojení k systému souborů:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Volitelné) Bezporuchový spouštěcí parametry
   
    **Konfigurace fstab**
   
    Množství distribucí zahrnout buď `nobootwait` nebo `nofail` připojit parametry, které mohou být přidány do souboru/etc/fstab. Tyto parametry umožňuje pro selhání v případě, že připojí systém určitý soubor a umožnit systému Linux, pokračujte ke spuštění i v případě, že není schopen správně připojení systému souborů RAID. V dokumentaci vaší distribuce pro další informace o těchto parametrech.
   
    Příklad (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Parametry spuštění systému Linux**
   
    Kromě výše uvedených parametrů jádra parametr "`bootdegraded=true`" může mít systém možnost spustit i v případě, že RAID bude považován za poškozený nebo snížení pro příklad, pokud datová jednotka je neúmyslně odebrán z virtuálního počítače. Ve výchozím nastavení to může také způsobit nespustitelného systému.
   
    Najdete dokumentaci vaší distribuce o tom, jak správně upravit parametry jádra. Například v množství distribucí (CentOS, Oracle Linux, SLES 11) tyto parametry lze přidat ručně položky "`/boot/grub/menu.lst`" soubor.  Na Ubuntu tento parametr lze přidat do `GRUB_CMDLINE_LINUX_DEFAULT` proměnné na "/ etc/výchozí/grub".


## <a name="trimunmap-support"></a>Podpora uvolnění dočasné paměti/UNMAP
Některé Linuxových jádrech podporovat operace TRIM/UNMAP zahodíte nepoužívané bloky na disku. Tyto operace jsou především užitečné ve standardním úložišti informovat Azure, které odstraní stránek už nejsou platné a mohou být zahozeny. Zahazuje se stránky můžete snížení nákladů, pokud vytvoříte velkých souborů a potom je odstraňte.

> [!NOTE]
> RAID nemusí zadávat příkazy zrušit, pokud je velikost bloku dat pro pole nastavena na hodnotu menší než výchozí (512 KB). Je to proto členitosti unmap na hostiteli je také 512KB. Pokud jste změnili velikost deduplikačního bloku dat tohoto pole prostřednictvím vaší mdadm `--chunk=` parametr a uvolnění dočasné paměti/unmap požadavky mohou být ignorovány jádro.

Existují dva způsoby, jak povolit TRIM podpory v virtuálního počítače s Linuxem. Obvyklým způsobem vaše distribuce najdete doporučený postup:

- Použití `discard` připojit možnost v `/etc/fstab`, například:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- V některých případech `discard` možnost může mít vliv na výkon. Alternativně můžete spustit `fstrim` příkaz ručně z příkazového řádku, nebo ho přidat do vaší crontab pravidelně spuštění:

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
