---
title: Optimalizace virtuálního počítače s Linuxem v Azure
description: Seznamte se s tipy pro optimalizaci a ujistěte se, že jste nastavili virtuální počítač Linux pro zajištění optimálního výkonu v Azure.
author: rickstercdn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: fceef1fa9f79ead0ffbbfd7de17b21b750659fc9
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370232"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Optimalizace virtuálního počítače s Linuxem v Azure
Vytváření virtuálních počítačů se systémem Linux je snadné z příkazového řádku nebo z portálu. V tomto kurzu se dozvíte, jak zajistit, že jste ho nastavili tak, aby optimalizoval jeho výkon na platformě Microsoft Azure. V tomto tématu se používá virtuální počítač s Ubuntu serverem, ale můžete také vytvořit virtuální počítač se systémem Linux pomocí [vlastních imagí jako šablon](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Předpoklady
V tomto tématu se předpokládá, že už máte funkční předplatné Azure ([registrace bezplatné zkušební verze](https://azure.microsoft.com/pricing/free-trial/)) a už jste do svého předplatného Azure ZŘÍDILI virtuální počítač. Ujistěte se, že máte nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlásili jste se k předplatnému Azure pomocí [AZ Login](/cli/azure/reference-index) , než [vytvoříte virtuální počítač](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Disk s operačním systémem Azure
Po vytvoření virtuálního počítače se systémem Linux v Azure jsou k němu přidruženy dva disky. **/dev/sda** je disk s operačním systémem, **/dev/sdb** je váš dočasný disk.  Nepoužívejte hlavní disk s operačním systémem (**/dev/sda**) pro cokoli s výjimkou operačního systému, protože je optimalizovaný pro rychlý čas spuštění virtuálního počítače a neposkytuje dobrý výkon pro vaše úlohy. Chcete k VIRTUÁLNÍmu počítači připojit jeden nebo více disků, aby bylo možné získat trvalé a optimalizované úložiště pro vaše data. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Přidávání disků pro cíle velikosti a výkonu
Na základě velikosti virtuálního počítače můžete připojit až 16 dalších disků na diskech řady A-Series 32 na discích řady D-Series a 64 na počítači s procesorem G-series – od velikosti až 32 TB. Podle požadavků na prostor a IOps přidejte další disky podle potřeby. Každý disk má cíl výkonu 500 IOps pro úložiště úrovně Standard a až 20 000 IOps na disk pro Premium Storage.

Aby bylo možné získat nejvyšší IOps na Premium Storage discích, kde nastavení mezipaměti bylo nastaveno na hodnotu **jen pro čtení** nebo **žádné**, je nutné zakázat **bariéry** při připojování systému souborů v systému Linux. Nepotřebujete překážky, protože zápisy do Premium Storage zálohovaných disků jsou pro tato nastavení mezipaměti trvalé.

* Pokud používáte **reiserFS**, zakažte bariéry pomocí možnosti připojení `barrier=none` (Pokud chcete povolit bariéry, použijte `barrier=flush` ).
* Pokud používáte **ext3/ext4**, zakažte bariéry pomocí možnosti připojení `barrier=0` (pro povolení bariéry použijte `barrier=1` ).
* Pokud používáte **XFS**, zakažte bariéry pomocí možnosti připojení `nobarrier` (pro povolení bariéry použijte možnost `barrier` ).

## <a name="unmanaged-storage-account-considerations"></a>Požadavky na účet nespravovaného úložiště
Výchozí akcí při vytváření virtuálního počítače pomocí Azure CLI je použití Azure Managed Disks.  Tyto disky jsou zpracovávány platformou Azure a nevyžadují žádnou přípravu ani umístění k jejich uložení.  Nespravované disky vyžadují účet úložiště a mají nějaké další požadavky na výkon.  Další informace o spravovaných discích najdete v tématu [Přehled služby Azure Managed Disks](../managed-disks-overview.md).  V následující části jsou popsány požadavky na výkon pouze v případě, že používáte nespravované disky.  Výchozím a doporučeným řešením úložiště je znovu použít Managed disks.

Pokud vytvoříte virtuální počítač s nespravovanými disky, nezapomeňte připojit disky z účtů úložiště, které se nacházejí ve stejné oblasti jako váš virtuální počítač, aby se zajistila těsná vzdálenost a minimalizovala latence sítě.  Každý účet úložiště úrovně Standard má maximálně 20 tisíc IOps a kapacitu velikosti 500 TB.  Toto omezení funguje na přibližně 40 intenzivně používaných disků, včetně disku s operačním systémem i všech datových disků, které vytvoříte. U Premium Storage účtů není limit počtu vstupně-výstupních operací stanoven, ale omezení velikosti 32 TB. 

Když se zaměříte na úlohy s vysokým počtem vstupně-výstupních operací a pro vaše disky jste zvolili úložiště úrovně Standard, budete možná muset rozdělit disky mezi několik účtů úložiště, abyste se ujistili, že jste nedosáhli limitu 20 000 IOps pro standardní účty úložiště. Váš virtuální počítač může obsahovat kombinaci disků z různých účtů úložiště a typů účtů úložiště, abyste dosáhli optimální konfigurace.
 

## <a name="your-vm-temporary-drive"></a>Dočasná jednotka virtuálního počítače
Když vytvoříte virtuální počítač, Azure ve výchozím nastavení poskytuje disk s operačním systémem (**/dev/sda**) a dočasný disk (**/dev/sdb**).  Všechny další disky, které přidáte, se zobrazí jako **/dev/sdc**, **/dev/SDD**, **/dev/SDE** atd. Všechna data na dočasném disku (**/dev/sdb**) nejsou trvalá a můžou se ztratit, pokud konkrétní události, jako je změna velikosti virtuálního počítače, opětovné nasazení nebo údržba, vynutí restartování vašeho virtuálního počítače.  Velikost a typ dočasného disku souvisí s velikostí virtuálního počítače, kterou jste zvolili v době nasazení. Na všech virtuálních počítačích s velikostí Premium (DS, G a DS_V2 Series) se dočasná jednotka zálohuje místní jednotkou SSD, aby se mohl zvýšit výkon až na 48k IOps. 

## <a name="linux-swap-partition"></a>Oddíl odkládacího oddílu Linux
Pokud je váš virtuální počítač Azure z image Ubuntu nebo CoreOS, můžete k odeslání cloudové konfigurace do Cloud-init použít CustomData. Pokud jste [nahráli vlastní image Linux](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , která používá Cloud-init, nakonfigurujete také odkládací oddíly pomocí Cloud-init.

Soubor **/etc/waagent.conf** nemůžete použít ke správě swapu pro všechny bitové kopie, které jsou zřízené a podporované modulem Cloud-init. Úplný seznam imagí najdete v tématu [použití Cloud-init](using-cloud-init.md). 

Nejjednodušší způsob, jak spravovat swap pro tyto image, je provést tyto kroky:

1. Ve složce **/var/lib/Cloud/Scripts/per-Boot** vytvořte soubor s názvem **create_swapfile. sh**:

   **$ sudo touch/var/lib/Cloud/Scripts/per-Boot/create_swapfile. sh**

1. Do souboru přidejte následující řádky:

   **$ sudo VI/var/lib/Cloud/Scripts/per-Boot/create_swapfile. sh**

   ```
   #!/bin/sh
   if [ ! -f '/mnt/swapfile' ]; then
   fallocate --length 2GiB /mnt/swapfile
   chmod 600 /mnt/swapfile
   mkswap /mnt/swapfile
   swapon /mnt/swapfile
   swapon -a ; fi
   ```

   > [!NOTE]
   > Hodnotu můžete změnit podle potřeby a na základě dostupného místa na disku s prostředky, které se liší v závislosti na používané velikosti virtuálního počítače.

1. Zajistěte spustitelný soubor souboru:

   **$ sudo chmod + x/var/lib/Cloud/Scripts/per-Boot/create_swapfile. sh**

1. Pokud chcete vytvořit swapfile, spusťte skript hned za posledním krokem:

   **$ sudo/var/lib/Cloud/Scripts/per-Boot/./create_swapfile. sh**

U imagí bez podpory Cloud-init mají image virtuálních počítačů nasazené z Azure Marketplace agenta pro Linux virtuálního počítače integrovaný s operačním systémem. Tento agent umožňuje VIRTUÁLNÍm počítačům komunikovat s různými službami Azure. Za předpokladu, že jste nasadili standardní bitovou kopii z Azure Marketplace, musíte provést následující kroky, abyste správně nakonfigurovali nastavení souborů odkládacího souboru se systémem Linux:

Vyhledejte a upravte dvě položky v souboru **/etc/waagent.conf** . Řídí existenci vyhrazeného stránkovacího souboru a velikosti odkládacího souboru. Parametry, které je třeba ověřit, jsou `ResourceDisk.EnableSwap` a `ResourceDisk.SwapSizeMB` 

Pokud chcete povolit řádně povolený disk a připojený soubor odkládacího souboru, ujistěte se, že parametry mají následující nastavení:

* ResourceDisk. EnableSwap = Y
* ResourceDisk. SwapSizeMB = {Size in MB pro splnění vašich potřeb 

Jakmile provedete změnu, musíte restartovat waagent nebo restartovat virtuální počítač se systémem Linux, aby se tyto změny projevily.  Věděli jste, že byly provedeny změny a při použití `free` příkazu k zobrazení volného místa byl vytvořen odkládací soubor. Následující příklad obsahuje 512 MB souboru vytvořeného v důsledku změny souboru **waagent. conf** :

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Vstupně-výstupní algoritmus plánování pro Premium Storage
V případě jádra 2.6.18 Linux byl výchozí algoritmus pro vstupně-výstupní operace změněn z koncového termínu na CFQ (zcela přiměřený algoritmus fronty). Ve vzorech vstupně-výstupních operací s náhodným přístupem je rozdíl mezi rozdíly ve výkonu mezi CFQ a konečným termínem nepatrný.  Pro disky založené na SSD, kde je v/v vzor disku předem sekvenční, přepínání zpátky na NOOP nebo konečný výkon může dosáhnout lepšího výkonu vstupu a výstupu.

### <a name="view-the-current-io-scheduler"></a>Zobrazit aktuálního plánovače/O
Použijte následující příkaz:  

```bash
cat /sys/block/sda/queue/scheduler
```

Zobrazí se následující výstup, který indikuje aktuální Plánovač.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Změnit aktuální zařízení (/dev/sda) pro vstupně-výstupní plánování algoritmu
Použijte následující příkazy:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Použití tohoto nastavení pouze pro **/dev/sda** není užitečné. Nastavte na všech datových discích, kde sekvenční vstupně-výstupní operace rozchází do vstupně-výstupního vzoru.  

Měl by se zobrazit následující výstup, který indikuje, že se **grub. cfg** úspěšně znovu sestavil a že se výchozí Plánovač AKTUALIZOVAL na NoOp.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

Pro skupinu distribuce Red Hat budete potřebovat jenom tento příkaz:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

Ubuntu 18,04 s jádrem optimalizovaným pro Azure používá vstupně-výstupní plánovače s více frontami. V tomto scénáři `none` je místo toho vhodný výběr `noop` . Další informace najdete v tématu [Ubuntu v/v plánovače](https://wiki.ubuntu.com/Kernel/Reference/IOSchedulers).

## <a name="using-software-raid-to-achieve-higher-iops"></a>Použití softwarového pole RAID k dosažení vyššího vstupně-funkční operace
Pokud vaše úlohy vyžadují víc IOps než jeden disk, budete muset použít softwarovou konfiguraci RAID o více discích. Vzhledem k tomu, že Azure už provádí odolnost disku v místní vrstvě prostředků infrastruktury, dosahuje nejvyšší úrovně výkonu z konfigurace prokládání RAID-0.  Zřizování a vytváření disků v prostředí Azure a jejich připojení k VIRTUÁLNÍmu počítači se systémem Linux před vytvořením oddílů, formátování a připojení jednotek.  Další podrobnosti o konfiguraci nastavení RAID softwaru na VIRTUÁLNÍm počítači Linux v Azure najdete v dokumentu **[Konfigurace softwarového pole RAID v systému Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** .

Jako alternativu k tradiční konfiguraci RAID se taky můžete rozhodnout nainstalovat Správce logických svazků (LVM), aby se nakonfigurovali řada fyzických disků na jeden prokládaný svazek logického úložiště. V této konfiguraci jsou čtení a zápisy distribuované na více disků, které jsou obsažené ve skupině svazků (podobně jako RAID0). Z důvodů výkonu je pravděpodobně vhodné proložit logické svazky tak, aby čtení a zápis využily všechny připojené datové disky.  Další podrobnosti o konfiguraci prokládaného logického svazku na VIRTUÁLNÍm počítači Linux v Azure najdete v části **[Konfigurace LVM na virtuálním počítači Linux v Azure](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** Documentation.

## <a name="next-steps"></a>Další kroky
Mějte na paměti, že stejně jako u všech diskusí optimalizace je potřeba provést testy před a po každé změně, aby se projevil dopad změny.  Optimalizace je podrobný proces, který má různé výsledky v různých počítačích ve vašem prostředí.  To, co pro jednu konfiguraci funguje, nemusí fungovat pro jiné.

Některé užitečné odkazy na další prostředky:

* [Uživatelská příručka nástroje Azure Linux Agent](../extensions/agent-linux.md)
* [Konfigurace softwarového pole RAID v systému Linux](configure-raid.md)
