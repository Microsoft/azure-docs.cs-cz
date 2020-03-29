---
title: Konfigurace LVM na virtuálním počítači se systémem Linux
description: Přečtěte si, jak nakonfigurovat LVM na Linuxu v Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/27/2018
ms.author: guybo
ms.subservice: disks
ms.openlocfilehash: 7f560a1e6266b5f2452bf9442d2d4c983de1236e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066798"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Konfigurace LVM na virtuálním počítači s Linuxem v Azure
Tento dokument bude diskutovat o tom, jak nakonfigurovat Správce logických svazků (LVM) ve virtuálním počítači Azure. LVM lze použít na disku operačního systému nebo datové disky ve virtuálních počítačích Azure, ale ve výchozím nastavení většina icloud image nebude mít LVM nakonfigurované na disku operačního systému. Následující postup se zaměří na konfiguraci LVM pro datové disky.

## <a name="linear-vs-striped-logical-volumes"></a>Lineární vs. prokládané logické svazky
LVM lze kombinovat několik fyzických disků do jednoho svazku úložiště. Ve výchozím nastavení LVM obvykle vytváří lineární logické svazky, což znamená, že fyzické úložiště je spojeno dohromady. V tomto případě budou operace čtení a zápisu obvykle odesílány pouze na jeden disk. Naproti tomu můžeme také vytvořit prokládané logické svazky, kde jsou čtení a zápisy distribuovány na více disků obsažených ve skupině svazků (podobně jako RAID0). Z důvodů výkonu je pravděpodobné, že budete chtít prokládat logické svazky tak, aby čtení a zápisy využívaly všechny připojené datové disky.

Tento dokument popisuje, jak zkombinovat několik datových disků do jedné skupiny svazků a potom vytvořit prokládaný logický svazek. Následující kroky jsou zobecněny pro práci s většinou distribucí. Ve většině případů nástroje a pracovní postupy pro správu LVM v Azure se zásadně neliší od jiných prostředí. Jako obvykle, také konzultovat svého dodavatele Linuxu pro dokumentaci a osvědčené postupy pro použití LVM s konkrétní distribuce.

## <a name="attaching-data-disks"></a>Připojení datových disků
Jeden bude obvykle chtít začít s dvěma nebo více prázdných datových disků při použití LVM. Na základě vašich potřeb vstupně-bojích můžete připojit disky, které jsou uloženy v našem standardním úložišti, s až 500 IO/ps na disk nebo s úložištěm Premium až s 5000 IO/ps na disk. Tento článek nebude zacházet do podrobností o tom, jak zřídit a připojit datové disky k virtuálnímu počítači Linux. Podrobný návod, jak připojit prázdný datový disk k virtuálnímu počítači Linuxu v Azure, najdete v článku Microsoft [Azure, kde najdete](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podrobné pokyny, jak připojit prázdný datový disk k virtuálnímu počítači Linuxu.

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

    Na SLES11, musíte `/etc/sysconfig/lvm` také `LVM_ACTIVATED_ON_DISCOVERED` upravit a nastavit na "povolit":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Konfigurace LVM
V této příručce budeme předpokládat, že jste připojili tři datové `/dev/sdc` `/dev/sdd` disky, které budeme označovat jako , a `/dev/sde`. Tyto cesty se nemusí shodovat s názvy cest disku ve vašem virtuálním počítači. Můžete spustit`sudo fdisk -l`příkaz ' ' nebo podobný seznam dostupných disků.

1. Připravte fyzické objemy:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Vytvořte skupinu svazků. V tomto příkladu voláme `data-vg01`skupinu svazků :

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Vytvořte logický svazek. Níže uvedený příkaz vytvoříme jeden `data-lv01` logický svazek, který bude rozložen na celou skupinu svazků, ale všimněte si, že je také možné vytvořit více logických svazků ve skupině svazků.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formátování logického svazku

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > S SLES11 `-t ext3` použití namísto ext4. SLES11 podporuje pouze přístup jen pro čtení k souborovým systémům ext4.

## <a name="add-the-new-file-system-to-etcfstab"></a>Přidání nového systému souborů do /etc/fstab
> [!IMPORTANT]
> Pokud byste soubor `/etc/fstab` upravili nesprávně, může se stát, že systém nepůjde spustit. Pokud si nejste jistí, podívejte se do dokumentace k distribuci, kde najdete informace o tom, jak soubor správně upravit. Doporučuje se také vytvořit zálohu `/etc/fstab` souboru před úpravou.

1. Vytvořte požadovaný přípojný bod pro nový systém souborů, například:

    ```bash  
    sudo mkdir /data
    ```

2. Vyhledání cesty logického svazku

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
    Potom uložte `/etc/fstab`a zavřete .

4. Otestujte, `/etc/fstab` zda je položka správná:

    ```bash    
    sudo mount -a
    ```

    Pokud tento příkaz má za následek chybovou zprávu, zkontrolujte syntaxi v souboru. `/etc/fstab`
   
    Další spuštění `mount` příkazu, aby se zajistilo, že systém souborů je připojen:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Nepovinné) Failsafe zaváděcí parametry v`/etc/fstab`
   
    Mnoho distribucí `nobootwait` `nofail` obsahuje parametry nebo parametry `/etc/fstab` připojení, které mohou být přidány do souboru. Tyto parametry umožňují selhání při montáži konkrétního systému souborů a umožňují systému Linux pokračovat v zavádění, i když není schopen správně připojit souborový systém RAID. Další informace o těchto parametrech naleznete v dokumentaci k distribuci.
   
    Příklad (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Podpora TRIM/UNMAP
Některá linuxová jádra podporují operace TRIM/UNMAP k vyřazení nepoužívaných bloků na disku. Tyto operace jsou primárně užitečné ve standardním úložišti informovat Azure, že odstraněné stránky již nejsou platné a mohou být zahozeny. Vyhození stránek může ušetřit náklady, pokud vytvoříte velké soubory a pak je odstraníte.

Existují dva způsoby, jak povolit podporu TRIM ve vašem virtuálním počítači s Linuxem. Jako obvykle, poraďte se s vaší distribuce pro doporučený přístup:

- Použijte `discard` možnost připojení `/etc/fstab`v části , například:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- V některých `discard` případech může mít možnost vliv na výkon. Případně můžete `fstrim` příkaz spustit ručně z příkazového řádku nebo jej přidat do crontabu a pravidelně spouštět:

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
