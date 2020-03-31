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
ms.openlocfilehash: 3eec6583ebdff35d7e40d2eec305a947de0cb87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299447"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Podporované oblasti
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Azure PowerShell můžete použít k vytvoření přírůstkového snímku. Budete potřebovat nejnovější verzi Azure PowerShellu, následující příkaz ho buď nainstaluje, nebo aktualizuje stávající instalaci na nejnovější:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Po instalaci se přihlaste k `az login`relaci prostředí PowerShell pomocí aplikace .

Chcete-li vytvořit přírůstkový snímek s Azure PowerShell, nastavte konfiguraci `-Incremental` s [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) s parametrem `-Snapshot` a pak předat jako proměnnou [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) prostřednictvím parametru.

Nahradit `<yourDiskNameHere>` `<yourResourceGroupNameHere>`, `<yourDesiredSnapShotNameHere>` a s hodnotami, pak můžete použít následující skript k vytvoření přírůstkového snímku:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Můžete identifikovat přírůstkové snímky ze stejného disku s `SourceResourceId` a vlastnosti `SourceUniqueId` snímků. `SourceResourceId`je ID prostředku Správce prostředků Azure nadřazeného disku. `SourceUniqueId`je hodnota zděděná z vlastnosti `UniqueId` disku. Pokud byste odstranili disk a potom vytvořili nový disk se `UniqueId` stejným názvem, hodnota vlastnosti se změní.

Můžete použít `SourceResourceId` `SourceUniqueId` a vytvořit seznam všech snímků přidružených k určitému disku. Nahraďte `<yourResourceGroupNameHere>` hodnotou a potom můžete použít následující příklad pro seznam existujících přírůstkových snímků:

```PowerShell
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

Šablony Azure Resource Manageru můžete taky použít k vytvoření přírůstkového snímku. Budete muset ujistěte se, že apiVersion je nastavena na **2019-03-01** a že přírůstkové vlastnost je také nastavena na hodnotu true. Následující úryvek je příkladem, jak vytvořit přírůstkový snímek pomocí šablon Správce prostředků:

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

Pokud chcete zobrazit ukázkový kód demonstrující rozdílové schopnosti přírůstkových snímků pomocí rozhraní .NET, přečtěte si část [Kopírování záloh spravovaných disků Azure do jiné oblasti s rozdílovou schopností přírůstkových snímků](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
