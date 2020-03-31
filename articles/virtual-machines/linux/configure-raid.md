---
title: Konfigurace softwarového raidu na virtuálním počítači s Linuxem
description: Přečtěte si, jak pomocí mdadmnakonfigurovat RAID na Linuxu v Azure.
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 122abda51b907491b322908c3c2c689bc1723e87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250254"
---
# <a name="configure-software-raid-on-linux"></a>Konfigurace softwarového raidu v Linuxu
Je běžné používat softwarový RAID na virtuálních počítačích SIP v Azure k prezentaci více připojených datových disků jako jednoho zařízení RAID. Obvykle to lze použít ke zlepšení výkonu a umožnit lepší propustnost ve srovnání s použitím pouze jeden disk.

## <a name="attaching-data-disks"></a>Připojení datových disků
Ke konfiguraci zařízení RAID jsou zapotřebí dva nebo více prázdných datových disků.  Hlavním důvodem pro vytvoření zařízení RAID je zlepšení výkonu vznětového zařízení disku.  Na základě vašich potřeb vstupně-bojích můžete připojit disky, které jsou uloženy v našem standardním úložišti, s až 500 IO/ps na disk nebo s úložištěm Premium až s 5000 IO/ps na disk. Tento článek nezajde do podrobností o tom, jak zřídit a připojit datové disky k virtuálnímu počítači Linux.  Podrobný návod, jak připojit prázdný datový disk k virtuálnímu počítači Linuxu v Azure, najdete v článku Microsoft [Azure, kde najdete](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podrobné pokyny, jak připojit prázdný datový disk k virtuálnímu počítači Linuxu.

> [!IMPORTANT]
>Nekombinujte disky různých velikostí, takže by to mělo za následek výkon raidset být omezena na nejpomalejší disk. 

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

## <a name="create-the-disk-partitions"></a>Vytvoření diskových oddílů
V tomto příkladu vytvoříme jeden diskový oddíl na /dev/sdc. Nový diskový oddíl se bude nazývat /dev/sdc1.

1. Začněte `fdisk` vytvářet oddíly

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

1. Stisknutím tlačítka 'n' na výzvu vytvořte **oddíl n**ew:

    ```bash
    Command (m for help): n
    ```

1. Dále stisknutím tlačítka 'p' vytvořte oddíl **p**rimary:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Stisknutím klávesy 1 vyberte oddíl číslo 1:

    ```bash
    Partition number (1-4): 1
    ```

1. Vyberte počáteční bod nového oddílu nebo stisknutím tlačítka přijměte `<enter>` výchozí umístění oddílu na začátek volného místa na jednotce:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Vyberte velikost oddílu, například zadejte '+10G' pro vytvoření oddílu 10 gigabajtů. Nebo stiskněte `<enter>` klávesu Create a single partition that spans the entire drive:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Dále změňte ID a **t**ype oddílu z výchozího ID '83' (Linux) na ID 'fd' (Linux raid auto):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Nakonec napište tabulku oddílů na jednotku a ukončete fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Vytvoření pole RAID
1. Následující příklad bude "prokládaný" (RAID úroveň 0) tři oddíly umístěné na třech samostatných datových discích (sdc1, sdd1, sde1).  Po spuštění tohoto příkazu je vytvořeno nové zařízení RAID s názvem **/dev/md127.** Všimněte si také, že pokud tyto datové disky jsme dříve součástí `--force` jiného `mdadm` zaniklého pole RAID může být nutné přidat parametr do příkazu:

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
   
    **SLES 11** - umožnit boot.md a vytvořit mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Po provedení těchto změn v systémech SUSE může být nutné restartovat počítač. Tento krok *není* nutný na SLES 12.
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>Přidání nového systému souborů do /etc/fstab
> [!IMPORTANT]
> Nesprávná úprava souboru /etc/fstab může vést k nespustitelnému systému. Pokud si nejste jistí, podívejte se do dokumentace k distribuci, kde najdete informace o tom, jak soubor správně upravit. Doporučuje se také, aby záloha souboru /etc/fstab byla vytvořena před úpravou.

1. Vytvořte požadovaný přípojný bod pro nový systém souborů, například:

    ```bash
    sudo mkdir /data
    ```
1. Při úpravách /etc/fstab by mělo být **UUID** použito k odkazování na systém souborů, nikoli k názvu zařízení.  Pomocí `blkid` nástroje určete UUID pro nový systém souborů:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Otevřete /etc/fstab v textovém editoru a přidejte položku pro nový systém souborů, například:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Nebo na **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Poté uložte a zavřete /etc/fstab.

1. Otestujte správnou položku /etc/fstab:

    ```bash  
    sudo mount -a
    ```

    Pokud tento příkaz má za následek chybovou zprávu, zkontrolujte syntaxi v souboru /etc/fstab.
   
    Další spuštění `mount` příkazu, aby se zajistilo, že systém souborů je připojen:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Nepovinné) Failsafe spouštěcí parametry
   
    **konfigurace fstab**
   
    Mnoho distribucí `nobootwait` `nofail` obsahuje parametry nebo přípojné parametry, které mohou být přidány do souboru /etc/fstab. Tyto parametry umožňují selhání při montáži konkrétního systému souborů a umožňují systému Linux pokračovat v zavádění, i když není schopen správně připojit souborový systém RAID. Další informace o těchto parametrech naleznete v dokumentaci k distribuci.
   
    Příklad (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linuxové zaváděcí parametry**
   
    Kromě výše uvedených parametrů může parametr`bootdegraded=true`jádra " " umožnit spuštění systému i v případě, že je RAID vnímán jako poškozený nebo zhoršený, například pokud je datová jednotka neúmyslně odebrána z virtuálního počítače. Ve výchozím nastavení by to mohlo mít za následek také nezaváděcí systém.
   
    Informace o tom, jak správně upravit parametry jádra, naleznete v dokumentaci k distribuci. Například v mnoha distribucích (CentOS, Oracle Linux, SLES 11) mohou`/boot/grub/menu.lst`být tyto parametry přidány ručně do " " souboru.  Na Ubuntu tento parametr lze `GRUB_CMDLINE_LINUX_DEFAULT` přidat do proměnné na "/etc/default/grub".


## <a name="trimunmap-support"></a>Podpora TRIM/UNMAP
Některá linuxová jádra podporují operace TRIM/UNMAP k vyřazení nepoužívaných bloků na disku. Tyto operace jsou primárně užitečné ve standardním úložišti informovat Azure, že odstraněné stránky již nejsou platné a mohou být zahozeny. Vyhození stránek může ušetřit náklady, pokud vytvoříte velké soubory a pak je odstraníte.

> [!NOTE]
> Raid nemusí vydávat zahodit příkazy, pokud je velikost bloku pro pole nastavena na méně než výchozí (512 kB). Důvodem je, že rozlišovací schopnost unmap na host je také 512 KB. Pokud jste upravili velikost bloku dat pomocí `--chunk=` parametru mdadm, mohou být požadavky TRIM/unmap jádrem ignorovány.

Existují dva způsoby, jak povolit podporu TRIM ve vašem virtuálním počítači s Linuxem. Jako obvykle, poraďte se s vaší distribuce pro doporučený přístup:

- Použijte `discard` možnost připojení `/etc/fstab`v části , například:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- V některých `discard` případech může mít možnost vliv na výkon. Případně můžete `fstrim` příkaz spustit ručně z příkazového řádku nebo jej přidat do crontabu a pravidelně spouštět:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/Centos**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
