---
title: Infrastruktura Azure Service Fabric jako osvědčené postupy pro kód
description: Osvědčené postupy a faktory návrhu pro správu Azure Service Fabric jako infrastruktury jako kódu.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: a0f0324d1f7308eb1392c4f7a98a6a5d226026be
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705502"
---
# <a name="infrastructure-as-code"></a>Infrastruktura jako kód

V produkčním scénáři Vytvořte clustery Azure Service Fabric pomocí šablon Správce prostředků. Šablony Správce prostředků poskytují větší kontrolu nad vlastnostmi prostředku a zajišťují, že máte konzistentní model prostředků.

Ukázkové šablony Správce prostředků jsou k dispozici pro Windows a Linux v [ukázkách Azure na GitHubu](https://github.com/Azure-Samples/service-fabric-cluster-templates). Tyto šablony lze použít jako výchozí bod pro šablonu clusteru. Stáhněte `azuredeploy.json` si `azuredeploy.parameters.json` je a upravte je tak, aby splňovaly vaše vlastní požadavky.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud chcete nasadit `azuredeploy.json` šablony a, `azuredeploy.parameters.json` které jste si stáhli, použijte následující příkazy rozhraní příkazového řádku Azure:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az deployment group create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Vytvoření prostředku pomocí PowerShellu

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Prostředky Azure Service Fabric

Do Service Fabric clusteru můžete pomocí Azure Resource Manager nasadit aplikace a služby. Podrobnosti najdete v tématu [Správa aplikací a služeb jako Azure Resource Manager prostředků](./service-fabric-application-arm-resource.md) . Níže jsou uvedené osvědčené postupy Service Fabric prostředků specifických pro aplikaci, které se mají zahrnout do prostředků šablon Správce prostředků.

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

Chcete-li nasadit aplikaci pomocí Azure Resource Manager, musíte nejprve [vytvořit](./service-fabric-package-apps.md#create-an-sfpkg) balíček aplikace Service Fabric sfpkg. Následující skript Pythonu je příkladem vytvoření sfpkg:

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
Upgrade virtuálních počítačů je operace iniciovaná uživatelem. doporučuje se použít [Automatický upgrade operačního systému](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) pro Azure Service Fabric clustery pro správu oprav hostitele; Aplikace orchestrace opravy je alternativní řešení, které je určené pro hostování mimo Azure, i když je v Azure možné použít POA, se základním důvodem pro hostování rodu POA v Azure je běžným důvodem pro upřednostnění automatického upgradu operačního systému virtuálního počítače přes POA. Níže jsou uvedeny vlastnosti šablony COMPUTE Virtual Machine Scale Správce prostředků, které umožňují automatický upgrade operačního systému:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Pokud používáte automatické upgrady operačního systému pomocí Service Fabric, nová image operačního systému se v jednu chvíli odvede na jednu aktualizační doménu, aby se zachovala vysoká dostupnost služeb běžících v Service Fabric. Pokud chcete použít automatické upgrady operačního systému v Service Fabric musíte cluster nakonfigurovat tak, aby používal úroveň odolnosti stříbra nebo vyšší.

Zajistěte, aby byl následující klíč registru nastaven na hodnotu false, aby bylo možné, aby hostitelské počítače s Windows nezahájily nekoordinované aktualizace: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Níže jsou uvedeny vlastnosti šablony COMPUTE Virtual Machine Scale Správce prostředků pro nastavení klíče registru WindowsUpdate na hodnotu false:
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
Následuje Service Fabric vlastnost Správce prostředků šablony clusteru, která umožňuje automatický upgrade:
```json
"upgradeMode": "Automatic",
```
Pokud chcete cluster upgradovat ručně, Stáhněte si distribuci souborů CAB/deb do virtuálního počítače clusteru a pak vyvolejte následující PowerShell:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Další kroky

* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Windows Server: [Service Fabric vytvoření clusteru pro Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Linux: [Vytvoření clusteru se systémem Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)
