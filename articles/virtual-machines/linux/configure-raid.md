---
title: Konfigurace softwaru diskového pole RAID na virtuálním počítači se systémem Linux | Microsoft Docs
description: Další informace o použití mdadm ke konfiguraci RAID na systému Linux v Azure.
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
ms.openlocfilehash: d6e831692da37645e264c6674f1ba54bb16d25d4
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="configure-software-raid-on-linux"></a>Konfigurace softwarového pole RAID v Linuxu
Je běžný scénář pomocí softwaru diskového pole RAID na virtuální počítače s Linuxem v Azure k dispozici více připojených datových disků jako jedno zařízení RAID. Obvykle to lze zlepšit výkon a umožňuje lepší výkon ve srovnání s použitím právě jeden disk.

## <a name="attaching-data-disks"></a>Připojení datových disků
Dvě nebo více prázdný datových disků jsou potřeba ke konfiguraci RAID zařízení.  Hlavním důvodem pro vytváření diskového pole RAID zařízení je ke zlepšení výkonu disku vstupně-výstupní operace.  Podle potřeb vstupně-výstupní operace, můžete připojit disky, které jsou uložené v našem standardního úložiště, s maximálně 500 vstupně-výstupní operace/ps pro na disk nebo naše storage úrovně Premium s až 5000 vstupně-výstupní operace za ps na disk. Tento článek nepřekračuje podrobnosti o tom, jak zřídit a připojte datových disků pro virtuální počítač s Linuxem.  Najdete v článku Microsoft Azure [připojit disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podrobné pokyny o tom, jak připojit prázdný datový disk pro virtuální počítač s Linuxem v Azure.

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

## <a name="create-the-disk-partitions"></a>Vytvoření oddílů disku
V tomto příkladu vytvoříme na /dev/sdc oddíl jediný disk. Nový diskový oddíl bude volána /dev/sdc1.

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

2. Stiskněte klávesu n příkazového řádku k vytvoření **n**ové oddílu:

    ```bash
    Command (m for help): n
    ```

3. Stiskněte klávesu "p" k vytvoření **p**rimární oddílu:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

4. Stiskněte klávesu '1' a vyberte číslo oddílu 1:

    ```bash
    Partition number (1-4): 1
    ```

5. Vyberte výchozí bod nového oddílu, nebo klikněte na tlačítko `<enter>` přijměte výchozí nastavení pro umístění oddílu na začátku volného místa na disku:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

6. Vyberte velikost oddílu, například typ '+10G' k vytvoření oddílu 10 GB. Také můžete stisknout klávesu `<enter>` vytvořte jeden oddíl, který zahrnuje celou jednotku:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

7. V dalším kroku změňte ID a **t**typ oddílu z výchozí ID '83' (Linux) ID, fd' (Linux raid automaticky):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

8. Nakonec tabulce oddílu zápis na disk a ukončete fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Vytvoření pole RAID
1. Následující příklad se "stripe" (úroveň pole RAID 0) tři oddíly nachází na tři samostatné datové disky (sdc1, sdd1, sde1).  Po spuštění tohoto příkazu nové zařízení RAID názvem **/dev/md127** je vytvořena. Všimněte si, že pokud tyto datové disky jsme dříve součástí jiného nefunkční pole RAID může být potřeba přidat `--force` parametru `mdadm` příkaz:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

2. Vytvořit systém souborů na nové zařízení RAID
   
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
   > Po provedení těchto změn na systémy SUSE může být nutný restart. Tento krok je *není* na SLES 12 vyžaduje.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Přidat nový systém souborů do /etc/fstab
> [!IMPORTANT]
> Nesprávně úprav souboru /etc/fstab může mít za následek nelze spustit systém. Pokud jistí, naleznete distribuční dokumentaci informace o tom, jak správně upravit tento soubor. Dále je doporučeno, jestli je vytvořená záloha souboru /etc/fstab před úpravou.

1. Vytvořte požadované přípojného bodu pro nový systém souborů, například:

    ```bash
    sudo mkdir /data
    ```
2. Při úpravě /etc/fstab, **UUID** slouží k odkazování na systém souborů, nikoli název zařízení.  Použití `blkid` nástroj k určení identifikátor UUID pro nový systém souborů:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

3. V textovém editoru otevřete /etc/fstab a přidejte položku pro nový systém souborů, například:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Nebo na **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Potom uložte a zavřete /etc/fstab.

4. Testovací správnost zadání fstab/etc /:

    ```bash  
    sudo mount -a
    ```

    Pokud tento příkaz výsledkem chybovou zprávu, Zkontrolujte prosím syntaxe v souboru /etc/fstab.
   
    Následně spusťte `mount` zajistěte připojení k systému souborů:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

5. (Volitelné) Bezporuchový spouštěcí parametry
   
    **Konfigurace fstab**
   
    Velkém množství distribucí obsahovat buď `nobootwait` nebo `nofail` připojit parametry, které mohou být přidány do souboru/etc/fstab. Tyto parametry umožňují selhání při připojení příslušného systému souborů a povolit spuštění i v případě, že nelze správně připojit RAID systému souborů i nadále systému Linux. Vyhledejte vaší distribuční dokumentaci další informace o těchto parametrů.
   
    Příklad (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Parametry spuštění systému Linux**
   
    Kromě výše uvedených parametrů, parametr jádra "`bootdegraded=true`" Povolit systém tak, aby i v případě, že RAID je považována za poškozený nebo sníženou pro příklad, pokud datová jednotka nechtěně odebrán z virtuálního počítače. Ve výchozím nastavení může také výsledkem-spouštěcího systému.
   
    Naleznete vaší distribuční dokumentaci o tom, jak správně upravit parametry jádra. Například ve velkém množství distribucí (CentOS, Oracle Linux SLES 11.) tyto parametry mohou být přidány ručně na "`/boot/grub/menu.lst`" soubor.  Na Ubuntu tento parametr lze přidat do `GRUB_CMDLINE_LINUX_DEFAULT` proměnné na "/ etc/výchozí/grub".


## <a name="trimunmap-support"></a>Podpora uvolnění dočasné paměti nebo UNMAP
Některé Linux jádra podporovat operace TRIM/UNMAP vyřadí nepoužívané bloky na disku. Tyto operace jsou užitečné hlavně v standardní úložiště k informování Azure, které odstraněné stránky již nejsou platné a může být vymazány. Zahození stránky můžete uložit náklady, pokud chcete vytvořit velkých souborů a pak odstraňte je.

> [!NOTE]
> RAID nemusí zadávání příkazů zahození, velikost bloku pro toto pole je nastaven na hodnotu menší než výchozí (512 KB). Je to proto členitost unmap na hostiteli je také 512KB. Pokud jste změnili velikost bloku pole prostřednictvím na mdadm `--chunk=` parametr a potom zrušit mapování/TRIM žádosti může být ignorován jádrem.

Existují dva způsoby, jak povolit TRIM podporují ve virtuálním počítačům s Linuxem. Obvyklým způsobem podívejte se distribuční o doporučený postup:

- Použití `discard` připojit možnost v `/etc/fstab`, například:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- V některých případech `discard` možnost může mít vliv na výkon. Alternativně můžete spustit `fstrim` ručně příkaz z příkazového řádku, nebo ho přidat do vaší crontab pravidelně spouštět:

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
