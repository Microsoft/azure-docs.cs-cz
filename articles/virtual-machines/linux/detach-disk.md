---
title: Odpojení datového disku od virtuálního počítače s Linuxem – Azure
description: Naučte se odpojit datový disk od virtuálního počítače v Azure pomocí Azure CLI nebo portálu Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f8a0790169b17ad7755386f9bdd4f9372efc83e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74036367"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Jak odpojit datový disk od virtuálního počítače s Linuxem

Když už nepotřebujete datový disk připojený k virtuálnímu počítači, můžete ho jednoduše odpojit. Tím odeberete disk z virtuálního počítače, ale neodeberete ho z úložiště. V tomto článku pracujeme s distribucí Ubuntu LTS 16.04. Pokud používáte jinou distribuci, pokyny pro odpojení disku se mohou lišit.

> [!WARNING]
> Pokud disk odpojíte, nebude automaticky odstraněn. Pokud jste se přihlásili k odběru úložiště Premium, budou vám na dále účtovány poplatky za úložiště na disku. Další informace najdete [v tématu Ceny a fakturace při použití úložiště Premium](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Pokud znovu chcete použít stávající data na disku, můžete ho znovu připojit ke stejnému nebo jinému virtuálnímu počítači.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Připojení k virtuálnímu počítače pro odpojení disku

Než budete moci disk odpojit pomocí cli nebo portálu, musíte disk odpojit a odebrat odkazy na if ze souboru fstab.

Připojte se k virtuálnímu počítači. V tomto příkladu je veřejná IP adresa virtuálního počítače *10.0.1.4* s uživatelským jménem *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Nejprve vyhledejte datový disk, který chcete odpojit. Následující příklad používá dmesg k filtrování na discích SCSI:

```bash
dmesg | grep SCSI
```

Výstup se podobá následujícímu příkladu:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

Zde *sdc* je disk, který chceme odpojit. Také byste měli uchopit UUID disku.

```bash
sudo -i blkid
```

Výstup vypadá podobně jako v následujícím příkladu:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Upravte soubor */etc/fstab* a odeberte odkazy na disk. 

> [!NOTE]
> Nesprávná úprava souboru **/etc/fstab** může vést k nespustitelnému systému. Pokud si nejste jistí, podívejte se do dokumentace k distribuci, kde najdete informace o tom, jak soubor správně upravit. Doporučuje se také, aby záloha souboru /etc/fstab byla vytvořena před úpravou.

Otevřete soubor */etc/fstab* v textovém editoru následujícím způsobem:

```bash
sudo vi /etc/fstab
```

V tomto příkladu je třeba odstranit následující řádek ze souboru */etc/fstab:*

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Slouží `umount` k odpojení disku. Následující příklad odpojí oddíl */dev/sdc1* od přípojného bodu */datadrive:*

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Odpojení datového disku pomocí příkazového příkazového příkazu Azure 

Tento příklad odpojí disk *myDataDisk* od virtuálního počítače s názvem *myVM* v *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Disk zůstane v úložišti, ale už není připojený k virtuálnímu počítači.


## <a name="detach-a-data-disk-using-the-portal"></a>Odpojení datového disku pomocí portálu

1. V levé nabídce vyberte **Virtuální počítače**.
2. Vyberte virtuální počítač s datovým diskem, který chcete odpojit, a kliknutím na **Zastavit** virtuální počítač navrátit.
3. V podokně virtuálních počítačů vyberte **Disky**.
4. V horní části podokna **Disky** vyberte **Upravit**.
5. V podokně **Disky** zcela vpravo od datového disku, který chcete ![odpojit,](./media/detach-disk/detach.png) klepněte na tlačítko odpojit obrázek tlačítka Odpojit.
5. Po odebrání disku klikněte v horní části podokna na Uložit.
6. V podokně virtuálního počítače klikněte na **Přehled** a potom kliknutím na tlačítko **Start** v horní části podokna restartujte virtuální počítač.

Disk zůstane v úložišti, ale už není připojený k virtuálnímu počítači.



## <a name="next-steps"></a>Další kroky
Pokud chcete datový disk znovu použít, stačí [ho připojit k jinému virtuálnímu virtuálnímu počítače](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

