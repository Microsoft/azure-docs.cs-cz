---
title: Kurz – Správa disků v Azure pomocí Azure CLI
description: V tomto kurzu zjistíte, jak pomocí Azure CLI vytvářet a spravovat disky Azure pro virtuální počítače.
author: cynthn
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 08/20/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.subservice: disks
ms.openlocfilehash: bbecaa32f85c42954cea6c8e533f0f658eb2dfee
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802280"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Kurz – Správa disků v Azure pomocí Azure CLI

Virtuální počítače Azure využívají disky k ukládání svých operačních systémů, aplikací a dat. Když vytváříte virtuální počítač, je důležité zvolit velikost disku a konfiguraci odpovídající očekávanému zatížení. V tomto kurzu se dozvíte, jak nasadit a spravovat disky virtuálních počítačů. Dozvíte se o těchto tématech:

> [!div class="checklist"]
> * Disky s operačním systémem a dočasné disky
> * Datové disky
> * Disky Standard a Premium
> * Výkon disků
> * Připojení a příprava datových disků
> * Snímky disků


## <a name="default-azure-disks"></a>Výchozí disky v Azure

Při vytvoření virtuálního počítače Azure se k němu automaticky připojí dva disky.

**Disk s operačním systémem:** Disky s operačním systémem hostují operační systém virtuálního počítače a mohou mít velikost až 2 TB. Disk s operačním systémem má ve výchozím nastavení popisek */dev/sda*. Konfigurace ukládání do mezipaměti na disku je u disku s operačním systémem optimalizovaná s ohledem na výkon operačního systému. Vzhledem k této konfiguraci byste disk s operačním systémem **neměli** používat k ukládání aplikací nebo dat. Pro aplikace a data použijte datové disky, které podrobněji probíráme dále v tomto kurzu.

**Dočasný disk:** Dočasné disky používají jednotku SSD, která je umístěná na stejném hostiteli Azure jako virtuální počítač. Dočasné disky mají vysoký výkon a můžou se používat pro operace, jako je zpracování dočasných dat. V případě přesunutí virtuálního počítače na nového hostitele se ale všechna data uložená na dočasném disku odeberou. Velikost dočasného disku se určuje podle velikosti virtuálního počítače. Dočasné disky mají popisek */dev/sdb* a mají přípojný bod */mnt*.

## <a name="azure-data-disks"></a>Datové disky Azure

Pokud potřebujete instalovat aplikace a ukládat data, můžete přidat další datové disky. Datové disky by se měly používat v každé situaci, kdy se vyžaduje odolné a responzivní úložiště dat. Velikost virtuálního počítače určuje, kolik datových disků se k němu může připojit.

## <a name="vm-disk-types"></a>Typy disků virtuálního počítače

Azure poskytuje dva typy disků.

**Disky Standard:** využívají pevné disky a poskytují nákladově efektivní úložiště se zachováním výkonu. Disky Standard jsou ideální pro nákladově efektivní vývoj a testování.

**Disky Premium založené na discích** SSD s vysokým výkonem a nízkou latencí. Jsou ideální pro virtuální počítače s produkčními úlohami. Velikosti virtuálních počítačů s názvem  **s** v [názvu velikosti](../vm-naming-conventions.md), obvykle podporují Premium Storage. Například virtuální počítače DS-Series, DSv2-Series, GS-Series a FS-series podporují Prémiové úložiště. Při výběru se hodnota velikosti disku zaokrouhluje nahoru na nejbližší typ. Například pokud je velikost disku větší než 64 GB, ale menší než 128 GB, je typ disku P10. 

<br>


[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Když zřizujete disk služby Premium Storage na rozdíl od standardního úložiště, zaručujete si kapacitu, IOPS a propustnost tohoto disku. Pokud například vytvoříte P50 disk, Azure zřídí 4 095 GB kapacity úložiště, 7 500 IOPS a propustnost 250 MB/s pro tento disk. Vaše aplikace může využívat celou kapacitu a výkon. SSD úrovně Premium disky jsou navržené tak, aby poskytovaly nízké latence v řádu milisekund a cílové IOPS a propustnost popsané v předchozí tabulce 99,9% času.

V tabulce výše se sice uvádí maximum vstupně-výstupních operací za sekundu (IOPS), ale prokládáním více datových disků je možné dosáhnout i vyšší úrovně výkonu. Například k virtuálnímu počítači Standard_GS5 je možné připojit 64 datových disků. Pokud je velikost každého z těchto disků P30, můžete dosáhnout maximální hodnoty 80 000 IOPS. Podrobné informace o maximálních hodnotách IOPS u virtuálních počítačů najdete v článku o [velikostech a typech virtuálních počítačů](../sizes.md).

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/bash) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

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
    --name myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Příprava datových disků

Po připojení disku k virtuálnímu počítači je třeba nakonfigurovat operační systém tak, aby mohl disk používat. Následující příklad ukazuje postup při ruční konfiguraci disku. Tento proces je také možné automatizovat pomocí sady nástrojů cloud-init, které se věnujeme v [tomto kurzu](./tutorial-automate-vm-deployment.md).


Vytvořte připojení SSH k virtuálnímu počítači. Ukázkovou IP adresu nahraďte veřejnou IP adresou virtuálního počítače.

```console
ssh 10.101.10.10
```

Rozdělte disk na oddíly pomocí příkazu `parted`.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
```

Zapište na oddíl systém souborů pomocí příkazu `mkfs`. Použijte `partprobe` k tomu, aby operační systém tuto změnu věděli.

```bash
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Připojte nový disk, aby byl přístupný v operačním systému.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

K disku je teď možné přistupovat prostřednictvím `/datadrive` přípojný bod, který se dá ověřit spuštěním `df -h` příkazu.

```bash
df -h | grep -i "sd"
```

Výstup ukazuje nově připojenou jednotku `/datadrive` .

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        29G  2.0G   27G   7% /
/dev/sda15      105M  3.6M  101M   4% /boot/efi
/dev/sdb1        14G   41M   13G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Aby se zajistilo, že se jednotka znovu připojí po restartování systému, je třeba ji přidat do souboru */etc/fstab*. Abyste to mohli udělat, získejte identifikátor UUID disku pomocí nástroje `blkid`.

```bash
sudo -i blkid
```

Ve výstupu se zobrazí identifikátor UUID jednotky, v tomto případě `/dev/sdc1`.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs"
```

> [!NOTE]
> Nesprávná úprava souboru **/etc/fstab** by mohla vést k nespouštěcímu systému. Pokud si nejste jistí, podívejte se do dokumentace k distribuci, kde najdete informace o tom, jak soubor správně upravit. Doporučuje se také vytvořit zálohu souboru/etc/fstab před úpravou.

Otevřete `/etc/fstab` soubor v textovém editoru následujícím způsobem:

```bash
sudo nano /etc/fstab
```

Do souboru */etc/fstab* přidejte řádek podobný tomuto: Nahraďte hodnotu UUID vlastními.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  xfs    defaults,nofail   1  2
```

Až budete hotovi s úpravou souboru, použijte `Ctrl+O` k zápisu souboru a `Ctrl+X` k ukončení editoru.

Po dokončení konfigurace disku zavřete relaci SSH.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Pořídit snímek disku

Když pořídíte snímek disku, Azure vytvoří kopii disku k danému okamžiku určenou jen pro čtení. Snímky virtuálních počítačů Azure jsou užitečné k rychlému uložení stavu virtuálního počítače před změnou konfigurace. V případě problému nebo chyby se dá virtuální počítač obnovit pomocí snímku. Pokud má virtuální počítač více než jeden disk, pořizuje se snímek každého disku nezávisle na ostatních. V zájmu vytváření konzistentních záloh (vzhledem k aplikacím) zvažte možnost virtuální počítač před pořizováním snímků zastavit. Můžete také použít [službu Azure Backup](../../backup/index.yml), která umožňuje provádět automatizované zálohování spuštěného virtuálního počítače.

### <a name="create-snapshot"></a>Vytvoření snímku

Před vytvořením snímku potřebujete ID nebo název disku. K snímku ID disku použijte [AZ VM show](/cli/azure/vm#az-vm-show) . V tomto příkladu se ID disku uloží do proměnné, aby se mohlo použít v pozdějším kroku.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Teď, když máte ID, vytvořte snímek disku pomocí [AZ Snapshot Create](/cli/azure/snapshot#az-snapshot-create) .

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Vytvoření disku ze snímku

Tento snímek se pak dá převést na disk pomocí [AZ disk Create](/cli/azure/disk#az-disk-create), který se dá použít k opětovnému vytvoření virtuálního počítače.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Obnovení virtuálního počítače ze snímku

Pokud chcete předvést obnovení virtuálního počítače, odstraňte existující virtuální počítač pomocí [AZ VM Delete](/cli/azure/vm#az-vm-delete).

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
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

Pomocí příkazu [AZ disk list](/cli/azure/disk#az-disk-list) vyhledejte název datového disku. V tomto příkladu se umístí název disku do proměnné s názvem `datadisk` , která se používá v dalším kroku.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

Disk můžete připojit pomocí příkazu [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach).

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --name $datadisk
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s disky virtuálních počítačů, jako jsou:

> [!div class="checklist"]
> * Disky s operačním systémem a dočasné disky
> * Datové disky
> * Disky Standard a Premium
> * Výkon disků
> * Připojení a příprava datových disků
> * Snímky disků

V dalším kurzu se dozvíte, jak automatizovat konfiguraci virtuálních počítačů.

> [!div class="nextstepaction"]
> [Automatizace konfigurace virtuálních počítačů](./tutorial-automate-vm-deployment.md)
