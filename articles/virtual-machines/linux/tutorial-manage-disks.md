---
title: Kurz – Správa disků v Azure pomocí Azure CLI | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí Azure CLI 2.0 vytvářet a spravovat disky Azure pro virtuální počítače.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/30/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 470e938aa4ef70f9682528003ccdaba2ef6cc013
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839686"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli-20"></a>Kurz – Správa disků v Azure pomocí Azure CLI 2.0

Virtuální počítače Azure využívají disky k ukládání svých operačních systémů, aplikací a dat. Při vytváření virtuálního počítače je důležité, abyste zvolili vhodnou velikost disku a konfiguraci s ohledem na očekávané úlohy. V tomto kurzu se dozvíte, jak nasadit a spravovat disky virtuálních počítačů. Dozvíte se o těchto tématech:

> [!div class="checklist"]
> * Disky s operačním systémem a dočasné disky
> * Datové disky
> * Disky Standard a Premium
> * Výkon disků
> * Připojení a příprava datových disků
> * Změna velikosti disků
> * Snímky disků

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte pro tento kurz mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="default-azure-disks"></a>Výchozí disky v Azure

Při vytvoření virtuálního počítače Azure se k němu automaticky připojí dva disky.

**Disk s operačním systémem:** Disky s operačním systémem hostují operační systém virtuálního počítače a mohou mít velikost až 2 TB. Disk s operačním systémem má ve výchozím nastavení popisek */dev/sda*. Konfigurace ukládání do mezipaměti na disku je u disku s operačním systémem optimalizovaná s ohledem na výkon operačního systému. Vzhledem k této konfiguraci byste disk s operačním systémem **neměli** používat k ukládání aplikací nebo dat. Pro aplikace a data použijte datové disky, které podrobněji probíráme dále v tomto kurzu.

**Dočasný disk:** Dočasné disky používají jednotku SSD, která je umístěná na stejném hostiteli Azure jako virtuální počítač. Dočasné disky mají vysoký výkon a můžou se používat pro operace, jako je zpracování dočasných dat. V případě přesunutí virtuálního počítače na nového hostitele se ale všechna data uložená na dočasném disku odeberou. Velikost dočasného disku se určuje podle velikosti virtuálního počítače. Dočasné disky mají popisek */dev/sdb* a mají přípojný bod */mnt*.

### <a name="temporary-disk-sizes"></a>Velikosti dočasného disku

| Typ | Běžné velikosti | Maximální velikost dočasného disku (GiB) |
|----|----|----|
| [Obecné účely](sizes-general.md) | Řady A, B a D | 1600 |
| [Optimalizované z hlediska výpočetních služeb](sizes-compute.md) | Řada F | 576 |
| [Optimalizované z hlediska paměti](sizes-memory.md) | Řady D, E, G a M | 6144 |
| [Optimalizované z hlediska úložiště](sizes-storage.md) | Řada L | 5630 |
| [GPU](sizes-gpu.md) | Řada N | 1440 |
| [Vysoký výkon](sizes-hpc.md) | Řady A a H | 2000 |

## <a name="azure-data-disks"></a>Datové disky Azure

Pokud potřebujete instalovat aplikace a ukládat data, můžete přidat další datové disky. Datové disky by se měly používat v každé situaci, kdy se vyžaduje odolné a responzivní úložiště dat. Každý datový disk má maximální kapacitu 4 TB. Velikost virtuálního počítače určuje, kolik datových disků se k němu může připojit. Na každý virtuální procesor virtuálního počítače je možné připojit dva datové disky.

### <a name="max-data-disks-per-vm"></a>Maximum datových disků na virtuální počítač

| Typ | Velikost virtuálního počítače | Maximum datových disků na virtuální počítač |
|----|----|----|
| [Obecné účely](sizes-general.md) | Řady A, B a D | 64 |
| [Optimalizované z hlediska výpočetních služeb](sizes-compute.md) | Řada F | 64 |
| [Optimalizované z hlediska paměti](../virtual-machines-windows-sizes-memory.md) | Řady D, E a G | 64 |
| [Optimalizované z hlediska úložiště](../virtual-machines-windows-sizes-storage.md) | Řada L | 64 |
| [GPU](sizes-gpu.md) | Řada N | 64 |
| [Vysoký výkon](sizes-hpc.md) | Řady A a H | 64 |

## <a name="vm-disk-types"></a>Typy disků virtuálního počítače

Azure poskytuje dva typy disků.

### <a name="standard-disk"></a>Disk Standard

Služba Storage úrovně Standard je založená na jednotkách HDD a poskytuje nákladově efektivní úložiště se zachováním výkonu. Disky Standard jsou ideální pro nákladově efektivní vývoj a testování.

### <a name="premium-disk"></a>Disk Premium

Disky Premium jsou založené na vysoce výkonných discích SSD s nízkou latencí. Jsou ideální pro virtuální počítače s produkčními úlohami. Služba Premium Storage podporuje virtuální počítače řad DS, DSv2, GS a FS. Při výběru se hodnota velikosti disku zaokrouhluje nahoru na nejbližší typ. Pokud je například velikost disku menší než 128 GB, typ disku je P10. Pokud je velikost disku mezi 129 až 512 GB, jde o typ (velikost) P20. V případě velikosti větší než 512 GB jde o typ P30.

### <a name="premium-disk-performance"></a>Výkon disků Premium

|Typ disku pro Premium Storage | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Velikost disku (zaokrouhluje se nahoru) | 32 GB | 64 GB | 128 GB | 512 GB | 1 024 GB (1 TB) | 2 048 GB (2 TB) | 4 095 GB (4 TB) |
| Maximum vstupně-výstupních operací za sekundu (IOPS) na disk | 120 | 240 | 500 | 2 300 | 5 000 | 7 500 | 7 500 |
Propustnost / disk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

V tabulce výše se sice uvádí maximum vstupně-výstupních operací za sekundu (IOPS), ale prokládáním více datových disků je možné dosáhnout i vyšší úrovně výkonu. Virtuální počítač Standard_GS5 může například dosáhnout maximálně 80 000 IOPS. Podrobné informace o maximálních hodnotách IOPS u virtuálních počítačů najdete v článku o [velikostech virtuálních počítačů s Linuxem](sizes.md).

## <a name="create-and-attach-disks"></a>Vytvoření a připojení disků

Datové disky můžete vytvořit a připojit při vytváření virtuálního počítače nebo k existujícímu virtuálnímu počítači.

### <a name="attach-disk-at-vm-creation"></a>Připojení disku při vytváření virtuálního počítače

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create).

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az-vm-create). Následující příklad vytvoří virtuální počítač s názvem *myVM*, přidá uživatelský účet s názvem *azureuser* a vygeneruje klíče SSH, pokud ještě neexistují. Pomocí argumentu `--datadisk-sizes-gb` je možné určit, že se má vytvořit další disk a připojit k virtuálnímu počítači. Pokud chcete vytvořit a připojit více než jeden disk, použijte seznam hodnot velikostí disků oddělený mezerami. V následujícím příkladu se virtuální počítač vytvoří se dvěma datovými disky, které budou mít oba velikost 128 GB. Vzhledem k velikosti disků 128 GB se oba tyto disky nakonfigurují jako typ P10, který poskytuje maximálně 500 IOPS na disk.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Připojení disku k existujícímu virtuálnímu počítači

Pokud chcete vytvořit nový disk a připojit ho k existujícímu virtuálnímu počítači, použijte příkaz [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach). Následující příklad vytvoří disk Premium o velikosti 128 gigabajtů a připojí ho k virtuálnímu počítači vytvořenému v předchozím kroku.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --disk myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Příprava datových disků

Po připojení disku k virtuálnímu počítači je třeba nakonfigurovat operační systém tak, aby mohl disk používat. Následující příklad ukazuje postup při ruční konfiguraci disku. Tento proces je také možné automatizovat pomocí sady nástrojů cloud-init, které se věnujeme v [tomto kurzu](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Ruční konfigurace

Vytvořte připojení SSH k virtuálnímu počítači. Ukázkovou IP adresu nahraďte veřejnou IP adresou virtuálního počítače.

```azurecli-interactive
ssh azureuser@52.174.34.95
```

Rozdělte disk na oddíly pomocí příkazu `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Zapište na oddíl systém souborů pomocí příkazu `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Připojte nový disk, aby byl přístupný v operačním systému.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

Disk je teď přístupný prostřednictvím přípojného bodu *datadrive*, což můžete ověřit spuštěním příkazu `df -h`.

```bash
df -h
```

Ve výstupu uvidíte novou jednotku připojenou na přípojném bodu */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Aby se zajistilo, že se jednotka znovu připojí po restartování systému, je třeba ji přidat do souboru */etc/fstab*. Abyste to mohli udělat, získejte identifikátor UUID disku pomocí nástroje `blkid`.

```bash
sudo -i blkid
```

Ve výstupu se zobrazí identifikátor UUID jednotky, v tomto případě `/dev/sdc1`.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Přidejte do souboru */etc/fstab* řádek podobný následujícímu.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail   1  2
```

Po dokončení konfigurace disku zavřete relaci SSH.

```bash
exit
```

## <a name="resize-vm-disk"></a>Změna velikosti disku virtuálního počítače

Po nasazení virtuálního počítače je možné zvýšit velikost disku s operačním systémem nebo jakýchkoliv připojených datových disků. Zvětšení disku je vhodné v případě, že potřebujete další úložný prostor nebo vyšší úroveň výkonu (například P10, P20 nebo P30). Velikost disků není možné snížit.

Před zvětšením disku budete potřebovat ID nebo název disku. Seznam všech disků ve skupině prostředků můžete získat pomocí příkazu [az disk list](/cli/azure/disk#az-disk-list). Poznamenejte si název disku, jehož velikost chcete změnit.

```azurecli-interactive
az disk list \
    --resource-group myResourceGroupDisk \
    --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
    --output table
```

Přidělení virtuálního počítače je nutné zrušit. K zastavení a zrušení přidělení virtuálního počítače použijte příkaz [az vm deallocate](/cli/azure/vm#az-vm-deallocate).

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupDisk --name myVM
```

Velikost disku změňte pomocí příkazu [az disk update](/cli/azure/vm/disk#az-vm-disk-update). V tomto příkladu se změní velikost disku s názvem *myDataDisk* na 1 terabajt.

```azurecli-interactive
az disk update --name myDataDisk --resource-group myResourceGroupDisk --size-gb 1023
```

Po dokončení operace změny velikosti spusťte virtuální počítač.

```azurecli-interactive
az vm start --resource-group myResourceGroupDisk --name myVM
```

Pokud jste změnili velikost disku s operačním systémem, oddíl se automaticky rozšíří. Když změníte velikost datového disku, je potřeba aktuální oddíly rozšířit v operačním systému virtuálního počítače.

## <a name="snapshot-azure-disks"></a>Vytváření snímků disků v Azure

Když pořídíte snímek disku, Azure vytvoří kopii disku k danému okamžiku určenou jen pro čtení. Snímky virtuálních počítačů Azure jsou užitečné k rychlému uložení stavu virtuálního počítače před změnou konfigurace. Pokud se změny konfigurace ukáží jako nevhodné, je možné obnovit stav virtuálního počítače pomocí snímku. Pokud má virtuální počítač více než jeden disk, pořizuje se snímek každého disku nezávisle na ostatních. V zájmu vytváření konzistentních záloh (vzhledem k aplikacím) zvažte možnost virtuální počítač před pořizováním snímků zastavit. Můžete také použít [službu Azure Backup](/azure/backup/), která umožňuje provádět automatizované zálohování spuštěného virtuálního počítače.

### <a name="create-snapshot"></a>Vytvoření snímku

Před vytvořením snímku disku virtuálního počítače potřebujete ID nebo název disku. Ke zjištění ID disku můžete použít příkaz [az vm show](/cli/azure/vm#az-vm-show). V tomto příkladu se ID disku uloží do proměnné, aby se mohlo použít v pozdějším kroku.

```azurecli-interactive
osdiskid=$(az vm show -g myResourceGroupDisk -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Jakmile máte ID disku virtuálního počítače, můžete jeho snímek vytvořit pomocí následujícího příkazu.

```azurcli
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Vytvoření disku ze snímku

Tento snímek pak můžete převést na disk a jeho pomocí znovu vytvořit virtuální počítač.

```azurecli-interactive
az disk create --resource-group myResourceGroupDisk --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Obnovení virtuálního počítače ze snímku

Abychom si předvedli obnovení virtuálního počítače, odstraníme napřed existující virtuální počítač.

```azurecli-interactive
az vm delete --resource-group myResourceGroupDisk --name myVM
```

Nový virtuální počítač vytvoříme ze snímku disku.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Opětovné připojení datového disku

K virtuálnímu počítači bude potřeba znovu připojit všechny datové disky.

Napřed zjistěte název datového disku pomocí příkazu [az disk list](/cli/azure/disk#az-disk-list). V tomto příkladu se název disku uloží do proměnné s názvem *datadisk*, která se použije v dalším kroku.

```azurecli-interactive
datadisk=$(az disk list -g myResourceGroupDisk --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Disk můžete připojit pomocí příkazu [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach).

```azurecli-interactive
az vm disk attach –g myResourceGroupDisk –-vm-name myVM –-disk $datadisk
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s disky virtuálních počítačů, jako jsou:

> [!div class="checklist"]
> * Disky s operačním systémem a dočasné disky
> * Datové disky
> * Disky Standard a Premium
> * Výkon disků
> * Připojení a příprava datových disků
> * Změna velikosti disků
> * Snímky disků

V dalším kurzu se dozvíte, jak automatizovat konfiguraci virtuálních počítačů.

> [!div class="nextstepaction"]
> [Automatizace konfigurace virtuálních počítačů](./tutorial-automate-vm-deployment.md)
