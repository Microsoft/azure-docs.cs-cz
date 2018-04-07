---
title: Vytvoření kompletní prostředí Linux pomocí Azure CLI 1.0 | Microsoft Docs
description: Vytvoření úložiště, virtuální počítač s Linuxem, virtuální síť a podsíť, nástroj pro vyrovnávání zatížení, seskupování, veřejnou IP adresu a skupinu zabezpečení sítě, všechny od základů pomocí Azure CLI 1.0.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 4a43e138d3497e01fe9e0e5c55a4a66adac767c6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-complete-linux-environment-with-the-azure-cli-10"></a>Vytvoření kompletní prostředí Linux pomocí Azure CLI 1.0
V tomto článku jsme sestavení jednoduchá síť se nástroj pro vyrovnávání zatížení a dvojice virtuálních počítačů, které jsou užitečné pro vývoj a jednoduchý computing. Provede jsme proces příkazu command, dokud nebudete mít dvě funkční a zabezpečené virtuální počítače s Linuxem do kterých můžete připojit z kdekoli v síti Internet. Potom můžete přesunout složitější sítě a prostředí.

Na cestě můžete další informace o hierarchii závislostí modelu nasazení Resource Manager nabízí, a o tom, kolik ho zapněte poskytuje. Jakmile uvidíte, jak se sestaví systém, můžete znovu sestavit je mnohem rychlejší pomocí [šablon Azure Resource Manageru](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Kromě toho po zjistíte, jak jsou navzájem propojené části vašeho prostředí, vytváření šablon pro automatizaci je jednodušší.

Prostředí obsahuje:

* Dva virtuální počítače uvnitř skupiny dostupnosti.
* Vyrovnávání zatížení s pravidlem Vyrovnávání zatížení na portu 80.
* Pravidla skupiny (NSG) zabezpečení sítě k ochraně virtuálního počítače z nevyžádaný provoz.

K vytvoření tohoto vlastního prostředí, je třeba nejnovější [Azure CLI 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) v režimu Resource Manager (`azure config mode arm`). Musíte taky analýza nástroj JSON. Tento příklad používá [jq](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#quick-commands) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků


## <a name="quick-commands"></a>Rychlé příkazy
Pokud potřebujete rychle provedení úlohy, následující část podrobně popisuje základní příkazy k nahrání virtuálního počítače do Azure. Podrobnější informace a kontext pro každý krok naleznete ve zbývající části dokumentu, od [zde](#detailed-walkthrough).

Ujistěte se, že máte [Azure CLI 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) přihlášení a použití režimu Resource Manager:

```azurecli
azure config mode arm
```

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Zahrnout názvy parametrů příklad `myResourceGroup`, `mystorageaccount`, a `myVM`.

Vytvořte skupinu prostředků. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v `westeurope` umístění:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Skupina prostředků ověřte pomocí analyzátoru JSON:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Vytvořte účet úložiště. Následující příklad vytvoří účet úložiště s názvem `mystorageaccount`. (Název účtu úložiště musí být jedinečný, takže zadejte svůj vlastní jedinečný název.)

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

Vytvoření podsítě. Následující příklad vytvoří podsíť s názvem `mySubnet`:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Ověřte virtuální síť a podsíť pomocí analyzátoru JSON:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Vytvořte veřejnou IP adresu. Následující příklad vytvoří veřejnou IP adresu s názvem `myPublicIP` s názvem DNS `mypublicdns`. (Název DNS musí být jedinečný, takže zadejte svůj vlastní jedinečný název.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Vytvořte nástroj pro vyrovnávání zatížení. Následující příklad vytvoří nástroj pro vyrovnávání zatížení s názvem `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Vytvořte fond IP front-endu nástroje pro vyrovnávání zatížení a přidružte veřejnou IP adresu. Následující příklad vytvoří front-end IP fond s názvem `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Vytvořte fond back-end IP nástroje pro vyrovnávání zatížení. Následující příklad vytvoří fond back-end IP s názvem `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Vytvoření příchozích síťových SSH pravidla překladu adres nástroje pro vyrovnávání zatížení. Následující příklad vytvoří dvě pravidla nástroje pro vyrovnávání zatížení, `myLoadBalancerRuleSSH1` a `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Vytvoření webu příchozí pravidla NAT nástroje pro vyrovnávání zatížení. Následující příklad vytvoří pravidlo Vyrovnávání zatížení s názvem `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Vytvořte test stavu nástroje pro vyrovnávání zatížení. Následující příklad vytvoří sondou TCP s názvem `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Ověřte nástroj pro vyrovnávání zatížení, fondy IP adres a pravidla NAT pomocí analyzátoru JSON:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Vytvoření první síťové karty (NIC). Nahraďte `#####-###-###` oddíly s vlastními ID předplatného Azure. Vaše předplatné ID je uvedeno ve výstupu **jq** při kontrole prostředků, kterou vytváříte. Můžete také zobrazit svoje ID předplatného s `azure account list`.

Následující příklad vytvoří síťový adaptér s názvem `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Vytvořte druhý síťový adaptér. Následující příklad vytvoří síťový adaptér s názvem `myNic2`:

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

Přidejte dva příchozích pravidel pro skupinu zabezpečení sítě. Následující příklad vytvoří dvě pravidla `myNetworkSecurityGroupRuleSSH` a `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Skupina zabezpečení sítě a ověřte příchozí pravidla pomocí analyzátoru JSON:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Vytvořit skupinu zabezpečení sítě vazbu na dva síťové adaptéry:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Vytvořte sadu dostupnosti. Následující příklad vytvoří sadu s názvem dostupnosti `myAvailabilitySet`:

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

Pomocí analyzátoru JSON ověřte, zda vše, co byla vytvořena:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exportujte do šablony rychle znovu vytvořit nové instance nové prostředí:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Podrobný postup
Podrobné kroky, které následují popisují, co každý příkaz probíhá při sestavování na vašem prostředí. Tyto koncepty jsou užitečné, když vytváříte vlastní vlastní prostředí pro vývoj nebo produkčního prostředí.

Ujistěte se, že máte [Azure CLI 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) přihlášení a použití režimu Resource Manager:

```azurecli
azure config mode arm
```

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Zahrnout názvy parametrů příklad `myResourceGroup`, `mystorageaccount`, a `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Vytvoření skupiny prostředků a vyberte umístění nasazení
Skupiny prostředků Azure jsou logické nasazení entit, které obsahují informace o konfiguraci a metadata povolení logické správy nasazení prostředků. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v `westeurope` umístění:

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
Budete potřebovat účty úložiště pro disky virtuálního počítače a pro jakýchkoli dalších datových disků, které chcete přidat. Můžete vytvořit účty úložiště téměř okamžitě po vytvoření skupiny prostředků.

Tady používáme `azure storage account create` příkazu předávání umístění účtu, skupinu prostředků, které řídí a typ úložiště s podporou chcete. Následující příklad vytvoří účet úložiště s názvem `mystorageaccount`:

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

Prozkoumat naše skupinu prostředků s použitím `azure group show` příkaz, použijeme [jq](https://stedolan.github.io/jq/) nástroj spolu s `--json` možnost příkazového řádku Azure CLI. (Můžete použít **jsawk** nebo všechny knihovny jazyka dáváte přednost analyzovat ve formátu JSON.)

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

K prozkoumání účet úložiště pomocí rozhraní příkazového řádku, musíte nejdřív nastavit názvy účtů a klíčů. Nahraďte název, který zvolíte, název účtu úložiště v následujícím příkladu:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Potom můžete zobrazit informace z vašeho úložiště snadno:

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
Potom budete muset vytvořit virtuální síť spuštěná ve službě Azure a podsíť, ve kterém můžete vytvořit virtuální počítače. Následující příklad vytvoří virtuální síť s názvem `myVnet` s `192.168.0.0/16` předpona adresy:

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

Znovu, budeme používat možnost--json `azure group show` a `jq` zobrazíte jak vytváříme naše prostředky. Nyní je k dispozici `storageAccounts` prostředků a `virtualNetworks` prostředků.  

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

Nyní vytvoříme podsíť v `myVnet` virtuální sítě, do které jsou nasazené virtuální počítače. Používáme `azure network vnet subnet create` příkazu, spolu s prostředky, které jste už vytvořili: `myResourceGroup` skupinu prostředků a `myVnet` virtuální sítě. V následujícím příkladu přidáme podsíť s názvem `mySubnet` s předponou adresy podsítě z `192.168.1.0/24`:

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

Protože podsíť je logicky ve virtuální síti, podíváme pro informace o podsíti pomocí příkazu mírně lišit. Příkaz používáme je `azure network vnet show`, ale budeme pokračovat, vyhledejte ve výstupu JSON pomocí `jq`.

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
Nyní vytvoříme veřejnou IP adresu (PIP), který jsme přiřadit nástroj pro vyrovnávání zatížení. Umožňuje připojení k virtuální počítače z Internetu pomocí `azure network public-ip create` příkaz. Protože výchozí adresa je dynamická, můžeme vytvořit záznam DNS s názvem v **cloudapp.azure.com** domény pomocí `--domain-name-label` možnost. Následující příklad vytvoří veřejnou IP adresu s názvem `myPublicIP` s názvem DNS `mypublicdns`. Vzhledem k tomu, že název DNS musí být jedinečný, zadáte svůj vlastní jedinečný název DNS:

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

Veřejná IP adresa je také prostředek nejvyšší úrovně, zobrazí se s `azure group show`.

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

Můžete prozkoumat další podrobnosti prostředků, včetně plně kvalifikovaný název domény (FQDN) subdomény, pomocí kompletní `azure network public-ip show` příkaz. Logicky byl přidělen prostředek veřejné IP adresy, ale ještě nebyly přiřazeny konkrétní adresu. Pokud chcete získat IP adresu, budete potřebovat pro vyrovnávání zatížení, které jste dosud nevytvořili.

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

## <a name="create-a-load-balancer-and-ip-pools"></a>Vytvořit nástroj pro vyrovnávání zatížení a fondy IP adres
Když vytvoříte Vyrovnávání zatížení, umožňuje distribuci přenosů mezi několika virtuálními počítači. Také poskytuje redundance, aby vaše aplikace s spuštění několika virtuálních počítačů, které reagují na požadavky uživatele v případě údržby nebo velkým zatížením. Následující příklad vytvoří nástroj pro vyrovnávání zatížení s názvem `myLoadBalancer`:

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

Naše nástroj pro vyrovnávání zatížení je docela prázdná, takže vytvoříme některé fondy IP adres. Chceme vytvořit dva fondy IP adres pro naše nástroj pro vyrovnávání zatížení, jeden pro front-endu a jeden pro back-end. Front-endu fond IP adres je veřejně viditelný. Je také umístění, do které budeme přiřadit PIP, kterou jsme vytvořili předtím. Potom používáme fond back-end jako umístění pro naše virtuální počítače pro připojení k. Tímto způsobem můžete provoz procházet skrz nástroje pro vyrovnávání zatížení pro virtuální počítače.

Nejdříve vytvoříme naše front-end fond IP adres. Následující příklad vytvoří front-end fond s názvem `myFrontEndPool`:

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

Všimněte si, jak jsme použili `--public-ip-name` přepínač předávat `myPublicIP` kterou jsme vytvořili předtím. Přiřazení veřejnou IP adresu nástroji pro vyrovnávání zatížení, můžete dosáhnout na virtuální počítače na Internetu.

V dalším kroku vytvoříme naše druhý fond IP této doby provozu našich back-end. Následující příklad vytvoří fond back-end s názvem `myBackEndPool`:

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

Vidíme úspěšnost naše nástroj pro vyrovnávání zatížení tak, že vyhledá s `azure network lb show` a prozkoumání výstup JSON:

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

## <a name="create-load-balancer-nat-rules"></a>Vytvoření pravidla NAT nástroje pro vyrovnávání zatížení
Získat provoz v našem nástroj pro vyrovnávání zatížení, je potřeba vytvořit síťová adresa pravidla překlad (NAT), která zadejte příchozí nebo odchozí akce. Můžete zadat protokol používat a pak mapování externí porty na interní porty podle potřeby. Pro naše prostředí vytvoříme některá pravidla, které umožňují SSH pomocí našich nástroj pro vyrovnávání zatížení pro naše virtuální počítače. Porty TCP 4222 a 4223 jsme nastavení pro přesměrování na TCP port 22 na našem virtuálních počítačích (které vytvoříme později). Následující příklad vytvoří pravidlo s názvem `myLoadBalancerRuleSSH1` mapovat TCP port 4222 port 22:

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

Opakujte postup pro druhý pravidla NAT pro SSH. Následující příklad vytvoří pravidlo s názvem `myLoadBalancerRuleSSH2` mapovat TCP port 4223 port 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Můžeme také pokračujte a vytvořte tak pravidlo NAT pro port TCP 80 webových přenosů, zapojování pravidlo až naše fondy IP adres. Pokud jsme spojit pravidlo k fondu IP adres, místo zapojování pravidlo naše virtuálních počítačů jednotlivě, jsme můžete přidat nebo odebrat z fondu IP adres virtuálních počítačů. Nástroje pro vyrovnávání zatížení automaticky upraví tok provozu. Následující příklad vytvoří pravidlo s názvem `myLoadBalancerRuleWeb` k mapování TCP port 80 na portu 80:

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
Stavu testů pravidelně kontroluje virtuálních počítačů, které jsou za naše pro vyrovnávání zatížení a ujistěte se, že jsou operační a reagovat na požadavky, jak jsou definovány. V opačném případě se odebere z operace zajistit, že uživatelé nejsou směrovat na ně. Můžete definovat vlastní kontroly pro kontrolu stavu, spolu s intervalech a hodnoty časového limitu. Další informace o sondy stavu najdete v tématu [nástroj pro vyrovnávání zatížení sondy](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Následující příklad vytvoří TCP stavu zjištěný pojmenované `myHealthProbe`:

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

Zde jsme zadali interval 15 sekund pro naše kontroly stavu. Jsme můžete neproběhly maximálně čtyři sondy (jedné minuty) před nástroje pro vyrovnávání zatížení domnívá, že hostitel již není funkční.

## <a name="verify-the-load-balancer"></a>Ověřte, nástroje pro vyrovnávání zatížení
Nyní se provádí konfigurace služby Vyrovnávání zatížení. Tady jsou kroky, které jste pořídili:

1. Vytvořili jste nástroj pro vyrovnávání zatížení.
2. Vytvořit fond IP front-endu a přiřazenou veřejnou IP adresu.
3. Můžete vytvořit fond back-end IP, které se mohou připojit virtuální počítače.
4. Můžete vytvořit pravidla NAT, které umožňují SSH pro virtuální počítače pro správu, společně s pravidlo, které umožní port TCP 80 pro webovou aplikaci.
5. Jste přidali test stavu na pravidelně kontrolovat, virtuální počítače. Tento test stavu zajistí, že si uživatelé pokusí o přístup virtuální počítač, který je už funguje nebo poskytování obsahu.

Pojďme si shrnout, co nástroj pro vyrovnávání zatížení vypadá jako nyní:

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

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Vytvořte síťovou kartu k použití s virtuálního počítače s Linuxem
Síťové adaptéry jsou prostřednictvím kódu programu k dispozici, protože pravidla můžete použít pro jejich použití. Také můžete mít více než jednu. V následujícím `azure network nic create` příkaz spojit síťový adaptér a fond zatížení back-end IP a přidružte ji k pravidlo NAT pro povolení komunikace SSH.

Nahraďte `#####-###-###` oddíly s vlastními ID předplatného Azure. Vaše předplatné ID je uvedeno ve výstupu `jq` při kontrole prostředků, kterou vytváříte. Můžete také zobrazit svoje ID předplatného s `azure account list`.

Následující příklad vytvoří síťový adaptér s názvem `myNic1`:

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

Prověřením přímo prostředek můžete zobrazit podrobnosti. Zkontrolujte prostředek pomocí `azure network nic show` příkaz:

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

Nyní vytvoříme druhý síťový adaptér, zapojování v do našich fondu back-end IP znovu. Toto pravidlo NAT čas druhý povolí provoz SSH. Následující příklad vytvoří síťový adaptér s názvem `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Vytvořte skupinu zabezpečení sítě a pravidla
Teď vytvoříme skupinu zabezpečení sítě a příchozích pravidel, které řídí přístup na síťový adaptér. Skupina zabezpečení sítě je použít pro síťový adaptér nebo podsíť. Můžete definovat pravidla pro řízení toku provozu do a z virtuálních počítačů. Následující příklad vytvoří skupinu zabezpečení sítě s názvem `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Umožňuje přidat příchozí pravidlo skupiny nsg umožňuje příchozí připojení na portu 22 (pro podporu SSH). Následující příklad vytvoří pravidlo s názvem `myNetworkSecurityGroupRuleSSH` umožňující TCP na portu 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Nyní Pojďme přidat příchozí pravidlo skupiny nsg umožňuje příchozí připojení na portu 80 (pro podporu webový provoz). Následující příklad vytvoří pravidlo s názvem `myNetworkSecurityGroupRuleHTTP` umožňující TCP na portu 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> Příchozí pravidlo je filtr pro příchozí síťová připojení. V tomto příkladu jsme vazbu NSG na virtuální počítače virtuální síťový adaptér, což znamená, že všechny žádosti na port 22 předána do síťového adaptéru na našem virtuálních počítačů. Toto pravidlo příchozí je o připojení k síti a není o koncový bod, který je co by bylo o v nasazení classic. Otevření portu, musí zůstat `--source-port-range` nastavena na "\*" (výchozí hodnota) pro příjem příchozích požadavků z **všechny** požaduje port. Porty jsou obvykle dynamická.
>
>

## <a name="bind-to-the-nic"></a>Vytvořit vazbu na síťový adaptér
Vazbu NSG k síťové karty. Je potřeba připojit naše síťové adaptéry s naše skupinu zabezpečení sítě. Spuštění obou příkazů, spojit i naše síťových karet:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti
Dostupnost nastaví nápovědu šíření virtuální počítače napříč domén selhání a domén upgradu. Umožňuje vytvořit sadu dostupnosti pro virtuální počítače. Následující příklad vytvoří sadu s názvem dostupnosti `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Domén selhání definovat seskupování virtuálních počítačů, které sdílejí společné přepínač zdroje a sítě power. Ve výchozím nastavení jsou oddělené virtuální počítače, které jsou nakonfigurované v rámci vaší skupiny dostupnosti v rámci až tři domény selhání. Cílem je, že problémem hardwaru v jednom z těchto domén selhání nemá vliv na každý virtuální počítač, který běží vaše aplikace. Virtuální počítače Azure automaticky distribuuje mezi doménami selhání, když umístění do nastavení dostupnosti.

Domén upgradu označují skupiny virtuálních počítačů a základní fyzický hardware, který může být restartován ve stejnou dobu. Pořadí, ve kterém se restartují upgradu domény nemusí být po sobě jdoucích během plánované údržby, ale jenom jeden upgradu po restartu najednou. Znovu Azure automaticky distribuuje virtuální počítače napříč doménami upgradu, když umístění do stránku dostupnosti.

Další informace o [Správa dostupnosti virtuálních počítačů](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Vytvořit virtuální počítače Linux
Vytvoření úložiště a síťové prostředky pro podporu přístupné z Internetu virtuálních počítačů. Nyní Pojďme vytvořit ty virtuální počítače a zabezpečit protokolem klíč SSH, který nemá heslo. V tomto případě vytvoříme vytvořit Ubuntu podle nejnovější LTS virtuálních počítačů. Nemůžeme najít informace o tomto obrázku pomocí `azure vm image list`, jak je popsáno v [hledání Image virtuálního počítače Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Jsme vybrali bitovou kopii pomocí příkazu `azure vm image list westeurope canonical | grep LTS`. V tomto případě používáme `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. V poli poslední jsme předat `latest` tak, aby v budoucnu nám vždycky získat nejnovější sestavení. (Je řetězec používáme `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Tento další krok je pro každý, kdo již vytvořen ssh veřejného a privátního klíče rsa spárujte na systému Linux nebo Mac. pomocí **ssh-keygen - t rsa -b 2048**. Pokud nemáte žádné páry klíčů certifikátů vaší `~/.ssh` adresáře, můžete je vytvořit:

* Automaticky pomocí `azure vm create --generate-ssh-keys` možnost.
* Ručně pomocí [pokyny pro vytvoření sami](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Alternativně můžete použít `--admin-password` metodu pro ověřování připojení SSH po vytvoření virtuálního počítače. Tato metoda je obvykle méně bezpečné.

Nemůžeme vytvořit virtuální počítač tak, že převedou všechny naše prostředky a informace o společně s `azure vm create` příkaz:

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

Připojením k virtuálnímu počítači okamžitě pomocí klíče SSH výchozí. Ujistěte se, zadejte odpovídající port, protože jsme se prošla pro vyrovnávání zatížení. (Pro naše první virtuální počítač, nastavíme pravidlo NAT předávání port 4222 do našich virtuálního počítače.)

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

A teď můžete použít `azure vm show myResourceGroup myVM1` příkaz zjistit, co jste vytvořili. V tomto okamžiku používáte Ubuntu virtuálních počítačů za službou Vyrovnávání zatížení v Azure, který můžete se přihlaste pouze s dvojici klíčů SSH (protože hesla jsou zakázané). Můžete nainstalovat nginx nebo httpd, nasazení webové aplikace a sledovat provoz toku prostřednictvím nástroje pro vyrovnávání zatížení do obou virtuálních počítačů.

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
Teď, který jste vytvořili na tomto prostředí, jak postupovat, pokud chcete vytvořit další vývoj prostředí se stejnými parametry nebo produkčním prostředí, který odpovídá jeho? Správce prostředků používá šablony JSON, které definují všech parametrů pro vaše prostředí. Můžete vytvořit na celé prostředí podle odkazující na tuto šablonu JSON. Můžete [vytvořit šablony JSON ručně](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo exportovat stávajícího prostředí pro vytvoření šablony JSON pro vás:

```azurecli
azure group export --name myResourceGroup
```

Tento příkaz vytvoří `myResourceGroup.json` souboru v aktuální pracovní adresář. Při vytváření prostředí z této šablony, zobrazí se výzva pro všechny názvy prostředků, včetně názvů pro nástroj pro vyrovnávání zatížení, síťová rozhraní nebo virtuální počítače. Tyto názvy v souboru šablony může vyplnit přidáním `-p` nebo `--includeParameterDefaultValue` parametru `azure group export` příkaz, který byl dříve vidět. Upravte svou šablonu JSON zadat názvy prostředků, nebo [vytvoření souboru parameters.JSON tímto kódem](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) určující názvy prostředků.

K vytvoření prostředí z vaší šablony:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Můžete chtít číst [Další informace o tom, jak nasadit ze šablon](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Další informace o tom, jak přírůstkově aktualizovat prostředí, použijte soubor parametrů a přístup k šablony z jedno umístění úložiště.

## <a name="next-steps"></a>Další postup
Nyní jste připraveni začít pracovat s více síťovými součástmi a virtuálními počítači. Tato ukázka prostředí můžete vytváří vaše aplikace pomocí základních komponent zavedená sem.
