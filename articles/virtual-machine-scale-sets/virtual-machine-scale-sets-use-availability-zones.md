---
title: Vytvořit sadu Azure škálování používající dostupnost zóny (Preview) | Microsoft Docs
description: Informace o vytvoření sady škálování virtuálního počítače Azure, které používají dostupnost zóny pro vyšší redundance proti výpadkům
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: iainfou
ms.openlocfilehash: 8b497af8bc7e3060e184dd6a029b23ccb2d2bbfb
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Vytvoření škálovací sadu virtuálních počítačů, který používá dostupnost zóny (Preview)
K ochraně vaší sady škálování virtuálního počítače datacenter úrovni selhání, můžete vytvořit v rámci zóny dostupnosti nastavit škálování. Oblasti Azure, které podporují dostupnost zóny mít minimálně tři samostatné zóny, každou s vlastní nezávisle spotřeby zdroje, sítě a chlazení. Další informace najdete v tématu [přehled dostupnosti zón](../availability-zones/az-overview.md).

[!INCLUDE [availability-zones-preview-statement.md](../../includes/availability-zones-preview-statement.md)]


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Sady škálování jedním zóny a zónově redundantní
Když nasadíte škálovací sadu virtuálních počítačů, můžete použít jednu zónu dostupnosti v oblasti nebo několika zón.

Při vytváření sad v jedné oblasti, kterou řídíte, zóně, ve které všechny tyto instance virtuálních počítačů spouštění v a je spravovaný škálovací sadu a autoscales pouze v rámci této zóny škálování. Zónově redundantní škálovací sadu umožňuje vytvářet jeden škálovací sadu, která přesahuje více zón. Vytvářené instance virtuálních počítačů, ve výchozím nastavení se jsou rovnoměrně rozložena mezi zóny. V jedné ze zón dojít přerušení, škálovací sadu není škálovat automaticky navýšení kapacity. Osvědčeným postupem bude ke konfiguraci pravidel škálování podle využití procesoru nebo paměti. Pravidla automatického škálování by umožní reagovat na ztrátu instance virtuálních počítačů v této jednu zónu pomocí škálování nové instance v zbývající provozní zóny sad škálování.

K používání zón dostupnosti, musí být škálovací sadu vytvořené v [podporované oblasti Azure](../availability-zones/az-overview.md#regions-that-support-availability-zones). Musíte také [zaregistrovat pro náhled zóny dostupnosti](http://aka.ms/azenroll). Můžete vytvořit sada škálování, které používá dostupnost zóny s jedním z následujících metod:

- [Azure Portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Šablony Azure Resource Manageru](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Proces vytvoření sada škálování, které používá dostupnosti zóny je stejný jako podrobné v [Začínáme článku](quick-create-portal.md). Ujistěte se, že máte [zaregistrovanou zóny dostupnosti Náhled](http://aka.ms/azenroll). Když vyberete podporovanou oblast Azure, můžete vytvořit škálování nastavit v jedné ze zón k dispozici, jak je znázorněno v následujícím příkladu:

![Vytvoření sad v jedné zóně dostupnosti škálování](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

Sada škálování a podpůrné prostředky, například pro vyrovnávání zatížení Azure a veřejné IP adresy, jsou vytvořené v jedné oblasti, který určíte.


## <a name="use-the-azure-cli-20"></a>Použití Azure CLI 2.0
Proces vytvoření sada škálování, které používá dostupnosti zóny je stejný jako podrobné v [Začínáme článku](quick-create-cli.md). K používání zón dostupnost, musíte vytvořit škálovací sadu v podporovanou oblast Azure a mít [zaregistrovanou zóny dostupnosti Náhled](http://aka.ms/azenroll).

Přidat `--zones` parametru [vytvořit az vmss](/cli/azure/vmss#az_vmss_create) příkaz a zadejte zóně, ve které chcete použít (například zóny *1*, *2*, nebo *3*). Následující příklad vytvoří zónu jedním škálování nastavení s názvem *myScaleSet* v zóně *1*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```
Úplný příklad měřítka jedním zóny nastavení a síťové prostředky najdete [tento ukázkový skript rozhraní příkazového řádku](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.)

### <a name="zone-redundant-scale-set"></a>Zónově redundantní škálovací sadu
Vytvořit zónově redundantní měřítko nastaven, je použít *standardní* SKU veřejné IP adresy a zatížení vyrovnávání. Rozšířené redundanci *standardní* SKU vytvoří zónově redundantní síťovým prostředkům. Další informace najdete v tématu [Azure zatížení vyrovnávání standardní přehled](../load-balancer/load-balancer-standard-overview.md). Při prvním vytvoření zónově redundantní škálování nastavit nebo službu Vyrovnávání zatížení, musí dokončit následující kroky pro registraci účtu pro tyto funkce verze preview.

1. Registrace účtu pro sadu zónově redundantní škálování a síťových funkcí s použitím [zaregistrovat funkci az](/cli/azure/feature#az_feature_register) následujícím způsobem:

    ```azurecli
    az feature register --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. Ho může trvat několik minut k registraci pro funkce. Můžete zkontrolovat stav operaci s [zobrazit funkce az](/cli/azure/feature#az_feature_show):

    ```azurecli
    az feature show --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```

    Následující příklad ukazuje požadovaný stav funkce jako *registrovaná*:
    
    ```json
    "properties": {
          "state": "Registered"
       },
    ```

3. Po nastavení zónově redundantní škálování a síťové prostředky hlásit jako *registrovaná*, znovu zaregistrovat *výpočetní* a *sítě* zprostředkovatelé s [az Registrace zprostředkovatele](/cli/azure/provider#az_provider_register) následujícím způsobem:

    ```azurecli
    az provider register --namespace Microsoft.Compute
    az provider register --namespace Microsoft.Network
    ```

Pokud chcete vytvořit sadu zónově redundantní škálování, zadejte více zónách s `--zones` parametr. Následující příklad vytvoří zónu redundantní škálování nastavení s názvem *myScaleSet* napříč zón *1,2,3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones {1,2,3}
```

Trvá několik minut vytvořit a nakonfigurovat všechny, které měřítka nastavit zdroje a virtuální počítače v zón, který určíte. Zónově redundantní měřítka kompletní příklad sady a síťové prostředky naleznete v tématu [tento ukázkový skript rozhraní příkazového řádku](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)


## <a name="use-azure-powershell"></a>Použití Azure Powershell
Proces vytvoření sada škálování, které používá dostupnosti zóny je stejný jako podrobné v [Začínáme článku](quick-create-powershell.md). K používání zón dostupnost, musíte vytvořit škálovací sadu v podporovanou oblast Azure a mít [zaregistrovanou zóny dostupnosti Náhled](http://aka.ms/azenroll). Přidat `-Zone` parametru [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) příkaz a zadejte zóně, ve které chcete použít (například zóny *1*, *2*, nebo *3*). 

Následující příklad vytvoří konfigurace sady škálování jedním zónu s názvem *vmssConfig* v *východní USA 2* zóny *1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

Úplný příklad měřítka jedním zóny nastavení a síťové prostředky najdete [tento ukázkový skript prostředí PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1)

### <a name="zone-redundant-scale-set"></a>Zónově redundantní škálovací sadu
Vytvořit zónově redundantní měřítko nastaven, je použít *standardní* SKU veřejné IP adresy a zatížení vyrovnávání. Rozšířené redundanci *standardní* SKU vytvoří zónově redundantní síťovým prostředkům. Další informace najdete v tématu [Azure zatížení vyrovnávání standardní přehled](../load-balancer/load-balancer-standard-overview.md). Při prvním vytvoření zónově redundantní škálování nastavit nebo službu Vyrovnávání zatížení, musí dokončit následující kroky pro registraci účtu pro tyto funkce verze preview.

1. Registrace účtu pro sadu zónově redundantní škálování a síťových funkcí s použitím [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) následujícím způsobem:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. Ho může trvat několik minut k registraci pro funkce. Můžete zkontrolovat stav operaci s [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature):

    ```powershell
    Get-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute 
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    Následující příklad ukazuje požadovaný stav funkce jako *registrovaná*:
    
    ```powershell
    RegistrationState
    -----------------
    Registered
    ```

3. Po nastavení zónově redundantní škálování a síťové prostředky hlásit jako *registrovaná*, znovu zaregistrovat *výpočetní* a *sítě* zprostředkovatelé s [ Registrace AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) následujícím způsobem:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

Pokud chcete vytvořit sadu zónově redundantní škálování, zadejte více zónách s `-Zone` parametr. Následující příklad vytvoří konfigurace zónově redundantní škálování sadu s názvem *myScaleSet* napříč *východní USA 2* zón *1, 2, 3*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1", "2", "3"
```

Pokud vytvoříte veřejnou IP adresu s [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) nebo Vyrovnávání zatížení s [New-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/New-AzureRmLoadBalancer), zadejte *- SKU "Standard"* k vytvoření zónově redundantní síťovým prostředkům. Také musíte vytvořit skupinu zabezpečení sítě a pravidla tak, aby povolovala přenosy. Další informace najdete v tématu [Azure zatížení vyrovnávání standardní přehled](../load-balancer/load-balancer-standard-overview.md).

Zónově redundantní měřítka kompletní příklad sady a síťové prostředky naleznete v tématu [tento ukázkový skript prostředí PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1)


## <a name="use-azure-resource-manager-templates"></a>Použití šablon Azure Resource Manageru
Proces vytvoření sada škálování, které používá dostupnosti zóny je stejný jako v článku na získávání Začínáme pro podrobné [Linux](quick-create-template-linux.md) nebo [Windows](quick-create-template-windows.md). K používání zón dostupnost, musíte vytvořit škálovací sadu v podporovanou oblast Azure a mít [zaregistrovanou zóny dostupnosti Náhled](http://aka.ms/azenroll). Přidat `zones` vlastnost, která má *Microsoft.Compute/virtualMachineScaleSets* prostředků zadejte v šabloně a zóně, ve které chcete použít (například zóny *1*, *2*, nebo *3*).

Následující příklad vytvoří škálování jedním zóny Linux nastavení s názvem *myScaleSet* v *východní USA 2* zóny *1*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Kompletní příklad měřítka jedním zóny nastavit a síťové prostředky, najdete v části [této ukázkové šablony Resource Manageru](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zónově redundantní škálovací sadu
Pokud chcete vytvořit sadu zónově redundantní škálování, zadejte více hodnot v `zones` vlastnost *Microsoft.Compute/virtualMachineScaleSets* typ prostředku. Následující příklad vytvoří zónu redundantní škálování nastavení s názvem *myScaleSet* napříč *východní USA 2* zón *1,2,3*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Pokud vytvoříte veřejnou IP adresu nebo Vyrovnávání zatížení, zadejte *"sku": {"název": "Standard"} "* vlastnost k vytvoření zóny redundantních síťových prostředků. Také musíte vytvořit skupinu zabezpečení sítě a pravidla tak, aby povolovala přenosy. Další informace najdete v tématu [Azure zatížení vyrovnávání standardní přehled](../load-balancer/load-balancer-standard-overview.md).

Zónově redundantní měřítka kompletní příklad sady a síťové prostředky naleznete v tématu [této ukázkové šablony Resource Manageru](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)


## <a name="next-steps"></a>Další postup
Teď, když jste vytvořili škálování nastavit v zóně dostupnosti, můžete další postup [nasazení aplikace na virtuálním počítači škálování sady](virtual-machine-scale-sets-deploy-app.md) nebo [škálování pomocí sady škálování virtuálního počítače](virtual-machine-scale-sets-autoscale-overview.md).
