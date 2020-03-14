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
ms.openlocfilehash: 69be71a58c3aed4f52b77e63c9ddf12365301b08
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299151"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Podporované oblasti
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>Rozhraní příkazového řádku

Můžete vytvořit přírůstkový snímek pomocí Azure CLI, budete potřebovat nejnovější verzi rozhraní příkazového řádku Azure CLI. 

V systému Windows následující příkaz buď nainstaluje nebo aktualizuje stávající instalaci na nejnovější verzi:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
V systému Linux se instalace rozhraní příkazového řádku liší v závislosti na verzi operačního systému.  Informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) pro konkrétní verzi systému Linux.

K vytvoření přírůstkového snímku použijte příkaz [AZ Snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) s parametrem `--incremental`.

Následující příklad vytvoří přírůstkový snímek, nahraďte `<yourDesiredSnapShotNameHere>`, `<yourResourceGroupNameHere>`,`<exampleDiskName>`a `<exampleLocation>` vlastními hodnotami a pak spusťte příklad:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Můžete identifikovat přírůstkové snímky ze stejného disku s `SourceResourceId` a `SourceUniqueId` vlastností snímků. `SourceResourceId` je ID prostředku Azure Resource Manager nadřazeného disku. `SourceUniqueId` je hodnota zděděná z vlastnosti `UniqueId` disku. Pokud byste chtěli odstranit disk a pak vytvořit nový disk se stejným názvem, změní se hodnota vlastnosti `UniqueId`.

Pomocí `SourceResourceId` a `SourceUniqueId` můžete vytvořit seznam všech snímků přidružených k určitému disku. Následující příklad zobrazí seznam všech přírůstkových snímků přidružených k určitému disku, ale vyžaduje instalaci.

V tomto příkladu se pro dotazování na data používá JQ. Chcete-li spustit příklad, musíte [nainstalovat JQ](https://stedolan.github.io/jq/download/).

Nahraďte `<yourResourceGroupNameHere>` a `<exampleDiskName>` hodnotami a pak můžete použít následující příklad k vypsání stávajících přírůstkových snímků, pokud jste také nainstalovali JQ:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
