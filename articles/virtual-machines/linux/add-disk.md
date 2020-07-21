---
title: Přidání datového disku do virtuálního počítače se systémem Linux pomocí Azure CLI
description: Naučte se přidat trvalý datový disk k VIRTUÁLNÍmu počítači se systémem Linux pomocí Azure CLI.
author: roygara
manager: twooley
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 06/13/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1791d33627f04f69d10916c8ff0a154f7d8b967b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502822"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Přidání disku do virtuálního počítače s Linuxem
V tomto článku se dozvíte, jak ke svému VIRTUÁLNÍmu počítači připojit trvalý disk, abyste mohli zachovat vaše data – i když se váš virtuální počítač znovu zřídí z důvodu údržby nebo změny velikosti.


## <a name="attach-a-new-disk-to-a-vm"></a>Připojení nového disku k virtuálnímu počítači

Pokud na svém VIRTUÁLNÍm počítači chcete přidat nový prázdný datový disk, použijte příkaz [AZ VM disk Attach](/cli/azure/vm/disk?view=azure-cli-latest) s `--new` parametrem. Pokud je váš virtuální počítač v zóně dostupnosti, disk se automaticky vytvoří ve stejné zóně jako virtuální počítač. Další informace najdete v tématu [přehled zóny dostupnosti](../../availability-zones/az-overview.md). Následující příklad vytvoří disk s názvem *myDataDisk* , který má velikost 50 GB:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Připojení stávajícího disku

Pokud chcete připojit stávající disk, najděte ID disku a předejte ho příkazu [AZ VM disk Attach](/cli/azure/vm/disk?view=azure-cli-latest) . Následující příklady dotazů na disk s názvem *myDataDisk* v *myResourceGroup*ho připojí k virtuálnímu počítači s názvem *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Připojte se k virtuálnímu počítači se systémem Linux a připojte nový disk.

Pokud chcete svůj nový disk rozdělit do oddílů, zformátovat ho a připojit ho, aby ho váš virtuální počítač Linux mohl použít, připojte se k VIRTUÁLNÍmu počítači přes SSH. Další informace najdete v tématu [Jak použít SSH s Linuxem v Azure](mac-create-ssh-keys.md). Následující příklad se připojí k virtuálnímu počítači s použitím veřejné položky DNS *mypublicdns.westus.cloudapp.Azure.com* s uživatelským jménem *azureuser*:

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Po připojení k VIRTUÁLNÍmu počítači jste připraveni k připojení disku. Nejdřív Najděte disk s použitím `dmesg` (metoda, kterou použijete ke zjišťování nového disku), se může lišit. Následující příklad používá dmesg k filtrování na discích *SCSI* :

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

> [!NOTE]
> Doporučuje se používat nejnovější verze nástroje Fdisk nebo částečně, které jsou k dispozici pro vaši distribuce.

Tady je *SDC* disk, který chceme. Na disku vytvořte oddíly `parted` , pokud je velikost disku 2 tebibytes (TIB) nebo větší, musíte použít dělení GPT, pokud je v oblasti 2TiB, pak můžete použít dělení na oddíly MBR nebo GPT. Pokud používáte vytváření oddílů MBR, můžete použít `fdisk` . Nastavte primární disk na oddíl 1 a přijměte ostatní výchozí hodnoty. Následující příklad spustí `fdisk` proces na */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

K přidání nového oddílu použijte příkaz `n`. V tomto příkladu jsme také zvolili `p` pro primární oddíl a přijali zbytek výchozích hodnot. Výstup se bude podobat tomuto příkladu:

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

Vytiskněte tabulku oddílů zadáním `p` a pak použijte `w` k zápisu tabulky na disk a konec. Výstup by měl vypadat zhruba jako v tomto příkladu:

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
K aktualizaci jádra použijte následující příkaz:
```
partprobe 
```

Nyní do oddílu zapište systém souborů pomocí `mkfs` příkazu. Zadejte typ systému souborů a název zařízení. Následující příklad vytvoří systém souborů *ext4* na oddílu */dev/sdc1* , který byl vytvořen v předchozích krocích:

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

Nyní vytvořte adresář pro připojení systému souborů pomocí `mkdir` . Následující příklad vytvoří adresář na adrese */datadrive*:

```bash
sudo mkdir /datadrive
```

`mount`K následnému připojení systému souborů použijte. Následující příklad připojí oddíl */dev/sdc1* k přípojnému bodu */datadrive* :

```bash
sudo mount /dev/sdc1 /datadrive
```

Aby bylo zajištěno, že se jednotka po restartování automaticky znovu připojí, je nutné ji přidat do souboru */etc/fstab* . Důrazně doporučujeme, abyste v */etc/fstab* použili UUID (univerzálně jedinečný identifikátor), aby odkazoval na jednotku, a ne jenom název zařízení (například, */dev/sdc1*). Pokud operační systém zjistí při spouštění chybu disku, zabráníte použitím identifikátoru UUID připojení nesprávného disku do daného umístění. Zbývajícím datovým diskům by se přiřadily stejné ID zařízení. Ke zjištění UUID nového disku použijte nástroj `blkid`:

```bash
sudo blkid
```

Výstup bude vypadat podobně jako v následujícím příkladu:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Nesprávná úprava souboru **/etc/fstab** by mohla vést k nespouštěcímu systému. Pokud si nejste jistí, podívejte se do dokumentace k distribuci, kde najdete informace o tom, jak soubor správně upravit. Doporučuje se také vytvořit zálohu souboru/etc/fstab před úpravou.

Potom v textovém editoru otevřete soubor */etc/fstab* následujícím způsobem:

```bash
sudo vi /etc/fstab
```

V tomto příkladu použijte hodnotu UUID pro zařízení */dev/sdc1* , které bylo vytvořeno v předchozích krocích, a přípojný bod of */datadrive*. Přidejte následující řádek na konec souboru */etc/fstab* :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Pozdější odebrání datového disku bez úprav fstab by mohlo způsobit, že se virtuální počítač nepodaří spustit. Většina distribucí poskytuje možnosti *nobootwait* a */* nebo fstab. Tyto možnosti umožňují spuštění systému i v případě, že se nepodaří připojit disk při spuštění. Další informace o těchto parametrech najdete v dokumentaci k distribuci.
>
> Možnost *neúspěšné* zajistí, že se virtuální počítač spustí i v případě, že je poškozen systém souborů nebo když disk v době spuštění neexistuje. Bez této možnosti se můžete setkat s chováním, jak je popsáno v tématu [nejde přes SSH pro virtuální počítač se systémem Linux z důvodu chyb FSTAB](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting) .
>
> Sériová konzola virtuálních počítačů Azure se dá použít pro přístup k VIRTUÁLNÍmu počítači z konzoly, pokud změna fstab způsobila selhání při spuštění. Další podrobnosti najdete v dokumentaci ke [sériové konzole](../troubleshooting/serial-console-linux.md).

### <a name="trimunmap-support-for-linux-in-azure"></a>Podpora STŘIHu a odmapování pro Linux v Azure
Některé jádro systému Linux podporují operace OŘEZÁVÁNÍ a odmapování, aby bylo možné zahodit nepoužívané bloky na disku. Tato funkce je primárně užitečná ve službě Storage úrovně Standard pro informování Azure o tom, že odstraněné stránky už nejsou platné a můžou být zahozeny, a pokud chcete vytvořit velké soubory a odstranit je, můžete ušetřit peníze.

Existují dva způsoby, jak na svém VIRTUÁLNÍm počítači se systémem Linux povolit podporu OŘEZÁVÁNÍ. V obvyklých případech si prostudujte doporučený postup:

* Použijte `discard` možnost Mount v */etc/fstab*, například:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* V některých případech `discard` může mít možnost vliv na výkon. Alternativně můžete `fstrim` příkaz spustit ručně z příkazového řádku nebo ho přidat do crontab, aby se pravidelně spouštěl:

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

## <a name="next-steps"></a>Další kroky

* Pokud chcete mít jistotu, že je váš virtuální počítač se systémem Linux nakonfigurovaný správně, Projděte si doporučení [optimalizace výkonu počítačů se systémem Linux](optimization.md) .
* Kapacitu úložiště můžete rozšířit přidáním dalších disků a [konfigurací RAID](configure-raid.md) pro další výkon.
