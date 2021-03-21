---
title: 'CLI: nasazení privátního koncového bodu pro webovou aplikaci pomocí Azure CLI'
description: Naučte se používat rozhraní příkazového řádku Azure k nasazení privátního koncového bodu pro vaši webovou aplikaci.
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acebc75b579b13ebb2cfad0e18057245781165ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175273"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>Vytvoření aplikace App Service a nasazení privátního koncového bodu pomocí Azure CLI

Tento ukázkový skript vytvoří aplikaci v App Service se souvisejícími prostředky a pak nasadí privátní koncový bod.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - V tomto kurzu se vyžaduje verze rozhraní příkazového řádku Azure 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než budete moct vytvořit libovolný prostředek, musíte vytvořit skupinu prostředků, která bude hostovat webovou aplikaci, Virtual Network a další síťové součásti. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *francecentral* :

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>Vytvoří plán služby App Service.

Je potřeba vytvořit plán App Service pro hostování vaší webové aplikace.
Pomocí [AZ AppService Plan Create](/cli/azure/appservice/plan#az-appservice-plan-create)vytvořte plán App Service.
Tento příklad vytvoří plán App Service s názvem *myAppServicePlan* v umístění *francecentral* s *P1V2* SKU a pouze jedním pracovním procesem: 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Teď, když máte plán App Service, můžete nasadit webovou aplikaci.
Vytvořte webovou aplikaci pomocí [az AppService Plan Create] (/CLI/Azure/WebApp # AZ-WebApp-Create.
Tento příklad vytvoří webovou aplikaci s názvem *mySiteName* v plánu s názvem *myAppServicePlan*

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>Vytvoření virtuální sítě

Vytvořte Virtual Network pomocí [AZ Network VNet Create](/cli/azure/network/vnet). Tento příklad vytvoří výchozí Virtual Network s názvem *myVNet* s jednou podsítí s názvem *mySubnet*:

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>Konfigurace podsítě 

Chcete-li zakázat zásady sítě privátního koncového bodu, je nutné aktualizovat podsíť. Aktualizujte konfiguraci podsítě s názvem *mySubnet* pomocí [AZ Network VNet Subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>Vytvoření privátního koncového bodu

Vytvořte privátní koncový bod pro webovou aplikaci pomocí [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint). Tento příklad vytvoří privátní koncový bod s názvem *myPrivateEndpoint* ve virtuální síti *MyVNet* v podsíti *MySubnet* s připojením s názvem *myConnectionName* a ID prostředku moje webové aplikace/Subscriptions/SubscriptionId/resourceGroups/myResourceGroup/Providers/Microsoft.Web/Sites/MyWebApp. parametr Group je *pracoviště* pro typ webové aplikace. 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>Konfigurace privátní zóny

Na konci musíte vytvořit privátní zónu DNS s názvem *privatelink.azurewebsites.NET* propojenou s virtuální sítí, abyste mohli PŘELOŽIT název DNS webové aplikace.


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>Další kroky

- Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).
- Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../samples-cli.md).
