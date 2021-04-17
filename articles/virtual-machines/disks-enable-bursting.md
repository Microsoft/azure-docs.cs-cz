---
title: Povolit shlukování disků na vyžádání
description: Povolte na spravovaném disku nárůst využití disku na vyžádání.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 5110e580bada7bb1090b17d6df22a9354622e8e4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483142"
---
# <a name="enable-on-demand-bursting"></a>Povolit shlukování na vyžádání

Jednotky SSD (Solid-State Drive) úrovně Premium mají dva dostupné modely shlukování; shlukování a shlukování na vyžádání na základě kreditu. Tento článek popisuje, jak přepnout na roztržení na vyžádání. Disky, které používají model na vyžádání, můžou překračovat nad rámec původních zřízených cílů. K vypínání na vyžádání dochází tak často, jak to vyžaduje zatížení, až do maximálního cíle pro nárůst zatížení. Při roztržení na vyžádání se účtují další poplatky.

Podrobnosti o shlukování disku najdete v tématu věnovaném [správě shluku disků](disk-bursting.md).

> [!IMPORTANT]
> Nemusíte postupovat podle kroků v tomto článku, pokud chcete použít shlukování na základě kreditu. Ve výchozím nastavení je shlukování na základě kreditu povolené na všech vhodných discích.

Než povolíte shlukování na vyžádání, můžete pochopit následující:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Regionální dostupnost

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Začínáme

Rozpínání na vyžádání se dá povolit buď pomocí modulu Azure PowerShell, Azure CLI nebo šablon Azure Resource Manager. V následujících příkladech najdete informace o tom, jak vytvořit nový disk s povoleným vytvářením shlukování na vyžádání a jak na stávajících discích povolit vytváření shluků na vyžádání.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rutiny pro shlukování na vyžádání jsou k dispozici ve verzi 5.5.0 a novější z modulu AZ Module. Alternativně můžete použít [Azure Cloud Shell](https://shell.azure.com/).
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Vytvoření prázdného datového disku pomocí shlukování na vyžádání

Aby bylo možné povolit shlukování na vyžádání, musí být spravovaný disk větší než 512 GiB. Nahraďte `<myResourceGroupDisk>` parametry a a `<myDataDisk>` pak spuštěním následujícího skriptu vytvořte disk SSD úrovně Premium s využitím shlukování na vyžádání:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Povolení rozšíření na vyžádání na stávajícím disku

Aby bylo možné povolit shlukování na vyžádání, musí být spravovaný disk větší než 512 GiB. Nahraďte `<myResourceGroupDisk>` `<myDataDisk>` parametry, parametry a spusťte tento příkaz, aby se na stávajícím disku povolilo roztržení na vyžádání:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Rutiny pro shlukování na vyžádání jsou k dispozici ve verzi 2.19.0 a novějších modulech rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli). Alternativně můžete použít [Azure Cloud Shell](https://shell.azure.com/).

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Vytvoření a připojení disku s přenosem dat na vyžádání

Aby bylo možné povolit shlukování na vyžádání, musí být spravovaný disk větší než 512 GiB. Nahraďte `<yourDiskName>` `<yourResourceGroup>` parametry, a a `<yourVMName>` pak spusťte následující příkazy, abyste vytvořili disk SSD úrovně Premium s využitím shlukování na vyžádání:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Povolení rozšíření na vyžádání pro existující disk – rozhraní příkazového řádku

Aby bylo možné povolit shlukování na vyžádání, musí být spravovaný disk větší než 512 GiB. Nahraďte `<myResourceGroupDisk>` `<yourDiskName>` parametry a a spuštěním tohoto příkazu aktivujte na stávajícím disku vypínání na vyžádání:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

Pomocí `2020-09-30` diskového rozhraní API můžete povolit vytváření shlukování na vyžádání na nově vytvořené nebo existující úrovni Premium SSD větší než 512 GIB. `2020-09-30`Rozhraní API zavedlo novou vlastnost `burstingEnabled` . Ve výchozím nastavení je tato vlastnost nastavena na hodnotu false. Následující vzorová šablona vytvoří v Středozápadní USA 1TiB Premium SSD s povoleným diskovým shlukem:

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>Další kroky

Informace o tom, jak získat přehled o prostředcích shlukování, najdete v tématu [metriky shlukování disku](disks-metrics.md).
