---
title: Vytvoření kompletního prostředí Linux pomocí Azure Classic CLI | Microsoft Docs
description: Vytvořte úložiště, virtuální počítač se systémem Linux, virtuální síť a podsíť, nástroj pro vyrovnávání zatížení, síťovou kartu, veřejnou IP adresu a skupinu zabezpečení sítě, a to všechno od základu pomocí rozhraní příkazového řádku Azure Classic.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: aaf91aa81be5fc4c5944dde804798a61ceffc5a6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083722"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Vytvoření kompletního prostředí Linux pomocí Azure Classic CLI
V tomto článku sestavíme jednoduchou síť s nástrojem pro vyrovnávání zatížení a pár virtuálních počítačů, které jsou užitečné pro vývoj a jednoduché výpočetní účely. Provedeme vás příkazem zpracovat příkaz, dokud nebudete mít dva pracovní a zabezpečené virtuální počítače se systémem Linux, ke kterým se můžete připojit odkudkoli na internetu. Pak můžete přejít na komplexnější sítě a prostředí.

V takovém případě se dozvíte o hierarchii závislostí, kterou vám nabízí model nasazení Správce prostředků, a o tom, kolik energie nabízí. Až uvidíte, jak se systém sestavuje, můžete ho znovu sestavit mnohem rychleji pomocí [Azure Resource Manager šablon](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). I když se dozvíte, jak se součásti vašeho prostředí vejdou dohromady, je vytváření šablon pro automatizaci snazší.

Prostředí obsahuje:

* Dva virtuální počítače v rámci skupiny dostupnosti.
* Nástroj pro vyrovnávání zatížení s pravidlem vyrovnávání zatížení na portu 80.
* Pravidla skupiny zabezpečení sítě (NSG) pro ochranu virtuálního počítače před nežádoucím provozem.

K vytvoření tohoto vlastního prostředí budete potřebovat nejnovější rozhraní příkazového [řádku Azure Classic](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) v režimu správce prostředků`azure config mode arm`(). Potřebujete také nástroj pro analýzu JSON. V tomto příkladu se používá [JQ](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure Classic CLI](#quick-commands) – naše rozhraní příkazového řádku pro modely nasazení Classic a Resource Management (Tento článek)
- [Azure CLI](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků


## <a name="quick-commands"></a>Rychlé příkazy
Pokud potřebujete úkol rychle provést, následující část podrobně popisuje základní příkazy pro nahrání virtuálního počítače do Azure. Podrobnější informace a kontext pro každý krok najdete ve zbývající části dokumentu, začněte [tady](#detailed-walkthrough).

Ujistěte se, že máte k dispozici rozhraní příkazového [řádku Azure Classic](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a používáte režim správce prostředků:

```azurecli
azure config mode arm
```

V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami. Příklady názvů parametrů zahrnují `myResourceGroup`, `mystorageaccount`, a `myVM`.

Vytvořte skupinu prostředků. Následující příklad vytvoří skupinu prostředků `myResourceGroup` v umístění `westeurope`:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Ověřte skupinu prostředků pomocí analyzátoru JSON:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Vytvořte účet úložiště. Následující příklad vytvoří účet úložiště s názvem `mystorageaccount`. (Název účtu úložiště musí být jedinečný, zadejte tak vlastní jedinečný název.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Ověřte účet úložiště pomocí analyzátoru JSON:

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

Ověřte virtuální síť a podsíť pomocí analyzátoru JSON:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Vytvořte veřejnou IP adresu. Následující příklad vytvoří veřejnou IP adresu s názvem `myPublicIP` s `mypublicdns`názvem DNS. (Název DNS musí být jedinečný, takže zadejte vlastní jedinečný název.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Vytvořte Nástroj pro vyrovnávání zatížení. Následující příklad vytvoří nástroj pro vyrovnávání zatížení `myLoadBalancer`s názvem:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Vytvořte front-end fond IP adres pro nástroj pro vyrovnávání zatížení a přidružte veřejnou IP adresu. Následující příklad vytvoří front-end fond IP adres s názvem `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Vytvořte back-end fond IP adres pro nástroj pro vyrovnávání zatížení. Následující příklad vytvoří fond back-end IP adresy s názvem `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Pro nástroj pro vyrovnávání zatížení vytvořte pravidla překladu adres příchozích síťových adres SSH (NAT). Následující příklad vytvoří dvě pravidla `myLoadBalancerRuleSSH1` nástroje pro vyrovnávání zatížení a: `myLoadBalancerRuleSSH2`

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Vytvořte pravidla webového příchozího překladu adres (NAT) pro nástroj pro vyrovnávání zatížení. Následující příklad vytvoří pravidlo nástroje pro vyrovnávání zatížení `myLoadBalancerRuleWeb`s názvem:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Vytvořte sondu stavu nástroje pro vyrovnávání zatížení. Následující příklad vytvoří test TCP s názvem `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Ověření nástroje pro vyrovnávání zatížení, fondů IP adres a pravidel NAT pomocí analyzátoru JSON:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Vytvořte první síťovou kartu (NIC). Nahraďte `#####-###-###` oddíly vlastním ID předplatného Azure. ID vašeho předplatného je uvedeno ve výstupu **JQ** při prohlédnutí vytvářených prostředků. Můžete si také zobrazit ID `azure account list`vašeho předplatného.

Následující příklad vytvoří síťovou kartu s názvem `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Vytvořte druhou síťovou kartu. Následující příklad vytvoří síťovou kartu s názvem `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Pomocí analyzátoru JSON ověřte dvě síťové karty:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Vytvořte skupinu zabezpečení sítě. Následující příklad vytvoří skupinu zabezpečení sítě s názvem `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Přidejte dvě příchozí pravidla pro skupinu zabezpečení sítě. Následující příklad vytvoří dvě pravidla `myNetworkSecurityGroupRuleSSH` a: `myNetworkSecurityGroupRuleHTTP`

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Ověřte skupinu zabezpečení sítě a příchozí pravidla pomocí analyzátoru JSON:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Navažte skupinu zabezpečení sítě na tyto dvě síťové karty:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Vytvořte skupinu dostupnosti. Následující příklad vytvoří skupinu dostupnosti s názvem `myAvailabilitySet`:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Vytvořte první virtuální počítač Linux. Následující příklad vytvoří virtuální počítač s názvem `myVM1`:

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

Vytvořte druhý virtuální počítač se systémem Linux. Následující příklad vytvoří virtuální počítač s názvem `myVM2`:

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

Pomocí analyzátoru JSON ověřte, že všechno, co bylo sestaveno:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exportujte nové prostředí do šablony, abyste mohli rychle znovu vytvořit nové instance:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Podrobný postup
Následující postup vysvětluje, co jednotlivé příkazy dělají při sestavování vašeho prostředí. Tyto koncepty jsou užitečné, když vytváříte vlastní prostředí pro vývoj nebo produkci.

Ujistěte se, že máte k dispozici rozhraní příkazového [řádku Azure Classic](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a používáte režim správce prostředků:

```azurecli
azure config mode arm
```

V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami. Příklady názvů parametrů zahrnují `myResourceGroup`, `mystorageaccount`, a `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Vytvoření skupin prostředků a výběr umístění nasazení
Skupiny prostředků Azure jsou logické entity nasazení, které obsahují informace o konfiguraci a metadata, aby bylo možné logickou správu nasazení prostředků. Následující příklad vytvoří skupinu prostředků `myResourceGroup` v umístění `westeurope`:

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
Pro disky virtuálních počítačů potřebujete účty úložiště a pro všechny další datové disky, které chcete přidat. Účty úložiště vytvoříte skoro hned po vytvoření skupin prostředků.

V `azure storage account create` tomto příkladu použijeme příkaz, projdeme umístění účtu, skupinu prostředků, která ho řídí, a typ požadované podpory úložiště. Následující příklad vytvoří účet úložiště s názvem `mystorageaccount`:

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

K prohlédnutí naší skupiny prostředků pomocí `azure group show` příkazu použijte `--json` nástroj [JQ](https://stedolan.github.io/jq/) spolu s možností Azure CLI. (Můžete použít **jsawk** nebo libovolnou jazykovou knihovnu, kterou dáváte přednost analýze formátu JSON.)

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

Pokud chcete prozkoumat účet úložiště pomocí rozhraní příkazového řádku, musíte nejdřív nastavit názvy účtů a klíče. V následujícím příkladu nahraďte název účtu úložiště názvem, který zvolíte:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Pak můžete snadno zobrazit informace o úložišti:

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
Teď budete muset vytvořit virtuální síť běžící v Azure a podsíť, ve které můžete vytvářet virtuální počítače. Následující příklad vytvoří virtuální síť s názvem `myVnet` `192.168.0.0/16` s předponou adresy:

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

Znovu použijeme možnost--JSON pro `azure group show` a `jq` k zobrazení způsobu vytváření prostředků. Teď `storageAccounts` máme prostředek `virtualNetworks` a prostředek.  

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

Nyní vytvoříme podsíť ve `myVnet` virtuální síti, do které jsou nasazené virtuální počítače. `azure network vnet subnet create` Příkaz používáme společně s prostředky, které jsme už vytvořili `myResourceGroup` : Skupina prostředků a `myVnet` virtuální síť. V následujícím příkladu přidáme podsíť s názvem `mySubnet` s předponou adresy podsítě pro: `192.168.1.0/24`

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

Vzhledem k tomu, že podsíť je logicky uvnitř virtuální sítě, podíváme se na informace o podsíti trochu jiným příkazem. Tento příkaz používáme `azure network vnet show`, ale budeme dál kontrolovat výstup JSON pomocí `jq`.

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
Teď vytvoříme veřejnou IP adresu (PIP), kterou přiřadíme vašemu nástroji pro vyrovnávání zatížení. Umožňuje připojit se k virtuálním počítačům z Internetu pomocí `azure network public-ip create` příkazu. Vzhledem k tomu, že výchozí adresa je dynamická, vytvoříme pomocí `--domain-name-label` možnosti pojmenovanou položku DNS v doméně **cloudapp.Azure.com** . Následující příklad vytvoří veřejnou IP adresu s názvem `myPublicIP` s `mypublicdns`názvem DNS. Vzhledem k tomu, že název DNS musí být jedinečný, zadejte vlastní jedinečný název DNS:

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

Veřejná IP adresa je také prostředkem nejvyšší úrovně, takže ji můžete zobrazit v `azure group show`.

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

Pomocí příkazu Complete `azure network public-ip show` můžete prozkoumat další podrobnosti o prostředcích, včetně plně kvalifikovaného názvu domény (FQDN) subdomény. Prostředek veřejné IP adresy byl přidělen logicky, ale konkrétní adresa ještě nebyla přiřazena. Pokud chcete získat IP adresu, budete potřebovat nástroj pro vyrovnávání zatížení, který jsme ještě nevytvořili.

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

## <a name="create-a-load-balancer-and-ip-pools"></a>Vytvoření nástroje pro vyrovnávání zatížení a fondů IP adres
Když vytvoříte Nástroj pro vyrovnávání zatížení, umožníte distribuci provozu napříč několika virtuálními počítači. Poskytuje taky redundanci pro vaši aplikaci spuštěním několika virtuálních počítačů, které reagují na požadavky uživatelů v případě údržby nebo těžkého zatížení. Následující příklad vytvoří nástroj pro vyrovnávání zatížení `myLoadBalancer`s názvem:

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

Náš nástroj pro vyrovnávání zatížení je poměrně prázdný, takže vytvoříme několik fondů IP adres. Chceme pro náš nástroj pro vyrovnávání zatížení vytvořit dvě fondy IP adres, jednu pro front-end a jednu pro back-end. Front-end fond IP adres je veřejně viditelný. Je to také místo, ke kterému přiřadíme PIP, kterou jsme vytvořili dříve. Pak používáme back-end fond jako umístění pro připojení k virtuálním počítačům. Tímto způsobem může přenos procházet nástrojem pro vyrovnávání zatížení do virtuálních počítačů.

Nejdřív vytvořte náš front-end fond IP adres. Následující příklad vytvoří front-end fond s názvem `myFrontEndPool`:

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

Všimněte si `myPublicIP` , jak jsme `--public-ip-name` použili přepínač k předání do, který jsme vytvořili dříve. Přiřazení veřejné IP adresy k nástroji pro vyrovnávání zatížení vám umožní oslovit vaše virtuální počítače přes Internet.

Nyní vytvoříme náš druhý fond IP adres, tentokrát pro náš back-end provoz. Následující příklad vytvoří fond back-end s názvem `myBackEndPool`:

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

Jak si vyhledá `azure network lb show` náš nástroj pro vyrovnávání zatížení, zjistíte výstup JSON a prozkoumáte ho:

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

## <a name="create-load-balancer-nat-rules"></a>Vytvořit pravidla překladu adres (NAT) pro vyrovnávání zatížení
Abychom mohli provozovat tok prostřednictvím našeho nástroje pro vyrovnávání zatížení, musíme vytvořit pravidla překladu adres (NAT), která určují buď příchozí, nebo odchozí akce. Můžete zadat protokol, který se má použít, a pak podle potřeby mapovat Externí porty na interní porty. V našem prostředí vytvoříme některá pravidla, která umožní prostřednictvím našeho nástroje pro vyrovnávání zatížení pro naše virtuální počítače protokol SSH. Nastavili jste porty TCP 4222 a 4223 na směrování na port TCP 22 na našich virtuálních počítačích (které vytvoříme později). Následující příklad vytvoří pravidlo s názvem `myLoadBalancerRuleSSH1` pro mapování portu TCP 4222 na port 22:

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

Opakujte postup pro druhé pravidlo překladu adres (NAT) pro SSH. Následující příklad vytvoří pravidlo s názvem `myLoadBalancerRuleSSH2` pro mapování portu TCP 4223 na port 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Pojďme se také vystavit pravidlo překladu adres (NAT) pro port TCP 80 pro webový provoz a toto pravidlo se bude zapojovat do našich fondů IP adres. Pokud pravidlo připravujeme k fondu IP adres, místo toho, aby se do našich virtuálních počítačů přidalo pravidlo jednotlivě, můžeme přidat nebo odebrat virtuální počítače z fondu IP adres. Nástroj pro vyrovnávání zatížení automaticky upraví tok provozu. Následující příklad vytvoří pravidlo s názvem `myLoadBalancerRuleWeb` pro mapování portu TCP 80 na port 80:

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
Sonda stavu pravidelně kontroluje virtuální počítače, které jsou za naším nástrojem pro vyrovnávání zatížení, a zajišťuje tak, že budou pracovat a reagovat na požadavky podle definice. Pokud ne, odeberou se z provozu, aby se zajistilo, že se na ně uživatelé nesměrují. Můžete definovat vlastní kontroly pro sondu stavu spolu s intervaly a hodnotami časového limitu. Další informace o sondách stavu naleznete v tématu [Load Balancer PROBE](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Následující příklad vytvoří test stavu TCP s názvem `myHealthProbe`:

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

Tady jsme pro naše kontroly stavu určili interval 15 sekund. Před tím, než Nástroj pro vyrovnávání zatížení bude mít za to, že hostitel přestane fungovat, může to trvat maximálně čtyři sondy (jedna minuta).

## <a name="verify-the-load-balancer"></a>Ověření nástroje pro vyrovnávání zatížení
Konfigurace nástroje pro vyrovnávání zatížení se teď dokončila. Tady je postup, který jste udělali:

1. Vytvořili jste nástroj pro vyrovnávání zatížení.
2. Vytvořili jste front-end fond IP adres a k němu jste přiřadili veřejnou IP adresu.
3. Vytvořili jste fond back-end IP adres, ke kterému se můžou virtuální počítače připojit.
4. Vytvořili jste pravidla překladu adres (NAT), která umožňují SSH pro virtuální počítače pro správu, spolu s pravidlem, které umožňuje pro naši webovou aplikaci port TCP 80.
5. Přidali jste sondu stavu pro pravidelnou kontrolu virtuálních počítačů. Tato sonda stavu zajišťuje, že se uživatelé nesnaží získat přístup k virtuálnímu počítači, který už nepracuje ani neposkytuje obsah.

Pojďme si prohlédnout, co váš nástroj pro vyrovnávání zatížení vypadá teď:

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

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Vytvoření síťového adaptéru pro použití s virtuálním počítačem se systémem Linux
Síťové adaptéry jsou k dispozici programově, protože můžete použít pravidla na jejich použití. Můžete mít také více než jednu. V následujícím `azure network nic create` příkazu připojíte síťovou kartu ke fondu IP back-endu zatížení a přidružíte ho k pravidlu NAT, aby se povolil přenos přes SSH.

Nahraďte `#####-###-###` oddíly vlastním ID předplatného Azure. ID vašeho předplatného je uvedeno ve výstupu `jq` , když prohlížíte prostředky, které vytváříte. Můžete si také zobrazit ID `azure account list`vašeho předplatného.

Následující příklad vytvoří síťovou kartu s názvem `myNic1`:

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

Podrobnosti si můžete prohlédnout přímo prozkoumáním prostředku. Prověřte prostředek pomocí `azure network nic show` příkazu:

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

Teď vytvoříme druhou síťovou kartu, která se znovu připojí k back-endovému fondu IP adres. Tentokrát druhé pravidlo překladu adres (NAT) povolí přenos přes SSH. Následující příklad vytvoří síťovou kartu s názvem `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Vytvoření skupiny zabezpečení sítě a pravidel
Nyní vytvoříme skupinu zabezpečení sítě a příchozí pravidla, která řídí přístup k síťovému rozhraní. Skupinu zabezpečení sítě lze použít pro síťové rozhraní nebo podsíť. Můžete definovat pravidla pro řízení toku provozu do virtuálních počítačů a z nich. Následující příklad vytvoří skupinu zabezpečení sítě s názvem `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Pojďme přidat příchozí pravidlo pro NSG, které povolí příchozí připojení na portu 22 (pro podporu SSH). Následující příklad vytvoří pravidlo s názvem `myNetworkSecurityGroupRuleSSH` , které povolí TCP na portu 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Nyní přidáme příchozí pravidlo pro NSG, které povolí příchozí připojení na portu 80 (pro podporu webového provozu). Následující příklad vytvoří pravidlo s názvem `myNetworkSecurityGroupRuleHTTP` , které povolí TCP na portu 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> Příchozí pravidlo je filtr pro příchozí síťová připojení. V tomto příkladu vytvoříme NSG k virtuálním síťovým ADAPTÉRům virtuálních počítačů, což znamená, že všechny požadavky na port 22 se přenesou do síťového rozhraní na našem virtuálním počítači. Toto příchozí pravidlo se týká připojení k síti, a ne od koncového bodu, což je v klasických nasazeních. Chcete-li otevřít port, je nutné nechat `--source-port-range` vlastnost nastavenou\*na hodnotu (výchozí hodnota), aby přijímala příchozí požadavky z **libovolného** požadavku na port. Porty jsou obvykle dynamické.
>
>

## <a name="bind-to-the-nic"></a>Vytvoření vazby na síťový adaptér
Navažte NSG na síťové karty. Musíme připojit Naše síťové karty k naší skupině zabezpečení sítě. Spusťte oba příkazy, aby se připojily oba naše síťové karty:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti
Skupiny dostupnosti pomůžou rozšířit vaše virtuální počítače napříč doménami selhání a upgradovacími doménami. Pojďme vytvořit skupinu dostupnosti pro vaše virtuální počítače. Následující příklad vytvoří skupinu dostupnosti s názvem `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Domény selhání definují seskupení virtuálních počítačů, které sdílejí společný zdroj napájení a síťový přepínač. Ve výchozím nastavení jsou virtuální počítače, které jsou nakonfigurované v rámci skupiny dostupnosti, oddělené v rámci až tří domén selhání. Výsledkem je, že problém hardwaru v jedné z těchto domén selhání nemá vliv na všechny virtuální počítače, na kterých je vaše aplikace spuštěná. Azure automaticky distribuuje virtuální počítače napříč doménami selhání při jejich umísťování do skupiny dostupnosti.

Upgradovací domény označují skupiny virtuálních počítačů a základní fyzický hardware, které je možné restartovat současně. Pořadí, ve kterém jsou domény upgradu restartovány, nemusí být sekvenční během plánované údržby, ale v jednu chvíli se restartuje jenom jeden upgrade. Azure při jejich umístění do lokality dostupnosti automaticky distribuuje vaše virtuální počítače mezi upgradovací domény.

Přečtěte si další informace o [správě dostupnosti virtuálních počítačů](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Vytvoření virtuálních počítačů se systémem Linux
Vytvořili jste úložiště a síťové prostředky pro podporu virtuálních počítačů s přístupem k Internetu. Nyní vytvoříme tyto virtuální počítače a zabezpečte je pomocí klíče SSH, který nemá heslo. V tomto případě vytvoříme virtuální počítač Ubuntu založený na nejnovějším LTS. Tyto informace o imagi vyhledáme `azure vm image list`pomocí, jak je popsáno v tématu [vyhledání imagí virtuálních počítačů Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Vybrali jsme Image pomocí příkazu `azure vm image list westeurope canonical | grep LTS`. V tomto případě používáme `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Pro poslední pole předáte `latest` , aby v budoucnu vždycky získalo nejnovější sestavení. (Řetězec, který používáme `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`, je).

Tento další krok je známý pro kohokoli, kdo už vytvořil pár veřejného a privátního klíče SSH RSA v systému Linux nebo Mac pomocí **ssh-keygen-t RSA-b 2048**. Pokud ve svém `~/.ssh` adresáři nemáte žádné páry klíčů certifikátů, můžete je vytvořit:

* Automaticky pomocí `azure vm create --generate-ssh-keys` možnosti.
* Ručně pomocí [pokynů pro jejich vytvoření](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Alternativně můžete použít `--admin-password` metodu k ověření připojení SSH po vytvoření virtuálního počítače. Tato metoda je obvykle méně bezpečná.

Vytvoříme virtuální počítač tím, že všechny naše prostředky a informace spojíme `azure vm create` s příkazem:

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

K VIRTUÁLNÍmu počítači se můžete připojit hned pomocí výchozích klíčů SSH. Ujistěte se, že jste zadali příslušný port, protože projdete nástrojem pro vyrovnávání zatížení. (Pro náš první virtuální počítač nastavíme pravidlo překladu adres (NAT) pro přeposílání portu 4222 na náš virtuální počítač.)

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
    https://www.ubuntu.com/business/services/cloud

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

A teď můžete pomocí `azure vm show myResourceGroup myVM1` příkazu zkontrolovat, co jste vytvořili. V tuto chvíli spouštíte virtuální počítače s Ubuntu za nástrojem pro vyrovnávání zatížení v Azure, které se můžete přihlásit jenom pomocí páru klíčů SSH (protože hesla jsou zakázaná). Můžete nainstalovat Nginx nebo httpd, nasadit webovou aplikaci a sledovat tok přenosů přes Nástroj pro vyrovnávání zatížení do obou virtuálních počítačů.

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


## <a name="export-the-environment-as-a-template"></a>Exportovat prostředí jako šablonu
Teď, když jste vytvořili toto prostředí, co když chcete vytvořit další vývojové prostředí se stejnými parametry, nebo produkčním prostředím, které odpovídá tomuto prostředí? Správce prostředků používá šablony JSON, které definují všechny parametry vašeho prostředí. Můžete sestavit celá prostředí odkazem na tuto šablonu JSON. Můžete vytvořit [šablony JSON ručně](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo exportovat existující prostředí a vytvořit šablonu JSON pro vás:

```azurecli
azure group export --name myResourceGroup
```

Tento příkaz vytvoří `myResourceGroup.json` soubor v aktuálním pracovním adresáři. Když z této šablony vytvoříte prostředí, zobrazí se výzva k zadání všech názvů prostředků, včetně názvů pro nástroj pro vyrovnávání zatížení, síťová rozhraní nebo virtuálních počítačů. Tyto názvy můžete vyplnit v souboru šablony přidáním `-p` parametru nebo `--includeParameterDefaultValue` do `azure group export` příkazu, který byl zobrazen dříve. Upravte šablonu JSON tak, aby určovala názvy prostředků, nebo [vytvořte soubor Parameters. JSON](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , který určuje názvy prostředků.

Vytvoření prostředí ze šablony:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Můžete si přečíst [Další informace o tom, jak nasadit z šablon](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Přečtěte si, jak postupně aktualizovat prostředí, použít soubor parametrů a získat přístup k šablonám z jediného umístění úložiště.

## <a name="next-steps"></a>Další postup
Teď jste připraveni začít pracovat s několika síťovými součástmi a virtuálními počítači. Toto ukázkové prostředí můžete použít k sestavení aplikace pomocí základních součástí, které jsou zde zavedeny.
