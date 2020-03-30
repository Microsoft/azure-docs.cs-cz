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
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80343036"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>Rozhraní příkazového řádku

Můžete vytvořit přírůstkový snímek s Azure CLI, budete potřebovat nejnovější verzi Azure CLI. 

V systému Windows nainstaluje nebo aktualizuje stávající instalaci na nejnovější verzi následující příkaz:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
V systému Linux se instalace funkce vpořádku liší v závislosti na verzi operačního systému.  Viz [Instalace nastavení příkazového příkazu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) pro konkrétní verzi Linuxu.

Chcete-li vytvořit přírůstkový snímek, `--incremental` použijte [az snímek vytvořit](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) s parametrem.

Následující příklad vytvoří přírůstkový `<yourDesiredSnapShotNameHere>`snímek, nahraďte , `<yourResourceGroupNameHere>``<exampleDiskName>`, a `<exampleLocation>` s vlastními hodnotami a spusťte příklad:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Můžete identifikovat přírůstkové snímky ze stejného disku s `SourceResourceId` a vlastnosti `SourceUniqueId` snímků. `SourceResourceId`je ID prostředku Správce prostředků Azure nadřazeného disku. `SourceUniqueId`je hodnota zděděná z vlastnosti `UniqueId` disku. Pokud byste odstranili disk a potom vytvořili nový disk se `UniqueId` stejným názvem, hodnota vlastnosti se změní.

Můžete použít `SourceResourceId` `SourceUniqueId` a vytvořit seznam všech snímků přidružených k určitému disku. V následujícím příkladu budou uvedeny všechny přírůstkové snímky přidružené k určitému disku, ale vyžaduje určité nastavení.

Tento příklad používá jq pro dotazování na data. Chcete-li spustit příklad, je nutné [nainstalovat jq](https://stedolan.github.io/jq/download/).

Nahradit `<yourResourceGroupNameHere>` `<exampleDiskName>` a s hodnotami, pak můžete použít následující příklad pro seznam stávajících přírůstkové snímky, pokud jste také nainstalovali jq:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
