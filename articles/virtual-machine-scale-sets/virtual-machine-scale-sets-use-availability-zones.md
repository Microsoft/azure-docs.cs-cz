---
title: Vytvoření sady škálování Azure, který používá zón dostupnosti | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit škálovací sady virtuálních počítačů Azure, které používají pro zvýšení zajištění redundance proti výpadkům zóny dostupnosti
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: cynthn
ms.openlocfilehash: dbf614f9749039b054e1134df31334a6248e2b78
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981396"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Vytvoření škálovací sady virtuálního počítače, který používá zóny dostupnosti

K ochraně vaší škálovací sady virtuálních počítačů před výpadky na úrovni datacentra, můžete vytvořit škálovací sadu napříč zónami dostupnosti. Oblasti Azure, které podporují zóny dostupnosti mít minimálně tří samostatných zón, každý s vlastní nezávislé napájení zdroje, síť a chlazení. Další informace najdete v tématu [přehledu zón dostupnosti](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Aspekty dostupnosti

Když nasadíte škálovací sadě na jeden nebo více zónách od verze rozhraní API *2017-12-01*, máte možnost nasazení "Maximální rozprostření" nebo "statické 5 selhání domény šíření". S maximální rozprostření šíří škálovací sady virtuálních počítačů napříč jako počet domén selhání v každé zóně nejvíce. Toto rozšíření může být v rámci větší nebo méně než pět domény za zónu selhání. S "statické 5 selhání domény šíření", šíří škálovací sady virtuálních počítačů napříč doménami selhání přesně pět za zónu. Pokud škálovací sada nemůže najít pěti doménami selhání odlišné za zónu by vyhovovaly žádosti o přidělení, požadavek selže.

**Doporučujeme nasadit s maximální rozprostření pro většinu úloh**, protože tento přístup poskytuje nejlepší rozšíření ve většině případů. Pokud potřebujete repliky možné rozdělit do různých hardwarových jednotek izolace, doporučujeme šíření napříč zónami dostupnosti a využívat maximální rozprostření v každé zóně.

S maximální rozprostření, se zobrazí jenom v zobrazení instance škálovací sady virtuálních počítačů a v metadatech instance bez ohledu na počet domén selhání virtuální počítače jsou rozloženy jednu doménu selhání. Rozšíření v každé zóně je implicitní.

Chcete-li použít maximální rozprostření, nastavte *platformFaultDomainCount* k *1*. Pokud chcete použít statické pět selhání domény rozprostření, nastavte *platformFaultDomainCount* k *5*. Ve verzi rozhraní API *2017-12-01*, *platformFaultDomainCount* výchozí hodnota je *1* jednou zónou a napříč zónami škálovací sad. V současné době pouze statické pět selhání domény rozprostření je podporováno pro místní škálovací sady.

### <a name="placement-groups"></a>Skupiny umístění

Když nasadíte škálovací sadu, máte možnost nasadit pomocí jediného [skupinu umístění](./virtual-machine-scale-sets-placement-groups.md) za zónu dostupnosti, nebo s několika za zónu. Pro místní škálovací sady je mít jediné skupiny umístění v oblasti nebo mít více v oblasti. Pro většinu úloh doporučujeme více skupin umístění, která umožňuje větší škálovatelnost. Ve verzi rozhraní API *2017-12-01*, škálovacích sad výchozí do více skupin umístění pro jednu zónu a napříč zónami škálovací sady, ale budou mít výchozí hodnotu jediné skupiny umístění pro místní škálovací sady.

> [!NOTE]
> Pokud používáte maximální rozprostření, je nutné použít více skupin umístění.

### <a name="zone-balancing"></a>Zóna vyrovnávání

Nakonec pro škálovací sady nasadit napříč několika zónami, máte také možnost zvolit "nejlepší poměr úsilí zónu" nebo "vyvažování zón přísné". Škálovací sada se považuje za "balanced", pokud každá zóna stejný počet virtuálních počítačů nebo +\\– 1 virtuální počítač v jiné zóně pro škálovací sadu. Příklad:

- Rovnováha škálovací sadu s 2 virtuální počítače v zóně 1, 3 virtuální počítače v zóně 2 a 3 virtuální počítače v zóně 3 je považován za. Existuje pouze jednu zónu s jinou počet virtuálních počítačů a je pouze 1 menší než jiné zóny. 
- Škálovací sadu s 1 virtuální počítač v zóně 1, 3 virtuální počítače v zóně 2 a 3 virtuální počítače v zóně 3 je považován za nevyvážené uvozovky. Zóna 1 má 2 menší počet virtuálních počítačů než zóny 2 a 3.

Je možné, že virtuální počítače ve škálovací sadě se úspěšně vytvoří, ale rozšíření na tyto virtuální počítače se nepodařilo nasadit. Tyto virtuální počítače s chybami rozšíření se pořád počítají při určování, pokud je škálovací sada s vyrovnáváním. Například škálovací sady s 3 virtuálními počítači v zóně 1, 3 virtuální počítače v zóně 2 a 3 virtuální počítače v zóně 3 je považován za vyrovnáváním i v případě, že všechna rozšíření se nepovedlo v zóně 1 a všechna rozšíření úspěšně zóny 2 a 3.

Škálovací sady s vyvažování zón best effort, pokusy o škálování a při zachování zůstatek. Nicméně pokud z nějakého důvodu není možné (například pokud jednu zónu přestane fungovat, škálovací sady nelze vytvořit nový virtuální počítač v této zóně), umožňuje dočasné imbalance na úspěšně horizontální snížení nebo navýšení kapacity škálovací sady. Na následné pokusy o horizontální navýšení kapacity škálovací sady přidá virtuální počítače zón, které potřebují další virtuální počítače pro škálovací sady, jež mají být použita. Podobně stupnice následných pokusů o přihlášení odebere škálovací sady virtuálních počítačů ze zón, které vyžadují menší počet virtuálních počítačů pro škálovací sady, jež mají být použita. Škálovací sady s "vyvažování zón striktní", se nezdaří všechny pokusy o horizontální snížení nebo navýšení kapacity, pokud to by způsobilo nevyvážené.

Chcete-li použít vyvažování zón best effort, nastavte *zoneBalance* k *false*. Toto nastavení je výchozí hodnotou v rozhraní API verze *2017-12-01*. Pokud chcete použít vyvažování zón strict, nastavte *zoneBalance* k *true*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Jednou zónou a zónově redundantní škálovací sady

Když nasadíte škálovací sadu virtuálních počítačů, můžete použít jednu zónu dostupnosti, do oblasti nebo několika zónami.

Při vytváření škálovací sady v jedné oblasti, můžete ovládací prvek zóně, ve které všechny tyto instance virtuálních počítačů v a spravuje škálovací sady a pravidla automatického škálování provedou jenom v rámci této zóny. Zónově redundantní škálovací sada vám umožní vytvářet jedné škálovací sadě, která zahrnuje více zón. Při vytváření instancí virtuálních počítačů se ve výchozím nastavení jsou rovnoměrně vyvažují napříč zónami. Dojde k výpadku v jedné ze zón, škálovací sady nejsou škálovat automaticky na zvýšení kapacity. Osvědčený postup je nakonfigurovat pravidla automatického škálování podle využití procesoru nebo paměti. Pravidla automatického škálování by umožnilo škálovací sady na ztrátu instancí virtuálních počítačů v této jednu zónu horizontálním navýšením kapacity nových instancí ve zbývajících provozní zónách.

K používání zón dostupnosti, musí být vytvořené škálovací sady v [podporované oblasti Azure](../availability-zones/az-overview.md#regions-that-support-availability-zones). Můžete vytvořit škálovací sadu, která používá zón dostupnosti s jedním z následujících metod:

- [Azure Portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Šablony Azure Resource Manageru](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Proces vytvoření škálovací sady, který využívá zónu dostupnosti je stejný jako podrobné v [Začínáme článku](quick-create-portal.md). Když vyberete podporovanou oblast Azure, můžete vytvořit škálovací sadu v jedné nebo více dostupných zón, jak je znázorněno v následujícím příkladu:

![Vytvoření škálovací sady v jedné zóně dostupnosti](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Škálovací sady a podpůrné prostředky, jako je například nástroj pro vyrovnávání zatížení Azure a veřejnou IP adresu, se vytvoří v jedné oblasti, který zadáte.

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Proces vytvoření škálovací sady, který využívá zónu dostupnosti je stejný jako podrobné v [Začínáme článku](quick-create-cli.md). K používání zón dostupnosti, musíte vytvořit svou škálovací sadu v podporované oblasti Azure.

Přidat `--zones` parametr [az vmss vytvořit](/cli/azure/vmss) příkaz a zadejte zóně, ve které chcete použít (například zóny *1*, *2*, nebo *3*). Následující příklad vytvoří škálovací sadu s názvem pro jednu zónu *myScaleSet* v zóně *1*:

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

Kompletní příklad jednu zónu škálovací sadu a síťové prostředky, najdete v článku [tento ukázkový skript rozhraní příkazového řádku](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.)

### <a name="zone-redundant-scale-set"></a>Zónově redundantní škálovací sady

Vytvoření zónově redundantní škálovací sady, můžete použít *standardní* SKU veřejné IP adresy a zatížení nástroje pro vyrovnávání. Vylepšené redundanci *standardní* SKU vytvoří zónově redundantních síťových prostředků. Další informace najdete v tématu [Přehled služby Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md) a [Load balanceru úrovně Standard a zóny dostupnosti](../load-balancer/load-balancer-standard-availability-zones.md).

Pokud chcete vytvořit zónově redundantní škálovací sady, zadejte více zón se `--zones` parametru. Následující příklad vytvoří zónově redundantní škálovací sadu s názvem *myScaleSet* napříč zónami *1,2,3*:

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

Trvá několik minut vytvořit a nakonfigurovat vše, co škálovací sady prostředků a virtuální počítače v zóna nebo zóny, který zadáte. Kompletní příklad zónově redundantní škálovací sady a síťové prostředky, najdete v článku [tento ukázkový skript rozhraní příkazového řádku](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Použití Azure Powershell

K používání zón dostupnosti, musíte vytvořit svou škálovací sadu v podporované oblasti Azure. Přidat `-Zone` parametr [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) příkaz a zadejte zóně, ve které chcete použít (například zóny *1*, *2*, nebo *3*).

Následující příklad vytvoří škálovací sadu s názvem pro jednu zónu *myScaleSet* v *USA – východ 2* zóny *1*. Automaticky se vytvoří síťové prostředky Azure pro virtuální síť, veřejná IP adresa a nástroj pro vyrovnávání zatížení. Po zobrazení výzvy zadejte požadované přihlašovací údaje pro správu instancí virtuálních počítačů ve škálovací sadě:

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

### <a name="zone-redundant-scale-set"></a>Zónově redundantní škálovací sady

Pokud chcete vytvořit zónově redundantní škálovací sady, zadejte více zón se `-Zone` parametru. Následující příklad vytvoří zónově redundantní škálovací sadu s názvem *myScaleSet* napříč *USA – východ 2* zóny *1, 2, 3*. Zónově redundantní síťové prostředky Azure pro virtuální síť, veřejnou IP adresu a nástroje pro vyrovnávání zatížení se automaticky vytvoří. Po zobrazení výzvy zadejte požadované přihlašovací údaje pro správu instancí virtuálních počítačů ve škálovací sadě:

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

Postup vytvoření škálovací sady, který využívá zónu dostupnosti je stejný podle popisu v článku rychlý pro [Linux](quick-create-template-linux.md) nebo [Windows](quick-create-template-windows.md). K používání zón dostupnosti, musíte vytvořit svou škálovací sadu v podporované oblasti Azure. Přidat `zones` vlastnost *Microsoft.Compute/virtualMachineScaleSets* prostředků zadejte v šabloně a zóně, ve které chcete použít (například zóny *1*, *2*, nebo *3*).

Následující příklad vytvoří škálovací sadu s názvem jednu zónu Linuxem *myScaleSet* v *USA – východ 2* zóny *1*:

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

Kompletní příklad jednu zónu škálovací sadu a síťové prostředky, najdete v článku [Tato ukázková šablona Resource Manageru](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zónově redundantní škálovací sady

Pokud chcete vytvořit zónově redundantní škálovací sady, zadat více hodnot v `zones` vlastnost *Microsoft.Compute/virtualMachineScaleSets* typ prostředku. Následující příklad vytvoří zónově redundantní škálovací sadu s názvem *myScaleSet* napříč *USA – východ 2* zóny *1,2,3*:

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

Pokud vytvoříte veřejnou IP adresu nebo nástroj pro vyrovnávání zatížení, zadejte *"sku": {"name": "Standard"} "* vlastnost vytvořit zónově redundantních síťových prostředků. Musíte také vytvořit skupinu zabezpečení sítě a pravidla povolují veškerý provoz. Další informace najdete v tématu [Přehled služby Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md) a [Load balanceru úrovně Standard a zóny dostupnosti](../load-balancer/load-balancer-standard-availability-zones.md).

Kompletní příklad zónově redundantní škálovací sady a síťové prostředky, najdete v článku [Tato ukázková šablona Resource Manageru](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Další postup

Teď, když vytvoříte škálovací sadu v zóně dostupnosti, se dozvíte postupy [škálovací sady nasadit aplikace na virtuálním počítači](tutorial-install-apps-cli.md) nebo [použití automatického škálování u škálovacích sad virtuálních počítačů](tutorial-autoscale-cli.md).
