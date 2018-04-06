---
title: Škálovací sady virtuálních počítačů Azure – připojené datové disky | Dokumentace Microsoftu
description: Naučte se používat připojené datové disky se škálovacími sadami virtuálních počítačů.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: ec11a2d66530129fb61d97681e6882b887c8654c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Škálovací sady virtuálních počítačů Azure a připojené datové disky
[Škálovací sady virtuálních počítačů](/azure/virtual-machine-scale-sets/) Azure podporují instance virtuálních počítačů s připojenými datovými disky a umožňují tak rozšíření úložiště, které máte k dispozici. Datové disky můžete připojit při vytváření škálovací sady nebo k existující škálovací sadě.

> [!NOTE]
>  Když vytvoříte škálovací sadu s připojenými datovými disky, před jejich použitím musíte disky připojit a naformátovat na virtuálním počítači (stejně jako u samostatných virtuálních počítačů Azure). Praktický způsob, jak to provést, je použít rozšíření vlastních skriptů, které volá skript, který všechny datové disky virtuálního počítače rozdělí do oddílů a naformátuje je. Tady najdete příklady pro [Azure CLI 2.0](tutorial-use-disks-cli.md#prepare-the-data-disks) a [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Vytváření a správa disků ve škálovací sadě
Podrobné informace o vytvoření škálovací sady s připojenými datovými disky a o přípravě, formátování, přidávání a odebírání datových disků najdete v následujících kurzech:

- [Azure CLI 2.0](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

Zbývající část tohoto článku popisuje konkrétní případy použití, jako jsou například clustery Service Fabric vyžadující datové disky nebo připojení existujících datových disků s obsahem ke škálovací sadě.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Vytvoření clusteru Service Fabric s připojenými datovými disky
Každý [typ uzlu](../service-fabric/service-fabric-cluster-nodetypes.md) v clusteru [Service Fabric](/azure/service-fabric) spuštěném v Azure využívá škálovací sadu virtuálních počítačů.  Pomocí šablony Azure Resource Manageru můžete připojit datové disky ke škálovacím sadám, ze kterých se skládá cluster Service Fabric. Jako výchozí bod můžete použít [existující šablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates). V šabloně do části _storageProfile_ prostředků _Microsoft.Compute/virtualMachineScaleSets_ vložte část _dataDisks_ a pak šablonu nasaďte. Následující příklad připojí 128GB datový disk:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Při nasazování clusteru můžete datové disky automaticky dělit, formátovat a připojovat.  Do části _extensionProfile_ profilu _virtualMachineProfile_ škálovacích sad přidejte rozšíření vlastních skriptů.

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


## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>Přidání disků předem naplněných daty do existující škálovací sady 
> Když přidáváte disky do stávajícího modelu škálovací sady, vytvoří se z principu vždy prázdný disk. Tento scénář také zahrnuje nové instance vytvořené škálovací sadou. Toto chování je způsobené tím, že definice škálovací sady obsahuje prázdný datový disk. Chcete-li vytvořit pro model existující škálovací sady diskové jednotky předem naplněné daty, můžete zvolit jednu z následujících dvou možností:

* Zkopírujte data z virtuálního počítače instance 0 na datové disky v jiných virtuálních počítačích spuštěním vlastního skriptu.
* Vytvořte spravovanou image s diskem operačního systému a datovým diskem (s požadovanými daty) a vytvořte novou škálovací sadu s touto imagí. Tímto způsobem bude mít každý nově vytvořený virtuální počítač datový disk, který je v definici škálovací sady. Vzhledem k tomu, že definice odkazuje na image s datovým diskem obsahujícím přizpůsobená data, bude každý virtuální počítač škálovací sady obsahovat tyto změny.

> Postup vytvoření vlastní image najdete v tématu s popisem [vytvoření spravované image zobecněného virtuálního počítače v Azure](/azure/virtual-machines/windows/capture-image-resource/). 

> Uživatel musí zachytit instanci 0 virtuálního počítače s požadovanými daty a pak tento virtuální pevný disk použít k definici image.


## <a name="additional-notes"></a>Další poznámky
Podpora služby Azure Managed Disks a připojených datových disků škálovacích sad je dostupná v rozhraní Microsoft.Compute API verze [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) nebo novější.

Během počáteční implementace podpory připojených disků pro škálovací sady nelze datové disky připojit k jednotlivým virtuálním počítačům ve škálovací sadě, ani je od nich odpojit.

Podpora připojených datových disků ve škálovacích sadách je na webu Azure Portal zpočátku omezená. V závislosti na požadavcích můžete ke správě připojených disků použít šablony Azure, rozhraní příkazového řádku, PowerShell, sady SDK nebo rozhraní REST API.


