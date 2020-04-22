---
title: Optimalizace virtuálního počítače s Linuxem v Azure
description: Naučte se několik optimalizačních tipů, abyste měli jistotu, že jste nastavili virtuální počítač s Linuxem pro optimální výkon v Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 87776c14e45ff4bb3cce6661323d74a1315c8ab2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757101"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Optimalizace virtuálního počítače s Linuxem v Azure
Vytvoření virtuálního počítače (VM) Linux uděláte snadno z příkazového řádku nebo z portálu. V tomto kurzu se můžete ujistit, že jste ho nastavili tak, aby optimalizoval její výkon na platformě Microsoft Azure. Toto téma používá Ubuntu Server VM, ale můžete také vytvořit linuxový virtuální stroj pomocí [vlastních obrázků jako šablon](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Požadavky
Toto téma předpokládá, že už máte funkční předplatné Azure[(registrace zkušební verze zdarma)](https://azure.microsoft.com/pricing/free-trial/)a už jste do předplatného Azure zřídit virtuální počítač. Ujistěte se, že máte nejnovější [Azure CLI](/cli/azure/install-az-cli2) nainstalována a přihlášena k předplatnému Azure s [az přihlášení](/cli/azure/reference-index) před [vytvořením virtuálního počítače](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Disk azure operačního systému
Jakmile vytvoříte virtuální počítač s Linuxem v Azure, má k němu přidružené dva disky. **/dev/sda** je váš disk operačního systému, **/dev/sdb** je dočasný disk.  Nepoužívejte hlavní disk operačního systému (**/dev/sda**) pro nic kromě operačního systému, protože je optimalizovaný pro rychlé spuštění virtuálního počítače a neposkytuje dobrý výkon pro vaše úlohy. Chcete k virtuálnímu počítači připojit jeden nebo více disků, abyste získali trvalé a optimalizované úložiště pro vaše data. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Přidání disků pro cíle velikosti a výkonu
Na základě velikosti virtuálního počítače můžete připojit až 16 dalších disků na dřadu A, 32 disků na d-series a 64 disků na počítači řady G – každý o velikosti až 32 TB. Další disky přidáte podle potřeby podle vašeho místa a požadavků vis. Každý disk má cíl výkonu 500 iOps pro standardní úložiště a až 20 000 vinami na disk pro úložiště Premium.

Chcete-li dosáhnout nejvyšších iOps na disky úložiště Premium, kde jejich nastavení mezipaměti byly nastaveny buď **ReadOnly** nebo **Žádný**, musíte zakázat **překážky** při montáži systému souborů v Linuxu. Nepotřebujete překážky, protože zápisy na disky zálohované úložiště majevky premium jsou trvalé pro tato nastavení mezipaměti.

* Pokud používáte **reiserFS**, zakažte bariéry pomocí `barrier=none` `barrier=flush`možnosti připojení (Pro povolení bariér použijte)
* Pokud používáte **ext3/ext4**, zakažte bariéry pomocí `barrier=0` `barrier=1`možnosti připojení (Pro povolení bariér použijte)
* Pokud používáte **XFS**, zakažte bariéry pomocí `nobarrier` možnosti `barrier`připojení (Pro povolení bariér použijte možnost )

## <a name="unmanaged-storage-account-considerations"></a>Důležité informace o účtu nespravovaného úložiště
Výchozí akce při vytváření virtuálního počítače s Azure CLI je použití spravovaných disků Azure.  Tyto disky jsou zpracovány platformou Azure a nevyžadují žádnou přípravu nebo umístění k jejich uložení.  Nespravované disky vyžadují účet úložiště a mají některé další aspekty výkonu.  Další informace o spravovaných discích najdete v tématu [Přehled služby Azure Managed Disks](../windows/managed-disks-overview.md).  Následující část popisuje aspekty výkonu pouze v případě, že používáte nespravované disky.  Opět platí, že výchozí a doporučené řešení úložiště je použití spravovaných disků.

Pokud vytvoříte virtuální počítač s nespravovanými disky, ujistěte se, že připojíte disky z účtů úložiště, které se nachází ve stejné oblasti jako virtuální počítač, abyste zajistili blízkost a minimalizovali latenci sítě.  Každý účet úložiště Standard má maximálně 20 kB via a kapacitu velikosti 500 TB.  Toto omezení funguje na přibližně 40 silně používaných disků, včetně disku operačního systému a všech datových disků, které vytvoříte. Pro účty úložiště Premium neexistuje žádný limit maximální viš, ale existuje limit velikosti 32 TB. 

Při práci s vysokými úlohami viops a jste zvolili standardní úložiště pro vaše disky, možná budete muset rozdělit disky mezi více účtů úložiště, abyste se ujistili, že jste nedosáhli limitu 20 000 iOps pro účty standard storage. Virtuální počítač může obsahovat kombinaci disků z různých účtů úložiště a typů účtů úložiště k dosažení optimální konfigurace.
 

## <a name="your-vm-temporary-drive"></a>Váš dočasný disk virtuálního počítače
Ve výchozím nastavení při vytváření virtuálního počítače vám Azure poskytuje disk operačního systému (**/dev/sda**) a dočasný disk (**/dev/sdb**).  Všechny další disky, které přidáte, se zobrazí jako **/dev/sdc**, **/dev/sdd**, **/dev/sde** a tak dále. Všechna data na dočasném disku (**/dev/sdb**) není trvalá a může být ztracena, pokud konkrétní události, jako je změna velikosti virtuálního počítače, opětovné nasazení nebo údržba, vynutí restartování virtuálního počítače.  Velikost a typ dočasného disku souvisí s velikostí virtuálního počítače, kterou jste zvolili v době nasazení. Všechny virtuální počítače velikosti premium (DS, G a DS_V2 řady) dočasné jednotky jsou zálohovány místní SSD pro další výkon až 48k VOps. 

## <a name="linux-swap-partition"></a>Odkládací oddíl Linuxu
Pokud váš virtuální počítač Azure pochází z bitové kopie Ubuntu nebo CoreOS, můžete pomocí CustomData odeslat cloudovou konfiguraci do cloud-initu. Pokud jste [nahráli vlastní bitovou kopii Linuxu,](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) která používá cloud-init, můžete také nakonfigurovat odkládací oddíly pomocí cloud-init.

Na Ubuntu Cloud Images musíte ke konfiguraci odkládacího oddílu použít cloud-init. Další informace naleznete v tématu [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Pro image bez podpory cloud-init, image virtuálních počítače nasazené z Azure Marketplace mají Agent pro Virtuální Počítač Linux integrovaný s operačním systémem. Tento agent umožňuje virtuálnímu počítači komunikovat s různými službami Azure. Za předpokladu, že jste nasadili standardní bitovou kopii z Azure Marketplace, budete muset udělat následující kroky, abyste správně nakonfigurovali nastavení odkládacího souboru Linuxu:

Vyhledejte a upravte dvě položky v souboru **/etc/waagent.conf.** Řídí existenci vyhrazeného odkládacího souboru a velikost odkládacího souboru. Parametry, které je třeba `ResourceDisk.EnableSwap` ověřit, jsou a`ResourceDisk.SwapSizeMB` 

Chcete-li povolit správně povolený disk a připojený odkládací soubor, ujistěte se, že parametry mají následující nastavení:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size in MB to meet your needs} 

Jakmile jste provedli změnu, musíte restartovat waagent nebo restartovat virtuální počítač SIP, aby tyto změny odrážely.  Víte, že změny byly implementovány a odkládací `free` soubor byl vytvořen při použití příkazu k zobrazení volného místa. Následující příklad obsahuje odkládací soubor 512 MB vytvořený v důsledku úpravy souboru **waagent.conf:**

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Vstupně-no su-li plánování algoritmus pro úložiště Premium
S jádrem 2.6.18 linuxového jádra byl výchozí algoritmus plánování V/O změněn z Deadline na CFQ (Algoritmus zcela spravedlivého řazení do fronty). Pro vstupně-va/o vzorky náhodného přístupu je zanedbatelný rozdíl ve výkonu rozdíly mezi CFQ a konečný termín.  U disků založených na SSD, kde je vstupně-vstupně-vně disku převážně sekvenční, může přepnutí zpět na algoritmus NOOP nebo Deadline dosáhnout lepšího výkonu vstupně-va/o.

### <a name="view-the-current-io-scheduler"></a>Zobrazení aktuálního plánovače vstupně-v/V
Použijte následující příkaz:  

```bash
cat /sys/block/sda/queue/scheduler
```

Zobrazí se následující výstup, který označuje aktuální plánovač.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Změna aktuálního zařízení (/dev/sda) plánovacího algoritmu vstupně-in
Použijte následující příkazy:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Použití tohoto nastavení pro **/dev/sda** sám není užitečné. Nastavte na všech datových discích, kde vstupně-v.,O dominuje vstupně-va.  

Měli byste vidět následující výstup označující, že **grub.cfg** byl úspěšně znovu sestaven a že výchozí plánovač byl aktualizován na NOOP.  

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

Pro distribuční rodinu Red Hat potřebujete pouze následující příkaz:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Použití softwarového raidu k dosažení vyšších i/ops
Pokud vaše úlohy vyžadují více iOpů, než může poskytnout jeden disk, musíte použít konfiguraci softwarového raidu na více discích. Vzhledem k tomu, že Azure již provádí odolnost disku na místní vrstvě infrastruktury, dosáhnete nejvyšší úrovně výkonu z konfigurace prokládání RAID-0.  Zřiďte a vytvořte disky v prostředí Azure a připojte je k virtuálnímu počítači SIF před rozdělením, formátováním a montáží jednotek.  Další podrobnosti o konfiguraci nastavení softwarového RAID na vašem virtuálním počítači s Linuxem v Azure najdete v dokumentu **[Configuring Software RAID on Linux.](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Jako alternativu k tradiční konfiguraci RAID můžete také nainstalovat Správce logických svazků (LVM) a nakonfigurovat tak počet fyzických disků do jednoho proklápěného svazku logického úložiště. V této konfiguraci jsou čtení a zápisy distribuovány na více disků obsažených ve skupině svazků (podobně jako raid0). Z důvodů výkonu je pravděpodobné, že budete chtít prokládat logické svazky tak, aby čtení a zápisy využívaly všechny připojené datové disky.  Další podrobnosti o konfiguraci proklápěcí hologického svazku na virtuálním počítači s Linuxem v Azure najdete v **[dokumentu Konfigurace LVM na virtuálním počítači s Linuxem v Azure.](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

## <a name="next-steps"></a>Další kroky
Nezapomeňte, že stejně jako u všech diskusí optimalizace je třeba provést testy před a po každé změně, abyste změřili dopad změny.  Optimalizace je krok za krokem proces, který má různé výsledky v různých počítačích ve vašem prostředí.  Co funguje pro jednu konfiguraci nemusí fungovat pro ostatní.

Některé užitečné odkazy na další zdroje:

* [Uživatelská příručka nástroje Azure Linux Agent](../extensions/agent-linux.md)
* [Konfigurace softwarového raidu v Linuxu](configure-raid.md)
