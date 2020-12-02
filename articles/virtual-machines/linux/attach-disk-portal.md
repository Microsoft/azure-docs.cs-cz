---
title: Připojení datového disku k virtuálnímu počítači se systémem Linux
description: Pomocí portálu připojte nový nebo existující datový disk k virtuálnímu počítači se systémem Linux.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/28/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 8f60c83417e9c614ca30f140e6acbbf08e5643cf
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500644"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Připojení datového disku k virtuálnímu počítači se systémem Linux pomocí portálu 
V tomto článku se dozvíte, jak připojit nové i stávající disky k virtuálnímu počítači se systémem Linux prostřednictvím Azure Portal. [Datový disk můžete také připojit k virtuálnímu počítači s Windows v Azure Portal](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Než připojíte disky k VIRTUÁLNÍmu počítači, přečtěte si tyto tipy:

* Velikost virtuálního počítače určuje, kolik datových disků můžete připojit. Podrobnosti najdete v tématu [velikosti pro virtuální počítače](../sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Disky připojené k virtuálním počítačům jsou ve skutečnosti soubory VHD uložené v Azure. Podrobnosti najdete v našem [úvodu ke spravovaným diskům](../managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Po připojení disku se musíte [připojit k virtuálnímu počítači se systémem Linux a připojit nový disk](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Najít virtuální počítač
1. Pokud chcete najít virtuální počítač, otevřete [Azure Portal](https://portal.azure.com/) . Vyhledejte a vyberte **virtuální počítače**.
2. V seznamu vyberte virtuální počítač.
3. Na stránce **virtuální počítače** v části **Nastavení** vyberte **disky**.


## <a name="attach-a-new-disk"></a>Připojit nový disk

1. V podokně **disky** v části **datové disky** vyberte **vytvořit a připojit nový disk**.

1. Zadejte název spravovaného disku. Zkontrolujte výchozí nastavení a podle potřeby aktualizujte **Typ úložiště**, **Velikost (GIB)**, **šifrování** a **ukládání do mezipaměti hostitele** .
   
   :::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Zkontrolujte nastavení disku.":::


1. Po dokončení vyberte **Uložit** v horní části stránky a vytvořte spravovaný disk a aktualizujte konfiguraci virtuálního počítače.


## <a name="attach-an-existing-disk"></a>Připojení stávajícího disku
1. V podokně **disky** v části **datové disky** vyberte  **připojit existující disky**.
1. Klikněte na rozevírací nabídku pro **název disku** a vyberte disk ze seznamu dostupných spravovaných disků. 

1. Kliknutím na **Save (Uložit** ) připojte existující spravovaný disk a aktualizujte konfiguraci virtuálních počítačů:
   

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Připojte se k virtuálnímu počítači se systémem Linux a připojte nový disk.
Pokud chcete svůj nový disk rozdělit do oddílů, zformátovat ho a připojit ho, aby ho váš virtuální počítač Linux mohl použít, připojte se k VIRTUÁLNÍmu počítači přes SSH. Další informace najdete v tématu [Jak použít SSH s Linuxem v Azure](mac-create-ssh-keys.md). Následující příklad se připojuje k virtuálnímu počítači s veřejnou IP adresou *10.123.123.25* s uživatelským jménem *azureuser*: 

```bash
ssh azureuser@10.123.123.25
```

## <a name="find-the-disk"></a>Vyhledání disku

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
sdc     3:0:0:0       4G
```

V tomto příkladu je disk, který jsem přidal, `sdc` . Je to logická jednotka 0 a je 4 GB.

V případě složitějšího příkladu je třeba, aby na portálu vypadalo více datových disků:

:::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Zkontrolujte nastavení disku.":::

V imagi vidíte, že je k dispozici 3 datové disky: 4 GB na logických jednotkách 0, 16GB na logické jednotce (LUN) 1 a 32G na logické jednotce (LUN) 2.

Tady je postup, který může vypadat jako při použití `lsblk` :

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
sdd     3:0:0:1      16G
sde     3:0:0:2      32G
```

Z výstupu můžete `lsblk` vidět, že disk 4 GB na logické jednotce (LUN) 0 je `sdc` , disk 16GB na logické jednotce 1 `sdd` , a disk 32G na logické jednotce 2 je `sde` .

### <a name="partition-a-new-disk"></a>Rozdělení nového disku na oddíly

Pokud používáte existující disk, který obsahuje data, přeskočte k připojení disku. Pokud připojujete nový disk, budete muset disk rozdělit na oddíly.

`parted`Nástroj lze použít k rozdělení a formátování datového disku.

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

Vytvořte adresář pro připojení systému souborů pomocí `mkdir` . Následující příklad vytvoří adresář v `/datadrive` :

```bash
sudo mkdir /datadrive
```

`mount`K následnému připojení systému souborů použijte. Následující příklad připojí oddíl */dev/sdc1* k `/datadrive` přípojnému bodu:

```bash
sudo mount /dev/sdc1 /datadrive
```

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

Používali jsme Editor nano, takže až budete hotovi s úpravou souboru, použijte `Ctrl+O` k zápisu souboru a `Ctrl+X` k ukončení editoru.

> [!NOTE]
> Pozdější odebrání datového disku bez úprav fstab by mohlo způsobit, že se virtuální počítač nepodaří spustit. Většina distribucí poskytuje možnosti *nobootwait* a */* nebo fstab. Tyto možnosti umožňují spuštění systému i v případě, že se nepodaří připojit disk při spuštění. Další informace o těchto parametrech najdete v dokumentaci k distribuci.
> 
> Možnost *neúspěšné* zajistí, že se virtuální počítač spustí i v případě, že je poškozen systém souborů nebo když disk v době spuštění neexistuje. Bez této možnosti se můžete setkat s chováním, jak je popsáno v tématu [nejde přes SSH pro virtuální počítač se systémem Linux z důvodu chyb FSTAB](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting) .


## <a name="verify-the-disk"></a>Ověření disku

Nyní můžete znovu použít `lsblk` k zobrazení disku a přípojný bod.

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

Výstup bude vypadat zhruba takto:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
└─sdc1                4G /datadrive
```

Můžete vidět, že `sdc` je nyní připojen `/datadrive` .

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

## <a name="next-steps"></a>Další kroky
[Datový disk](add-disk.md) můžete také připojit pomocí rozhraní příkazového řádku Azure CLI.