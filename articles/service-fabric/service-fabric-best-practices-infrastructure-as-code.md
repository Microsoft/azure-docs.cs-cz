---
title: Azure Service Fabric infrastruktura jako kód osvědčené postupy | Dokumentace Microsoftu
description: Doporučené postupy pro správu Service Fabric jako infrastruktura jako kód.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 2dfe1493c6611fb69a417895aaa1028ad5881b9c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237417"
---
# <a name="infrastructure-as-code"></a>Infrastruktura jako kód

V případě produkčního prostředí vytvářejte clustery Azure Service Fabric pomocí šablon Resource Manageru. Šablony Resource Manageru poskytují větší kontrolu nad vlastnosti prostředku a ujistěte se, že máte model konzistentní prostředků.

Šablony Resource Manageru ukázky jsou k dispozici pro Windows a Linuxu v [ukázky na Githubu v Azure](https://github.com/Azure-Samples/service-fabric-cluster-templates). Tyto šablony lze použít jako výchozí bod pro šablony clusteru. Stáhněte si `azuredeploy.json` a `azuredeploy.parameters.json` a upravovat je podle vlastních požadavků.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Prostředky Azure Service Fabric

Aplikace a služby můžete do clusteru Service Fabric nasadit prostřednictvím Azure Resource Manageru. Zobrazit [spravovat aplikace a služby jako prostředky Azure Resource Manageru](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) podrobnosti. Níže jsou nejlepší postup Service Fabric konkrétní prostředky aplikace chcete zahrnout do vašich prostředků šablony Resource Manageru.

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

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Virtuální počítač Azure operační systém automatického upgradu konfigurace 
Upgradování vašich virtuálních počítačů je uživatelem iniciované operace a doporučuje se, že používáte [virtuálního počítače Škálovací nastavení automatického upgradu operačního systému](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) pro správu oprav hostitelů, clustery Azure Service Fabric Použití Orchestrace opravy je alternativní řešení, která je určená pro když jsou hostované mimo Azure, i když POA jde použít v Azure, s režijní náklady na provozování POA v Azure se běžným důvodem preferovat Upgrade automatické operačního systému virtuálního počítače přes POA. Toto jsou vlastnosti šablony výpočetní virtuální počítač Škálovací nastavit Resource Manageru umožňují upgrade operačního systému automaticky:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Při použití automatické upgrady operačního systému s využitím Service Fabric, image nového operačního systému je nasazeny v jedné aktualizační doméně se udržet vysokou dostupnost služby spuštěné v Service Fabric. Aby se začala používat automatické upgrady operačního systému v Service Fabric musí být váš cluster nastavená úroveň Silver odolnosti nebo vyšší.

Ujistěte se, že následující klíč registru je nastaven na hodnotu false zabránit inicializace nekoordinovaná aktualizace hostitelských počítačích windows: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Toto jsou vlastnosti šablony výpočetní virtuální počítač Škálovací nastavit Resource Manageru k nastavení klíče registru Windows Update na hodnotu false:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Konfigurace upgradu clusteru Azure Service Fabric
Vlastnost šablony Resource Manageru povolit automatický upgrade clusteru Service Fabric je následující:
```json
"upgradeMode": "Automatic",
```
Cluster ručně upgradovat, stáhněte si soubor cab/deb distribuční k virtuálnímu počítači clusteru a pak vyvolejte následující příkaz Powershellu:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Další postup

* Vytvoření clusteru na virtuální počítače nebo počítače se systémem Windows Server: [Vytvoření clusteru Service Fabric pro Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Vytvoření clusteru na počítače s Linuxem nebo virtuální počítače: [Vytvoření clusteru s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)
