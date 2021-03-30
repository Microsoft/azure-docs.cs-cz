---
title: Datové disky připojené k Azure Virtual Machine Scale Sets
description: Naučte se používat připojené datové disky se sadami škálování virtuálních počítačů prostřednictvím přehledů specifických případů použití.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 4/25/2017
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 9e4bdf868d3f8ddf3a049509ead30a4b1ba341b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86527434"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Škálovací sady virtuálních počítačů Azure a připojené datové disky
[Škálovací sady virtuálních počítačů](./index.yml) Azure podporují instance virtuálních počítačů s připojenými datovými disky a umožňují tak rozšíření úložiště, které máte k dispozici. Datové disky můžete připojit při vytváření škálovací sady nebo k existující škálovací sadě.

> [!NOTE]
> Když vytvoříte škálovací sadu s připojenými datovými disky, před jejich použitím musíte disky připojit a naformátovat na virtuálním počítači (stejně jako u samostatných virtuálních počítačů Azure). Praktický způsob, jak to provést, je použít rozšíření vlastních skriptů, které volá skript, který všechny datové disky virtuálního počítače rozdělí do oddílů a naformátuje je. Tady najdete příklady pro [Azure CLI a ](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Vytváření a správa disků ve škálovací sadě
Podrobné informace o vytvoření škálovací sady s připojenými datovými disky a o přípravě, formátování, přidávání a odebírání datových disků najdete v následujících kurzech:

- [Azure CLI](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

Zbývající část tohoto článku popisuje konkrétní případy použití, jako jsou například clustery Service Fabric vyžadující datové disky nebo připojení existujících datových disků s obsahem ke škálovací sadě.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Vytvoření clusteru Service Fabric s připojenými datovými disky
Každý [typ uzlu](../service-fabric/service-fabric-cluster-nodetypes.md) v clusteru [Service Fabric](../service-fabric/index.yml) spuštěném v Azure využívá škálovací sadu virtuálních počítačů. Pomocí šablony Azure Resource Manageru můžete připojit datové disky ke škálovacím sadám, ze kterých se skládá cluster Service Fabric. Jako výchozí bod můžete použít [existující šablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates). V šabloně do části _storageProfile_ prostředků _Microsoft.Compute/virtualMachineScaleSets_ vložte část _dataDisks_ a pak šablonu nasaďte. Následující příklad připojí 128GB datový disk:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Při nasazování clusteru můžete datové disky automaticky dělit, formátovat a připojovat. Do části _extensionProfile_ profilu _virtualMachineProfile_ škálovacích sad přidejte rozšíření vlastních skriptů.

Pokud chcete automaticky připravovat datové disky v clusteru s Windows, přidejte následující:

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Pokud chcete automaticky připravovat datové disky v clusteru s Linuxem, přidejte následující:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Přidání disků předem naplněných daty do existující škálovací sady
Datové disky zadané v modelu škálovací sady jsou vždy prázdné. Můžete ale přiřadit existující datové disky ke konkrétnímu virtuálnímu počítači ve škálovací sadě. Pokud chcete rozšířit data napříč všemi virtuálními počítači v sadě škálování, můžete duplikovat datový disk a připojit ho ke každému VIRTUÁLNÍmu počítači v sadě škálování nebo vytvořit vlastní image, která obsahuje data a zřídit sadu škálování z této vlastní image, nebo můžete použít soubory Azure nebo podobné nabídky úložiště dat.


## <a name="additional-notes"></a>Další poznámky
Podpora Spravovaných disků Azure a připojených datových disků škálovacích sad je dostupná v rozhraní Microsoft.Compute API verze [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) nebo novější.

Podpora Azure Portal pro připojené datové disky v sadách škálování je omezená. V závislosti na požadavcích můžete ke správě připojených disků použít šablony Azure, rozhraní příkazového řádku, PowerShell, sady SDK nebo rozhraní REST API.
