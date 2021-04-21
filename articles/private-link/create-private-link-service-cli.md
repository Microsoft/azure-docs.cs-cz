---
title: Rychlý Start – vytvoření služby privátního propojení Azure pomocí Azure CLI
description: V tomto rychlém startu se dozvíte, jak vytvořit službu privátního propojení Azure pomocí Azure CLI.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: c8e32a56148326104c3514b8a2fdb5d6bbd3f00a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778458"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>Rychlý Start: vytvoření služby privátního propojení pomocí Azure CLI

Začněte vytvářet službu privátního propojení, která odkazuje na vaši službu.  Poskytněte soukromému odkazu přístup k vaší službě nebo prostředku nasazenému za službou Azure Standard Load Balancer.  Uživatelé vaší služby mají privátní přístup ze své virtuální sítě.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- V tomto rychlém startu se vyžaduje verze Azure CLI 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az_group_create):

* Název **CreatePrivLinkService-RG**. 
* V umístění **eastus** .

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>Vytvořte interní nástroj pro vyrovnávání zatížení.

V této části vytvoříte virtuální síť a interní Azure Load Balancer.

### <a name="virtual-network"></a>Virtuální síť

V této části vytvoříte virtuální síť a podsíť pro hostování nástroje pro vyrovnávání zatížení, který přistupuje ke službě privátního propojení.

Vytvořte virtuální síť pomocí [AZ Network VNet Create](/cli/azure/network/vnet#az_network_vnet_create):

* S názvem **myVNet**.
* Předpona adresy **10.1.0.0/16**
* Podsíť s názvem **mySubnet**.
* Předpona podsítě **10.1.0.0/24**.
* Ve skupině prostředků **CreatePrivLinkService-RG** .
* Umístění **eastus2**
* Zakažte v podsíti zásadu sítě pro službu privátního propojení.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Chcete-li aktualizovat podsíť, aby se zakázaly síťové zásady služby privátního propojení, použijte příkaz [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Vytvořit službu Load Balancer úrovně Standard

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:

  * Fond IP adres front-endu, který přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
  * Back-end fond IP adres, ve kterém front-endu odesílá síťový provoz s vyrovnáváním zatížení.
  * Sonda stavu, která určuje stav instancí virtuálních počítačů back-endu.
  * Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-the-load-balancer-resource"></a>Vytvoření prostředku nástroje pro vyrovnávání zatížení

Vytvoření veřejného nástroje pro vyrovnávání zatížení pomocí [AZ Network](/cli/azure/network/lb#az_network_lb_create)diskont Create:

* S názvem **myLoadBalancer**.
* Front-endového fondu s názvem **myFrontEnd**.
* Back-end fond s názvem **myBackEndPool**.
* Přidruženo k virtuální síti **myVNet**.
* Přidruženo k **mySubnet** podsíti back-endu.

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních počítačů, aby bylo zajištěno, že budou moci odesílat síťový provoz. 

Z nástroje pro vyrovnávání zatížení se odebere virtuální počítač s neúspěšnou kontrolou testu. Po vyřešení chyby se virtuální počítač do nástroje pro vyrovnávání zatížení přidá zpátky.

Vytvořte sondu stavu pomocí [AZ Network disprobe test Create](/cli/azure/network/lb/probe#az_network_lb_probe_create):

* Monitoruje stav virtuálních počítačů.
* S názvem **myHealthProbe**.
* Protokol **TCP**.
* **Port monitorování 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje:

* Konfigurace IP adresy front-endu pro příchozí provoz.
* Fond IP adres back-endu pro příjem provozu.
* Požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [AZ Network diskont Rule Create](/cli/azure/network/lb/rule#az_network_lb_rule_create):

* Pojmenovaný **myHTTPRule**
* Naslouchat na **portu 80** ve fondu front-endu **myFrontEnd**.
* Odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres **myBackEndPool** pomocí **portu 80**. 
* Pomocí **myHealthProbe** sondy stavu.
* Protokol **TCP**.
* Časový limit nečinnosti **15 minut**.
* Povolte resetování protokolu TCP.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

## <a name="create-a-private-link-service"></a>Vytvoření služby privátního propojení

V této části vytvoříte službu privátního propojení, která používá Azure Load Balancer vytvořené v předchozím kroku.

Vytvoření služby privátního propojení pomocí konfigurace IP adresy standardního front-endu pro vyrovnávání zatížení pomocí metody [AZ Network Private-Link-Service Create](/cli/azure/network/private-link-service#az_network_private_link_service_create):

* S názvem **myPrivateLinkService**.
* Ve virtuální síti **myVNet**.
* Přidruženo ke službě Load Balancer úrovně Standard **myLoadBalancer** a front-endu Configuration **myFrontEnd**.
* V umístění **eastus2** .
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

Vaše služba privátního propojení se vytvoří a může přijímat provoz. Pokud chcete zobrazit přenosové toky, nakonfigurujte svoji aplikaci za vaším standardním nástrojem pro vyrovnávání zatížení.


## <a name="create-private-endpoint"></a>Vytvořit privátní koncový bod

V této části namapujete službu privátního propojení na soukromý koncový bod. Virtuální síť obsahuje privátní koncový bod pro službu privátního propojení. Tato virtuální síť obsahuje prostředky, které budou mít přístup ke službě privátního propojení.

### <a name="create-private-endpoint-virtual-network"></a>Vytvořit virtuální síť privátního koncového bodu

Vytvořte virtuální síť pomocí [AZ Network VNet Create](/cli/azure/network/vnet#az_network_vnet_create):

* S názvem **myVNetPE**.
* Předpona adresy **11.1.0.0/16**
* Podsíť s názvem **mySubnetPE**.
* Předpona podsítě **11.1.0.0/24**.
* Ve skupině prostředků **CreatePrivLinkService-RG** .
* Umístění **eastus2**

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Pokud chcete aktualizovat podsíť, aby se zakázaly zásady sítě privátního koncového bodu, použijte [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Vytvoření koncového bodu a připojení

* Chcete-li získat ID prostředku služby privátního propojení, použijte příkaz [AZ Network Private-Link-Service show](/cli/azure/network/private-link-service#az_network_private_link_service_show) . Příkaz umístí ID prostředku do proměnné pro pozdější použití.

* Pomocí [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) vytvořte privátní koncový bod ve virtuální síti, kterou jste předtím vytvořili.

* S názvem **MyPrivateEndpoint**.
* Ve skupině prostředků **CreatePrivLinkService-RG** .
* Název připojení **myPEconnectiontoPLS**.
* Umístění **eastus2**
* Ve virtuální síti **myVNetPE** a v podsíti **mySubnetPE**.

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte pomocí příkazu [AZ Group Delete](/cli/azure/group#az_group_delete) skupinu prostředků, službu privátního propojení, nástroj pro vyrovnávání zatížení a všechny související prostředky.

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu:

* Vytvořila se virtuální síť a interní Azure Load Balancer.
* Vytvořili jste službu privátního propojení.

Pokud chcete získat další informace o privátním koncovém bodu Azure, přejděte na:
> [!div class="nextstepaction"]
> [Rychlý Start: Vytvoření privátního koncového bodu pomocí Azure CLI](create-private-endpoint-cli.md)
