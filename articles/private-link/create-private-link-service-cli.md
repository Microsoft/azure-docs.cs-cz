---
title: Vytvoření služby privátního propojení Azure pomocí Azure CLI
description: Naučte se vytvořit službu privátního propojení Azure pomocí Azure CLI.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: cfffafaab2e2d4ef6b165ef03beb827342c94608
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "96018048"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Vytvoření služby privátního propojení pomocí Azure CLI
V tomto článku se dozvíte, jak vytvořit službu privátního propojení v Azure pomocí rozhraní příkazového řádku Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje nejnovější verzi rozhraní příkazového řádku Azure CLI. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-private-link-service"></a>Vytvoření služby privátního propojení
### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než budete moct vytvořit virtuální síť, musíte vytvořit skupinu prostředků, která bude hostovat virtuální síť. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *westcentralus* :

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Tento příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* s jednou podsítí s názvem *mySubnet*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Vytvoření podsítě
Vytvořte podsíť pro virtuální síť pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Tento příklad vytvoří podsíť s názvem *mySubnet* ve virtuální síti *myVirtualNetwork* :

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Vytvoření interní Load Balancer 
Vytvořte interní nástroj pro vyrovnávání zatížení pomocí [AZ Network](/cli/azure/network/lb#az-network-lb-create)diskont Create. Tento příklad vytvoří interní nástroj pro vyrovnávání zatížení s názvem *myILB* ve skupině prostředků s názvem *myResourceGroup*. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Vytvoření sondy stavu nástroje pro vyrovnávání zatížení

Sonda stavu kontroluje všechny instance virtuálních počítačů a ověřuje, že můžou přijímat síťový provoz. Instance virtuálního počítače, u níž se kontroly testu nezdaří, se odebere z nástroje pro vyrovnávání zatížení do té doby, než znovu přejde do režimu online a kontrola testu určí, že je v pořádku. Pomocí příkazu [az network lb probe create](/cli/azure/network/lb/probe?view=azure-cli-latest) vytvořte sondu stavu pro monitorování stavu virtuálních počítačů. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Pomocí příkazu [az network lb rule create](/cli/azure/network/lb/rule?view=azure-cli-latest) vytvořte pravidlo nástroje pro vyrovnávání zatížení *myHTTPRule* pro naslouchání na portu 80 ve front-endovém fondu *myFrontEnd* a odesílání síťového provozu s vyrovnáváním zatížení do back-endového fondu adres *myBackEndPool*, a to taky na portu 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>Vytvoření serverů back-end

V tomto příkladu nepokrýváme vytváření virtuálních počítačů. Můžete postupovat podle kroků v části [rychlý Start: vytvoření interního nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) rozhraní příkazového řádku Azure a vytvoření dvou virtuálních počítačů, které se použijí jako servery back-end pro nástroj pro vyrovnávání zatížení. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Zakázat zásady sítě služby privátního propojení v podsíti 
Služba privátního propojení vyžaduje IP adresu z libovolné podsítě podle vašeho výběru v rámci virtuální sítě. V současné době nepodporujeme pro tyto IP adresy zásady sítě.  Proto je nutné zakázat zásady sítě v podsíti. Aktualizujte podsíť, aby se zakázaly zásady sítě služby privátního propojení pomocí [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service-using-standard-load-balancer"></a>Vytvoření služby privátního propojení pomocí Standard Load Balancer 
 
Vytvoření služby privátního propojení pomocí Standard Load Balancer konfigurace IP adresy front [-Endu pomocí AZ Network Private-Link-Service Create](/cli/azure/network/private-link-service#az-network-private-link-service-create). Tento příklad vytvoří službu privátního propojení s názvem *myPLS* pomocí Standard Load Balancer s názvem *myLoadBalancer* ve skupině prostředků s názvem *myResourceGroup*. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
Po vytvoření si poznamenejte ID služby privátního propojení. Později budete potřebovat, abyste požádali o připojení k této službě.  
 
V této fázi je vaše služba privátního propojení úspěšně vytvořená a připravená na příjem provozu. Všimněte si, že výše uvedený příklad je pouze ukázka vytvoření služby privátního propojení pomocí Azure CLI.  Nenakonfigurovali jsme back-end fondy nástroje pro vyrovnávání zatížení ani žádné aplikace na back-end fondu, aby naslouchaly provozu. Pokud chcete zobrazit komplexní toky přenosů dat, důrazně doporučujeme nakonfigurovat aplikaci za vaším Standard Load Balancer.  
 
V dalším kroku ukážeme, jak namapovat tuto službu na privátní koncový bod v jiné virtuální síti pomocí Azure CLI. V tomto příkladu je tento příklad omezený na vytvoření privátního koncového bodu a připojení ke službě privátního propojení vytvořené výše pomocí Azure CLI. Kromě toho můžete vytvořit virtuální počítače ve virtuální síti pro odesílání a příjem provozu do privátního koncového bodu.        
 
## <a name="private-endpoints"></a>Soukromé koncové body

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě 
Vytvořte virtuální síť pomocí [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create). Tento příklad vytvoří virtuální síť s názvem  *myPEVNet*   ve skupině prostředků s názvem *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Vytvoření podsítě 
Vytvořte podsíť ve virtuální síti pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Tento příklad vytvoří podsíť s názvem  *mySubnet*   ve virtuální síti s názvem *myPEVnet* ve skupině prostředků s názvem *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Zakázat zásady sítě privátního koncového bodu v podsíti 
Privátní koncový bod se dá vytvořit v libovolné podsíti dle vašeho výběru v rámci virtuální sítě. V současné době nepodporujeme zásady sítě u privátních koncových bodů.  Proto je nutné zakázat zásady sítě v podsíti. Aktualizujte podsíť a zakažte zásady sítě privátního koncového bodu pomocí [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Vytvoření privátního koncového bodu a připojení ke službě privátního propojení 
Vytvořte privátní koncový bod pro využívání služby privátního propojení vytvořené výše ve vaší virtuální síti:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
Můžete získat *privátní připojení-Resource-ID* `az network private-link-service show` ve službě privátního propojení. ID bude vypadat takto:   
/subscriptions/subID/resourceGroups/*ResourceGroupName*/Providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Zobrazit připojení služby privátního propojení 
 
V tématu žádosti o připojení ve službě privátního propojení použijte příkaz [AZ Network Private-Link-Service show](/cli/azure/network/private-link-service#az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Další kroky
- Další informace o [službě Azure Private Link](private-link-service-overview.md)
