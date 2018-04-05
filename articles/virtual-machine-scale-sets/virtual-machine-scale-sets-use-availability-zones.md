---
title: Vytvořit sadu Azure škálování používající dostupnost zóny | Microsoft Docs
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
ms.date: 03/07/2018
ms.author: iainfou
ms.openlocfilehash: dee06eee045bc24c2864333a66a6d145a771b3ad
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Vytvořit sadu škálování virtuálního počítače, která používá dostupnost zóny
K ochraně vaší sady škálování virtuálního počítače datacenter úrovni selhání, můžete vytvořit v rámci zóny dostupnosti nastavit škálování. Oblasti Azure, které podporují dostupnost zóny mít minimálně tři samostatné zóny, každou s vlastní nezávisle spotřeby zdroje, sítě a chlazení. Další informace najdete v tématu [přehled dostupnosti zón](../availability-zones/az-overview.md).


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Sady škálování jedním zóny a zónově redundantní
Když nasadíte škálovací sadu virtuálních počítačů, můžete použít jednu zónu dostupnosti v oblasti nebo několika zón.

Při vytváření sad v jedné oblasti, kterou řídíte, zóně, ve které všechny tyto instance virtuálních počítačů spouštění v a je spravovaný škálovací sadu a autoscales pouze v rámci této zóny škálování. Zónově redundantní škálovací sadu umožňuje vytvářet jeden škálovací sadu, která přesahuje více zón. Vytvářené instance virtuálních počítačů, ve výchozím nastavení se jsou rovnoměrně rozložena mezi zóny. V jedné ze zón dojít přerušení, škálovací sadu není škálovat automaticky navýšení kapacity. Osvědčeným postupem bude ke konfiguraci pravidel škálování podle využití procesoru nebo paměti. Pravidla automatického škálování by umožní reagovat na ztrátu instance virtuálních počítačů v této jednu zónu pomocí škálování nové instance v zbývající provozní zóny sad škálování.

K používání zón dostupnosti, musí být škálovací sadu vytvořené v [podporované oblasti Azure](../availability-zones/az-overview.md#regions-that-support-availability-zones). Můžete vytvořit sada škálování, které používá dostupnost zóny s jedním z následujících metod:

- [Azure Portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Šablony Azure Resource Manageru](#use-azure-resource-manager-templates)

## <a name="availability-considerations"></a>Aspekty dostupnosti
Od verze rozhraní API verze 2017-12-01, když nasazujete škálování nastavení do několika zón, máte možnost nasadit s "maximální šíření" nebo "statické 5 selhání domény šíření". S maximální šíření šíří škálovací sadu virtuálních počítačů mezi počet domén poruchy nejdříve v každé zóně. Tato rozšíření by mohl být v rámci větší nebo méně než pět poruch domén na zóny. Na druhé straně s "statické 5 selhání domény šíření", šíří škálovací sadu virtuálních počítačů mezi doménami selhání přesně 5 na zóny. Pokud byly sadou škálování nemůže najít 5 domén selhání odlišné na zóny pro uspokojení požadavek na přidělení, žádost skončí s chybou.

**Doporučujeme, abyste nasazení s maximální šíření u většiny úloh** vzhledem k tomu, že maximální šíření poskytuje nejlepší šíření ve většině případů. Pokud potřebujete replik možné rozdělit do různých hardwaru izolace jednotky, doporučujeme nad pásem dostupnosti a využití maximální rozprostření v každé zóně. Se s maximální šíření jenom zobrazit doména selhání, která je v zobrazení instance škálovací sady virtuálních počítačů a v metadatech instance bez ohledu na to, kolik domén selhání virtuálních počítačů jsou ve skutečnosti rozloženy; rozšíření v rámci každé zóny je implicitní.

Chcete-li použít maximální rozprostření, nastavte "platformFaultDomainCount" 1. Pokud chcete používat statickou 5 selhání domény šíření, nastavena na 5 "platformFaultDomainCount". V rozhraní API verze 2017-12-01 "platformFaultDomainCount" výchozí hodnotu 1 pro sady škálování jedním zónu a cross zónu. V současné době pouze statické 5 selhání domény šíření je podporována pro místní škálovací sady.

Kromě toho při nasazení sady škálování, máte možnost nasazení s jedním [umístění skupiny](./virtual-machine-scale-sets-placement-groups.md) na dostupnost zóny, nebo s více za zóny (pro místní škálovací sady volba je tak, aby měl jeden umístění skupiny oblast nebo více v oblasti). U většiny úloh doporučujeme používat více skupin umístění, což umožňuje větší škálování. V rozhraní API verze 2017-12-01 škálování nastaví výchozí do několika skupin umístění pro sady škálování jedním zónu a cross zónu, ale jejich výchozí do jednoho umístění skupiny pro místní škálovací sady.

>[!NOTE]
> Pokud používáte maximální rozšíření, musíte použít více skupin pro umístění.

Nakonec pro sady škálování nasazení napříč několika zón, máte také možnost zvolit "nejlepší úsilí zóny balance" nebo "balance striktní zónu". Sada škálování považuje za "vyrovnáváním" Pokud je počet virtuálních počítačů v každé zóně do jedné z počet virtuálních počítačů ve všech zónách pro škálovací sadu. Pro instance škále nastavit s 2 virtuální počítače v zóně 1, 3 virtuální počítače v zóně 2 a 3 virtuální počítače v zóně 3 je považován za vyrovnáváním. Ale škálování nastavit s 1 virtuálního počítače v zóně 1, 3 virtuální počítače v zóně, 2 a 3 virtuální počítače v zóně 3 je považován za nevyvážené. Je možné, že virtuální počítače v sadě škálování se úspěšně vytvořil, při selhání rozšíření na těchto virtuálních počítačích. Tyto virtuální počítače s chybami rozšíření stále počítají při určování, pokud jsou rovnoměrně škálovací sadu. Pro instanci škálování nastavit s 3 virtuální počítače v zóně 1, 3 virtuální počítače v zóně 2 a 3 virtuální počítače v zóně 3 považuje za vyrovnáváním i v případě, že všechna rozšíření se nezdařilo v zóně 1 a 2 a 3, bylo úspěšné všechna rozšíření v zónách. S nejlepší balance zóny úsilí nastavte měřítka pokusy o škálování a odhlašování při zachování vyrovnávání. Nicméně pokud z nějakého důvodu není možné (například jednu zónu přestane fungovat, aby byly sadou škálování nelze vytvořit nový virtuální počítač v této zóně), pak škálovací sadu vám umožní dočasné nevyvážené, aby bylo možné úspěšně škálovat příchozí nebo odchozí. Na následné škálování pokusy o přidá škálovací sadu virtuálních počítačů do zóny, které pro škálování nastavena na vyváženy potřebovat další virtuální počítače. Podobně na následné škálování pokusů o přihlášení, odebere škálovací sadu virtuálních počítačů ze zón, které je třeba méně virtuálních počítačů pro škálování nastavena na vyváženy. Na druhé straně s "striktní zóny balance", byly sadou škálování nezdaří pokusy o škálování příchozí nebo odchozí, pokud to by způsobilo nevyvážené.

Pokud chcete použít vyvážit zóny úsilí, nastavte na hodnotu false (výchozí nastavení v rozhraní API verze 2017-12-01) "zoneBalance". Pokud chcete použít vyrovnávání striktní zóny, nastavte "zoneBalance" na hodnotu true.

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
Vytvořit zónově redundantní měřítko nastaven, je použít *standardní* SKU veřejné IP adresy a zatížení vyrovnávání. Rozšířené redundanci *standardní* SKU vytvoří zónově redundantní síťovým prostředkům. Další informace najdete v tématu [Azure zatížení vyrovnávání standardní přehled](../load-balancer/load-balancer-standard-overview.md). 

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
Vytvořit zónově redundantní měřítko nastaven, je použít *standardní* SKU veřejné IP adresy a zatížení vyrovnávání. Rozšířené redundanci *standardní* SKU vytvoří zónově redundantní síťovým prostředkům. Další informace najdete v tématu [Azure zatížení vyrovnávání standardní přehled](../load-balancer/load-balancer-standard-overview.md).

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

Kompletní příklad měřítka jedním zóny nastavit a síťové prostředky, najdete v části [této ukázkové šablony Resource Manageru](https://github.com/Azure/vm-scale-sets/blob/master/zones/singlezone.json)

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

Zónově redundantní měřítka kompletní příklad sady a síťové prostředky naleznete v tématu [této ukázkové šablony Resource Manageru](https://github.com/Azure/vm-scale-sets/blob/master/zones/multizone.json)


## <a name="next-steps"></a>Další postup
Teď, když jste vytvořili škálování nastavit v zóně dostupnosti, můžete další postup [nasazení aplikace na virtuálním počítači škálování sady](virtual-machine-scale-sets-deploy-app.md) nebo [škálování pomocí sady škálování virtuálního počítače](virtual-machine-scale-sets-autoscale-overview.md).
