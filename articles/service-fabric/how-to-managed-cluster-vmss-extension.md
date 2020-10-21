---
title: Přidání rozšíření sady škálování virtuálního počítače na Service Fabric typ uzlu spravovaného clusteru (Preview)
description: Tady je postup, jak přidat rozšíření sady škálování virtuálního počítače Service Fabric typ uzlu spravovaného clusteru.
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: be51a03494c90ba91cddb383b62b2477fabc8fc3
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309285"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a>Přidání rozšíření sady škálování virtuálního počítače na Service Fabric typ uzlu spravovaného clusteru (Preview)

Každý typ uzlu v Service Fabric spravovaném clusteru je zálohovaný pomocí sady škálování virtuálního počítače. To umožňuje přidat [rozšíření sady škálování virtuálních počítačů](../virtual-machines/extensions/overview.md) do Service Fabric typů uzlů spravovaného clusteru.

Rozšíření sady škálování virtuálního počítače můžete přidat k typu uzlu pomocí příkazu prostředí PowerShell [Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension?preserve-view=true&view=azps-4.7.0) .

Alternativně můžete v šabloně Azure Resource Manager pomocí rozšíření sady škálování virtuálního počítače v Service Fabric typ uzlu clusteru, například:

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Další informace o konfiguraci Service Fabric typů uzlů spravovaného clusteru najdete v tématu [typ uzlu spravovaného clusteru](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes).

## <a name="next-steps"></a>Další kroky

Další informace o Service Fabric spravovaných clusterech najdete v tématech:

> [!div class="nextstepaction"]
> [Nejčastější dotazy k Service Fabric spravovaných clusterů](./faq-managed-cluster.md)