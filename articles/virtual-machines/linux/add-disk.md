---
title: Přidání datového disku do virtuálního počítače s Linuxem pomocí azure cli
description: Naučte se přidat trvalý datový disk do virtuálního počítače s Linuxem pomocí azure CLI
author: roygara
manager: twooley
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/13/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: a80a1fe21ba0b40aebf9e426e3d49f499c2d2a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250410"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Přidání disku do virtuálního počítače s Linuxem
Tento článek ukazuje, jak připojit trvalý disk k virtuálnímu počítači, abyste mohli zachovat data – i když je virtuální počítač znovu zřízen z důvodu údržby nebo změna velikosti.


## <a name="attach-a-new-disk-to-a-vm"></a>Připojení nového disku k virtuálnímu počítače

Pokud chcete přidat nový prázdný datový disk na vašem virtuálním počítači, použijte `--new` příkaz připojení disku [az vm](/cli/azure/vm/disk?view=azure-cli-latest) s parametrem. Pokud je váš virtuální počítač v zóně dostupnosti, disk se automaticky vytvoří ve stejné zóně jako virtuální počítač. Další informace naleznete v [tématu Přehled zón dostupnosti](../../availability-zones/az-overview.md). Následující příklad vytvoří disk s názvem *myDataDisk* o velikosti 50 Gb:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Připojení stávajícího disku

Chcete-li připojit existující disk, vyhledejte ID disku a předavažte id [příkazu připojení disku az vm.](/cli/azure/vm/disk?view=azure-cli-latest) Následující příklad dotazuje na disk s názvem *myDataDisk* v *myResourceGroup*, pak jej připojí k virtuálnímu počítače s názvem *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Připojení k virtuálnímu počítači SIP k připojení nového disku

Chcete-li rozdělit, formátovat a připojit nový disk, aby ho virtuální počítač s Linuxem mohl používat, SSH do virtuálního počítače. Další informace najdete v tématu [Jak použít SSH s Linuxem v Azure](mac-create-ssh-keys.md). Následující příklad se připojí k virtuálnímu počítači s veřejnou položkou DNS *mypublicdns.westus.cloudapp.azure.com* s uživatelským jménem *azureuser*:

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Po připojení k virtuálnímu počítači můžete připojit disk. Nejprve vyhledejte `dmesg` disk pomocí (metoda, kterou používáte ke zjištění nového disku, se může lišit). Následující příklad používá dmesg k filtrování na discích *SCSI:*

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
> Doporučujeme používat nejnovější verze fdisk nebo parted, které jsou k dispozici pro vaše distro.

Zde *sdc* je disk, který chceme. Rozdělte disk `parted`s , pokud je velikost disku 2 tebibytes (TiB) nebo větší, pak je nutné použít oddíly GPT, pokud je pod 2TiB, pak můžete použít buď Oddíly MBR nebo GPT. Pokud používáte dělení MBR, můžete `fdisk`použít . Změřte jej jako primární disk na oddílu 1 a přijměte ostatní výchozí hodnoty. Následující příklad spustí `fdisk` proces na */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

K přidání nového oddílu použijte příkaz `n`. V tomto příkladu `p` také zvolíme pro primární oddíl a přijmeme zbývající výchozí hodnoty. Výstup se bude podobat tomuto příkladu:

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

Tabulku oddílů vytiskněte `p` `w` zadáním a potom ji použijte k zápisu na disk a ukončení. Výstup by měl vypadat podobně jako v následujícím příkladu:

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
Pomocí níže uvedeného příkazu aktualizujte jádro:
```
partprobe 
```

Nyní napište systém souborů do `mkfs` oddílu pomocí příkazu. Zadejte typ souborového systému a název zařízení. Následující příklad vytvoří souborový systém *ext4* v oddílu */dev/sdc1,* který byl vytvořen v předchozích krocích:

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

Nyní vytvořte adresář pro připojení `mkdir`systému souborů pomocí aplikace . Následující příklad vytvoří adresář na */datadrive*:

```bash
sudo mkdir /datadrive
```

Slouží `mount` k připojení souborového systému. Následující příklad připojí oddíl */dev/sdc1* k přípojnému bodu */datadrive:*

```bash
sudo mount /dev/sdc1 /datadrive
```

Chcete-li zajistit, aby byla jednotka po restartování automaticky připojena, musí být přidána do souboru */etc/fstab.* Důrazně se také doporučuje, aby uuid (univerzálně jedinečný IDentifier) se používá v */etc/fstab* odkazovat na jednotku, nikoli pouze název zařízení (například */dev/sdc1).* Pokud operační systém zjistí při spouštění chybu disku, zabráníte použitím identifikátoru UUID připojení nesprávného disku do daného umístění. Zbývajícím datovým diskům by se přiřadily stejné ID zařízení. Ke zjištění UUID nového disku použijte nástroj `blkid`:

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
> Nesprávná úprava souboru **/etc/fstab** může vést k nespustitelnému systému. Pokud si nejste jistí, podívejte se do dokumentace k distribuci, kde najdete informace o tom, jak soubor správně upravit. Doporučuje se také, aby záloha souboru /etc/fstab byla vytvořena před úpravou.

Dále otevřete soubor */etc/fstab* v textovém editoru následujícím způsobem:

```bash
sudo vi /etc/fstab
```

V tomto příkladu použijte hodnotu UUID pro zařízení */dev/sdc1,* které bylo vytvořeno v předchozích krocích, a přípojný bod */datadrive*. Na konec souboru */etc/fstab* přidejte následující řádek:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Pozdější odebrání datového disku bez úprav fstab může způsobit selhání spuštění virtuálního počítače. Většina distribucí poskytuje buď možnosti *nofail* a/nebo *nobootwait* fstab. Tyto možnosti umožňují spuštění systému i v případě, že se disk při spuštění nezdaří. Další informace o těchto parametrech naleznete v dokumentaci k distribuci.
>
> Možnost *nofail* zajišťuje spuštění virtuálního počítače i v případě, že je souborový systém poškozen nebo disk při spuštění neexistuje. Bez této možnosti se můžete setkat s chováním, jak je popsáno v [aplikaci Cannot SSH to Linux VM z důvodu chyb FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)
>
> Konzolu Xbox Pro uvaděčů virtuálního počítače lze použít pro přístup konzoly k virtuálnímu počítači, pokud změna fstab má za následek selhání spouštění. Další podrobnosti jsou k dispozici v [dokumentaci k konzole Serial Console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).

### <a name="trimunmap-support-for-linux-in-azure"></a>Podpora TRIM/UNMAP pro Linux v Azure
Některá linuxová jádra podporují operace TRIM/UNMAP k vyřazení nepoužívaných bloků na disku. Tato funkce je primárně užitečná ve standardním úložišti, aby informovala Azure, že odstraněné stránky již nejsou platné a mohou být zahozeny a mohou ušetřit peníze, pokud vytvoříte velké soubory a pak je odstraníte.

Existují dva způsoby, jak povolit podporu TRIM ve vašem virtuálním počítači s Linuxem. Jako obvykle, poraďte se s vaší distribuce pro doporučený přístup:

* Použijte `discard` možnost připojení v */etc/fstab*, například:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* V některých `discard` případech může mít možnost vliv na výkon. Případně můžete `fstrim` příkaz spustit ručně z příkazového řádku nebo jej přidat do crontabu a pravidelně spouštět:

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/Centos**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Řešení potíží

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Další kroky

* Chcete-li zajistit, aby byl virtuální počítač s Linuxem správně nakonfigurovaný, přečtěte si doporučení [pro optimalizaci výkonu počítače s Linuxem.](optimization.md)
* Rozšiřte kapacitu úložiště přidáním dalších disků a [nakonfigurujte raid](configure-raid.md) pro další výkon.
