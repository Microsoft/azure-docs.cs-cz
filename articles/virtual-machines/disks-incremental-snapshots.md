---
title: Vytvoření přírůstkového snímku
description: Přečtěte si o přírůstkových snímcích pro spravované disky, včetně toho, jak je vytvořit pomocí Azure Portal, Azure PowerShell modulu a Azure Resource Manager.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/15/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 52e491c88d3483f21aa74f1a9f176246033bee3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735788"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>Vytvoření přírůstkového snímku pro spravované disky

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

K vytvoření přírůstkového snímku můžete použít Azure PowerShell. Budete potřebovat nejnovější verzi Azure PowerShell, následující příkaz si buď nainstaluje nebo aktualizuje stávající instalaci na nejnovější:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Po nainstalování se přihlaste k relaci PowerShellu pomocí `Connect-AzAccount` .

Chcete-li vytvořit přírůstkový snímek s Azure PowerShell, nastavte konfiguraci pomocí parametru [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig) s `-Incremental` parametrem a pak jej předejte jako proměnnou do [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) prostřednictvím `-Snapshot` parametru.

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

Můžete identifikovat přírůstkové snímky ze stejného disku s `SourceResourceId` vlastnostmi a a `SourceUniqueId` vlastnostmi snímků. `SourceResourceId` je ID prostředku Azure Resource Manager nadřazeného disku. `SourceUniqueId` je hodnota zděděná z `UniqueId` Vlastnosti disku. Pokud byste chtěli odstranit disk a pak vytvořit nový disk se stejným názvem, změní se hodnota `UniqueId` Vlastnosti.

Pomocí a můžete `SourceResourceId` `SourceUniqueId` vytvořit seznam všech snímků přidružených k určitému disku. Nahraďte `<yourResourceGroupNameHere>` hodnotou a pak můžete použít následující příklad k vypsání stávajících přírůstkových snímků:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
[!INCLUDE [virtual-machines-disks-incremental-snapshots-portal](../../includes/virtual-machines-disks-incremental-snapshots-portal.md)]

# <a name="resource-manager-template"></a>[Šablona Správce prostředků](#tab/azure-resource-manager)

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
---

## <a name="next-steps"></a>Další kroky

Pokud se chcete podívat na vzorový kód prokazující rozdílovou schopnost přírůstkových snímků pomocí .NET, přečtěte si téma [kopírování záloh Azure Managed disks do jiné oblasti s rozdílovou schopností přírůstkových snímků](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
