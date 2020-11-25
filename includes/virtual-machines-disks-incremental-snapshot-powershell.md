---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 895a81df858e51a266cd87c96a161695a4bf2cc1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95992891"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Podporované oblasti
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

K vytvoření přírůstkového snímku můžete použít Azure PowerShell. Budete potřebovat nejnovější verzi Azure PowerShell, následující příkaz si buď nainstaluje nebo aktualizuje stávající instalaci na nejnovější:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Po nainstalování se přihlaste k relaci PowerShellu pomocí `Connect-AzAccount` .

Chcete-li vytvořit přírůstkový snímek s Azure PowerShell, nastavte konfiguraci pomocí parametru [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) s `-Incremental` parametrem a pak jej předejte jako proměnnou do [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) prostřednictvím `-Snapshot` parametru.

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

## <a name="next-steps"></a>Další kroky

Pokud se chcete podívat na vzorový kód prokazující rozdílovou schopnost přírůstkových snímků pomocí .NET, přečtěte si téma [kopírování záloh Azure Managed disks do jiné oblasti s rozdílovou schopností přírůstkových snímků](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).