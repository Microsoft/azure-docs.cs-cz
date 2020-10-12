---
title: 'Vytvoření VPN Gateway Azure založeného na trasách: CLI'
description: Pomocí Azure CLI můžete rychle vytvořit bránu Azure VPN Gateway založenou na trasách pro připojení VPN k místní síti nebo k propojení virtuálních sítí.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: e43f324a8c32615eded782e75291b575852c4382
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89393688"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Vytvoření brány sítě VPN založené na trasách pomocí rozhraní příkazového řádku

Tento článek vám pomůže rychle vytvořit bránu Azure VPN Gateway založenou na směrování pomocí Azure CLI. Brána sítě VPN se používá při vytváření připojení VPN k místní síti. K připojení virtuální sítě můžete použít taky bránu VPN.

Kroky v tomto článku vytvoří virtuální síť, podsíť, podsíť brány a bránu VPN založenou na směrování (bránu virtuální sítě). Brána virtuální sítě může vytvořit několik než 45 minut. Po dokončení vytváření brány můžete vytvořit připojení. Tyto kroky vyžadují předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0.4 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí příkazu [AZ Network VNet Create](/cli/azure/network/vnet) . Následující příklad vytvoří virtuální síť s názvem **VNet1** v umístění **EastUS** :

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

Podsíť brány obsahuje rezervované IP adresy, které používají služby brány virtuální sítě. K přidání podsítě brány použijte následující příklady:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Vyžádání veřejné IP adresy

Brána sítě VPN musí mít dynamicky přidělenou veřejnou IP adresu. Veřejná IP adresa se přidělí k bráně VPN, kterou vytvoříte pro virtuální síť. Pro vyžádání veřejné IP adresy použijte následující příklad:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>Vytvoření brány sítě VPN

Vytvořte bránu VPN pomocí příkazu [az network vnet-gateway create](/cli/azure/group).

Pokud tento příkaz spustíte pomocí `--no-wait` parametru, nezobrazí se žádná zpětná vazba ani výstup. `--no-wait`Parametr umožňuje vytvořit bránu na pozadí. Neznamená to, že se Brána VPN vytvoří hned.

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

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Zobrazit bránu VPN

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Odpověď vypadá nějak takto:

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

### <a name="view-the-public-ip-address"></a>Zobrazení veřejné IP adresy

Pokud chcete zobrazit veřejnou IP adresu přiřazenou k bráně, použijte následující příklad:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

Hodnota přidružená k poli **ipAddress** je veřejná IP adresa vaší brány VPN.

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

Když už nepotřebujete prostředky, které jste vytvořili, odstraňte skupinu prostředků pomocí [AZ Group Delete](/cli/azure/group) . Tím odstraníte skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Další kroky

Po vytvoření brány můžete vytvořit připojení mezi vaší virtuální sítí a jinou virtuální sítí. Nebo vytvořte připojení mezi vaší virtuální sítí a místním umístěním.

> [!div class="nextstepaction"]
> [Vytvoření připojení typu Site-to-Site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Vytvoření připojení typu Point-to-Site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Vytvoření připojení k jiné virtuální síti](vpn-gateway-vnet-vnet-rm-ps.md)
