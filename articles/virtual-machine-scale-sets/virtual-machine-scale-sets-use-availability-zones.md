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
ms.date: 04/05/2018
ms.author: iainfou
ms.openlocfilehash: cc91366bda4dd443392c78cd61e5cc0ddb261fe2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Vytvořit sadu škálování virtuálního počítače, která používá dostupnost zóny

K ochraně vaší sady škálování virtuálního počítače datacenter úrovni selhání, můžete vytvořit v rámci zóny dostupnosti nastavit škálování. Oblasti Azure, které podporují dostupnost zóny mít minimálně tři samostatné zóny, každou s vlastní nezávisle spotřeby zdroje, sítě a chlazení. Další informace najdete v tématu [přehled dostupnosti zón](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Aspekty dostupnosti

Když nasadíte škálování nastavení do jedné nebo více zón, od verze rozhraní API *2017-12-01*, máte možnost nasadit s "maximální šíření" nebo "statické 5 selhání domény šíření". S maximální šíření šíří škálovací sadu virtuálních počítačů mezi počet domén poruchy nejdříve v každé zóně. Tato rozšíření by mohl být v rámci větší nebo méně než pět poruch domén na zóny. S "statické 5 selhání domény šíření", šíří škálovací sadu virtuálních počítačů mezi doménami přesně pět selhání na zóny. Pokud byly sadou škálování nemůže najít pět domén selhání odlišné na zóny pro uspokojení požadavek na přidělení, žádost skončí s chybou.

**Doporučujeme, abyste nasazení s maximální šíření u většiny úloh**, jak tento přístup poskytuje nejlepší šíření ve většině případů. Pokud potřebujete replik možné rozdělit do různých hardwaru izolace jednotky, doporučujeme nad dostupnost zóny a využívat maximální rozprostření v každé zóně.

S maximální šíření zobrazí pouze doména selhání, která je v zobrazení instance škálovací sady virtuálních počítačů a v metadatech instance bez ohledu na to, kolik domén selhání jsou rozloženy virtuálních počítačů. Rozšíření v rámci každé zóny je implicitní.

Pro použití maximální rozšíření nastavte *platformFaultDomainCount* k *1*. Pro použití statické pět selhání domény rozšíření nastavte *platformFaultDomainCount* k *5*. Ve verzi rozhraní API *2017-12-01*, *platformFaultDomainCount* výchozí *1* jedním zónu a cross zónu škálování nastaví. V současné době pouze statické pět selhání domény šíření je podporována pro místní škálovací sady.

### <a name="placement-groups"></a>Skupiny umístění

Pokud nasadíte sadu škálování, máte také možnost nasadit s jedním [umístění skupiny](./virtual-machine-scale-sets-placement-groups.md) na dostupnost zóny, nebo s několika na zóny. Pro místní škálovací sady volba je tak, aby měl jeden umístění skupinu v oblasti nebo více v oblasti. U většiny úloh doporučujeme, abyste několika umístění skupin, které umožňuje větší škálování. Ve verzi rozhraní API *2017-12-01*, škálování nastaví výchozí do několika skupin umístění pro sady škálování jedním zónu a cross zónu, ale jejich výchozí do jednoho umístění skupiny pro místní škálovací sady.

> [!NOTE]
> Pokud používáte maximální rozšíření, musíte použít více skupin pro umístění.

### <a name="zone-balancing"></a>Zóny vyrovnávání

Nakonec pro sady škálování nasazení napříč několika zón, máte také možnost zvolit "nejlepší úsilí zóny balance" nebo "balance striktní zónu". Sada škálování považuje za "vyrovnáváním" Pokud je počet virtuálních počítačů v každé zóně do jedné z počet virtuálních počítačů ve všech zónách pro škálovací sadu. Příklad:

- Škálování nastavit s 2 virtuální počítače v zóně 1, 3 virtuální počítače v zóně, 2 a 3 virtuální počítače v zóně 3 je považován za vyrovnáváním.
- Škálování nastavit s 1 virtuálního počítače v zóně 1, 3 virtuální počítače v zóně, 2 a 3 virtuální počítače v zóně 3 je považován za nevyvážené.

Je možné, že virtuální počítače v sadě škálování se úspěšně vytvoří, ale rozšíření na těchto virtuálních počítačů se nepodařilo nasadit. Tyto virtuální počítače s chybami rozšíření stále počítají při určování, pokud jsou rovnoměrně škálovací sadu. Pro instanci škálování nastavit s 3 virtuální počítače v zóně 1, 3 virtuální počítače v zóně 2 a 3 virtuální počítače v zóně 3 považuje za vyrovnáváním i v případě, že všechna rozšíření se nezdařilo v zóně 1 a 2 a 3, bylo úspěšné všechna rozšíření v zónách.

S balance zóny best effort nastavte měřítka pokusy o škálování a odhlašování při zachování vyrovnávání. Nicméně pokud z nějakého důvodu není možné (například pokud jednu zónu přestane fungovat, škálovací sadu nelze vytvořit nový virtuální počítač v této zóně), byly sadou škálování umožňuje dočasné nevyváženosti úspěšně škálovat příchozí nebo odchozí. Při dalších pokusech Škálováním na více systémů přidá škálovací sadu virtuálních počítačů do zóny, které pro škálování nastavena na vyváženy potřebovat další virtuální počítače. Podobně na následné škálování pokusů o přihlášení, odebere škálovací sadu virtuálních počítačů ze zón, které je třeba méně virtuálních počítačů pro škálování nastavena na vyváženy. S "striktní zóny balance" se nezdaří pokusy o škálování příchozí nebo odchozí, pokud to by způsobilo nevyvážené škálovací sadu.

Chcete-li použít vyrovnávání zóny best effort, nastavte *zoneBalance* k *false*. Toto nastavení je výchozí verze rozhraní API *2017-12-01*. Chcete-li použít vyrovnávání striktní zóny, nastavte *zoneBalance* k *true*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Sady škálování jedním zóny a zónově redundantní

Když nasadíte škálovací sadu virtuálních počítačů, můžete použít jednu zónu dostupnosti v oblasti nebo několika zón.

Při vytváření sad v jedné oblasti, kterou řídíte, zóně, ve které všechny tyto instance virtuálních počítačů spouštění v a je spravovaný škálovací sadu a autoscales pouze v rámci této zóny škálování. Zónově redundantní škálovací sadu umožňuje vytvářet jeden škálovací sadu, která přesahuje více zón. Vytvářené instance virtuálních počítačů, ve výchozím nastavení se jsou rovnoměrně rozložena mezi zóny. V jedné ze zón dojít přerušení, škálovací sadu není škálovat automaticky navýšení kapacity. Osvědčeným postupem bude ke konfiguraci pravidel škálování podle využití procesoru nebo paměti. Pravidla automatického škálování by umožní reagovat na ztrátu instance virtuálních počítačů v této jednu zónu pomocí škálování nové instance v zbývající provozní zóny sad škálování.

K používání zón dostupnosti, musí být škálovací sadu vytvořené v [podporované oblasti Azure](../availability-zones/az-overview.md#regions-that-support-availability-zones). Můžete vytvořit sada škálování, které používá dostupnost zóny s jedním z následujících metod:

- [Azure Portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Šablony Azure Resource Manageru](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Proces vytvoření sada škálování, které používá dostupnosti zóny je stejný jako podrobné v [Začínáme článku](quick-create-portal.md). Když vyberete podporovanou oblast Azure, můžete vytvořit škálování nastavit v jedné nebo více dostupných zón, jak je znázorněno v následujícím příkladu:

![Vytvoření sad v jedné zóně dostupnosti škálování](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Sada škálování a podpůrné prostředky, například pro vyrovnávání zatížení Azure a veřejné IP adresy, jsou vytvořené v jedné oblasti, který určíte.

## <a name="use-the-azure-cli-20"></a>Použití Azure CLI 2.0

Proces vytvoření sada škálování, které používá dostupnosti zóny je stejný jako podrobné v [Začínáme článku](quick-create-cli.md). K používání zón dostupnosti, je nutné vytvořit váš škálování nastavit v podporovanou oblast Azure.

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
    --zones 1 2 3
```

Trvá několik minut vytvořit a nakonfigurovat všechny, které měřítka nastavit zdroje a virtuální počítače v zón, který určíte. Zónově redundantní měřítka kompletní příklad sady a síťové prostředky naleznete v tématu [tento ukázkový skript rozhraní příkazového řádku](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Použití Azure Powershell

K používání zón dostupnosti, je nutné vytvořit váš škálování nastavit v podporovanou oblast Azure. Přidat `-Zone` parametru [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) příkaz a zadejte zóně, ve které chcete použít (například zóny *1*, *2*, nebo *3*).

Následující příklad vytvoří zónu jedním škálování nastavení s názvem *myScaleSet* v *východní USA 2* zóny *1*. Automaticky se vytvoří síťové prostředky Azure pro virtuální síť, veřejná IP adresa a nástroj pro vyrovnávání zatížení. Po zobrazení výzvy zadejte požadované přihlašovací údaje pro správu instancí virtuálních počítačů ve škálovací sadě:

```powershell
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>Zónově redundantní škálovací sadu

Pokud chcete vytvořit sadu zónově redundantní škálování, zadejte více zónách s `-Zone` parametr. Následující příklad vytvoří zónu redundantní škálování nastavení s názvem *myScaleSet* napříč *východní USA 2* zón *1, 2, 3*. Zónově redundantní Azure síťovým prostředkům pro virtuální sítě, veřejnou IP adresu a nástroj pro vyrovnávání zatížení se automaticky vytvoří. Po zobrazení výzvy zadejte požadované přihlašovací údaje pro správu instancí virtuálních počítačů ve škálovací sadě:

```powershell
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Použití šablon Azure Resource Manageru

Proces vytvoření sada škálování, které používá dostupnosti zóny je stejný jako v článku na získávání Začínáme pro podrobné [Linux](quick-create-template-linux.md) nebo [Windows](quick-create-template-windows.md). K používání zón dostupnosti, je nutné vytvořit váš škálování nastavit v podporovanou oblast Azure. Přidat `zones` vlastnost, která má *Microsoft.Compute/virtualMachineScaleSets* prostředků zadejte v šabloně a zóně, ve které chcete použít (například zóny *1*, *2*, nebo *3*).

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

Teď, když jste vytvořili škálování nastavit v zóně dostupnosti, můžete další postup [nasazení aplikace na virtuálním počítači škálování sady](tutorial-install-apps-cli.md) nebo [škálování pomocí sady škálování virtuálního počítače](tutorial-autoscale-cli.md).
