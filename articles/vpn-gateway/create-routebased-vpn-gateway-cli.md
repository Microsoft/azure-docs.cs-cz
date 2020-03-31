---
title: 'Vytvoření brány Azure VPN založené na trase: CLI'
description: Rychle se dozvíte, jak vytvořit bránu VPN pomocí cli
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: 121790fce220874babedf67cd72471caa7e92ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241089"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Vytvoření brány VPN založené na trasách pomocí cli

Tento článek vám pomůže rychle vytvořit bránu Azure VPN založenou na trasách pomocí azure cli. Brána VPN se používá při vytváření připojení VPN k místní síti. K připojení virtuálních sítí můžete taky použít bránu VPN.

Kroky v tomto článku vytvoří virtuální síť, podsíť, podsíť brány a bránu VPN založenou na trasách (bránu virtuální sítě). Vytvoření brány virtuální sítě může trvat 45 minut nebo déle. Po dokončení vytvoření brány pak můžete vytvořit připojení. Tyto kroky vyžadují předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0.4 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [vytvořit skupinu az.](/cli/azure/group) Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí příkazu [vytvořit virtuální síť az.](/cli/azure/network/vnet) Následující příklad vytvoří virtuální síť s názvem **VNet1** v umístění **EastUS:**

```azurecli-interactive
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Přidání podsítě brány

Podsíť brány obsahuje vyhrazené IP adresy, které používají služby brány virtuální sítě. K přidání podsítě brány použijte následující příklady:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Vyžádání veřejné IP adresy

Brána VPN musí mít dynamicky přidělenou veřejnou IP adresu. Veřejná IP adresa bude přidělena bráně VPN, kterou vytvoříte pro virtuální síť. Pomocí následujícího příkladu můžete požádat o veřejnou IP adresu:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>Vytvoření brány VPN

Vytvořte bránu VPN pomocí příkazu [az network vnet-gateway create](/cli/azure/group).

Pokud spustíte tento příkaz `--no-wait` pomocí parametru, nevidíte žádnou zpětnou vazbu ani výstup. Parametr `--no-wait` umožňuje vytvoření brány na pozadí. To neznamená, že brána VPN je vytvořena okamžitě.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

Vytvoření brány VPN může trvat 45 minut nebo déle.

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Zobrazení brány VPN

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Odpověď vypadá podobně jako tato:

```output
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>Zobrazit veřejnou IP adresu

Chcete-li zobrazit veřejnou IP adresu přiřazenou vaší bráně, použijte následující příklad:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

Hodnota přidružená k poli **IPAddress** je veřejná IP adresa brány VPN.

Příklad odpovědi:

```output
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete prostředky, které jste vytvořili, odstraňte skupinu prostředků pomocí [odstranění skupiny az.](/cli/azure/group) Tím odstraníte skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Další kroky

Po dokončení vytváření brány můžete vytvořit připojení mezi virtuální sítí a jinou virtuální sítí. Nebo vytvořte připojení mezi virtuální sítí a místním umístěním.

> [!div class="nextstepaction"]
> [Vytvoření připojení mezi lokalitami](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Vytvoření připojení typu Point-to-Site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Vytvoření připojení k jiné virtuální síti](vpn-gateway-vnet-vnet-rm-ps.md)
