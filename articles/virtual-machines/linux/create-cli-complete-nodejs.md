---
title: Vytvoření kompletního linuxového prostředí pomocí rozhraní příkazového řádku Azure CLI 1.0 | Dokumentace Microsoftu
description: Vytvoření úložiště, virtuální počítač s Linuxem, virtuální sítě a podsítě, nástroj pro vyrovnávání zatížení, síťové rozhraní, veřejné IP adresy a skupinu zabezpečení sítě, vše od základů pomocí rozhraní příkazového řádku Azure CLI 1.0.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 1fb5542af77fbb584effca24a74b9e233359cf0e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932324"
---
# <a name="create-a-complete-linux-environment-with-the-azure-cli-10"></a>Vytvoření kompletního linuxového prostředí pomocí rozhraní příkazového řádku Azure CLI 1.0
V tomto článku jsme integrovali jednoduchá síť s nástroji pro vyrovnávání zatížení a dvojice virtuálních počítačů, které jsou užitečné pro vývoj a jednoduché výpočetního prostředí. Provedeme procesem příkazu command, dokud nebudete mít dvě funkční, zabezpečené virtuální počítače s Linuxem ke kterým se můžete připojit z kdekoliv na Internetu. Potom můžete přesunout složitější sítě a prostředí.

Cestou se dozvíte o hierarchii závislostí, že modelu nasazení Resource Manager nabízí, a o tom, kolik energie se poskytuje. Jakmile se zobrazí, jak se sestaví systém, můžete znovu sestavit ji mnohem rychleji s využitím [šablon Azure Resource Manageru](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Také Jakmile se dozvíte, jak jsou součástí vašeho prostředí navzájem propojené, vytváření šablony k automatizaci jejich je jednodušší.

Prostředí obsahuje:

* Dva virtuální počítače ve skupině dostupnosti.
* Nástroj pro vyrovnávání zatížení s pravidlem Vyrovnávání zatížení na portu 80.
* Pravidla skupiny zabezpečení (NSG) k ochraně vašeho virtuálního počítače z nevyžádaný provoz v síti.

Pokud chcete vytvořit vlastní prostředí, budete potřebovat nejnovější [příkazového řádku Azure CLI 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) v režimu Resource Manageru (`azure config mode arm`). Musíte také nástroj pro analýzu JSON. Tento příklad používá [jq](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#quick-commands) – naše rozhraní příkazového řádku pro classic a resource management modelech nasazení (Tento článek)
- [Azure CLI 2.0](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků


## <a name="quick-commands"></a>Rychlé příkazy
Pokud je potřeba rychle provést úlohu, následující část podrobně popisuje základní příkazy k odesílání virtuálního počítače do Azure. Podrobnější informace a kontext pro každý krok najdete ve zbývající části dokumentu, od [tady](#detailed-walkthrough).

Ujistěte se, že máte [rozhraní příkazového řádku Azure CLI 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) přihlášení a v režimu Resource Manageru:

```azurecli
azure config mode arm
```

V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Zahrnout názvy parametrů příklad `myResourceGroup`, `mystorageaccount`, a `myVM`.

Vytvořte skupinu prostředků. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v `westeurope` umístění:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Zkontrolujte skupinu prostředků pomocí analyzátoru JSON:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Vytvořte účet úložiště. Následující příklad vytvoří účet úložiště s názvem `mystorageaccount`. (Název účtu úložiště musí být jedinečný, takže poskytují vlastním jedinečným názvem.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Ověření účtu úložiště pomocí analyzátoru JSON:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Vytvořte virtuální síť. Následující příklad vytvoří virtuální síť s názvem `myVnet`:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Vytvořte podsíť. Následující příklad vytvoří podsíť s názvem `mySubnet`:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Ověření virtuální sítě a podsítě s použitím analyzátor JSON:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Vytvoření veřejné IP adresy. Následující příklad vytvoří veřejnou IP adresu s názvem `myPublicIP` s názvem služby DNS `mypublicdns`. (Název DNS musí být jedinečný, takže poskytují vlastním jedinečným názvem.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Vytvoření nástroje pro vyrovnávání zatížení. Následující příklad vytvoří nástroj pro vyrovnávání zatížení s názvem `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Vytvořte front-endový fond IP adres nástroje pro vyrovnávání zatížení a přidružte veřejnou IP adresu. Následující příklad vytvoří front-endový fond IP s názvem `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Vytvořte fond back-end IP adres nástroje pro vyrovnávání zatížení. Následující příklad vytvoří back endový fond IP `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Vytvoření pravidel překladu adres (NAT) pro nástroj pro vyrovnávání zatížení příchozích síťových SSH. Následující příklad vytvoří dvě pravidla nástroje pro vyrovnávání zatížení, `myLoadBalancerRuleSSH1` a `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Vytvoření webu příchozí pravidla NAT nástroje pro vyrovnávání zatížení. Následující příklad vytvoří pravidlo nástroje pro vyrovnávání zatížení s názvem `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Vytvoření sondy stavu nástroje pro vyrovnávání zatížení. Následující příklad vytvoří sondu protokolu TCP s názvem `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Ověřte nástroj pro vyrovnávání zatížení, fondy IP adres a pravidel překladu adres pomocí analyzátoru JSON:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Vytvoření první síťovou kartu (NIC). Nahradit `#####-###-###` oddíly s vlastním ID předplatného Azure. Vaše předplatné ID je uvedeno ve výstupu příkazu **jq** při zkoumání prostředků, kterou vytváříte. Můžete také zobrazit ID vašeho předplatného s `azure account list`.

Následující příklad vytvoří síťové rozhraní s názvem `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Vytvořte druhé síťové rozhraní Následující příklad vytvoří síťové rozhraní s názvem `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Dva síťové adaptéry ověřte pomocí analyzátoru JSON:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Vytvořte skupinu zabezpečení sítě. Následující příklad vytvoří skupinu zabezpečení sítě s názvem `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Přidání dvou příchozích pravidel pro skupiny zabezpečení sítě. Následující příklad vytvoří dvě pravidla `myNetworkSecurityGroupRuleSSH` a `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Ověřte skupiny zabezpečení sítě a příchozích pravidel pomocí analyzátoru JSON:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Skupina zabezpečení sítě svázat dva síťové adaptéry:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Vytvořte skupinu dostupnosti. Následující příklad vytvoří skupinu dostupnosti `myAvailabilitySet`:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Vytvoření prvního virtuálního počítače s Linuxem. Následující příklad vytvoří virtuální počítač s názvem `myVM1`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Vytvoření druhého virtuálního počítače s Linuxem. Následující příklad vytvoří virtuální počítač s názvem `myVM2`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Pomocí analyzátoru JSON ověřte, že vše, co byl vytvořen:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exportujte jako šablonu k rychlému vytvoření nové instance znovu nové prostředí:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Podrobný postup
Podrobné kroky, které následují vysvětlete, co každý příkaz dělá, když sestavujete vašeho prostředí. Tyto koncepty jsou užitečné při vytváření vlastního prostředí pro vývojové nebo produkční prostředí.

Ujistěte se, že máte [rozhraní příkazového řádku Azure CLI 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) přihlášení a v režimu Resource Manageru:

```azurecli
azure config mode arm
```

V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Zahrnout názvy parametrů příklad `myResourceGroup`, `mystorageaccount`, a `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Vytvoření skupiny prostředků a zvolte umístění nasazení
Skupiny prostředků Azure jsou logické nasazení entity, které obsahují informace o konfiguraci a metadata k povolení logické správy nasazení prostředků. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v `westeurope` umístění:

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Výstup:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
Budete potřebovat účty úložiště pro disky virtuálních počítačů a pro všechny další datové disky, které chcete přidat. Účty úložiště můžete vytvořit téměř okamžitě po vytvoření skupiny prostředků.

Tady používáme `azure storage account create` příkazu předávání umístění účtu, skupinu prostředků, která řídí a typ má podpora úložiště. Následující příklad vytvoří účet úložiště s názvem `mystorageaccount`:

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Výstup:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Prozkoumat naše skupiny prostředků s použitím `azure group show` příkazu, použijeme [jq](https://stedolan.github.io/jq/) nástroj spolu s `--json` možnost příkazového řádku Azure. (Můžete použít **jsawk** nebo všechny knihovny jazyka chcete analyzovat ve formátu JSON.)

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Výstup:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

K prozkoumání účtu úložiště pomocí rozhraní příkazového řádku, musíte nejdřív nastavit názvy účtů a klíčů. Název účtu úložiště v následujícím příkladu nahraďte název, který zvolíte:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Pak můžete zobrazit údaje o úložiště snadno:

```azurecli
azure storage container list
```

Výstup:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě
Dále budete muset vytvořit virtuální síť spuštěná v Azure a podsíť, ve kterém můžete vytvořit virtuální počítače. Následující příklad vytvoří virtuální síť s názvem `myVnet` s `192.168.0.0/16` předpona adresy:

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Výstup:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Znovu použijeme možnost--json `azure group show` a `jq` zobrazíte, jakým způsobem vytváříme naše zdroje. Nyní je k dispozici `storageAccounts` prostředků a `virtualNetworks` prostředků.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Výstup:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Nyní Pojďme vytvořit podsíť v `myVnet` virtuální síť, do které jsou nasazené virtuální počítače. Používáme `azure network vnet subnet create` příkaz spolu s prostředky jsme už vytvořili: `myResourceGroup` skupinu prostředků a `myVnet` virtuální sítě. V následujícím příkladu přidáme podsíť s názvem `mySubnet` s předponou adresy podsítě `192.168.1.0/24`:

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Výstup:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Vzhledem k tomu, že podsíť je logicky ve virtuální síti, vyhledáme informace o podsíti mírně odlišné příkazem. Pomocí příkazu používáme `azure network vnet show`, ale můžeme dál prohlédněte si výstup JSON pomocí `jq`.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Výstup:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy
Teď vytvoříme veřejné IP adresy (PIP), můžeme přiřadit nástroj pro vyrovnávání zatížení. Umožňuje připojení k vašim virtuálním počítačům z Internetu s použitím `azure network public-ip create` příkazu. Protože výchozí adresa je dynamická, můžeme vytvořit položku DNS s názvem v **cloudapp.azure.com** domény pomocí `--domain-name-label` možnost. Následující příklad vytvoří veřejnou IP adresu s názvem `myPublicIP` s názvem služby DNS `mypublicdns`. Protože název DNS musí být jedinečný, zadejte jedinečný název DNS:

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Výstup:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Veřejná IP adresa je také prostředku nejvyšší úrovně, abyste ji viděli `azure group show`.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Výstup:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Můžete prozkoumat další podrobnosti o prostředku, včetně plně kvalifikovaný název domény (FQDN) subdomény, s využitím kompletní `azure network public-ip show` příkazu. Prostředek veřejné IP adresy přiřazené logicky, ale konkrétní adresu ještě se nepřiřadily. Chcete-li získat IP adresu, budete potřebovat nástroj pro vyrovnávání zatížení, která jsme dosud nebyla vytvořena.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Výstup:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Vytvořte nástroj pro vyrovnávání zatížení a fondy IP adres
Když vytvoříte nástroj pro vyrovnávání zatížení, umožňuje vám za účelem distribuce provozu napříč několika virtuálními počítači. Také poskytuje redundanci pro vaši aplikaci ve spuštění několika virtuálních počítačů, které reagují na požadavky uživatelů v případě údržby nebo velkým zatížením. Následující příklad vytvoří nástroj pro vyrovnávání zatížení s názvem `myLoadBalancer`:

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Výstup:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Naše nástroje pro vyrovnávání zatížení je poměrně prázdný, takže vytvoříme některé fondy IP adres. Chceme vytvořit dva fondy IP adres pro naše nástroje pro vyrovnávání zatížení, jednu pro front-end a jednu pro back-endu. Front-endový fond IP adres je veřejně viditelné. Je také umístění, do kterého můžeme přiřadit PIP, který jsme vytvořili dříve. Potom použijeme back endovém fondu jako umístění pro naše virtuální počítače pro připojení k. Tímto způsobem provoz může probíhat přes nástroj pro vyrovnávání zatížení pro virtuální počítače.

Nejprve vytvoříme naši front-endový fond IP adres. Následující příklad vytvoří front-endový fond s názvem `myFrontEndPool`:

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Výstup:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Všimněte si, jak můžete využít `--public-ip-name` přepínače a zajistěte tak předání `myPublicIP` , kterou jsme vytvořili dříve. Přiřadíte veřejnou IP adresu nástroji pro vyrovnávání zatížení, můžete kontaktovat vaše virtuální počítače přes Internet.

V dalším kroku vytvoříme naši druhý fond IP adres, tentokrát pro naše provoz back-end. Následující příklad vytvoří back endový fond s názvem `myBackEndPool`:

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Výstup:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Můžeme vidět, jak naše nástroje pro vyrovnávání zatížení dělá vyhledáváním s `azure network lb show` a prozkoumání výstupu JSON:

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Výstup:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Vytvoření pravidel NAT nástroje pro vyrovnávání zatížení
Chcete-li získat provozu přes naše nástroje pro vyrovnávání zatížení, potřebujeme vytvořit síťová adresa pravidel překladu (NAT), které určují akce příchozí nebo odchozí. Můžete zadat protokol bude použit pak mapování externí portů na vnitřních portech podle potřeby. Pro naše prostředí vytvoříme některá pravidla, které umožňují SSH prostřednictvím naše nástroje pro vyrovnávání zatížení k virtuálním počítačům. Nastavíme porty TCP 4222 a 4223 pro přesměrování na TCP port 22 na naše virtuální počítače (které vytvoříme později). Následující příklad vytvoří pravidlo `myLoadBalancerRuleSSH1` na portu TCP 4222 mapují na port 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Výstup:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Opakujte tento postup pro druhé pravidlo NAT pro SSH. Následující příklad vytvoří pravidlo `myLoadBalancerRuleSSH2` na portu TCP 4223 mapují na port 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Můžeme také pokračujte a vytvořte tak pravidlo NAT pro port TCP 80 pro webový provoz, zapojení pravidlo až po naše fondy IP adres. Pokud jsme připojení pravidlo fondu IP adres, namísto zapojování pravidlo k virtuálním počítačům jednotlivě, můžeme přidat nebo odebrat virtuální počítače z fondu IP adres. Nástroje pro vyrovnávání zatížení automaticky přizpůsobí toku provozu. Následující příklad vytvoří pravidlo `myLoadBalancerRuleWeb` mapování portu TCP 80 na port 80:

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Výstup:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Vytvoření sondy stavu nástroje pro vyrovnávání zatížení
Stavu testu paměti pravidelně kontroluje na virtuálních počítačích, které jsou za naše nástroje pro vyrovnávání zatížení Ujistěte se, že se provoz a reakce na žádosti, jak jsou definovány. V opačném případě budou odebráni z operace Ujistěte se, že uživatelé nejsou nasměrování na ně. Můžete definovat vlastní kontroly pro sondu stavu, spolu s intervalech a hodnoty časového limitu. Další informace o sond stavu najdete v tématu [sondy nástroje pro vyrovnávání zatížení](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Následující příklad vytvoří TCP stavu zjištěný pojmenované `myHealthProbe`:

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Výstup:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Tady jsme zadali interval 15 sekund pro naše kontroly stavu. Můžete kdybychom promeškali maximálně čtyři sondy (jednu minutu) před spuštěním nástroje pro vyrovnávání zatížení bere v úvahu, že hostitele již není funkční.

## <a name="verify-the-load-balancer"></a>Ověřte, nástroj pro vyrovnávání zatížení
Nyní se provádí konfigurace služby Vyrovnávání zatížení. Tady jsou kroky, které jste provedli:

1. Jste vytvořili nástroj pro vyrovnávání zatížení.
2. Vytvoření front-endový fond IP a přiřazenou veřejnou IP adresu.
3. Vytvoříte back endového fondu IP, která virtuálním počítačům můžete připojit k.
4. Můžete vytvořit pravidla překladu adres, které umožňují SSH k virtuálním počítačům pro správu, spolu s pravidlo, které povolí port TCP 80 pro naši webovou aplikaci.
5. Můžete přidat sondu stavu, aby pravidelně kontrolovaly, virtuální počítače. Tato sonda stavu zajišťuje, že není uživatelé pokusí o přístup k virtuálnímu počítači, který je už nebude fungovat nebo odeslání obsahu.

Pojďme se podívat na nástroj pro vyrovnávání zatížení, která bude vypadat jako nyní:

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Výstup:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Vytvořte síťové rozhraní pro použití s virtuálního počítače s Linuxem
Síťové adaptéry představují prostřednictvím kódu programu k dispozici, protože pravidla lze použít k jejich použití. Také můžete mít více než jeden. V následujícím `azure network nic create` příkaz Připojit síťovou kartu pro fond zatížení back-end IP adres a přidružte jej k pravidlo překladu adres tak, aby povolovala provoz SSH.

Nahradit `#####-###-###` oddíly s vlastním ID předplatného Azure. Vaše předplatné ID je uvedeno ve výstupu příkazu `jq` při zkoumání prostředků, kterou vytváříte. Můžete také zobrazit ID vašeho předplatného s `azure account list`.

Následující příklad vytvoří síťové rozhraní s názvem `myNic1`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Výstup:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Zobrazit podrobnosti prozkoumáním prostředek přímo. Zkontrolujte prostředek s použitím `azure network nic show` příkaz:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Výstup:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Teď vytvoříme druhý síťový adaptér, zapojení do k naší back endového fondu IP znovu. Tento čas druhé pravidlo NAT povoluje provoz SSH. Následující příklad vytvoří síťové rozhraní s názvem `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Vytvoření skupiny zabezpečení sítě a pravidel
Teď vytvoříme skupinu zabezpečení sítě a příchozí pravidla, kterými se řídí přístup k síťové kartě Skupinu zabezpečení sítě můžete použít u síťové karty nebo podsítě. Můžete definovat pravidla pro řízení toku přenosů do a z vašich virtuálních počítačů. Následující příklad vytvoří skupinu zabezpečení sítě s názvem `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Pojďme přidat příchozí pravidlo pro skupinu NSG umožňuje příchozí připojení na portu 22 (pro podporu SSH). Následující příklad vytvoří pravidlo `myNetworkSecurityGroupRuleSSH` umožňující TCP na portu 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Nyní Pojďme přidat příchozí pravidlo pro skupinu NSG umožňuje příchozí připojení na portu 80 (pro podporu webového provozu). Následující příklad vytvoří pravidlo `myNetworkSecurityGroupRuleHTTP` umožňující TCP na portu 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> Příchozí pravidlo je filtr pro příchozí síťová připojení. V tomto příkladu jsme vytvořit vazbu skupiny zabezpečení sítě pro virtuální počítače virtuální síťové karty, což znamená, že všechny žádosti na port 22 se předává do síťového rozhraní na našich virtuálních počítačů. Toto pravidlo se týká připojení k síti, a o koncový bod, který není co by se jednat o v klasických nasazeních. Otevření portu, ponechte `--source-port-range` nastavena na "\*" (výchozí hodnota) tak, aby přijímal příchozí žádosti od **jakékoli** žádosti o portu. Porty jsou obvykle dynamická.
>
>

## <a name="bind-to-the-nic"></a>Vytvořit vazbu na síťový adaptér
Vytvoření vazby NSG na síťové karty. Musíme připojit náš síťové adaptéry s naší skupiny zabezpečení sítě. Spusťte oba příkazy, abyste připojili i naše síťových karet:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti
Skupiny dostupnosti nápovědy rozšíření virtuálních počítačů napříč doménami selhání a upgradovacích domén. Pojďme vytvořit skupinu dostupnosti pro virtuální počítače. Následující příklad vytvoří skupinu dostupnosti `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Domény selhání definují seskupení virtuálních počítačů, které sdílejí společný power přepínač zdroje a sítě. Ve výchozím nastavení virtuální počítače, které jsou nakonfigurované v rámci skupiny dostupnosti rozdělené až do tří domén selhání. Cílem je, že hardwarové potíže v jednom z těchto domén selhání nemá vliv na každý virtuální počítač, na kterém běží vaše aplikace. Virtuální počítače Azure automaticky distribuuje napříč doménami selhání, při vkládání je ve skupině dostupnosti.

Upgradovací domény označují skupiny virtuálních počítačů a podřízeného fyzického hardwaru, který lze restartovat současně. Pořadí, ve které se restartují upgradovací domény nemusí být sekvenční během plánované údržby, ale pouze jeden upgrade se restartuje. Znovu Azure automaticky distribuuje do upgradovacích doménách při uvedení na webu dostupnosti.

Další informace o [Správa dostupnosti virtuálních počítačů](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Vytvoření virtuálních počítačů s Linuxem
Vytvořili jste úložné a síťové prostředky pro podporu přístupné z Internetu virtuálních počítačů. Nyní Pojďme vytvořit tyto virtuální počítače a zabezpečení pomocí klíče SSH, který nemá heslo. V tomto případě chceme vytvořit virtuální počítač založený na nejnovější LTS Ubuntu. Pomocí vyhledat informace o této bitové kopii `azure vm image list`, jak je popsáno v [vyhledání imagí virtuálních počítačů Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Jsme vybrali jste image pomocí příkazu `azure vm image list westeurope canonical | grep LTS`. V tomto případě používáme `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Poslední pole předáme `latest` tak, aby v budoucnu vždy získáme nejnovějšího sestavení. (Je řetězec použijeme `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Jsou známy všem uživatelům, kteří už vytvořil tento další krok ssh veřejného a privátního klíče rsa spárovat v systému Linux nebo Mac pomocí **ssh-keygen - t rsa -b 2048**. Pokud nemáte žádné páry klíčů certifikátu vaší `~/.ssh` adresáře, můžete je vytvořit:

* Automaticky pomocí `azure vm create --generate-ssh-keys` možnost.
* Ručně pomocí [pokyny k vytvoření sami](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Alternativně můžete použít `--admin-password` metodu k ověření připojení SSH po vytvoření virtuálního počítače. Tato metoda je obvykle méně bezpečné.

Vytvoříme virtuální počítač tak, že všechny naše materiály a informace spolu s `azure vm create` příkaz:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Výstup:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Můžete připojit k virtuálnímu počítači okamžitě pomocí klíče SSH výchozí. Je nutné zadat příslušný port, protože jsme se předá prostřednictvím nástroje pro vyrovnávání zatížení. (Pro naši první virtuální počítač, nastavíme pravidlo NAT port 4222 předat virtuálního počítače.)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Výstup:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Pokračujte a vytvořte druhý virtuální počítač stejným způsobem:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

A teď můžete použít `azure vm show myResourceGroup myVM1` příkaz pro zjištění, co jste vytvořili. V tuto chvíli používáte vaše virtuální počítače s Ubuntu za nástrojem pro vyrovnávání zatížení v Azure, které se můžete přihlásit pouze pomocí páru klíčů SSH (protože hesla jsou zakázány). Můžete nainstalovat server nginx nebo httpd, nasaďte webovou aplikaci a sledovat provoz toku prostřednictvím nástroje pro vyrovnávání zatížení do obou virtuálních počítačů.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Výstup:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Export prostředí jako šablonu
Teď, když jste vytvořili mimo toto prostředí, co dělat, pokud chcete vytvořit další vývojová prostředí se stejnými parametry nebo produkčním prostředí, který mu odpovídá? Správce prostředků pomocí šablon JSON, které definují parametry vašeho prostředí. Pomocí odkazu na tuto šablonu JSON sestavujete celé prostředí. Je možné [ručně vytvářet šablony JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo exportovat stávající prostředí pro vás vytvořit šablonu JSON:

```azurecli
azure group export --name myResourceGroup
```

Tento příkaz vytvoří `myResourceGroup.json` soubor v aktuálním pracovním adresáři. Při vytváření prostředí z této šablony se zobrazí výzva k zadání všech prostředků názvy, včetně názvů pro nástroj pro vyrovnávání zatížení, síťová rozhraní nebo virtuální počítače. Tyto názvy můžete naplnit v souboru šablony tak, že přidáte `-p` nebo `--includeParameterDefaultValue` parametr `azure group export` příkaz, který byl je uvedeno výše. Upravte svou šablonu JSON k určení názvy prostředků, nebo [vytvoření souboru parameters.json](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , který určuje názvy prostředků.

Vytvoření prostředí ze šablony:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Můžete chtít číst [Další informace o tom, jak nasadit ze šablon](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Další informace o tom, jak přírůstkově aktualizovat prostředí, použijte soubor parametrů a přístup k šablony jedno umístění úložiště.

## <a name="next-steps"></a>Další postup
Nyní jste připraveni začít pracovat s více síťovými součástmi a virtuální počítače. Toto ukázkové prostředí můžete použít k sestavení aplikace s použitím základních komponent zavedené zde.
