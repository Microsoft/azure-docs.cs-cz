---
title: Azure Service Fabric infrastruktura jako kód osvědčené postupy | Dokumentace Microsoftu
description: Doporučené postupy pro správu Service Fabric jako infrastruktura jako kód.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 23c851008988af06927d387daaa5a6df980dab96
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913759"
---
# <a name="infrastructure-as-code"></a>Infrastruktura jako kód

V případě produkčního prostředí vytvářejte clustery Azure Service Fabric pomocí šablon Resource Manageru. Šablony Resource Manageru poskytují větší kontrolu nad vlastnosti prostředku a ujistěte se, že máte model konzistentní prostředků.

Šablony Resource Manageru ukázky jsou k dispozici pro Windows a Linuxu v [ukázky na Githubu v Azure](https://github.com/Azure-Samples/service-fabric-cluster-templates). Tyto šablony lze použít jako výchozí bod pro šablony clusteru. Stáhněte si `azuredeploy.json` a `azuredeploy.parameters.json` a upravovat je podle vlastních požadavků.

K nasazení `azuredeploy.json` a `azuredeploy.parameters.json` šablony, které jste stáhli, pomocí následujících příkazů rozhraní příkazového řádku Azure:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Vytvoření prostředku pomocí Powershellu

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
New-AzureRmResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Prostředky Azure Service Fabric

Do vašeho clusteru Service Fabric pomocí Azure Resource Manageru můžete nasadit aplikace a služby. Zobrazit [spravovat aplikace a služby jako prostředky Azure Resource Manageru](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) podrobnosti. Níže jsou nejlepší postup Service Fabric konkrétní prostředky aplikace chcete zahrnout do vašich prostředků šablony Resource Manageru.

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

K nasazení aplikace pomocí Azure Resource Manageru, je nejprve nutné [vytvořit sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) balíčku aplikace Service Fabric. Následující skript jazyka python je příkladem toho, jak vytvořit sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="next-steps"></a>Další postup

* Vytvoření clusteru na virtuální počítače nebo počítače se systémem Windows Server: [Vytvoření clusteru Service Fabric pro Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Vytvoření clusteru na počítače s Linuxem nebo virtuální počítače: [Vytvoření clusteru s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)