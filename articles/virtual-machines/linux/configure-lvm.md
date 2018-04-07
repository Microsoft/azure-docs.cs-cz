---
title: Konfigurace LVM na virtuální počítač s Linuxem | Microsoft Docs
description: Naučte se konfigurovat LVM v systému Linux v Azure.
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
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 9a22426d0422585714cb78d541a84d55d2fce6e0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Konfigurace LVM na virtuální počítač s Linuxem v Azure
Tento dokument popisuje postup konfigurace logické svazku Manager (LVM) ve virtuálním počítači Azure. I když je to vhodné konfigurace LVM na všechny disky připojené k virtuálnímu počítači, ve výchozím nastavení většina cloudu Image nebude mít LVM nakonfigurované na disk operačního systému. Toto je zabránit problémům s duplicitní svazku skupiny, pokud disk operačního systému je někdy připojena k jiné virtuální počítač se stejným distribuce a typem, tj. během na scénář zotavení. Proto se doporučuje jenom pro použití LVM v datových disků.

## <a name="linear-vs-striped-logical-volumes"></a>Lineární oproti logické prokládané svazky
LVM umožňuje zkombinovat do jednoho svazku úložiště počet fyzických disků. Ve výchozím nastavení LVM obvykle vytvoří lineární logické svazcích, což znamená, že fyzického úložiště je zřetězen dohromady. V takovém případě operace čtení a zápisu obvykle pouze odešle na jeden disk. Naproti tomu můžeme také prokládané svazky lze vytvářet logické kde čtení a zápisu jsou distribuovány do více disků, které jsou obsaženy ve skupině svazek (tj. Podobně jako 0). Z důvodů výkonu, pravděpodobně budete chtít rozkládají logické svazků tak, aby čtení a zápisy využívat všechny disky připojené data.

Tento dokument popisuje, jak kombinovat několik datových disků do skupiny jednom svazku a vytvořit Prokládané logické svazek. Následující postup poněkud zobecněné pro práci s většina distribuce. Ve většině případů nástroje a pracovní postupy pro správu LVM v Azure nejsou zásadně liší od jiných prostředích. Obvyklým způsobem také najdete dodavatele Linux dokumentace a osvědčené postupy pro použití s konkrétní distribuční LVM.

## <a name="attaching-data-disks"></a>Připojení datových disků
Jeden obvykle chtít začít s minimálně dva prázdné datových disků, při použití LVM. Podle potřeb vstupně-výstupní operace, můžete připojit disky, které jsou uložené v našem standardního úložiště, s maximálně 500 vstupně-výstupní operace/ps pro na disk nebo naše storage úrovně Premium s až 5000 vstupně-výstupní operace za ps na disk. Tento článek nebude přejděte do podrobnosti o tom, jak zřídit a připojte datových disků pro virtuální počítač s Linuxem. Podrobnosti najdete v článku Microsoft Azure [připojit disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podrobné pokyny o tom, jak připojit prázdný datový disk pro virtuální počítač s Linuxem v Azure.

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

    Na SLES11 je nutné upravit také `/etc/sysconfig/lvm` a nastavte `LVM_ACTIVATED_ON_DISCOVERED` "povolení":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Konfigurace LVM
V tomto průvodci budeme předpokládat připojeny tři datových disků, které budeme označovat jako `/dev/sdc`, `/dev/sdd` a `/dev/sde`. Všimněte si, že to nemusí být vždy stejné názvy cest v virtuálního počítače. Můžete spustit '`sudo fdisk -l`' nebo seznam dostupných disků podobné příkazu.

1. Příprava fyzických svazků:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Vytvořte skupinu svazku. V tomto příkladu voláme skupině svazku `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Vytvoření logické svazky. Příkaz níže jsme dojde k vytvoření jedné logické svazek nazvaný `data-lv01` span skupině celý svazek, ale Všimněte si, že je také nelze vytvořit více logických svazky ve skupině svazku.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Naformátujte svazek logického

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > S použitím SLES11 `-t ext3` místo ext4. SLES11 pouze podporuje přístup jen pro čtení k ext4 systémy.

## <a name="add-the-new-file-system-to-etcfstab"></a>Přidat nový systém souborů do /etc/fstab
> [!IMPORTANT]
> Nesprávně úpravy `/etc/fstab` souboru může mít za následek nelze spustit systém. Pokud si jisti, naleznete distribuční dokumentaci informace o tom, jak správně upravit tento soubor. Je také doporučeno zálohu `/etc/fstab` soubor je vytvořen před úpravou.

1. Vytvořte požadované přípojného bodu pro nový systém souborů, například:

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

3. Otevřete `/etc/fstab` v textovém editoru a přidejte položku pro nový systém souborů, například:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Potom uložte a zavřete `/etc/fstab`.

4. Otestujte, že `/etc/fstab` správnost zadání:

    ```bash    
    sudo mount -a
    ```

    Pokud tento příkaz výsledkem chybovou zprávu ve prosím zkontrolujte syntaxi `/etc/fstab` souboru.
   
    Následně spusťte `mount` zajistěte připojení k systému souborů:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Volitelné) Spouštěcí parametry bezporuchový v `/etc/fstab`
   
    Velkém množství distribucí obsahovat buď `nobootwait` nebo `nofail` připojit parametry, které mohou být přidány do `/etc/fstab` souboru. Tyto parametry umožňují selhání při připojení příslušného systému souborů a povolit spuštění i v případě, že nelze správně připojit RAID systému souborů i nadále systému Linux. Naleznete v dokumentaci distribuční na další informace o těchto parametrů.
   
    Příklad (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Podpora uvolnění dočasné paměti nebo UNMAP
Některé Linux jádra podporovat operace TRIM/UNMAP vyřadí nepoužívané bloky na disku. Tyto operace jsou užitečné hlavně v standardní úložiště k informování Azure, které odstraněné stránky již nejsou platné a může být vymazány. Zahození stránky můžete uložit náklady, pokud chcete vytvořit velkých souborů a pak odstraňte je.

Existují dva způsoby, jak povolit TRIM podporují ve virtuálním počítačům s Linuxem. Obvyklým způsobem podívejte se distribuční o doporučený postup:

- Použití `discard` připojit možnost v `/etc/fstab`, například:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- V některých případech `discard` možnost může mít vliv na výkon. Alternativně můžete spustit `fstrim` ručně příkaz z příkazového řádku, nebo ho přidat do vaší crontab pravidelně spouštět:

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
