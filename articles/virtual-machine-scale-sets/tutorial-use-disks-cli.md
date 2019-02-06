---
title: Kurz – Vytvoření a použití disků pro škálovací sady pomocí Azure CLI | Microsoft Docs
description: Zjistěte, jak pomocí Azure CLI vytvořit a používat spravované disky se škálovací sadou virtuálních počítačů, včetně přidání, přípravy, výpisu a odpojení disků.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 58090e860b79d59021d467fcf73596271c91c7f6
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55751153"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-the-azure-cli"></a>Kurz: Vytvoření a použití disků se škálovací sady virtuálních počítačů pomocí rozhraní příkazového řádku Azure
Škálovací sady virtuálních počítačů využívají disky k ukládání operačních systémů, aplikací a dat instancí virtuálních počítačů. Při vytváření a správě škálovací sady je důležité, abyste zvolili vhodnou velikost disku a konfiguraci pro očekávané úlohy. Tento kurz se zabývá vytvořením a správou disků virtuálních počítačů. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Disky s operačním systémem a dočasné disky
> * Datové disky
> * Disky Standard a Premium
> * Výkon disků
> * Připojení a příprava datových disků

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.29 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).


## <a name="default-azure-disks"></a>Výchozí disky v Azure
Při vytváření nebo škálování škálovací sady se ke každé instanci virtuálního počítače automaticky připojí dva disky.

**Disk s operačním systémem** – Disky s operačním systémem můžou mít velikost až 2 TB a hostují operační systém instance virtuálního počítače. Disk s operačním systémem má ve výchozím nastavení popisek */dev/sda*. Konfigurace ukládání do mezipaměti na disku je u disku s operačním systémem optimalizovaná s ohledem na výkon operačního systému. Vzhledem k této konfiguraci byste na disk s operačním systémem **neměli** ukládat aplikace nebo data. Pro aplikace a data použijte datové disky, které podrobněji probíráme dále v tomto článku.

**Dočasný disk** – Dočasné disky používají disk SSD, který je umístěný na stejném hostiteli Azure jako instance virtuálního počítače. Tyto disky mají vysoký výkon a můžou se používat pro operace, jako je zpracování dočasných dat. Pokud však dojde k přesunu instance virtuálního počítače na nového hostitele, všechna data uložená na dočasném disku se odeberou. Velikost dočasného disku se určuje podle velikosti instance virtuálního počítače. Dočasné disky mají popisek */dev/sdb* a mají přípojný bod */mnt*.

### <a name="temporary-disk-sizes"></a>Velikosti dočasného disku
| Type | Běžné velikosti | Maximální velikost dočasného disku (GiB) |
|----|----|----|
| [Obecné účely](../virtual-machines/linux/sizes-general.md) | Řady A, B a D | 1600 |
| [Optimalizované z hlediska výpočetních služeb](../virtual-machines/linux/sizes-compute.md) | Řada F | 576 |
| [Optimalizované z hlediska paměti](../virtual-machines/linux/sizes-memory.md) | Řady D, E, G a M | 6144 |
| [Optimalizované z hlediska úložiště](../virtual-machines/linux/sizes-storage.md) | Řada L | 5630 |
| [GPU](../virtual-machines/linux/sizes-gpu.md) | Řada N | 1440 |
| [Vysoký výkon](../virtual-machines/linux/sizes-hpc.md) | Řady A a H | 2000 |


## <a name="azure-data-disks"></a>Datové disky Azure
Pokud potřebujete instalovat aplikace a ukládat data, můžete přidat další datové disky. Datové disky by se měly používat v každé situaci, kdy se vyžaduje odolné a responzivní úložiště dat. Každý datový disk má maximální kapacitu 4 TB. Velikost instance virtuálního počítače určuje, kolik datových disků je možné připojit. Na každý virtuální procesor virtuálního počítače je možné připojit dva datové disky.

### <a name="max-data-disks-per-vm"></a>Maximum datových disků na virtuální počítač
| Type | Běžné velikosti | Maximum datových disků na virtuální počítač |
|----|----|----|
| [Obecné účely](../virtual-machines/linux/sizes-general.md) | Řady A, B a D | 64 |
| [Optimalizované z hlediska výpočetních služeb](../virtual-machines/linux/sizes-compute.md) | Řada F | 64 |
| [Optimalizované z hlediska paměti](../virtual-machines/linux/sizes-memory.md) | Řady D, E, G a M | 64 |
| [Optimalizované z hlediska úložiště](../virtual-machines/linux/sizes-storage.md) | Řada L | 64 |
| [GPU](../virtual-machines/linux/sizes-gpu.md) | Řada N | 64 |
| [Vysoký výkon](../virtual-machines/linux/sizes-hpc.md) | Řady A a H | 64 |


## <a name="vm-disk-types"></a>Typy disků virtuálního počítače
Azure poskytuje dva typy disků.

### <a name="standard-disk"></a>Disk Standard
Služba Storage úrovně Standard je založená na pevných discích a poskytuje nákladově efektivní úložiště a úroveň výkonu. Disky Standard jsou ideální pro nákladově efektivní vývoj a testování.

### <a name="premium-disk"></a>Disk Premium
Disky Premium jsou založené na vysoce výkonných discích SSD s nízkou latencí. Tyto disky se doporučují pro virtuální počítače, na kterých se spouští produkční úlohy. Služba Premium Storage podporuje virtuální počítače řad DS, DSv2, GS a FS. Při výběru se hodnota velikosti disku zaokrouhluje nahoru na nejbližší typ. Pokud je například velikost disku menší než 128 GB, typ disku je P10. Pokud je velikost disku mezi 129 až 512 GB, jde o typ (velikost) P20. V případě velikosti větší než 512 GB jde o typ P30.

### <a name="premium-disk-performance"></a>Výkon disků Premium
|Typ disku pro Premium Storage | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Velikost disku (zaokrouhluje se nahoru) | 32 GB | 64 GB | 128 GB | 512 GB | 1 024 GB (1 TB) | 2 048 GB (2 TB) | 4 095 GB (4 TB) |
| Maximum vstupně-výstupních operací za sekundu (IOPS) na disk | 120 | 240 | 500 | 2 300 | 5 000 | 7 500 | 7 500 |
Propustnost / disk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

V tabulce výše se sice uvádí maximum vstupně-výstupních operací za sekundu (IOPS), ale prokládáním více datových disků je možné dosáhnout i vyšší úrovně výkonu. Virtuální počítač Standard_GS5 může například dosáhnout maximálně 80 000 IOPS. Podrobné informace o maximálních hodnotách IOPS u virtuálních počítačů najdete v článku o [velikostech virtuálních počítačů s Linuxem](../virtual-machines/linux/sizes.md).


## <a name="create-and-attach-disks"></a>Vytvoření a připojení disků
Disky můžete vytvořit a připojit při vytváření škálovací sady nebo u existující škálovací sady.

### <a name="attach-disks-at-scale-set-creation"></a>Připojení disků při vytváření škálovací sady
Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). V tomto příkladu se vytvoří skupina prostředků s názvem *myResourceGroup* v oblasti *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Vytvořte škálovací sadu virtuálních počítačů pomocí příkazu [az vmss create](/cli/azure/vmss). Následující příklad vytvoří škálovací sadu *myScaleSet* a vygeneruje klíče SSH, pokud ještě neexistují. Pomocí parametru `--data-disk-sizes-gb` se vytvoří dva disky. První disk má velikost *64* GB a druhý disk *128* GB:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 64 128
```

Vytvoření a konfigurace všech prostředků škálovací sady a instancí virtuálních počítačů trvá několik minut.

### <a name="attach-a-disk-to-existing-scale-set"></a>Připojení disku k existující škálovací sadě
Disky můžete připojit také k existující škálovací sadě. Použijte škálovací sadu vytvořenou v předchozím kroku a přidejte další disk pomocí příkazu [az vmss disk attach](/cli/azure/vmss/disk). Následující příklad připojí další *128*GB disk:

```azurecli-interactive
az vmss disk attach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --size-gb 128
```


## <a name="prepare-the-data-disks"></a>Příprava datových disků
Disky, které se vytvoří a připojí k instancím virtuálních počítačů ve škálovací sadě, jsou holé disky. Než je budete moct použít pro svá data a aplikace, je potřeba disky připravit. Disky připravíte tak, že vytvoříte oddíl a systém souborů a připojíte je.

K automatizaci tohoto procesu napříč několika instancemi virtuálních počítačů ve škálovací sadě můžete použít rozšíření vlastních skriptů Azure. Toto rozšíření může na jednotlivých instancích virtuálních počítačů místně spouštět skripty, například pro přípravu připojených datových disků. Další informace najdete v tématu [Přehled rozšíření vlastních skriptů](../virtual-machines/linux/extensions-customscript.md).

Následující příklad na každé instanci virtuálního počítače pomocí příkazu [az vmss extension set](/cli/azure/vmss/extension) spustí skript z ukázkového úložiště GitHub, který připraví všechny připojené holé datové disky:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Pokud chcete potvrdit, že se disky správně připravily, připojte se přes SSH k některé z instancí virtuálních počítačů. Vypište informace o připojení pro vaši škálovací sadu pomocí příkazu [az vmss list-instance-connection-info](/cli/azure/vmss):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Připojte se k první instanci virtuálního počítače s použitím vlastní veřejné IP adresy a čísla portu, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
ssh azureuser@52.226.67.166 -p 50001
```

Následujícím způsobem prozkoumejte oddíly na instanci virtuálního počítače:

```bash
sudo fdisk -l
```

Následující příklad výstupu ukazuje, že jsou k instanci virtuálního počítače připojené tři datové disky – */dev/sdc*, */dev/sdd* a */dev/sde*. Každý z těchto disků obsahuje jeden oddíl, který využívá veškeré dostupné místo:

```bash
Disk /dev/sdc: 64 GiB, 68719476736 bytes, 134217728 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xa47874cb

Device     Boot Start       End   Sectors Size Id Type
/dev/sdc1        2048 134217727 134215680  64G 83 Linux

Disk /dev/sdd: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd5c95ca0

Device     Boot Start       End   Sectors  Size Id Type
/dev/sdd1        2048 268435455 268433408  128G 83 Linux

Disk /dev/sde: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x9312fdf5

Device     Boot Start       End   Sectors  Size Id Type
/dev/sde1        2048 268435455 268433408  128G 83 Linux
```

Následujícím způsobem prozkoumejte systémy souborů a přípojné body na instanci virtuálního počítače:

```bash
sudo df -h
```

Následující příklad výstupu ukazuje, že systémy souborů těchto tří disků jsou správně připojené k přípojnému bodu */datadisks*:

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.3G   28G   5% /
/dev/sdb1        50G   52M   47G   1% /mnt
/dev/sdc1        63G   52M   60G   1% /datadisks/disk1
/dev/sdd1       126G   60M  120G   1% /datadisks/disk2
/dev/sde1       126G   60M  120G   1% /datadisks/disk3
```

Disky na jednotlivých instancích virtuálních počítačů ve škálovací sadě se automaticky připraví stejným způsobem. Při případném vertikálním navýšení kapacity škálovací sady se požadované datové disky připojí k novým instancím virtuálních počítačů. Spustí se také rozšíření vlastních skriptů, které disky automaticky připraví.

Ukončete připojení SSH k instanci virtuálního počítače:

```bash
exit
```


## <a name="list-attached-disks"></a>Výpis připojených disků
Pokud chcete zobrazit informace o discích připojených ke škálovací sadě, použijte příkaz [az vmss show](/cli/azure/vmss) a dotaz na *virtualMachineProfile.storageProfile.dataDisks*:

```azurecli-interactive
az vmss show \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --query virtualMachineProfile.storageProfile.dataDisks
```

Zobrazí se informace o velikosti disku, úrovni úložiště a logické jednotce (LUN). Následující příklad výstupu ukazuje podrobnosti o třech datových discích připojených ke škálovací sadě:

```json
[
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 64,
    "lun": 0,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 1,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 2,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  }
]
```


## <a name="detach-a-disk"></a>Odpojení disku
Pokud už daný disk nepotřebujete, můžete ho od škálovací sady odpojit. Disk se odebere ze všech instancí virtuálních počítačů ve škálovací sadě. K odpojení disku od škálovací sady použijte příkaz [az vmss disk detach](/cli/azure/vmss/disk) a zadejte logickou jednotku (LUN) disku. Logické jednotky (LUN) se zobrazí ve výstupu příkazu [az vmss show](/cli/azure/vmss) z předchozí části. Následující příklad odpojí od škálovací sady logickou jednotku (LUN) *2*:

```azurecli-interactive
az vmss disk detach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --lun 2
```


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete odebrat škálovací sadu a disky, odstraňte skupinu prostředků a všechny její prostředky pomocí příkazu [az group delete](/cli/azure/group). Parametr `--no-wait` vrátí řízení na příkazový řádek bez čekání na dokončení operace. Parametr `--yes` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak vytvořit a používat disky se škálovacími sadami pomocí Azure CLI:

> [!div class="checklist"]
> * Disky s operačním systémem a dočasné disky
> * Datové disky
> * Disky Standard a Premium
> * Výkon disků
> * Připojení a příprava datových disků

V dalším kurzu se dozvíte, jak pro instance virtuálních počítačů ve škálovací sadě použít vlastní image.

> [!div class="nextstepaction"]
> [Použití vlastní image pro instance virtuálních počítačů ve škálovací sadě](tutorial-use-custom-image-cli.md)
