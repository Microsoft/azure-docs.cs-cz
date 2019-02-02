---
title: Přidání datového disku virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak přidat trvalých datových disků virtuálního počítače s Linuxem pomocí Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/13/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.subservice: disks
ms.openlocfilehash: fcd8f4f8408c7c51265802fde057146e6cdbb090
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657616"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Přidání disku do virtuálního počítače s Linuxem
V tomto článku se dozvíte, jak připojit trvalý disk k virtuálnímu počítači tak, aby můžete zachovat vaše data – i v případě, že váš virtuální počítač se znovu poskytne z důvodu údržby nebo změnou velikosti. 


## <a name="attach-a-new-disk-to-a-vm"></a>Připojit nový disk k virtuálnímu počítači

Pokud chcete přidat nový, prázdný datový disk na virtuálním počítači, použijte [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) příkazů `--new` parametru. Pokud je váš virtuální počítač v zóně dostupnosti, disk se automaticky vytvoří ve stejné zóně jako virtuální počítač. Další informace najdete v tématu [přehledu zón dostupnosti](../../availability-zones/az-overview.md). Následující příklad vytvoří disk s názvem *myDataDisk* , který je velikost 50 Gb:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --disk myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Připojení stávajícího disku 

Připojit stávající disk, najít ID disku a předat ID se má [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) příkazu. Následující příklady dotazů pro disk s názvem *myDataDisk* v *myResourceGroup*, připojí ho k virtuálnímu počítači s názvem *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```


## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Připojte se k počítači s Linuxem připojit nový disk
Do oddílů, formátování a připojte nový disk, virtuální počítač s Linuxem můžete použít SSH k virtuálnímu počítači. Další informace najdete v tématu [Jak použít SSH s Linuxem v Azure](mac-create-ssh-keys.md). Následující příklad se připojí k virtuálnímu počítači s veřejný záznam DNS *mypublicdns.westus.cloudapp.azure.com* k uživatelskému jménu *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Po připojení k vašemu virtuálnímu počítači, jste připraveni připojit disk. Nejdříve vyhledejte disk pomocí `dmesg` (metoda použijete ke zjištění nového disku může lišit). Následující příklad používá k filtrování dmesg *SCSI* disky:

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

Tady *sdc* je disk, který chceme. Rozdělit disk s `fdisk`, nastavte ji primárním disku v oddílu 1 a přijměte ostatní výchozí hodnoty. Následující příklad spustí `fdisk` zpracovat na */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Použití `n` příkaz pro přidání nového oddílu. V tomto příkladu jsme také zvolit `p` pro primární oddíl a přijměte ostatní výchozí hodnoty. Výstup bude podobný následujícím příkladu:

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

Tisk v tabulce oddílu tak, že zadáte `p` a pak použijte `w` zápis tabulky na disku a ukončíte. Výstup by měl vypadat podobně jako v následujícím příkladu:

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

Nyní, zapište systém souborů na oddíl s `mkfs` příkazu. Určuje typ systému souborů a název zařízení. Následující příklad vytvoří *ext4* systému souborů na */dev/sdc1* oddíl, který byl vytvořen v předchozích krocích:

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

Teď vytvořte adresář pro připojení systému souborů pomocí `mkdir`. Následující příklad vytvoří adresář na */datadrive*:

```bash
sudo mkdir /datadrive
```

Použití `mount` pak připojte systém souborů. Následující příklad připojí */dev/sdc1* oddílu na */datadrive* přípojný bod:

```bash
sudo mount /dev/sdc1 /datadrive
```

Aby bylo zajištěno, že jednotka znovu připojí automaticky po restartování, je nutné přidat do */etc/fstab* souboru. Je také důrazně doporučujeme, aby UUID (univerzálně jedinečný identifikátor) se používá v */etc/fstab* k odkazování na jednotce, ne pouze název zařízení (jako jsou například */dev/sdc1*). Pokud operační systém zjistí chyba disku během spouštění, pomocí identifikátoru UUID předchází nesprávné disku, připojení, na dané místo. Zbývající datových disků by se mu přiřadit stejné ID zařízení. Chcete-li najít identifikátor UUID novou jednotku, použijte `blkid` nástroje:

```bash
sudo blkid
```

Výstup vypadá podobně jako v následujícím příkladu:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Nesprávně úpravy **/etc/fstab** souboru by mohlo způsobit systém nelze spustit. Pokud nejste jisti, najdete v dokumentaci vaší distribuce pro informace o tom, jak správně upravit tento soubor. Doporučujeme také, že je záloha souboru /etc/fstab vytvořená před úpravou.

Dále otevřete */etc/fstab* souboru v textovém editoru následovně:

```bash
sudo vi /etc/fstab
```

V tomto příkladu použijte hodnotu identifikátoru UUID */dev/sdc1* zařízení, který byl vytvořen v předchozích krocích a přípojný bod z */datadrive*. Přidejte následující řádek na konec objektu */etc/fstab* souboru:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Později odebrání datového disku bez úprav fstab může způsobit selhání spuštění virtuálního počítače. Většině distribucí zadejte buď *nofail* a/nebo *nobootwait* možnosti fstab. Tyto možnosti umožňují spustit i v případě, že disk se nepodařilo připojit při spuštění systému. Další informace o těchto parametrech naleznete v dokumentaci vaší distribuce.
> 
> *Nofail* možnost zajišťuje, že virtuální počítač spustí i v případě systému souborů je poškozený nebo na disku v době spuštění neexistuje. Bez této možnosti může dojít chování, jak je popsáno v [nelze SSH k virtuálnímu počítači s Linuxem kvůli chybám FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>Podpora uvolnění dočasné paměti/UNMAP pro Linux v Azure
Některé Linuxových jádrech podporovat operace TRIM/UNMAP zahodíte nepoužívané bloky na disku. Tato funkce je užitečné hlavně ve standardním úložišti informovat Azure, které odstraní stránky již nejsou platným můžete zahodit a můžete ušetřit, pokud vytvoříte velkých souborů a potom je odstraňte.

Existují dva způsoby, jak povolit TRIM podpory v virtuálního počítače s Linuxem. Obvyklým způsobem vaše distribuce najdete doporučený postup:

* Použití `discard` připojit možnost v */etc/fstab*, například:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* V některých případech `discard` možnost může mít vliv na výkon. Alternativně můžete spustit `fstrim` příkaz ručně z příkazového řádku, nebo ho přidat do vaší crontab pravidelně spuštění:
  
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
* Chcete-li zajistit správnou konfiguraci virtuálního počítače s Linuxem, zkontrolovat [optimalizaci výkonu počítačů systému Linux](optimization.md) doporučení.
* Rozšířit tak úložnou kapacitu přidáním dalších disků a [konfigurace RAID](configure-raid.md) další výkonu.

