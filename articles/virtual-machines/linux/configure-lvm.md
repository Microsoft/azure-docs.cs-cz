---
title: Konfigurace LVM na virtuálním počítači se systémem Linux
description: Přečtěte si, jak nakonfigurovat LVM pro Linux v Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 09/27/2018
ms.author: guybo
ms.subservice: disks
ms.openlocfilehash: 9a3498939ddf57e2520a140ff693a30de913fae0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84658295"
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

    V SLES11 musíte také upravit `/etc/sysconfig/lvm` a nastavit `LVM_ACTIVATED_ON_DISCOVERED` na Povolit:

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Konfigurace LVM
V tomto průvodci předpokládáme, že máte připojené tři datové disky, které budeme označovat jako `/dev/sdc` `/dev/sdd` a `/dev/sde` . Tyto cesty nemusí odpovídat názvům cest k disku ve vašem VIRTUÁLNÍm počítači. Můžete spustit `sudo fdisk -l` nebo podobný příkaz pro výpis dostupných disků.

1. Příprava fyzických svazků:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Vytvořte skupinu svazků. V tomto příkladu voláme skupinu svazků `data-vg01` :

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Vytvořte logické svazky. Následující příkaz vytvoří jeden logický svazek `data-lv01` s názvem pro celou skupinu svazků, ale Všimněte si, že je také možné vytvořit více logických svazků ve skupině svazků.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Naformátovat logický svazek

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Pomocí SLES11 použijte `-t ext3` místo ext4. SLES11 podporuje přístup jen pro čtení k systémům souborů ext4.

## <a name="add-the-new-file-system-to-etcfstab"></a>Přidat nový systém souborů do/etc/fstab
> [!IMPORTANT]
> Pokud byste soubor `/etc/fstab` upravili nesprávně, může se stát, že systém nepůjde spustit. Pokud si nejste jistí, podívejte se do dokumentace k distribuci, kde najdete informace o tom, jak soubor správně upravit. Doporučuje se také `/etc/fstab` vytvořit zálohu souboru před úpravou.

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

3. Otevřete `/etc/fstab` v textovém editoru a přidejte položku pro nový systém souborů, například:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Pak ho uložte a zavřete `/etc/fstab` .

4. Otestujte `/etc/fstab` správnost položky:

    ```bash    
    sudo mount -a
    ```

    Pokud tento příkaz způsobí chybovou zprávu, Projděte si syntaxi v `/etc/fstab` souboru.
   
    V dalším kroku spusťte `mount` příkaz, abyste zajistili, že je systém souborů připojený:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. Volitelné Failsafe parametry spuštění v `/etc/fstab`
   
    Mnoho distribucí zahrnuje buď `nobootwait` `nofail` parametry připojení nebo parametry, které mohou být přidány do `/etc/fstab` souboru. Tyto parametry umožňují selhání při připojování konkrétního systému souborů a umožňuje, aby se systém Linux spouštěl i v případě, že není schopen správně připojit systém souborů RAID. Další informace o těchto parametrech najdete v dokumentaci k distribuci.
   
    Příklad (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Podpora OŘEZÁVÁNÍ a odmapování
Některé jádro systému Linux podporují operace OŘEZÁVÁNÍ a odmapování, aby bylo možné zahodit nepoužívané bloky na disku. Tyto operace jsou primárně užitečné ve službě Storage úrovně Standard pro informování Azure o tom, že odstraněné stránky už nejsou platné a můžou být zahozeny. Vypuštění stránek může ušetřit náklady, pokud vytvoříte velké soubory a pak je odstraníte.

Existují dva způsoby, jak na svém VIRTUÁLNÍm počítači se systémem Linux povolit podporu OŘEZÁVÁNÍ. V obvyklých případech si prostudujte doporučený postup:

- Použijte `discard` možnost připojení v `/etc/fstab` , například:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- V některých případech `discard` může mít možnost vliv na výkon. Alternativně můžete `fstrim` příkaz spustit ručně z příkazového řádku nebo ho přidat do crontab, aby se pravidelně spouštěl:

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
