---
title: Vytvoření služby Azure Private Link pomocí azure cli
description: Zjistěte, jak vytvořit službu Azure Private Link pomocí azure cli
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 6e6148d305af26f7933567ae58023d2ba73263eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350242"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Vytvoření služby Private Link pomocí azure cli
Tento článek ukazuje, jak vytvořit službu private link v Azure pomocí Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a použít Azure CLI místně místo, tento rychlý start vyžaduje, abyste použili nejnovější verzi příkazového příkazového příkazu Azure. Chcete-li najít nainstalovanou verzi, spusťte program `az --version`. Informace o instalaci nebo upgradu [najdete v tématu Instalace příkazového příkazového](/cli/azure/install-azure-cli) příkazu Konzumu Azure.
## <a name="create-a-private-link-service"></a>Vytvoření služby privátního propojení
### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením virtuální sítě je třeba vytvořit skupinu prostředků pro hostování ve virtuální síti. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *westcentralus:*

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Tento příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* s jednou podsítí s názvem *mySubnet*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Vytvoření podsítě
Vytvořte podsíť pro virtuální síť s [vytvořením podsítě sítě AZ](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Tento příklad vytvoří podsíť s názvem *mySubnet* ve virtuální síti *myVirtualNetwork:*

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Vytvoření interního systému vyrovnávání zatížení 
Vytvořte interní systém vyrovnávání zatížení s [vytvořením az network lb](/cli/azure/network/lb#az-network-lb-create). Tento příklad vytvoří interní vyrovnávání zatížení s názvem *myILB* ve skupině prostředků s názvem *myResourceGroup*. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Vytvoření sondy stavu nástroje pro vyrovnávání zatížení

Sonda stavu kontroluje všechny instance virtuálních počítačů a ověřuje, že můžou přijímat síťový provoz. Instance virtuálního počítače, u níž se kontroly testu nezdaří, se odebere z nástroje pro vyrovnávání zatížení do té doby, než znovu přejde do režimu online a kontrola testu určí, že je v pořádku. Pomocí příkazu [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) vytvořte sondu stavu pro monitorování stavu virtuálních počítačů. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Pomocí příkazu [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) vytvořte pravidlo nástroje pro vyrovnávání zatížení *myHTTPRule* pro naslouchání na portu 80 ve front-endovém fondu *myFrontEnd* a odesílání síťového provozu s vyrovnáváním zatížení do back-endového fondu adres *myBackEndPool*, a to taky na portu 80. 

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

V tomto příkladu nepokrýváme vytváření virtuálních strojů. Můžete postupovat podle kroků v [části Vytvoření interního nástroje pro vyrovnávání zatížení pro virtuální počítače vyrovnávání zatížení pomocí Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) k vytvoření dvou virtuálních počítačů, které se použijí jako back-endové servery pro nástroj pro vyrovnávání zatížení. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Zakázání zásad sítě služby Private Link v podsíti 
Služba Private Link vyžaduje ip adresu z libovolné podsítě podle vašeho výběru ve virtuální síti. V současné době nepodporujeme zásady sítě na těchto IP programech.  Proto musíme zakázat zásady sítě v podsíti. Aktualizujte podsíť a zakažte zásady sítě privátního propojení aktualizací [sítě AZ](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Vytvoření služby privátního propojení  
 
Vytvořte službu Private Link pomocí konfigurace front-endip nástroje Standard Load Balancer s [vytvořením privátní služby az network](/cli/azure/network/private-link-service#az-network-private-link-service-create). Tento příklad vytvoří službu Private Link s názvem *myPLS* pomocí standardního vykladače zatížení s názvem *myLoadBalancer* ve skupině prostředků s názvem *myResourceGroup*. 
 
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
Po vytvoření si poznamenejte ID služby Private Link Service. Budete to potřebovat později pro vyžádání připojení k této službě.  
 
V této fázi je služba Private Link úspěšně vytvořena a je připravena přijímat přenosy. Všimněte si, že výše uvedený příklad je pouze k prokázání vytváření služby private link pomocí Azure CLI.  Ještě jsme nenakonfigurovali back-endové fondy vyrovnávání zatížení ani žádnou aplikaci v back-endových fondech, aby naslouchaly provozu. Pokud chcete zobrazit toky přenosů od konce, důrazně doporučujeme nakonfigurovat aplikaci za standardním vyrovnáváním zatížení.  
 
Dále ukážeme, jak mapovat tuto službu na privátní koncový bod v různých virtuálních sítích pomocí rozhraní příkazového příkazového příkazu Azure. Opět příklad je omezen na vytvoření privátní koncový bod a připojení k privátní link služby vytvořené výše pomocí Azure CLI. Kromě toho můžete vytvořit virtuální počítače ve virtuální síti pro odesílání a přijímání přenosů do soukromého koncového bodu.        
 
## <a name="private-endpoints"></a>Soukromé koncové body

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě 
Vytvořte virtuální síť s [vytvořením virtuální sítě AZ](/cli/azure/network/vnet#az-network-vnet-create). Tento příklad vytvoří virtuální síť s názvem *myPEVNet* ve skupině prostředků s názvem *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Vytvoření podsítě 
Vytvořte podsíť ve virtuální síti s [vytvořením podsítě sítě AZ](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Tento příklad vytvoří podsíť s názvem *mySubnet* ve virtuální síti s názvem *myPEVnet* ve skupině prostředků s názvem *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Zakázání zásad sítě privátního koncového bodu v podsíti 
Soukromý koncový bod lze vytvořit v libovolné podsíti podle vašeho výběru v rámci virtuální sítě. V současné době nepodporujeme zásady sítě na soukromé koncové body.  Proto musíme zakázat zásady sítě v podsíti. Aktualizujte podsíť a zakažte zásady privátní sítě koncových bodů aktualizací [sítě AZ](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Vytvoření privátního koncového bodu a připojení ke službě privátního propojení 
Vytvořte privátní koncový bod pro využívání služby Private Link vytvořené výše ve vaší virtuální síti:
  
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
Můžete získat *privátní připojení prostředku id* se `az network private-link-service show` službou Private Link. ID bude vypadat takto:   
/subscriptions/subID/resourceGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Zobrazit připojení služby Private Link 
 
Zobrazit požadavky na připojení ve službě Private Link pomocí [show soukromé služby sítě az](/cli/azure/network/private-link-service#az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Další kroky
- Další informace o [službě Azure Private Link](private-link-service-overview.md)
 
