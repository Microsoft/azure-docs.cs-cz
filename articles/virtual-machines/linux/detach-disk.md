---
title: Odpojení datového disku z virtuálního počítače s Linuxem – Azure | Dokumentace Microsoftu
description: Zjistěte, jak odpojit datový disk od virtuálního počítače v Azure pomocí webu Azure portal nebo rozhraní příkazového řádku Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 07/18/2018
ms.author: cynthn
ms.openlocfilehash: b087040e5d219941ec0b2ee0f58dfe0058a1ad04
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968339"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Jak odpojit datový disk od virtuálního počítače s Linuxem

Když už nepotřebujete datový disk připojený k virtuálnímu počítači, můžete ho jednoduše odpojit. Odebrání disku z virtuálního počítače, ale neodebere se z úložiště. V tomto článku Pracujeme s distribucí Ubuntu LTS 16.04. Pokud používáte jiný distribuční, pokyny k odpojení disku může lišit.

> [!WARNING]
> Pokud odpojíte disk není automaticky odstraněn. Pokud odběru jste přihlášeni do Premium storage, budou dál účtovat poplatky za úložiště pro disk. Další informace najdete v tématu [ceny a fakturace při použití služby Premium Storage](../windows/premium-storage.md#pricing-and-billing). 
> 
> 

Pokud znovu chcete použít stávající data na disku, můžete ho znovu připojit ke stejnému nebo jinému virtuálnímu počítači.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Připojení k virtuálnímu počítači odpojit disk

Než budete moci odpojit disk pomocí portálu nebo rozhraní příkazového řádku, je třeba se odpojit disk a odebrat odkazy na-li ze svého souboru fstab.

Připojte se k virtuálnímu počítači. V tomto příkladu se veřejná IP adresa virtuálního počítače je *10.0.1.4* k uživatelskému jménu *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Nejdříve vyhledejte datový disk, který se má odpojit. Následující příklad používá k filtrování disky rozhraní SCSI dmesg:

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

Tady *sdc* je disk, který chcete odpojit. Také by měl získejte identifikátor UUID disku.

```bash
sudo -i blkid
```

Výstup vypadá podobně jako v následujícím příkladu:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Upravit */etc/fstab* souboru odebrat odkazy na disk. 

> [!NOTE]
> Nesprávně úpravy **/etc/fstab** souboru by mohlo způsobit systém nelze spustit. Pokud nejste jisti, najdete v dokumentaci vaší distribuce pro informace o tom, jak správně upravit tento soubor. Doporučujeme také, že je záloha souboru /etc/fstab vytvořená před úpravou.

Otevřít */etc/fstab* souboru v textovém editoru následovně:

```bash
sudo vi /etc/fstab
```

V tomto příkladu je třeba odstranit z následující řádek */etc/fstab* souboru:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Použití `umount` odpojit disk. Následující příklad odpojí */dev/sdc1* z oddílu */datadrive* přípojný bod:

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Odpojení datového disku pomocí rozhraní příkazového řádku Azure 

Tento příklad odpojí *myDataDisk* disk od virtuálního počítače s názvem *myVM* v *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Disk zůstává v úložišti, ale už není připojen k virtuálnímu počítači.


## <a name="detach-a-data-disk-using-the-portal"></a>Odpojení datového disku pomocí portálu

1. V nabídce vlevo vyberte **virtuálních počítačů**.
2. Vyberte virtuální počítač, který má datový disk, který chcete odpojit a klikněte na tlačítko **Zastavit** k uvolnění virtuálního počítače.
3. V podokně virtuální počítač vyberte **disky**.
4. V horní části **disky** vyberte **upravit**.
5. V **disky** podokno na pravé straně datový disk, který chcete odpojit, klikněte na tlačítko ![obrázek tlačítka odpojení](./media/detach-disk/detach.png) odpojit tlačítko.
5. Po odebrání disku horní části podokna klikněte na Uložit.
6. V podokně virtuální počítač, klikněte na tlačítko **přehled** a potom klikněte na tlačítko **Start** tlačítko v horní části podokna restartování virtuálního počítače.

Disk zůstává v úložišti, ale už není připojen k virtuálnímu počítači.



## <a name="next-steps"></a>Další postup
Pokud chcete znovu použít datový disk, jste právě [připojit k jinému virtuálnímu počítači](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

