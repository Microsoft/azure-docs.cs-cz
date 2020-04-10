---
title: Vytvoření škálovací sady Azure, která používá zóny dostupnosti
description: Zjistěte, jak vytvořit škálovací sady virtuálních strojů Azure, které používají zóny dostupnosti pro zvýšení redundance proti výpadkům
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: jushiman
ms.openlocfilehash: c8795f46e47b2ab43898f6f436b9ee6026a22fa7
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011561"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Vytvoření škálovací sady virtuálních strojů, která používá zóny dostupnosti

Chcete-li chránit škálovací sady virtuálních strojů před selháními na úrovni datového centra, můžete vytvořit škálovací sadu napříč zónami dostupnosti. Oblasti Azure, které podporují zóny dostupnosti, mají minimálně tři samostatné zóny, z nichž každá má vlastní nezávislý zdroj napájení, síť a chlazení. Další informace naleznete v [tématu Přehled zón dostupnosti](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Aspekty dostupnosti

Při nasazení škálovací sady do jedné nebo více zón od verze rozhraní API *2017-12-01*, máte možnost nasadit s "maximální rozprostření" nebo "statické 5 poruchy domény šíření". S maximální rozprostření škálování, škálovací sada šíří vaše virtuální počítače v co největším počtu domén selhání, jak je to možné v rámci každé zóny. Toto rozprostření může být napříč větší nebo méně než pět domén selhání na zónu. Při šíření "statické 5 chybové domény" škálovací sada rozloží vaše virtuální počítače přesně na pět domén selhání na zónu. Pokud škálovací sada nemůže najít pět různých domén selhání na zónu, aby vyhověla požadavku na přidělení, požadavek se nezdaří.

**Doporučujeme nasazení s maximální rozprostření pro většinu úloh**, protože tento přístup poskytuje nejlepší rozprostření ve většině případů. Pokud potřebujete repliky, které mají být rozloženy mezi různé jednotky izolace hardwaru, doporučujeme rozprostřít napříč zónami dostupnosti a využít maximální rozprostření v rámci každé zóny.

Při maximálním rozprostření se v zobrazení instance škálovací sady a v metadatech instance zobrazí pouze jedna doména selhání bez ohledu na to, kolik domén selhání jsou virtuální servery rozprostřeny. Šíření v rámci každé zóny je implicitní.

Chcete-li použít maximální rozprostření, nastavte *platformFaultDomainCount* na *1*. Chcete-li použít statické pět chyb domény šíření, nastavte *platformFaultDomainCount* na *5*. V rozhraní API verze *2017-12-01* *platformFaultDomainCount* výchozí *1* pro jednozónové a mezizónové škálovací sady. V současné době je podporováno pouze statické rozložení domény selhání pro regionální (jiné zonální) škálovací sady.

### <a name="placement-groups"></a>Skupiny umístění

Při nasazení škálovací sady máte také možnost nasadit s jednou [skupinou umístění](./virtual-machine-scale-sets-placement-groups.md) pro každou zónu dostupnosti nebo s více zónami. Pro regionální sady měřítka (mimo oblast) je volbou mít jednu skupinu umístění v oblasti nebo mít více v oblasti. Pro většinu úloh doporučujeme více skupin umístění, což umožňuje větší škálování. Ve verzi rozhraní API *2017-12-01*sady škálování výchozí více skupin umístění pro jednozónové a mezizónové škálovací sady, ale ve výchozím nastavení pro jednu skupinu umístění pro regionální (non-zonal) škálovací sady.

> [!NOTE]
> Pokud používáte maximální rozprostření, musíte použít více skupin umístění.

### <a name="zone-balancing"></a>Vyvažování zón

A konečně, pro škálovací sady nasazené ve více zónách máte také možnost zvolit "rovnováhu zón nejlepšího úsilí" nebo "striktní rovnováhu zón". Škálovací sada se považuje za "vyváženou", pokud každá\\zóna má stejný počet virtuálních virtuálních uživatelů nebo + - 1 virtuální hod ve všech ostatních zónách pro škálovací sadu. Příklad:

- Škálovací sada se 2 virtuálními virtuálními aplikacemi v zóně 1, 3 virtuálními aplikacemi v zóně 2 a 3 virtuálními aplikacemi v zóně 3 se považuje za vyvážená. Existuje pouze jedna zóna s jiným počtem virtuálních uživatelů a je pouze o 1 menší než ostatní zóny. 
- Škálovací sada s 1 virtuálním virtuálním prostorem v zóně 1, 3 virtuálními virtuálními aplikacemi v zóně 2 a 3 virtuálními aplikacemi v zóně 3 se považuje za nevyváženou. Zóna 1 má o 2 virtuální chod méně než zóny 2 a 3.

Je možné, že virtuální chody ve škálovací sadě se úspěšně vytvoří, ale rozšíření na těchto virtuálních počítačích se nedaří nasadit. Tyto virtuální virtuální aplikace s selháním rozšíření se stále počítají při určování, pokud je škálovací sada vyvážená. Například škálovací sada se 3 virtuálními virtuálními aplikacemi v zóně 1, 3 virtuálními virtuálními aplikacemi v zóně 2 a 3 virtuálními aplikacemi v zóně 3 se považuje za vyvážená, i když všechna rozšíření selhala v zóně 1 a všechna rozšíření byla úspěšná v zónách 2 a 3.

Při vyvažování zóny s maximálním úsilím se škálovací sada pokouší škálovat a zmenšovat při zachování rovnováhy. However, if for some reason this is not possible (for example, if one zone goes down, the scale set cannot create a new VM in that zone), the scale set allows temporary imbalance to successfully scale in or out. Při následných pokusech o horizontální navýšení kapacity škálovací sada přidá virtuální chody do zón, které potřebují více virtuálních počítačů pro škálování nastavit, aby byla vyvážená. Podobně v následném měřítku v pokusech škálovací sada odebere virtuální chody ze zón, které potřebují méně virtuálních počítačů pro škálování nastavit, aby byla vyvážená. S "striktní rovnováhu zóny", škálovací sada selže všechny pokusy o škálování nebo ven, pokud by to způsobit nevyváženost.

Chcete-li použít rovnováhu zóny s nejlepším úsilím, nastavte *hodnotu zoneBalance* na *hodnotu false*. Toto nastavení je výchozí v rozhraní API verze *2017-12-01*. Chcete-li použít přísné saldo zóny, nastavte *hodnotu zoneBalance* na *hodnotu true*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Škálovací sady s jednou zónou a nadbytečnou zónou

Při nasazení škálovací sady virtuálních strojů můžete použít jednu zónu dostupnosti v oblasti nebo více zónách.

Když vytvoříte škálovací sadu v jedné zóně, řídíte, ve které zóně se spouštějí všechny instance virtuálních virtuálních< hprostředka, a škálovací sada se spravuje a automaticky škáluje pouze v rámci této zóny. Zónově redundantní škálovací sada umožňuje vytvořit jednu škálovací sadu, která zahrnuje více zón. Při vytváření instancí virtuálních uživatelů jsou ve výchozím nastavení rovnoměrně vyváženy napříč zónami. Dojde-li k přerušení v jedné ze zón, škálovací sada není automaticky horizontální navýšení kapacity ke zvýšení kapacity. Osvědčeným postupem by bylo nakonfigurovat pravidla automatického škálování na základě využití procesoru nebo paměti. Pravidla automatického škálování by umožnila škálovací sadě reagovat na ztrátu instancí virtuálních virtuálních uživatelů v této zóně škálováním nových instancí ve zbývajících provozních zónách.

Chcete-li použít zóny dostupnosti, škálovací sada musí být vytvořena v [podporované oblasti Azure](../availability-zones/az-overview.md#services-support-by-region). Škálovací sadu, která používá zóny dostupnosti, můžete vytvořit jednou z následujících metod:

- [portál Azure](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Šablony Azure Resource Manageru](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Proces vytvoření škálovací sady, která používá zónu dostupnosti, je stejný jako podrobně popsaný v [článku Začínáme](quick-create-portal.md). Když vyberete podporovanou oblast Azure, můžete vytvořit škálovací sadu v jedné nebo více dostupných zónách, jak je znázorněno v následujícím příkladu:

![Vytvoření škálovací sady v jedné zóně dostupnosti](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Škálovací sada a podpůrné prostředky, jako je například Azure vyrovnávání zatížení a veřejná IP adresa, jsou vytvořeny v jedné zóně, kterou zadáte.

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Proces vytvoření škálovací sady, která používá zónu dostupnosti, je stejný jako podrobně popsaný v [článku Začínáme](quick-create-cli.md). Chcete-li použít zóny dostupnosti, musíte vytvořit škálovací sadu v podporované oblasti Azure.

Přidejte `--zones` parametr do [příkazu az vmss create](/cli/azure/vmss) a určete, kterou zónu chcete použít (například zóna *1*, *2*nebo *3*). Následující příklad vytvoří jednozónovou škálovací sadu s názvem *myScaleSet* v zóně *1*:

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

Úplný příklad jednozónové škálovací sady a síťových prostředků najdete v [této ukázce skriptu funkce cli.](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)

### <a name="zone-redundant-scale-set"></a>Zónově redundantní škálovací sada

Chcete-li vytvořit zónově redundantní škálovací sadu, použijte veřejnou IP adresu *sku standardu* a vytáčící stav zatížení. Pro vylepšenou redundanci vytvoří *standardní* skladová položka síťové prostředky redundantní zóny. Další informace naleznete [v tématu Přehled azure nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-standard-overview.md) a standardní [vyrovnávání zatížení a zóny dostupnosti](../load-balancer/load-balancer-standard-availability-zones.md).

Chcete-li vytvořit zónově redundantní škálovací `--zones` sadu, zadejte více zón s parametrem. Následující příklad vytvoří zónově redundantní škálovací sadu s názvem *myScaleSet* napříč zónami *1,2,3*:

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

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítače v zadaných zónách trvá několik minut. Úplný příklad zónově redundantní škálovací sady a síťových prostředků najdete [v této ukázce skriptu zapínatelného pomocí funkce CLI.](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Použití Azure Powershell

Chcete-li použít zóny dostupnosti, musíte vytvořit škálovací sadu v podporované oblasti Azure. Přidejte `-Zone` parametr do příkazu [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) a určete, kterou zónu chcete použít (například zóna *1*, *2*nebo *3*).

Následující příklad vytvoří jednozónovou škálovací sadu s názvem *myScaleSet* v zóně *1* *– východní US 2* . Automaticky se vytvoří síťové prostředky Azure pro virtuální síť, veřejná IP adresa a nástroj pro vyrovnávání zatížení. Po zobrazení výzvy zadejte požadované přihlašovací údaje pro správu instancí virtuálních počítačů ve škálovací sadě:

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

### <a name="zone-redundant-scale-set"></a>Zónově redundantní škálovací sada

Chcete-li vytvořit zónově redundantní škálovací `-Zone` sadu, zadejte více zón s parametrem. Následující příklad vytvoří zónově redundantní škálovací sadu s názvem *myScaleSet* napříč zónami *1, 2, 3* *.* Zónově redundantní síťové prostředky Azure pro virtuální síť, veřejnou IP adresu a nástroj pro vyrovnávání zatížení se vytvoří automaticky. Po zobrazení výzvy zadejte požadované přihlašovací údaje pro správu instancí virtuálních počítačů ve škálovací sadě:

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

Proces vytvoření škálovací sady, která používá zónu dostupnosti, je stejný jako v článku Začínáme pro [Linux](quick-create-template-linux.md) nebo [Windows](quick-create-template-windows.md). Chcete-li použít zóny dostupnosti, musíte vytvořit škálovací sadu v podporované oblasti Azure. Přidejte `zones` vlastnost do typu prostředku *Microsoft.Compute/virtualMachineScaleSets* v šabloně a určete, kterou zónu chcete použít (například zóna *1*, *2*nebo *3*).

Následující příklad vytvoří linuxovou jednozónovou škálovací sadu s názvem *myScaleSet* v zóně 1 *– východní US* *2*:

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

Úplný příklad jednozónové škálovací sady a síťových prostředků naleznete v [této vzorové šabloně Správce prostředků.](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zónově redundantní škálovací sada

Chcete-li vytvořit zónově redundantní škálovací `zones` sadu, zadejte ve vlastnosti více hodnot pro typ prostředku *Microsoft.Compute/virtualMachineScaleSets.* Následující příklad vytvoří zónově redundantní škálovací sadu s názvem *myScaleSet* napříč zónami *VÝCHODNÍ US 2* *1,2,3*:

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

Pokud vytvoříte veřejnou IP adresu nebo vyrovnávání zatížení, zadejte vlastnost *"sku": { "name": "Standard" }"* pro vytvoření zónově redundantních síťových prostředků. Je také nutné vytvořit skupinu zabezpečení sítě a pravidla, která umožní jakýkoli provoz. Další informace naleznete [v tématu Přehled azure nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-standard-overview.md) a standardní [vyrovnávání zatížení a zóny dostupnosti](../load-balancer/load-balancer-standard-availability-zones.md).

Úplný příklad zónově redundantní škálovací sady a síťových prostředků najdete v [této ukázkové šabloně Správce prostředků.](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili škálovací sadu v zóně dostupnosti, můžete se naučit [nasazovat aplikace na škálovacísady virtuálních strojů](tutorial-install-apps-cli.md) nebo [použít automatické škálování se sadami škálování virtuálních strojů](tutorial-autoscale-cli.md).
