---
title: Připojení datového disku k virtuálnímu počítači se systémem Linux
description: Pomocí portálu připojte nový nebo existující datový disk k virtuálnímu počítači se systémem Linux.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 746cef8dfe026c731a677cbf77f729d36342f007
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969358"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Připojení datového disku k virtuálnímu počítači se systémem Linux pomocí portálu 
V tomto článku se dozvíte, jak připojit nové i stávající disky k virtuálnímu počítači se systémem Linux prostřednictvím Azure Portal. [Datový disk můžete také připojit k virtuálnímu počítači s Windows v Azure Portal](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Než připojíte disky k VIRTUÁLNÍmu počítači, přečtěte si tyto tipy:

* Velikost virtuálního počítače určuje, kolik datových disků můžete připojit. Podrobnosti najdete v tématu [velikosti pro virtuální počítače](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Disky připojené k virtuálním počítačům jsou ve skutečnosti soubory VHD uložené v Azure. Podrobnosti najdete v našem [úvodu ke spravovaným diskům](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Po připojení disku se musíte [připojit k virtuálnímu počítači se systémem Linux a připojit nový disk](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Najít virtuální počítač
1. Pokud chcete najít virtuální počítač, otevřete [Azure Portal](https://portal.azure.com/) . Vyhledejte a vyberte **virtuální počítače**.
2. V seznamu vyberte virtuální počítač.
3. Na stránce **virtuální počítače** v části **Nastavení**vyberte **disky**.
   
    ![Otevřít nastavení disku](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Připojit nový disk

1. V podokně **disky** klikněte na **+ přidat datový disk**.
2. Klikněte na rozevírací nabídku pro **název** a vyberte **vytvořit disk**:

    ![Vytvoření spravovaného disku Azure](./media/attach-disk-portal/create-new-md.png)

3. Zadejte název spravovaného disku. Zkontrolujte výchozí nastavení, podle potřeby aktualizujte a pak klikněte na **vytvořit**.
   
   ![Kontrola nastavení disku](./media/attach-disk-portal/create-new-md-settings.png)

4. Kliknutím na **Uložit** vytvořte spravovaný disk a aktualizujte konfiguraci virtuálního počítače:

   ![Uložit nový Azure Managed disk](./media/attach-disk-portal/confirm-create-new-md.png)

5. Když Azure vytvoří disk a připojí ho k virtuálnímu počítači, nový disk se zobrazí v nastavení disku virtuálního počítače v části **datové disky**. Jelikož jsou spravované disky prostředky nejvyšší úrovně, disk se zobrazí v kořenovém adresáři skupiny prostředků:

   ![Spravovaný disk Azure ve skupině prostředků](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Připojení stávajícího disku
1. V podokně **disky** klikněte na **+ přidat datový disk**.
2. Kliknutím na rozevírací nabídku **název** zobrazíte seznam existujících spravovaných disků přístupných k vašemu předplatnému Azure. Vyberte spravovaný disk, který chcete připojit:

   ![Připojit stávající Azure Managed disk](./media/attach-disk-portal/select-existing-md.png)

3. Kliknutím na **Save (Uložit** ) připojte existující spravovaný disk a aktualizujte konfiguraci virtuálních počítačů:
   
   ![Uložit aktualizace služby Azure Managed disks](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Jakmile Azure připojí disk k virtuálnímu počítači, zobrazí se v nastavení disku virtuálního počítače v části **datové disky**.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Připojte se k virtuálnímu počítači se systémem Linux a připojte nový disk.
Pokud chcete svůj nový disk rozdělit do oddílů, zformátovat ho a připojit ho, aby ho váš virtuální počítač Linux mohl použít, připojte se k VIRTUÁLNÍmu počítači přes SSH. Další informace najdete v tématu [Jak použít SSH s Linuxem v Azure](mac-create-ssh-keys.md). Následující příklad se připojí k virtuálnímu počítači s použitím veřejné položky DNS *mypublicdns.westus.cloudapp.Azure.com* s uživatelským jménem *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Po připojení k VIRTUÁLNÍmu počítači jste připraveni k připojení disku. Nejdřív Najděte disk pomocí `dmesg` (metoda, kterou použijete ke zjišťování nového disku, se může lišit). Následující příklad používá dmesg k filtrování na discích *SCSI* :

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

Tady je *SDC* disk, který chceme. 

### <a name="partition-a-new-disk"></a>Rozdělení nového disku na oddíly
Pokud používáte existující disk, který obsahuje data, přeskočte k připojení disku. Pokud připojujete nový disk, budete muset disk rozdělit na oddíly.

> [!NOTE]
> Doporučuje se používat nejnovější verze nástroje Fdisk nebo částečně, které jsou k dispozici pro vaši distribuce.

Rozdělte disk na oddíly pomocí příkazu `fdisk`. Pokud je velikost disku 2 tebibytes (TiB) nebo větší, je nutné použít dělení GPT, pomocí `parted` můžete provádět dělení GPT. Pokud je velikost disku pod 2TiB, můžete použít dělení na oddíly MBR nebo GPT. Nastavte primární disk na oddíl 1 a přijměte ostatní výchozí hodnoty. Následující příklad spustí `fdisk` proces na */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

K přidání nového oddílu použijte příkaz `n`. V tomto příkladu jsme také zvolili `p` pro primární oddíl a přijali zbytek výchozích hodnot. Výstup bude vypadat podobně jako v následujícím příkladu:

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

Vytiskněte tabulku oddílů zadáním `p` a potom pomocí `w` zapište tabulku na disk a ukončete. Výstup by měl vypadat podobně jako v následujícím příkladu:

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

Nyní zapište systém souborů do oddílu pomocí příkazu `mkfs`. Zadejte typ systému souborů a název zařízení. Následující příklad vytvoří systém souborů *ext4* na oddílu */dev/sdc1* , který byl vytvořen v předchozích krocích:

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

#### <a name="alternate-method-using-parted"></a>Alternativní metoda používající částečně
Nástroj Fdisk potřebuje interaktivní vstup, a proto není ideální pro použití v rámci skriptů automatizace. [Nástroj, který se dá](https://www.gnu.org/software/parted/) využít, ale může být skriptovaná, takže se ve scénářích automatizace lépe zahodí. Částečný nástroj lze použít k rozdělení na oddíly a naformátování datového disku. V tomto návodu použijeme nový datový disk/dev/sdc a naformátujeme ho pomocí systému souborů [XFS](https://xfs.wiki.kernel.org/) .
```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
partprobe /dev/sdc1
```
Jak vidíte výše, používáme nástroj [partprobe](https://linux.die.net/man/8/partprobe) k tomu, abyste se ujistili, že jádro okamžitě ví o novém oddílu a systému souborů. Neúspěšné použití partprobe může způsobit, že blkid nebo lslbk příkazy nevrátí UUID pro nový systém souborů hned.

### <a name="mount-the-disk"></a>Připojit disk
Vytvořte adresář pro připojení systému souborů pomocí `mkdir`. Následující příklad vytvoří adresář na adrese */datadrive*:

```bash
sudo mkdir /datadrive
```

K následnému připojení systému souborů použijte `mount`. Následující příklad připojí oddíl */dev/sdc1* k přípojnému bodu */datadrive* :

```bash
sudo mount /dev/sdc1 /datadrive
```

Aby bylo zajištěno, že se jednotka po restartování automaticky znovu připojí, je nutné ji přidat do souboru */etc/fstab* . Důrazně doporučujeme, abyste v */etc/fstab* použili UUID (univerzálně jedinečný identifikátor), aby odkazoval na jednotku, a ne jenom název zařízení (například, */dev/sdc1*). Pokud operační systém při spuštění detekuje chybu disku, při použití UUID se vyhnete nesprávnému disku připojenému k danému umístění. K zbývajícím datovým diskům by pak byla přiřazena stejná ID zařízení. Chcete-li zjistit UUID nové jednotky, použijte nástroj `blkid`:

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
> Nesprávná úprava souboru **/etc/fstab** by mohla vést k nespouštěcímu systému. Pokud si nejste jistí, přečtěte si dokumentaci k distribuci, kde najdete informace o tom, jak tento soubor správně upravit. Doporučuje se také vytvořit zálohu souboru/etc/fstab před úpravou.

Potom v textovém editoru otevřete soubor */etc/fstab* následujícím způsobem:

```bash
sudo vi /etc/fstab
```

V tomto příkladu použijte hodnotu UUID pro zařízení */dev/sdc1* , které bylo vytvořeno v předchozích krocích, a přípojný bod of */datadrive*. Přidejte následující řádek na konec souboru */etc/fstab* :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```
Až budete hotovi, uložte soubor */etc/fstab* a restartujte systém.
> [!NOTE]
> Pozdější odebrání datového disku bez úprav fstab by mohlo způsobit, že se virtuální počítač nepodaří spustit. Většina distribucí poskytuje možnosti *nobootwait* a */* nebo fstab. Tyto možnosti umožňují spuštění systému i v případě, že se nepodaří připojit disk při spuštění. Další informace o těchto parametrech najdete v dokumentaci k distribuci.
> 
> Možnost *neúspěšné* zajistí, že se virtuální počítač spustí i v případě, že je poškozen systém souborů nebo když disk v době spuštění neexistuje. Bez této možnosti se můžete setkat s chováním, jak je popsáno v tématu [nejde přes SSH pro virtuální počítač se systémem Linux z důvodu chyb FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) .

### <a name="trimunmap-support-for-linux-in-azure"></a>Podpora STŘIHu a odmapování pro Linux v Azure
Některé jádro systému Linux podporují operace OŘEZÁVÁNÍ a odmapování, aby bylo možné zahodit nepoužívané bloky na disku. Tato funkce je primárně užitečná ve službě Storage úrovně Standard pro informování Azure o tom, že odstraněné stránky už nejsou platné a můžou být zahozeny, a pokud chcete vytvořit velké soubory a odstranit je, můžete ušetřit peníze.

Existují dva způsoby, jak na svém VIRTUÁLNÍm počítači se systémem Linux povolit podporu OŘEZÁVÁNÍ. V obvyklých případech si prostudujte doporučený postup:

* V */etc/fstab*použijte možnost připojení `discard`, například:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* V některých případech může mít možnost `discard` dopad na výkon. Alternativně můžete spustit příkaz `fstrim` ručně z příkazového řádku nebo ho přidat do crontab, aby se pravidelně spouštěl:
  
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

## <a name="next-steps"></a>Další kroky
[Datový disk](add-disk.md) můžete také připojit pomocí rozhraní příkazového řádku Azure CLI.
