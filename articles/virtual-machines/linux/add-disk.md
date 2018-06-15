---
title: Přidejte disk do virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Zjistěte, jak přidat trvalé disk k virtuálním počítačům s Linuxem pomocí Azure CLI 1.0 a 2.0.
keywords: virtuální počítač Linux, přidejte disk prostředků
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 3005a066-7a84-4dc5-bdaa-574c75e6e411
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 02/02/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c3d3e3468b491f366473899f5d073704ea9a95ea
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30837002"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Přidání disku do virtuálního počítače s Linuxem
Tento článek ukazuje, jak připojit trvalé disk k virtuálnímu počítači tak, aby můžete zachovat data - i v případě, že virtuální počítač je znovu poskytnuto z důvodu údržby nebo změnou velikosti. 


## <a name="use-managed-disks"></a>Používat spravované disky
Služba Azure Managed Disks zjednodušuje správu pro virtuální počítače Azure, neboť spravuje účty úložiště přidružené k diskům virtuálních počítačů. Stačí jenom zadat typ (Premium nebo Standard) a velikosti disku, který potřebujete, a Azure ho vytvoří a bude spravovat za vás. Další informace najdete v tématu [spravované disky přehled](managed-disks-overview.md).


### <a name="attach-a-new-disk-to-a-vm"></a>Připojit nový disk k virtuálnímu počítači
Pokud potřebujete pouze nový disk na vašem virtuálním počítači, použijte [připojit disk virtuálního počítače az](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) s `--new` parametr. Pokud virtuální počítač je v zóně dostupnosti, disk je automaticky vytvořen ve stejné zóny jako virtuální počítač. Další informace najdete v tématu [přehled dostupnosti zón](../../availability-zones/az-overview.md). Následující příklad vytvoří disk s názvem *myDataDisk* tedy *50*Gb velikost:

```azurecli
az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk \
  --new --size-gb 50
```

### <a name="attach-an-existing-disk"></a>Připojení stávajícího disku 
V mnoha případech. připojíte disky, které již byly vytvořeny. Připojit stávající disk, vyhledejte ID disku a předejte ID, který má [připojit disk virtuálního počítače az](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) příkaz. Následující příklad dotazy pro disk s názvem *myDataDisk* v *myResourceGroup*, připojí jej k virtuálnímu počítači s názvem *Můjvp*:

```azurecli
# find the disk id
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)
az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```

Výstup bude vypadat nějak takto (můžete použít `-o table` možnost k libovolnému příkazu k formátování výstupu v):

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Empty",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 50,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/rasquill-script/providers/Microsoft.Compute/disks/myDataDisk",
  "location": "westus",
  "name": "myDataDisk",
  "osType": null,
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-02T23:35:47.708082+00:00",
  "type": "Microsoft.Compute/disks"
}
```


## <a name="use-unmanaged-disks"></a>Použití nespravovaného disky
Nespravované disky se vyžaduje další režii k vytváření a správě základní účty úložiště. Nespravované disky jsou vytvořeny ve stejném účtu úložiště jako disk s operačním systémem. Chcete-li vytvořit a připojit nespravované disk, použijte [nespravované virtuální počítač az-disk připojit](/cli/azure/vm/unmanaged-disk?view=azure-cli-latest#az_vm_unmanaged_disk_attach) příkaz. Následující příklad se připojí *50*GB místa na disku nespravované na virtuální počítač s názvem *Můjvp* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az vm unmanaged-disk attach -g myResourceGroup -n myUnmanagedDisk --vm-name myVM \
  --new --size-gb 50
```


## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Připojit k virtuálnímu počítači Linux připojit nový disk
K oddílu, formátování a připojte nový disk, virtuálním počítačům s Linuxem můžete použít ho SSH do svého virtuálního počítače Azure. Další informace najdete v tématu [Jak použít SSH s Linuxem v Azure](mac-create-ssh-keys.md). V následujícím příkladu se připojuje k virtuálnímu počítači pomocí veřejné položka DNS *mypublicdns.westus.cloudapp.azure.com* k uživatelskému jménu *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Po připojení k virtuálnímu počítači, jste připravení připojit disk. Nejdříve vyhledejte disk pomocí `dmesg` (metodu můžete použít ke zjištění nový disk se liší). Následující příklad používá k filtrování dmesg *SCSI* disky:

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

Zde *sdc* je na disk, který má být. Rozdělit disk s `fdisk`proveďte primární disku v oddílu 1 a přijměte ostatní výchozí hodnoty. Následující příklad spustí `fdisk` zpracování na */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Výstup se podobá následujícímu příkladu:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Vytvořte oddíl zadáním `p` řádku následujícím způsobem:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Nyní, zápis systém souborů k oddílu s `mkfs` příkaz. Zadejte typ vašeho systému souborů a název zařízení. Následující příklad vytvoří *ext4* systému souborů na */dev/sdc1* oddílu, který byl vytvořen v předchozích krocích:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Výstup se podobá následujícímu příkladu:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Teď vytvořte adresář, který chcete připojit, systém souborů pomocí `mkdir`. Následující příklad vytvoří adresář na */datadrive*:

```bash
sudo mkdir /datadrive
```

Použití `mount` pak připojit systém souborů. Následující příklad se připojí */dev/sdc1* oddílu na */datadrive* přípojného bodu:

```bash
sudo mount /dev/sdc1 /datadrive
```

Aby se zajistilo, že jednotka je znovu připojeny automaticky po restartování systému, je nutné přidat do */etc/fstab* souboru. Je také důrazně doporučujeme, aby identifikátor UUID (univerzálně jedinečný identifikátor) se používá v */etc/fstab* k odkazování na jednotku, nikoli jen název zařízení (jako je třeba */dev/sdc1*). Pokud operační systém zjistí chyba disku během spouštění, pomocí identifikátoru UUID zabraňuje nesprávnou disku montáže do daného umístění. Zbývající datových disků by pak přiřadit tyto stejné ID zařízení. Chcete-li najít identifikátor UUID nový disk, použijte `blkid` nástroj:

```bash
sudo -i blkid
```

Výstup bude vypadat podobně jako v následujícím příkladu:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Nesprávně úpravy **/etc/fstab** soubor může mít za následek nelze spustit systém. Pokud jistí, naleznete distribuční dokumentaci informace o tom, jak správně upravit tento soubor. Dále je doporučeno, jestli je vytvořená záloha souboru /etc/fstab před úpravou.

Dále otevřete */etc/fstab* soubor v textovém editoru následujícím způsobem:

```bash
sudo vi /etc/fstab
```

V tomto příkladu používáme hodnota identifikátoru UUID */dev/sdc1* zařízení, který byl vytvořen v předchozích krocích a přípojný bod systému */datadrive*. Přidejte následující řádek na konec */etc/fstab* souboru:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Později odstranění datový disk bez úprav fstab může způsobit selhání spuštění virtuálního počítače. Většina distribuce zadejte buď *nofail* nebo *nobootwait* fstab možnosti. Tyto možnosti umožňují spustit i v případě, že na disku se nepodaří připojit při spuštění systému. Další informace o těchto parametrů naleznete v dokumentaci vaší distribuce.
> 
> *Nofail* možnost zajistí, že virtuální počítač spustí i v případě, že systém souborů je poškozený nebo disk neexistuje při spuštění. Bez této možnosti se můžete setkat chování jak je popsáno v [nelze SSH pro virtuální počítač s Linuxem z důvodu chyb FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>Podpora uvolnění dočasné paměti nebo UNMAP pro Linux v Azure
Některé Linux jádra podporovat operace TRIM/UNMAP vyřadí nepoužívané bloky na disku. Tato funkce je užitečné hlavně v standardní úložiště k informování Azure, které odstraněné stránky již nejsou platné a může být vymazány a můžete ušetřit peníze, pokud chcete vytvořit velkých souborů a pak odstraňte je.

Existují dva způsoby, jak povolit TRIM podporují ve virtuálním počítačům s Linuxem. Obvyklým způsobem podívejte se distribuční o doporučený postup:

* Použití `discard` připojit možnost v */etc/fstab*, například:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* V některých případech `discard` možnost může mít vliv na výkon. Alternativně můžete spustit `fstrim` ručně příkaz z příkazového řádku, nebo ho přidat do vaší crontab pravidelně spouštět:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Řešení potíží
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Další postup
* Mějte na paměti, že nový disk není k dispozici pro virtuální počítač, pokud dojde k restartování Pokud napíšete tyto informace k vaší [fstab](http://en.wikipedia.org/wiki/Fstab) souboru.
* Aby systém Linux virtuálního počítače je správně nakonfigurovaná, zkontrolujte [optimalizovat výkon počítače Linux](optimization.md) doporučení.
* Rozbalte vaše kapacita úložiště přidáním dalších disků a [konfigurace RAID](configure-raid.md) další výkonu.

