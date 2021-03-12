---
title: Odpojení datového disku od virtuálního počítače se systémem Linux – Azure
description: Naučte se odpojit datový disk od virtuálního počítače v Azure pomocí rozhraní příkazového řádku Azure nebo Azure Portal.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli
ms.openlocfilehash: 29a2cbbf2c390b81aa62b064a7cf93decbaa7457
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565984"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Jak odpojit datový disk od virtuálního počítače s Linuxem

Když už nepotřebujete datový disk připojený k virtuálnímu počítači, můžete ho jednoduše odpojit. Tím se disk z virtuálního počítače odebere, ale neodebere se z úložiště. V tomto článku pracujeme s distribucí Ubuntu LTS 16,04. Pokud používáte jinou distribuci, pokyny pro odpojení disku můžou být odlišné.

> [!WARNING]
> Pokud disk odpojíte, nedojde k jeho automatickému odstranění. Pokud jste se přihlásili k odběru služby Premium Storage, bude se vám nadále účtovat poplatky za úložiště pro disk. Další informace najdete v tématu [ceny a fakturace při použití Premium Storage](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Pokud znovu chcete použít stávající data na disku, můžete ho znovu připojit ke stejnému nebo jinému virtuálnímu počítači.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Připojte se k virtuálnímu počítači a odpojte disk.

Než budete moci odpojit disk pomocí rozhraní příkazového řádku nebo portálu, je třeba odpojit disk a odebrat odkazy na, pokud se nacházíte v souboru fstab.

Připojte se k virtuálnímu počítači. V tomto příkladu je veřejná IP adresa virtuálního počítače *10.0.1.4* s uživatelským jménem *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Nejprve najděte datový disk, který chcete odpojit. Následující příklad používá dmesg k filtrování na discích SCSI:

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

Zde je *SDC* disk, který chceme odpojit. Měli byste také vzít identifikátor UUID disku.

```bash
sudo -i blkid
```

Výstup bude vypadat podobně jako v následujícím příkladu:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Úpravou souboru */etc/fstab* odeberte odkazy na disk. 

> [!NOTE]
> Nesprávná úprava souboru **/etc/fstab** by mohla vést k nespouštěcímu systému. Pokud si nejste jistí, podívejte se do dokumentace k distribuci, kde najdete informace o tom, jak soubor správně upravit. Doporučuje se také vytvořit zálohu souboru/etc/fstab před úpravou.

V textovém editoru otevřete soubor */etc/fstab* následujícím způsobem:

```bash
sudo vi /etc/fstab
```

V tomto příkladu je nutné odstranit následující řádek ze souboru */etc/fstab* :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

`umount`K odpojení disku použijte. Následující příklad odpojí oddíl */dev/sdc1* z přípojného bodu */datadrive* :

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Odpojení datového disku pomocí Azure CLI 

Tento příklad odpojí disk *myDataDisk* z virtuálního počítače s názvem *myVM* v *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Disk zůstává v úložišti, ale už není připojený k virtuálnímu počítači.


## <a name="detach-a-data-disk-using-the-portal"></a>Odpojení datového disku pomocí portálu

1. V nabídce vlevo vyberte **Virtual Machines**.
1. V okně virtuálního počítače vyberte **disky**.
1. V okně **disky** v pravém dolním rohu datového disku, který chcete odpojit, vyberte tlačítko **X** a odpojte disk.
1. Po odebrání disku vyberte **Uložit** v horní části okna.

Disk zůstává v úložišti, ale už není připojený k virtuálnímu počítači. Disk není odstraněný.

## <a name="next-steps"></a>Další kroky
Pokud chcete znovu použít datový disk, můžete [ho jednoduše připojit k jinému virtuálnímu počítači](add-disk.md).

Pokud chcete disk odstranit, takže už nebudete mít náklady na úložiště, přečtěte si téma [vyhledání a odstranění nepřipojeného spravovaného a nespravovaného disku Azure – Azure Portal](../disks-find-unattached-portal.md).