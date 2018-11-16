---
title: Kurz – Správa disků v Azure pomocí Azure CLI | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí Azure CLI vytvářet a spravovat disky Azure pro virtuální počítače.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 69ffd2dd4df8ca0a64036f7a96c88d5c83353211
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685363"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Kurz – Správa disků v Azure pomocí Azure CLI

Virtuální počítače Azure využívají disky k ukládání svých operačních systémů, aplikací a dat. Při vytváření virtuálního počítače je důležité, abyste zvolili vhodnou velikost disku a konfiguraci s ohledem na očekávané úlohy. V tomto kurzu se dozvíte, jak nasadit a spravovat disky virtuálních počítačů. Dozvíte se o těchto tématech:

> [!div class="checklist"]
> * Disky s operačním systémem a dočasné disky
> * Datové disky
> * Disky Standard a Premium
> * Výkon disků
> * Připojení a příprava datových disků
> * Změna velikosti disků
> * Snímky disků


## <a name="default-azure-disks"></a>Výchozí disky v Azure

Při vytvoření virtuálního počítače Azure se k němu automaticky připojí dva disky.

**Disk s operačním systémem:** Disky s operačním systémem hostují operační systém virtuálního počítače a mohou mít velikost až 2 TB. Disk s operačním systémem má ve výchozím nastavení popisek */dev/sda*. Konfigurace ukládání do mezipaměti na disku je u disku s operačním systémem optimalizovaná s ohledem na výkon operačního systému. Vzhledem k této konfiguraci byste disk s operačním systémem **neměli** používat k ukládání aplikací nebo dat. Pro aplikace a data použijte datové disky, které podrobněji probíráme dále v tomto kurzu.

**Dočasný disk:** Dočasné disky používají jednotku SSD, která je umístěná na stejném hostiteli Azure jako virtuální počítač. Dočasné disky mají vysoký výkon a můžou se používat pro operace, jako je zpracování dočasných dat. V případě přesunutí virtuálního počítače na nového hostitele se ale všechna data uložená na dočasném disku odeberou. Velikost dočasného disku se určuje podle velikosti virtuálního počítače. Dočasné disky mají popisek */dev/sdb* a mají přípojný bod */mnt*.


## <a name="azure-data-disks"></a>Datové disky Azure

Pokud potřebujete instalovat aplikace a ukládat data, můžete přidat další datové disky. Datové disky by se měly používat v každé situaci, kdy se vyžaduje odolné a responzivní úložiště dat. Každý datový disk má maximální kapacitu 4 TB. Velikost virtuálního počítače určuje, kolik datových disků se k němu může připojit. Na každý virtuální procesor virtuálního počítače je možné připojit čtyři datové disky.


## <a name="vm-disk-types"></a>Typy disků virtuálního počítače

Azure nabízí dva typy disků úrovně standard a Premium.

### <a name="standard-disk"></a>Disk Standard

Služba Storage úrovně Standard je založená na jednotkách HDD a poskytuje nákladově efektivní úložiště se zachováním výkonu. Disky Standard jsou ideální pro nákladově efektivní vývoj a testování.

### <a name="premium-disk"></a>Disk Premium

Disky Premium jsou založené na vysoce výkonných discích SSD s nízkou latencí. Jsou ideální pro virtuální počítače s produkčními úlohami. Služba Premium Storage podporuje virtuální počítače řad DS, DSv2, GS a FS. Při výběru se hodnota velikosti disku zaokrouhluje nahoru na nejbližší typ. Pokud je například velikost disku menší než 128 GB, typ disku je P10. Pokud je velikost disku mezi 129 až 512 GB, jde o typ (velikost) P20. V případě velikosti větší než 512 GB jde o typ P30.

### <a name="premium-disk-performance"></a>Výkon disků Premium

|Typ disku pro Premium Storage | P4 | P6 | P10 | P20 | P30 | P40 | P50 | P60 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Velikost disku (zaokrouhluje se nahoru) | 32 GiB | 64 GiB | 128 GiB | 512 GiB | 1 024 GiB (1 TiB) | 2 048 GiB (2 TiB) | 4 095 GiB (4 TiB) | 8 192 GiB (8 TiB)
| Maximum vstupně-výstupních operací za sekundu (IOPS) na disk | 120 | 240 | 500 | 2 300 | 5 000 | 7 500 | 7 500 | 12 500 |
Propustnost / disk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s | 480 MB/s |

V tabulce výše se sice uvádí maximum vstupně-výstupních operací za sekundu (IOPS), ale prokládáním více datových disků je možné dosáhnout i vyšší úrovně výkonu. Virtuální počítač Standard_GS5 může například dosáhnout maximálně 80 000 IOPS. Podrobné informace o maximálních hodnotách IOPS u virtuálních počítačů najdete v článku o [velikostech virtuálních počítačů s Linuxem](sizes.md).


## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/powershell](https://shell.azure.com/bash). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

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


## <a name="snapshot-a-disk"></a>Pořízení snímku disku

Když pořídíte snímek disku, Azure vytvoří kopii disku k danému okamžiku určenou jen pro čtení. Snímky virtuálních počítačů Azure jsou užitečné k rychlému uložení stavu virtuálního počítače před změnou konfigurace. V případě problému nebo chyby je možné obnovit virtuální počítač pomocí snímku. Pokud má virtuální počítač více než jeden disk, pořizuje se snímek každého disku nezávisle na ostatních. V zájmu vytváření konzistentních záloh (vzhledem k aplikacím) zvažte možnost virtuální počítač před pořizováním snímků zastavit. Můžete také použít [službu Azure Backup](/azure/backup/), která umožňuje provádět automatizované zálohování spuštěného virtuálního počítače.

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

## <a name="next-steps"></a>Další postup

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
