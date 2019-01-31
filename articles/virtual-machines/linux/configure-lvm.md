---
title: Konfigurace LVM na virtuálním počítači s Linuxem | Dokumentace Microsoftu
description: Zjistěte, jak konfigurace LVM v Linuxu v Azure.
services: virtual-machines-linux
documentationcenter: na
author: szarkos
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: szark
ms.subservice: disks
ms.openlocfilehash: 08f98775360b8c0a82f68f322053cb71f0e79af3
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457322"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Konfigurace LVM na virtuální počítač s Linuxem v Azure
Tento dokument popisuje ke konfiguraci Správce logických svazků (LVM) ve virtuálním počítači Azure. LVM může být použita na disk s operačním systémem nebo datové disky ve virtuálních počítačích Azure, ale ve výchozím nastavení většina cloudových imagí nemá LVM nakonfigurované na disk s operačním systémem. Následující postup se zaměří na konfigurace LVM pro datové disky.

## <a name="linear-vs-striped-logical-volumes"></a>Lineární porovnání prokládané svazky logické
LVM umožňuje zkombinovat do jednoho svazku úložiště počtem fyzických disků. Ve výchozím nastavení LVM obvykle vytvoří lineární logických svazků, což znamená, že je ve fyzickém úložišti zřetězených dohromady. V tomto případě operací čtení a zápisu se obvykle pouze pošle na jeden disk. Naproti tomu můžeme můžete také vytvořit prokládané svazky logické, ve kterých se operace čtení a zápisy distribuují na více disků, které jsou součástí skupiny svazků (podobně jako 0). Z důvodů výkonu bude pravděpodobně že budete chtít prokládanou logických svazků tak, aby přečtených a zapsaných využívat všechny připojené datové disky.

Tento dokument popisuje, jak sloučit několik datových disků do skupiny jednom svazku a pak vytvořit prokládané logický svazek. Následující postup zobecněné pro práci s většině distribucí. Ve většině případů nástrojů a pracovních postupů pro správu LVM v Azure nejsou zásadně liší od jiných prostředích. Jako obvykle také získáte od Linuxu dodavatele pro dokumentaci a doporučené postupy pro používání LVM s zvláštní distribuci.

## <a name="attaching-data-disks"></a>Připojení spravovaných disků
Jeden obvyklé začínat dvěma nebo více prázdných datových disků, při použití LVM. Podle svých potřeb vstupně-výstupní operace, můžete připojit disků, které jsou uloženy v našich úložiště úrovně Standard s až 500 vstupně-výstupních operací/ps pro jeden disk nebo naše služby Premium storage s až 5000 vstupně-výstupních operací/ps na disk. Tento článek nezkoumá podrobnosti o tom, jak zřídit a připojit datové disky virtuálního počítače s Linuxem. Přečtěte si článek Microsoft Azure [připojení disku](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podrobné pokyny o tom, jak připojit prázdný datový disk do virtuálního počítače s Linuxem v Azure.

## <a name="install-the-lvm-utilities"></a>Nainstalujte nástroje LVM
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS a Oracle Linux**

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

    Na SLES11, je nutné upravit také `/etc/sysconfig/lvm` a nastavte `LVM_ACTIVATED_ON_DISCOVERED` "Povolit":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Konfigurace LVM
V této příručce budeme předpokládat mít připojené tři datové disky, které budete označujeme jako `/dev/sdc`, `/dev/sdd` a `/dev/sde`. Tyto cesty nesmí shodovat s názvy cest disk ve virtuálním počítači. Můžete spustit "`sudo fdisk -l`' nebo podobné zobrazte seznam dostupných disků.

1. Příprava fyzických svazků:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Vytvořte skupinu svazku. V tomto příkladu voláme skupiny svazků `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Vytvoření logické svazky. Příkaz níže jsme se vytvoří jeden logický svazek volá `data-lv01` span skupině celý svazek, ale mějte na paměti, že je také možné vytvořit víc logických svazků ve skupině svazku.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Naformátujte logickou svazek

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > S využitím SLES11 `-t ext3` místo ext4. SLES11 podporuje pouze přístup jen pro čtení k ext4 systémy souborů.

## <a name="add-the-new-file-system-to-etcfstab"></a>Přidat nový systém souborů /etc/fstab
> [!IMPORTANT]
> Nesprávně úpravy `/etc/fstab` souboru by mohlo způsobit systém nelze spustit. Pokud nejste jisti, najdete v dokumentaci vaší distribuce pro informace o tom, jak správně upravit tento soubor. Také je doporučujeme zálohu `/etc/fstab` před úpravou se vytvoří soubor.

1. Vytvořte požadované přípojný bod pro nový systém souborů, například:

    ```bash  
    sudo mkdir /data
    ```

2. Vyhledejte cestu logické svazku

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Otevřít `/etc/fstab` v textovém editoru a přidejte záznam pro nový systém souborů, například:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Potom uložte a zavřete `/etc/fstab`.

4. Otestujte, jestli `/etc/fstab` správnost položka:

    ```bash    
    sudo mount -a
    ```

    Pokud tento příkaz vrátí chybovou zprávu, zkontrolujte syntaxi `/etc/fstab` souboru.
   
    Další spuštění `mount` příkaz, kterým zajistíte, připojení k systému souborů:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Volitelné) Spouštěcí parametry bezporuchový v `/etc/fstab`
   
    Množství distribucí zahrnout buď `nobootwait` nebo `nofail` připojit parametry, které mohou být přidány do `/etc/fstab` souboru. Tyto parametry umožňuje pro selhání v případě, že připojí systém určitý soubor a umožnit systému Linux, pokračujte ke spuštění i v případě, že není schopen správně připojení systému souborů RAID. V dokumentaci vaší distribuce pro další informace o těchto parametrech.
   
    Příklad (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Podpora uvolnění dočasné paměti/UNMAP
Některé Linuxových jádrech podporovat operace TRIM/UNMAP zahodíte nepoužívané bloky na disku. Tyto operace jsou především užitečné ve standardním úložišti informovat Azure, které odstraní stránek už nejsou platné a mohou být zahozeny. Zahazuje se stránky můžete snížení nákladů, pokud vytvoříte velkých souborů a potom je odstraňte.

Existují dva způsoby, jak povolit TRIM podpory v virtuálního počítače s Linuxem. Obvyklým způsobem vaše distribuce najdete doporučený postup:

- Použití `discard` připojit možnost v `/etc/fstab`, například:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- V některých případech `discard` možnost může mít vliv na výkon. Alternativně můžete spustit `fstrim` příkaz ručně z příkazového řádku, nebo ho přidat do vaší crontab pravidelně spuštění:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
