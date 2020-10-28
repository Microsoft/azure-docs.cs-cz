---
title: Vytvoření sady škálování Azure, která používá Zóny dostupnosti
description: Naučte se vytvářet služby Azure Virtual Machine Scale Sets, které využívají Zóny dostupnosti ke zvýšení redundance před výpadky.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 08/08/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: c5ddd5846be91e9fc99a251d6ad45ade8bde2937
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745843"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Vytvoření sady škálování virtuálních počítačů, která používá Zóny dostupnosti

Pokud chcete ochránit vaše služby Virtual Machine Scale Sets před selháními na úrovni datacentra, můžete vytvořit sadu škálování napříč Zóny dostupnosti. Oblasti Azure, které podporují Zóny dostupnosti, mají minimálně tři samostatné zóny, z nichž každý má vlastní nezávislý zdroj napájení, síť a chlazení. Další informace najdete v tématu [přehled zóny dostupnosti](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Aspekty dostupnosti

Když nasadíte škálu regionů (mimo oblast) do jedné nebo více zón jako rozhraní API verze *2017-12-01* , máte následující možnosti dostupnosti:
- Maximální rozprostření (platformFaultDomainCount = 1)
- Statické pevné rozprostření (platformFaultDomainCount = 5)
- Rozprostření zarovnané k doménám selhání disků úložiště (platforFaultDomainCount = 2 nebo 3)

Díky maximálnímu rozprostření sada škálování rozšiřuje vaše virtuální počítače napříč tolik domén selhání v rámci každé zóny. Toto rozprostření může být v rozmezí větší nebo menší než pět domén selhání na zónu. Při statickém pevném rozprostření sada škálování rozšíří vaše virtuální počítače napříč přesně pět domén selhání na zónu. Pokud sada škálování nemůže najít pět jedinečných domén selhání na zónu, aby splňovala požadavek na přidělení, požadavek selže.

Doporučujeme, abyste nasadili **maximální rozprostření pro většinu úloh** , protože tento přístup ve většině případů poskytuje nejlepší rozprostření. Pokud potřebujete, aby byly repliky rozloženy v rámci různých jednotek izolace hardwaru, doporučujeme rozprostření napříč Zóny dostupnosti a využití maximálního rozprostření v rámci každé zóny.

> [!NOTE]
> Při maximálním rozprostření se v zobrazení instance virtuálního počítače sady škálování a v metadatech instance zobrazí jenom jedna doména selhání, a to bez ohledu na to, kolik domén selhání jsou virtuální počítače rozdělené napříč. Rozprostření v rámci každé zóny je implicitní.

### <a name="placement-groups"></a>Skupiny umístění

Když nasadíte sadu škálování, máte také možnost nasazení s jedinou [skupinou umístění](./virtual-machine-scale-sets-placement-groups.md) na jednu zónu dostupnosti nebo s více než jednou zónou. Pro škálované sady oblastí (mimo oblast) je volba jedinou skupinou umístění v oblasti nebo má více v oblasti. Pro většinu úloh doporučujeme více skupin umístění, které umožňují větší škálování. V rozhraní API verze *2017-12-01* sada škálování nastaví jako výchozí více skupin umístění pro sadu škálování s jednou zónou a mezi zónami, ale jako výchozí nastaví jednu skupinu umístění pro oblastní sady škálování (mimo oblast).

> [!NOTE]
> Použijete-li maximální rozprostření, je nutné použít více skupin umístění.

### <a name="zone-balancing"></a>Vyrovnávání zóny

Pro škálované sady nasazené napříč několika zónami máte také možnost zvolit "nejlepší bilance zóny" nebo "striktní bilance zóny". Sada škálování se považuje za vyváženou, pokud každá zóna má stejný počet virtuálních počítačů nebo \\ virtuálních počítačů +-1 ve všech ostatních zónách pro sadu škálování. Příklad:

- Sada škálování se dvěma virtuálními počítači v zóně 1, 3 virtuálními počítači v zóně 2 a 3 virtuálními počítači v zóně 3 je považována za vyváženou. Existuje pouze jedna zóna s jiným počtem virtuálních počítačů a je pouze 1 méně než ostatní zóny. 
- Sada škálování s 1 virtuálním počítačem v zóně 1, 3 virtuálními počítači v zóně 2 a 3 virtuálními počítači v zóně 3 se považují za nevyvážené. Zóna 1 má 2 méně virtuálních počítačů než zóny 2 a 3.

Je možné, že se virtuální počítače v sadě škálování úspěšně vytvořily, ale rozšíření na těchto virtuálních počítačích se nepodaří nasadit. Tyto virtuální počítače s chybami rozšíření se pořád počítají při určování, jestli je sada škálování vyvážená. Například škálování sady se 3 virtuálními počítači v zóně 1, 3 virtuálními počítači v zóně 2 a 3 virtuální počítače v zóně 3 se považují za vyvážené, i když všechna rozšíření v zóně 1 selhala a všechna rozšíření byla úspěšná v zónách 2 a 3.

S využitím rovnováhy zón se sada škálování při zachování rovnováhy pokusí škálovat a zmenšit. Nicméně pokud z nějakého důvodu to není možné (například pokud jedna zóna přestane fungovat, sada škálování nemůže v této zóně vytvořit nový virtuální počítač), sada škálování umožňuje dočasnou nerovnováhu mezi tím, aby bylo možné úspěšně škálovat nebo snížit kapacitu. Při následném pokusu o horizontální navýšení kapacity přidá sada škálování virtuální počítače do zón, které potřebují pro vyvážení sady škálování víc virtuálních počítačů. Podobně při následném pokusu o škálování zruší sada škálování virtuální počítače ze zón, které potřebují méně virtuálních počítačů pro vyvážení sady škálování. V případě striktního vyrovnávání zóny se v sadě škálování nezdařily žádné pokusy o změnu nebo zmenšení, pokud by to vedlo k vyvážení.

Pokud chcete použít bilanci zóny, nastavte *zoneBalance* na *false* . Toto nastavení je výchozí v rozhraní API verze *2017-12-01* . Pokud chcete použít striktní rovnováhu mezi zónami, nastavte *zoneBalance* na *true* .

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Sady škálování s jednou zónou a zónou – redundantní sady

Když nasadíte sadu škálování virtuálního počítače, můžete zvolit použití jedné zóny dostupnosti v oblasti nebo několika zónách.

Když vytvoříte sadu škálování v jedné zóně, řídíte, na které zóně všechny tyto instance virtuálních počítačů běží, a sada škálování je spravovaná a automatické škálování se dá v rámci této zóny. Sada škálování s redundantní zónou umožňuje vytvořit jednu sadu škálování, která zahrnuje několik zón. Při vytváření instancí virtuálních počítačů se ve výchozím nastavení rovnoměrně vyrovnávají napříč zónami. Pokud dojde k přerušení v jedné ze zón, sada škálování se automaticky neškáluje a zvyšuje kapacitu. Osvědčeným postupem je nakonfigurovat pravidla automatického škálování na základě využití procesoru nebo paměti. Pravidla automatického škálování umožní, aby škálování nastaveno na ztrátu instancí virtuálních počítačů v této jedné zóně tím, že se škáluje nové instance ve zbývajících provozních zónách.

Pokud chcete použít Zóny dostupnosti, musí být vaše sada škálování vytvořená v [podporované oblasti Azure](../availability-zones/az-region.md). Můžete vytvořit sadu škálování, která používá Zóny dostupnosti, s jednou z následujících metod:

- [Azure Portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Šablony Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Proces vytvoření sady škálování, která používá zónu dostupnosti, je stejný, jak je popsáno v [článku Začínáme](quick-create-portal.md). Když vyberete podporovanou oblast Azure, můžete vytvořit sadu škálování v jedné nebo několika dostupných zónách, jak je znázorněno v následujícím příkladu:

![Vytvoření sady škálování v jedné zóně dostupnosti](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Sada škálování a podpůrné prostředky, jako je Azure Load Balancer a veřejná IP adresa, se vytvoří v jedné zóně, kterou určíte.

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Proces vytvoření sady škálování, která používá zónu dostupnosti, je stejný, jak je popsáno v [článku Začínáme](quick-create-cli.md). Pokud chcete použít Zóny dostupnosti, musíte vytvořit sadu škálování v podporované oblasti Azure.

Přidejte `--zones` parametr do příkazu [AZ VMSS Create](/cli/azure/vmss) a určete, která zóna se má použít (například zóna *1* , *2* nebo *3* ). V následujícím příkladu se vytvoří sada škálování s jednou zónou s názvem *myScaleSet* v zóně *1* :

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

Úplný příklad sady škálování s jednou zónou a síťových prostředků najdete v [tomto ukázkovém skriptu CLI](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh) .

### <a name="zone-redundant-scale-set"></a>Sada škálování s redundantní zónou

Chcete-li vytvořit zónu s redundantní sadou škálování, použijte veřejnou IP adresu *standardní* SKU a nástroj pro vyrovnávání zatížení. Pro zajištění rozšířené redundance vytvoří *standardní* SKU síťové prostředky redundantní v zóně. Další informace najdete v tématu [přehled Azure Load Balancer Standard](../load-balancer/load-balancer-overview.md) a [Standard Load Balancer a zóny dostupnosti](../load-balancer/load-balancer-standard-availability-zones.md).

Chcete-li vytvořit zónu s redundantní sadou škálování, určete více zón s `--zones` parametrem. Následující příklad vytvoří zónu redundantní sady škálování s názvem *myScaleSet* napříč zónami *1, 2, 3* :

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

Vytvoření a konfigurace všech prostředků sady škálování a virtuálních počítačů v zónách, které zadáte, trvá několik minut. Úplný příklad redundantní sady škálování a síťových prostředků v zóně najdete v [tomto ukázkovém skriptu CLI](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh) .

## <a name="use-azure-powershell"></a>Použití Azure PowerShellu

Pokud chcete použít Zóny dostupnosti, musíte vytvořit sadu škálování v podporované oblasti Azure. Přidejte `-Zone` parametr do příkazu [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) a určete, která zóna se má použít (například zóna *1* , *2* nebo *3* ).

Následující příklad vytvoří sadu škálování s jednou zónou s názvem *myScaleSet* v *východní USA 2* zóně *1* . Automaticky se vytvoří síťové prostředky Azure pro virtuální síť, veřejná IP adresa a nástroj pro vyrovnávání zatížení. Po zobrazení výzvy zadejte požadované přihlašovací údaje pro správu instancí virtuálních počítačů ve škálovací sadě:

```powershell
New-AzVmss `
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

### <a name="zone-redundant-scale-set"></a>Sada škálování s redundantní zónou

Chcete-li vytvořit zónu s redundantní sadou škálování, určete více zón s `-Zone` parametrem. Následující příklad vytvoří v zóně redundantní sadu škálování s názvem *myScaleSet* napříč *východní USA 2* zónami *1, 2, 3* . Automaticky se vytvoří zóna redundantní síťové prostředky Azure pro virtuální síť, veřejnou IP adresu a nástroj pro vyrovnávání zatížení. Po zobrazení výzvy zadejte požadované přihlašovací údaje pro správu instancí virtuálních počítačů ve škálovací sadě:

```powershell
New-AzVmss `
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

Proces vytvoření sady škálování, která používá zónu dostupnosti, je stejný, jak je popsáno v článku Začínáme pro [Linux](quick-create-template-linux.md) nebo [Windows](quick-create-template-windows.md). Pokud chcete použít Zóny dostupnosti, musíte vytvořit sadu škálování v podporované oblasti Azure. Přidejte `zones` vlastnost do typu prostředku *Microsoft. COMPUTE/virtualMachineScaleSets* ve vaší šabloně a určete, která zóna se má použít (například zóna *1* , *2* nebo *3* ).

Následující příklad vytvoří sadu škálování s jednou zónou s názvem *myScaleSet* v *východní USA 2* zóně *1* :

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

Úplný příklad sady škálování s jednou zónou a síťových prostředků najdete v [této ukázkové správce prostředků šabloně](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json) .

### <a name="zone-redundant-scale-set"></a>Sada škálování s redundantní zónou

Chcete-li vytvořit zónu s redundantní sadou škálování, zadejte ve `zones` vlastnosti pro typ prostředku *Microsoft. COMPUTE/virtualMachineScaleSets* více hodnot. Následující příklad vytvoří zónu redundantní sady škálování s názvem *myScaleSet* napříč *východní USA 2* zónami *1, 2, 3* :

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

Pokud vytvoříte veřejnou IP adresu nebo nástroj pro vyrovnávání zatížení, zadejte vlastnost *"SKU": {"Name": "Standard"}* pro vytváření redundantních síťových prostředků zóny. Také je nutné vytvořit skupinu zabezpečení sítě a pravidla pro povolení jakéhokoli provozu. Další informace najdete v tématu [přehled Azure Load Balancer Standard](../load-balancer/load-balancer-overview.md) a [Standard Load Balancer a zóny dostupnosti](../load-balancer/load-balancer-standard-availability-zones.md).

Úplný příklad redundantní sady škálování a síťových prostředků v zóně najdete v [této ukázkové správce prostředků šabloně](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json) .

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili sadu škálování v zóně dostupnosti, se můžete dozvědět, jak [nasazovat aplikace do služby Virtual Machine Scale Sets](tutorial-install-apps-cli.md) , nebo jak [používat automatické škálování se službou Virtual Machine Scale Sets](tutorial-autoscale-cli.md).
