---
title: 'Vytvoření brány Azure VPN založené na trasách: rozhraní příkazového řádku | Dokumentace Microsoftu'
description: Rychle se naučíte, jak vytvořit VPN Gateway pomocí rozhraní příkazového řádku
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: b8ca2d74012418dbd8ca9e878f133a250ebb5991
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465096"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Vytvoření pomocí rozhraní příkazového řádku brány VPN založené na trasách

Tento článek vám pomůže rychle vytvořit založené na směrování Azure VPN gateway pomocí Azure CLI. Brány VPN se používá při vytváření připojení VPN k místní síti. Bránu sítě VPN můžete použít také k propojení virtuálních sítí.

Kroky v tomto článku se vytvoří virtuální síť, podsíť, podsítě brány a bránu VPN založenou na směrování (brány virtuální sítě). Bránu virtuální sítě může trvat 45 minut nebo více otázek a vytvořit. Po dokončení vytvoření brány můžete pak vytvořit připojení. Tyto kroky vyžadují předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0.4 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Abyste vytvořili skupinu prostředků pomocí [vytvořit skupiny az](/cli/azure/group#az_group_create) příkazu. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 


```azurecli-interactive 
az group create --name TestRG1 --location eastus
```

## <a name="vnet"></a>Vytvoření virtuální sítě

Vytvoření virtuální sítě pomocí [az network vnet vytvořit](/cli/azure/network/vnet#az_network_vnet_create) příkazu. Následující příklad vytvoří virtuální síť s názvem **ze sítě VNet1** v **EastUS** umístění:

```azurecli-interactive 
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="gwsubnet"></a>Přidání podsítě brány

Podsíť brány obsahuje vyhrazené IP adresy, které používají služby brány virtuální sítě. Chcete-li přidat podsíť brány použijte následující příklady:

```azurepowershell-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="PublicIP"></a>Vyžádejte si veřejnou IP adresu

Brána VPN musí mít dynamicky přidělené veřejné IP adresy. Veřejná IP adresa přidělí k bráně VPN, který vytvoříte pro vaše virtuální síť. Vyžádejte si veřejnou IP adresu pomocí příkazu v následujícím příkladu:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="CreateGateway"></a>Vytvoření brány VPN

Vytvořte bránu VPN pomocí příkazu [az network vnet-gateway create](/cli/azure/group#az_network_vnet_gateway_create).

Pokud spustíte tento příkaz pomocí `--no-wait` parametr, se nezobrazí žádná zpětná vazba ani výstup. `--no-wait` Parametr umožňuje bránu vytvořit na pozadí. Neznamená to, je vytvořená služba VPN gateway okamžitě.

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

## <a name="viewgw"></a>Zobrazení brány sítě VPN

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Odpověď vypadá nějak takto:

```
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

Chcete-li zobrazit přiřazenou k vaší bráně veřejnou IP adresu, použijte následující příklad:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

Hodnota přidružená k **ipAddress** pole je veřejnou IP adresu vaší brány VPN.

Příklad odpovědi:

```
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

Pokud už nepotřebujete prostředky, které jste vytvořili, použijte [odstranění skupiny az](/cli/azure/group#az_group_delete) odstranit skupinu prostředků. Tím odstraníte skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Další postup

Po dokončení vytváření brány můžete vytvořit připojení mezi virtuální sítí a jiné virtuální síti. Nebo vytvořte připojení mezi virtuální sítí a místního umístění.

> [!div class="nextstepaction"]
> [Vytvoření připojení site-to-site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Vytvoření připojení point-to-site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Vytvoření připojení k jiné virtuální síti](vpn-gateway-vnet-vnet-rm-ps.md)