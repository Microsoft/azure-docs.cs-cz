---
title: Přidání datového disku do virtuálního počítače se systémem Linux pomocí Azure CLI
description: Naučte se přidat trvalý datový disk k VIRTUÁLNÍmu počítači se systémem Linux pomocí Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/20/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: ad3a2ae9fd9a99398b384ef4e4a44820faccf8c7
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675881"
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

Pokud chcete připojit stávající disk, najděte ID disku a předejte ho příkazu [AZ VM disk Attach](/cli/azure/vm/disk?view=azure-cli-latest) . Následující příklady dotazů na disk s názvem *myDataDisk* v *myResourceGroup* ho připojí k virtuálnímu počítači s názvem *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="format-and-mount-the-disk"></a>Formátování a připojení disku

Pokud chcete svůj nový disk rozdělit do oddílů, zformátovat ho a připojit ho, aby ho váš virtuální počítač Linux mohl použít, připojte se k VIRTUÁLNÍmu počítači přes SSH. Další informace najdete v tématu [Jak použít SSH s Linuxem v Azure](mac-create-ssh-keys.md). Následující příklad se připojuje k virtuálnímu počítači s veřejnou IP adresou *10.123.123.25* s uživatelským jménem *azureuser*:

```bash
ssh azureuser@10.123.123.25
```

### <a name="find-the-disk"></a>Vyhledání disku

Po připojení k VIRTUÁLNÍmu počítači je potřeba disk najít. V tomto příkladu používáme `lsblk` k vypsání disků. 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

Výstup se podobá následujícímu příkladu:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0      50G
```

Tady `sdc` je disk, který chceme, protože je 50G. Pokud si nejste jistí, který disk je založený jenom na velikosti, můžete přejít na stránku virtuálního počítače na portálu, vybrat **disky** a zkontrolovat číslo logické jednotky disku v části **datové disky**. 


### <a name="format-the-disk"></a>Naformátovat disk

Naformátujte disk tak `parted` , že pokud je velikost disku 2 tebibytes (TIB) nebo větší, musíte použít dělení GPT, pokud se nachází pod 2TiB, a pak můžete použít dělení na oddíly MBR nebo GPT. 

> [!NOTE]
> Doporučuje se použít nejnovější verzi `parted` , která je k dispozici pro vaši distribuce.
> Pokud je velikost disku 2 tebibytes (TiB) nebo větší, je nutné použít dělení GPT. Pokud je velikost disku menší než 2 TiB, můžete použít dělení na oddíly MBR nebo GPT.  


V následujícím příkladu se `parted` používá `/dev/sdc` , kde první datový disk bude obvykle na většině virtuálních počítačů. Nahraďte `sdc` správnou možností pro váš disk. Naformátujeme ho také pomocí systému souborů [XFS](https://xfs.wiki.kernel.org/) .

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Pomocí tohoto nástroje se ujistěte, [`partprobe`](https://linux.die.net/man/8/partprobe) že jádro ví o novém oddílu a systému souborů. Neúspěšné použití `partprobe` může způsobit, že příkazy blkid nebo lslbk nevrátí UUID pro nový systém souborů hned.


### <a name="mount-the-disk"></a>Připojit disk

Nyní vytvořte adresář pro připojení systému souborů pomocí `mkdir` . Následující příklad vytvoří adresář v `/datadrive` :

```bash
sudo mkdir /datadrive
```

`mount`K následnému připojení systému souborů použijte. Následující příklad připojí `/dev/sdc1` oddíl ke `/datadrive` přípojnému bodu:

```bash
sudo mount /dev/sdc1 /datadrive
```

### <a name="persist-the-mount"></a>Zachovat připojení

Aby bylo zajištěno, že se jednotka po restartování automaticky znovu připojí, je nutné ji přidat do souboru */etc/fstab* . Důrazně doporučujeme, abyste v */etc/fstab* použili UUID (univerzálně jedinečný identifikátor), aby odkazoval na jednotku, a ne jenom název zařízení (například, */dev/sdc1*). Pokud operační systém zjistí při spouštění chybu disku, zabráníte použitím identifikátoru UUID připojení nesprávného disku do daného umístění. Zbývajícím datovým diskům by se přiřadily stejné ID zařízení. Ke zjištění UUID nového disku použijte nástroj `blkid`:

```bash
sudo blkid
```

Výstup bude vypadat podobně jako v následujícím příkladu:

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> Nesprávná úprava souboru **/etc/fstab** by mohla vést k nespouštěcímu systému. Pokud si nejste jistí, podívejte se do dokumentace k distribuci, kde najdete informace o tom, jak soubor správně upravit. Doporučuje se také vytvořit zálohu souboru/etc/fstab před úpravou.

Potom v textovém editoru otevřete soubor */etc/fstab* následujícím způsobem:

```bash
sudo nano /etc/fstab
```

V tomto příkladu použijte hodnotu UUID pro `/dev/sdc1` zařízení, které bylo vytvořeno v předchozích krocích, a přípojný bod `/datadrive` . Na konec souboru přidejte následující řádek `/etc/fstab` :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

V tomto příkladu používáme Editor nano, takže až budete hotovi s úpravou souboru, použijte `Ctrl+O` k zápisu souboru a `Ctrl+X` k ukončení editoru.

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
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
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

* Pokud chcete mít jistotu, že je váš virtuální počítač se systémem Linux nakonfigurovaný správně, Projděte si doporučení [optimalizace výkonu počítačů se systémem Linux](/previous-versions/azure/virtual-machines/linux/optimization) .
* Kapacitu úložiště můžete rozšířit přidáním dalších disků a [konfigurací RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) pro další výkon.