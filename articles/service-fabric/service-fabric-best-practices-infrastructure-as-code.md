---
title: Infrastruktura Azure Service Fabric jako doporučené postupy kódu
description: Doporučené postupy a aspekty návrhu pro správu Azure Service Fabric jako infrastruktury jako kódu.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 1c044d5fd973d3c577088a887f2fac413d2ab79d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551807"
---
# <a name="infrastructure-as-code"></a>Infrastruktura jako kód

V produkčním scénáři vytvořte clustery Azure Service Fabric pomocí šablon Správce prostředků. Šablony Správce prostředků poskytují větší kontrolu nad vlastnostmi prostředků a zajišťují konzistentní model prostředků.

Ukázkové šablony Správce prostředků jsou k dispozici pro Windows a Linux v [ukázkách Azure na GitHubu](https://github.com/Azure-Samples/service-fabric-cluster-templates). Tyto šablony lze použít jako výchozí bod pro šablonu clusteru. Stáhněte `azuredeploy.parameters.json` a `azuredeploy.json` upravte je tak, aby splňovaly vaše vlastní požadavky.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li `azuredeploy.json` `azuredeploy.parameters.json` nasadit výše stažené šablony a šablony, použijte následující příkazy rozhraní příkazového příkazu Azure:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Vytvoření prostředku pomocí prostředí Powershell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Prostředky Azure Service Fabric

Aplikace a služby můžete nasadit do clusteru Service Fabric prostřednictvím Správce prostředků Azure. Podrobnosti najdete [v tématu Správa aplikací a služeb jako prostředků Azure Resource Manageru.](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) Níže jsou uvedeny osvědčené postupy Service Fabric aplikace specifické prostředky zahrnout do prostředků šablony Správce prostředků Resource Manager.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Chcete-li nasadit aplikaci pomocí Správce prostředků Azure, musíte [nejprve vytvořit balíček aplikace sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) Service Fabric. Následující skript pythonu je příkladem toho, jak vytvořit sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Konfigurace automatického upgradu operačního systému virtuálního počítače Azure 
Inovace virtuálních počítačů je operace iniciovaná uživatelem a doporučujeme použít [automatickou inovaci operačního systému Sady virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) pro správu oprav clusterů Azure Service Fabric; Aplikace Orchestrace oprav je alternativní řešení, které je určené pro hostování mimo Azure, i když POA lze použít v Azure, přičemž režie hostování POA v Azure je běžným důvodem pro upřednostňování automatického upgradu operačního systému virtuálního počítače před POA. Následující jsou vlastnosti šablony Compute Virtual Machine Scale Set Resource Manager, které umožňují automatický upgrade operačního systému:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Při použití automatické upgrady operačního systému s Service Fabric, nová bitová kopie operačního systému je zavedena jednu aktualizační doménu najednou zachovat vysokou dostupnost služeb spuštěných v Service Fabric. Chcete-li využít automatické upgrady operačního systému v service fabric, musí být váš cluster nakonfigurován tak, aby používal úroveň odolnosti silver nebo vyšší.

Ujistěte se, že je následující klíč registru nastaven na hodnotu false, aby hostitelské počítače se systémem Windows nemohly iniciovat nekoordinované aktualizace: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Následují vlastnosti šablony Compute Virtual Machine Scale Set Resource Manager pro nastavení klíče registru WindowsUpdate na hodnotu false:
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

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Konfigurace upgradu clusteru Infrastruktury služby Azure
Pro automatické upgrady je uveden anásledující vlastnost vlastnost isprávce prostředků clusteru Service Fabric pro povolení automatického upgradu:
```json
"upgradeMode": "Automatic",
```
Chcete-li ručně upgradovat cluster, stáhněte distribuci cab/deb do virtuálního počítače clusteru a pak vyvoláte následující prostředí PowerShell:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Další kroky

* Vytvoření clusteru na virtuálních počítačích nebo v počítačích se systémem Windows Server: [Vytvoření clusteru Service Fabric pro Systém Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Vytvoření clusteru na virtuálních počítačích nebo počítačích s [Linuxem: Vytvoření linuxového clusteru](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)
