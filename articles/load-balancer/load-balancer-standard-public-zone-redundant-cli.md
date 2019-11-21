---
title: Load balance zone-redundant VMs using Azure CLI
titleSuffix: Azure Load Balancer
description: Learn how to create a public Standard Load Balancer with zone redundant frontend using Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: allensu
ms.openlocfilehash: af327f751a0af67b6d17330dbaeb717df8660bfd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225273"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Load balance VMs across all availability zones using Azure CLI

This article steps through creating a public [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) with a zone-redundant frontend to achieve zone-redundancy without dependency on multiple DNS records. A single front-end IP address is automatically zone-redundant.  Using a zone redundant frontend for your load balancer, with a single IP address you can now reach any VM in a virtual network within a region that is across all Availability Zones. Zóny dostupnosti se používají k ochraně aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra.

Další informace o používání zón dostupnosti s Load Balancerem úrovně Standard najdete v tématu o [Load Balanceru úrovně Standard a zónách dostupnosti](load-balancer-standard-availability-zones.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

If you choose to install and use the CLI locally, this tutorial requires that you are running Azure CLI version 2.0.17 or higher.  Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Support for Availability Zones is available for select Azure resources and regions, and VM size families. For more information on how to get started, and which Azure resources, regions, and VM size families you can try availability zones with, see [Overview of Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

The following example creates a resource group named *myResourceGroupSLB* in the *westeurope* location:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Create a zone redundant public IP Standard
Pokud chcete mít k aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. A zone-redundant front-end is served by all availability zones in a region simultaneously. Create a zone redundant public IP address with [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). When you create a Standard Public  IP address, it is zone redundant by default.

The following example creates a zone redundant public IP address named *myPublicIP* in the *myResourceGroupLoadBalancer* resource group.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Create Azure Standard Load Balancer
Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:
- Front-endový fond IP adres, který přijímá příchozí síťový provoz do nástroje pro vyrovnávání zatížení.
- Back-endový fond IP adres, kam front-endový fond odesílá síťový provoz s vyrovnáváním zatížení.
- Sonda stavu, která určuje stav back-endových instancí virtuálních počítačů.
- Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-the-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení
Create a Standard load balancer with [az network lb create](/cli/azure/network/lb#az-network-lb-create). The following example creates a load balancer named *myLoadBalancer* and assigns the *myPublicIP* address to the front-end IP configuration.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Create health probe on port 80

Test stavu kontroluje všechny instance virtuálních počítačů a ověřuje, že mohou posílat síťový provoz. Instance virtuálního počítače, u níž se kontroly testu nezdaří, se odebere z nástroje pro vyrovnávání zatížení do té doby, než znovu přejde do režimu online a kontrola testu určí, že je v pořádku. Create a health probe with az network lb probe create to monitor the health of the virtual machines. Sondu stavu protokolu TCP vytvoříte pomocí příkazu [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). Následující příklad vytvoří sondu stavu *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Create load balancer rule for port 80
Pravidlo nástroje pro vyrovnávání zatížení definuje konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Pomocí příkazu *myLoadBalancerRuleWeb* vytvořte pravidlo nástroje pro vyrovnávání zatížení [myLoadBalancerRuleWeb](/cli/azure/network/lb/rule#az-network-lb-rule-create) pro naslouchání na portu 80 ve front-endovém fondu *myFrontEndPool* a odesílání síťového provozu s vyrovnáváním zatížení do back-endového fondu adres *myBackEndPool* rovněž na portu 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě
Než nasadíte několik virtuálních počítačů a budete moci otestovat svůj nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Create a virtual network named *myVnet* with a subnet named *mySubnet* in the myResourceGroup using [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Create network security group named *myNetworkSecurityGroup* to define inbound connections to your virtual network  with [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Create a network security group rule named *myNetworkSecurityGroupRule* for port 80 with [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
--protocol tcp \
--direction inbound \
--source-address-prefix '*' \
--source-port-range '*' \
--destination-address-prefix '*' \
--destination-port-range 80 \
--access allow \
--priority 200
```
### <a name="create-nics"></a>Vytvoření síťových rozhraní
Create three virtual NICs with [az network nic create](/cli/azure/network/nic#az-network-nic-create) and associate them with the Public IP address and the network security group. The following example creates six virtual NICs. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích). Kdykoli můžete vytvořit další virtuální síťové karty a virtuální počítače a přidat je do nástroje pro vyrovnávání zatížení:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupSLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Vytvoření serverů back-end
In this example, you create three virtual machines located in zone 1, zone 2, and zone 3 to be used as backend servers for the load balancer. You also install NGINX on the virtual machines to verify that the load balancer was successfully created.

### <a name="create-cloud-init-config"></a>Vytvoření konfigurace cloud-init

K instalaci serveru NGINX a spuštění aplikace Hello World v Node.js na virtuálním počítači s Linuxem můžete použít konfigurační soubor cloud-init. V aktuálním prostředí vytvořte soubor cloud-init.txt a zkopírujte následující konfiguraci a vložte ji do prostředí. Ujistěte se, že správně kopírujete celý soubor cloud-init, zejména první řádek:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-the-zonal-virtual-machines"></a>Create the zonal virtual machines
Create the VMs with [az vm create](/cli/azure/vm#az-vm-create) in zone 1, zone 2, and zone 3. The following example creates a VM in each zone and generates SSH keys if they do not already exist:

Create a VM in each zone (zone 1, zone2, and zone 3) of the *westeurope* location.

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

Get the public IP address of the load balancer using [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Veřejnou IP adresu pak můžete zadat do webového prohlížeče. Nezapomeňte, že příprava virtuálních počítačů několik minut trvá, a až pak do nich začne nástroj pro vyrovnávání zatížení distribuovat provoz. Zobrazí se aplikace, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz, jako v následujícím příkladu:

![Spuštěná aplikace Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

To see the load balancer distribute traffic across VMs in all three availability zones running your app, you can stop a VM in a particular zone and refresh your browser.

## <a name="next-steps"></a>Další kroky
- Další informace o [Load Balanceru úrovně Standard](./load-balancer-standard-overview.md)



