---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e39f294f7902eabef401d4c8145f4f19a07f267f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224574"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>Vytváření přírůstkového snímku (ve verzi Preview) pro spravované disky

Přírůstkové snímky (Preview) jsou zálohy v čase pro spravované disky, které se při pořízení skládají pouze ze všech změn od posledního snímku. Při pokusu o stažení nebo jiné použití přírůstkového snímku se použije úplný virtuální pevný disk. Tato nová funkce pro snímky spravovaných disků může potenciálně dovolit, aby bylo cenově výhodnější, protože už nebudete muset ukládat celý disk s každým snímkem, pokud se nerozhodnete. Stejně jako běžné snímky lze přírůstkové snímky použít k vytvoření úplného spravovaného disku, nebo k vytvoření normálního snímku.

Existuje několik rozdílů mezi přírůstkovým snímkem a běžným snímkem. Přírůstkové snímky budou vždy používat standardní úložiště HDD bez ohledu na typ úložiště disku, zatímco běžné snímky mohou používat Premium SSD. Pokud používáte běžné snímky na Premium Storage k horizontálnímu navýšení kapacity nasazení virtuálních počítačů, doporučujeme, abyste v [galerii sdílených imagí](../articles/virtual-machines/linux/shared-image-galleries.md)používali vlastní image ve službě Storage úrovně Standard. Pomůže vám dosáhnout větší obrovské škály s nižšími náklady. Navíc přírůstkové snímky potenciálně nabízejí lepší spolehlivost pomocí [redundantního úložiště](../articles/storage/common/storage-redundancy-zrs.md) (ZRS) zóny. Pokud je ve vybrané oblasti k dispozici ZRS, přírůstkový snímek bude automaticky používat ZRS. Pokud ZRS není v této oblasti k dispozici, bude mít tento snímek výchozí [místně redundantní úložiště](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Toto chování můžete přepsat a vybrat ho ručně, ale nedoporučujeme to.

Přírůstkové snímky také nabízí rozdílovou možnost, která je jednoznačně dostupná pro spravované disky. Umožňují získat změny dvou přírůstkových snímků stejných spravovaných disků na úrovni bloku. Tuto možnost můžete použít ke snížení objemu dat při kopírování snímků napříč oblastmi.

Pokud jste se ještě nezaregistrovali ve verzi Preview a chcete začít používat přírůstkové snímky, pošlete nám AzureDisks@microsoft.com prosím e-mail na adresu, kde získáte přístup k verzi Public Preview.

## <a name="restrictions"></a>Omezení

- Přírůstkové snímky momentálně nelze vytvořit poté, co jste změnili velikost disku.
- Přírůstkové snímky se aktuálně nedají přesunout mezi předplatnými.
- V současné době můžete v daném okamžiku generovat pouze identifikátory URI SAS až na pět snímků konkrétní rodiny snímků.
- Pro určitý disk nemůžete vytvořit přírůstkový snímek mimo předplatné tohoto disku.
- Až sedm přírůstkových snímků na disk lze vytvořit každých pět minut.
- Pro jeden disk lze vytvořit celkem 200 přírůstkových snímků.

## <a name="powershell"></a>PowerShell

K vytvoření přírůstkového snímku můžete použít Azure PowerShell. Nejnovější verzi PowerShellu můžete nainstalovat místně. Budete potřebovat nejnovější verzi Azure PowerShell, následující příkaz si buď nainstaluje nebo aktualizuje stávající instalaci na nejnovější:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Po nainstalování se přihlaste k relaci PowerShellu `az login`pomocí.

`<yourDiskNameHere>`Nahraďte `<yourResourceGroupNameHere>`, a`<yourDesiredSnapShotNameHere>` hodnotami a pak můžete použít následující skript k vytvoření přírůstkového snímku:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting:
# 1. Incremental property
# 2. SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 

# You can identify incremental snapshots of the same disk by using the SourceResourceId and SourceUniqueId properties of snapshots. 
# SourceResourceId is the Azure Resource Manager resource ID of the parent disk. 
# SourceUniqueId is the value inherited from the UniqueId property of the disk. If you delete a disk and then create a disk with the same name, the value of the UniqueId property will change. 
# Following script shows how to get all the incremental snapshots in a resource group of same disk
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Šablona Resource Manageru

Můžete také použít šablony Azure Resource Manager k vytvoření přírůstkového snímku. Musíte se ujistit, že je apiVersion nastavené na **2019-03-01** a že vlastnost Increment je nastavená taky na true. Následující fragment kódu je příkladem vytvoření přírůstkového snímku pomocí šablon Správce prostředků:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="cli"></a>Rozhraní příkazového řádku

Přírůstkový snímek můžete pomocí Azure CLI vytvořit pomocí příkaz [AZ Snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create). Příklad příkazu by vypadal takto:

```bash
az snapshot create -g <exampleResourceGroup> \
-n <exampleSnapshotName> \
-l <exampleLocation> \
--source <exampleVMId> \
--incremental
```

Můžete také určit, `--query` které snímky jsou přírůstkové snímky v rozhraní příkazového řádku, pomocí parametru na [AZ Snapshot show](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-show). Tento parametr můžete použít k přímému dotazování vlastností **parametr sourceresourceid** a **SourceUniqueId** snímků. Parametr sourceresourceid je ID prostředku Azure Resource Manager nadřazeného disku. **SourceUniqueId** je hodnota zděděná z vlastnosti **UniqueID** disku. Pokud disk odstraníte a pak vytvoříte disk se stejným názvem, změní se hodnota vlastnosti **UniqueID** .

Příklady obou dotazů by vypadaly takto:

```bash
az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceResourceId] -o tsv

az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceUniqueId] -o tsv
```

## <a name="next-steps"></a>Další kroky

Pokud jste se ještě nezaregistrovali ve verzi Preview a chcete začít používat přírůstkové snímky, pošlete nám AzureDisks@microsoft.com prosím e-mail na adresu, kde získáte přístup k verzi Public Preview.
