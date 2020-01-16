---
title: Konfigurace LVM na virtuálním počítači se systémem Linux
description: Přečtěte si, jak nakonfigurovat LVM pro Linux v Azure.
services: virtual-machines-linux
documentationcenter: na
author: MicahMcKittrick-MSFT
manager: gwallace
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/27/2018
ms.author: mimckitt
ms.subservice: disks
ms.openlocfilehash: 4fdb3af0433cc32aa7cdbee3c8ca9bdb85031135
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045339"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Konfigurace LVM na virtuálním počítači se systémem Linux v Azure
Tento dokument popisuje, jak na virtuálním počítači Azure nakonfigurovat Správce logických svazků (LVM). LVM se dá použít na disku s operačním systémem nebo na datových discích ve virtuálních počítačích Azure, ale ve výchozím nastavení většina cloudových imagí nebude mít LVM nakonfigurovanou na disku s operačním systémem. Následující postup se zaměřuje na konfiguraci LVM pro datové disky.

## <a name="linear-vs-striped-logical-volumes"></a>Lineární vs. prokládané logické svazky
LVM lze použít ke kombinování několika fyzických disků do jednoho svazku úložiště. Ve výchozím nastavení bude LVM obvykle vytvářet lineární logické svazky, což znamená, že fyzické úložiště je zřetězené dohromady. V takovém případě se operace čtení/zápisu obvykle odesílají jenom na jeden disk. Naproti tomu můžeme také vytvořit prokládané logické svazky, ve kterých jsou čtení a zápisy distribuované na více discích obsažených ve skupině Volume (podobně jako RAID0). Z důvodů výkonu je pravděpodobně vhodné proložit logické svazky tak, aby čtení a zápis využily všechny připojené datové disky.

Tento dokument popisuje, jak zkombinovat několik datových disků do jedné skupiny svazků, a pak vytvořit prokládaný logický svazek. Následující kroky jsou zobecněné pro práci s většinou distribucí. Ve většině případů se nástroje a pracovní postupy pro správu LVM v Azure neliší od jiných prostředí. V obvyklých případech si také prostudujte dokumentaci a osvědčené postupy pro používání LVM s vaší konkrétní distribucí.

## <a name="attaching-data-disks"></a>Připojování datových disků
Jeden bude obvykle chtít začít se dvěma nebo více prázdnými datovými disky při použití LVM. Na základě požadavků na vstupně-výstupní operace se můžete rozhodnout připojit disky, které jsou uložené v naší službě Storage úrovně Standard, s až 500 IO/PS na disk nebo naše úložiště Premium s až 5000/s/PS na disk. Tento článek nepřejde do podrobností o tom, jak zřídit a připojit datové disky k virtuálnímu počítači se systémem Linux. Podrobné pokyny k připojení prázdného datového disku k virtuálnímu počítači se systémem Linux v Azure najdete v článku o Microsoft Azureu [připojit disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

## <a name="install-the-lvm-utilities"></a>Instalace nástrojů LVM
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS & Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 a openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    V SLES11 musíte také upravit `/etc/sysconfig/lvm` a nastavit `LVM_ACTIVATED_ON_DISCOVERED` na "Povolit":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Konfigurace LVM
V tomto průvodci předpokládáme, že máte připojené tři datové disky, které budeme označovat jako `/dev/sdc`, `/dev/sdd` a `/dev/sde`. Tyto cesty nemusí odpovídat názvům cest k disku ve vašem VIRTUÁLNÍm počítači. K vypsání dostupných disků můžete spustit "`sudo fdisk -l`" nebo podobný příkaz.

1. Příprava fyzických svazků:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Vytvořte skupinu svazků. V tomto příkladu voláme skupinu svazků `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Vytvořte logické svazky. Následující příkaz vytvoří jeden logický svazek s názvem `data-lv01` pro celou skupinu svazků, ale Všimněte si, že je také možné vytvořit více logických svazků ve skupině svazků.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Naformátovat logický svazek

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > SLES11 místo ext4 použít `-t ext3`. SLES11 podporuje přístup jen pro čtení k systémům souborů ext4.

## <a name="add-the-new-file-system-to-etcfstab"></a>Přidat nový systém souborů do/etc/fstab
> [!IMPORTANT]
> Pokud byste soubor `/etc/fstab` upravili nesprávně, může se stát, že systém nepůjde spustit. Pokud si nejste jistí, podívejte se do dokumentace k distribuci, kde najdete informace o tom, jak soubor správně upravit. Před úpravou se taky doporučuje vytvořit zálohu `/etc/fstab`ho souboru.

1. Vytvořte požadovaný přípojný bod pro nový systém souborů, například:

    ```bash  
    sudo mkdir /data
    ```

2. Najít cestu k logickému svazku

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. V textovém editoru otevřete `/etc/fstab` a přidejte položku pro nový systém souborů, například:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Pak `/etc/fstab`uložte a zavřete.

4. Otestujte správnost položky `/etc/fstab`:

    ```bash    
    sudo mount -a
    ```

    Pokud tento příkaz způsobí chybovou zprávu, Projděte si syntaxi v souboru `/etc/fstab`.
   
    Dále spusťte příkaz `mount`, abyste zajistili, že je systém souborů připojen:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. Volitelné Failsafe parametry spuštění v `/etc/fstab`
   
    Mnoho distribucí zahrnuje buď parametry `nobootwait`, nebo `nofail` připojení, které mohou být přidány do souboru `/etc/fstab`. Tyto parametry umožňují selhání při připojování konkrétního systému souborů a umožňuje, aby se systém Linux spouštěl i v případě, že není schopen správně připojit systém souborů RAID. Další informace o těchto parametrech najdete v dokumentaci k distribuci.
   
    Příklad (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Podpora OŘEZÁVÁNÍ a odmapování
Některé jádro systému Linux podporují operace OŘEZÁVÁNÍ a odmapování, aby bylo možné zahodit nepoužívané bloky na disku. Tyto operace jsou primárně užitečné ve službě Storage úrovně Standard pro informování Azure o tom, že odstraněné stránky už nejsou platné a můžou být zahozeny. Vypuštění stránek může ušetřit náklady, pokud vytvoříte velké soubory a pak je odstraníte.

Existují dva způsoby, jak na svém VIRTUÁLNÍm počítači se systémem Linux povolit podporu OŘEZÁVÁNÍ. V obvyklých případech si prostudujte doporučený postup:

- V `/etc/fstab`použijte možnost `discard` připojení, například:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- V některých případech může mít možnost `discard` dopad na výkon. Alternativně můžete spustit příkaz `fstrim` ručně z příkazového řádku nebo ho přidat do crontab, aby se pravidelně spouštěl:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL, CentOS & Oracle Linux**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
