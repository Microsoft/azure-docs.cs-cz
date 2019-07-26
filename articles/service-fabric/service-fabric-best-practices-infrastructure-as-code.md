---
title: Infrastruktura Azure Service Fabric jako osvědčené postupy pro kód | Microsoft Docs
description: Osvědčené postupy pro správu Service Fabric jako infrastruktury jako kódu.
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
ms.openlocfilehash: ae1cd0912733116dce1b550dd937cc9fc5f8737b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359766"
---
# <a name="infrastructure-as-code"></a>Infrastruktura jako kód

V produkčním scénáři Vytvořte clustery Azure Service Fabric pomocí šablon Správce prostředků. Šablony Správce prostředků poskytují větší kontrolu nad vlastnostmi prostředku a zajišťují, že máte konzistentní model prostředků.

Ukázkové šablony Správce prostředků jsou k dispozici pro Windows a Linux v [ukázkách Azure na GitHubu](https://github.com/Azure-Samples/service-fabric-cluster-templates). Tyto šablony lze použít jako výchozí bod pro šablonu clusteru. Stáhněte si `azuredeploy.json` je a upravte je tak, aby splňovaly vaše vlastní požadavky. `azuredeploy.parameters.json`

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud chcete nasadit `azuredeploy.json` šablony `azuredeploy.parameters.json` a, které jste si stáhli, použijte následující příkazy rozhraní příkazového řádku Azure:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
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

Aplikace a služby můžete do clusteru Service Fabric nasadit prostřednictvím Azure Resource Manageru. Podrobnosti najdete v tématu [Správa aplikací a služeb jako Azure Resource Manager prostředků](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) . Níže jsou uvedené osvědčené postupy Service Fabric prostředků specifických pro aplikaci, které se mají zahrnout do prostředků šablon Správce prostředků.

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

Chcete-li nasadit aplikaci pomocí Azure Resource Manager, musíte nejprve [vytvořit](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) balíček aplikace Service Fabric sfpkg. Následující skript Pythonu je příkladem vytvoření sfpkg:

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
Upgrade virtuálních počítačů je operace iniciovaná uživatelem. doporučuje se použít [Automatický upgrade operačního systému](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) pro Azure Service Fabric clustery pro správu oprav hostitele; Aplikace Orchestration je alternativní řešení, které je určené pro hostování mimo Azure, i když POA se v Azure dá použít i v případě, že je v Azure k disrežii hostování rodu POA v Azure, což je běžný důvod k upřednostnění automatického upgradu operačního systému virtuálního počítače. přes POA. Níže jsou uvedeny vlastnosti šablony COMPUTE Virtual Machine Scale Správce prostředků, které umožňují automatický upgrade operačního systému:

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

Zajistěte, aby byl následující klíč registru nastaven na hodnotu false, aby bylo možné, aby hostitelské počítače s Windows nezahájily nekoordinovatelné aktualizace: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

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

* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Windows Server: [Vytvoření clusteru Service Fabric pro Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Linux: [Vytvoření clusteru se systémem Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)
