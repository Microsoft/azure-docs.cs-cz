---
title: Datové disky připojené k Azure Virtual Machine Scale Sets
description: Naučte se používat připojené datové disky se sadami škálování virtuálních počítačů prostřednictvím přehledů specifických případů použití.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 4/25/2017
ms.author: manayar
ms.openlocfilehash: c7fd4d89fcc66fb4110029be45ad94e21faea0e0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386641"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure Virtual Machine Scale Sets a připojené datové disky
Aby bylo možné rozšířit dostupné úložiště, služba Azure [Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/) podporuje instance virtuálních počítačů s připojenými datovými disky. Datové disky můžete připojit při vytvoření sady škálování nebo v existující sadě škálování.

> [!NOTE]
> Když vytvoříte sadu škálování s připojenými datovými disky, musíte disky připojit a naformátovat na virtuálním počítači, abyste je mohli použít (stejně jako u samostatných virtuálních počítačů Azure). Pohodlný způsob, jak tento proces dokončit, je použít rozšíření vlastních skriptů, které volá skript a naformátuje všechny datové disky na virtuálním počítači a naformátuje je. Příklady najdete v tématu [Azure CLI](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Vytváření a Správa disků v sadě škálování
Podrobné informace o tom, jak vytvořit sadu škálování s připojenými datovými disky, připravit a formátovat nebo přidávat a odebírat datové disky, najdete v jednom z následujících kurzů:

- [Rozhraní příkazového řádku Azure](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

Zbývající část tohoto článku popisuje konkrétní případy použití, například Service Fabric clustery, které vyžadují datové disky, nebo připojení existujících datových disků k obsahu do sady škálování.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Vytvoření clusteru Service Fabric s připojenými datovými disky
Každý [typ uzlu](../service-fabric/service-fabric-cluster-nodetypes.md) v clusteru [Service Fabric](/azure/service-fabric) spuštěném v Azure je zálohovaný pomocí sady škálování virtuálních počítačů. Pomocí šablony Azure Resource Manager můžete připojit datové disky ke množinám škálování, které tvoří Cluster Service Fabric. Jako výchozí bod můžete použít [existující šablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates) . V šabloně zahrňte do _storageProfilei_ prostředků _Microsoft. COMPUTE/VirtualMachineScaleSets_ část _datadisks_ a nasaďte šablonu. Následující příklad připojí datový disk 128 GB:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Datové disky můžete automaticky rozdělit, formátovat a připojovat, když je cluster nasazený. Přidejte rozšíření vlastních skriptů do _extensionProfileu_ _virtualMachineProfile_ sady škálování.

Pokud chcete automaticky připravovat datové disky v clusteru Windows, přidejte následující:

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
Pokud chcete automaticky připravovat datové disky v clusteru se systémem Linux, přidejte následující:
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


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Přidávání předem naplněných datových disků do existující sady škálování
Datové disky zadané v modelu sady škálování jsou vždycky prázdné. Existující datový disk ale můžete připojit ke konkrétnímu virtuálnímu počítači v sadě škálování. Tato funkce je ve verzi Preview, s příklady na [GitHubu](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk). Pokud chcete rozšířit data napříč všemi virtuálními počítači v sadě škálování, můžete datový disk duplikovat a připojit ho ke každému virtuálnímu počítači v sadě škálování. můžete vytvořit vlastní image obsahující data a zřídit sadu škálování z této vlastní image. nebo můžete použít soubory Azure nebo podobnou nabídku úložiště dat.


## <a name="additional-notes"></a>Další poznámky
Podpora služby Azure Managed disks a připojených datových disků Sady škálování je dostupná v rozhraní API verze [_2016-04-30-Preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) nebo NOVĚJŠÍCH rozhraní API Microsoft. Compute.

U připojených datových disků v sadách pro škálování se zpočátku omezí Azure Portal podpora. V závislosti na vašich požadavcích můžete ke správě připojených disků použít šablony Azure, rozhraní příkazového řádku, PowerShell, sady SDK a REST API.


