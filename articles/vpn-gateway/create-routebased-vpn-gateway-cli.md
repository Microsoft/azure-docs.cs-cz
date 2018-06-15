---
title: 'Vytvoření brány Azure VPN založené na trasách: rozhraní příkazového řádku | Microsoft Docs'
description: Rychle zjistěte, jak vytvořit bránu sítě VPN pomocí rozhraní příkazového řádku
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: d0f4e292c6f5a2725b4a9efe91e78c6e634ea64e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30840901"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Vytváření pomocí rozhraní příkazového řádku brány VPN založené na směrování

Tento článek vám pomůže rychle vytvořit pomocí rozhraní příkazového řádku Azure brány Azure VPN založené na trasách. Brána sítě VPN se používá při vytváření připojení VPN k síti na pracovišti. Můžete také použít bránu VPN propojení virtuálních sítí.

Kroky v tomto článku vytvořte virtuální síť, podsíť, podsíť brány a bránu VPN založené na směrování (brány virtuální sítě). Brána virtuální sítě může trvat 45 minut nebo déle vytvořit. Po dokončení vytvoření brány můžete vytvořit připojení. Tyto kroky vyžadují předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, v tomto článku vyžaduje, že používáte Azure CLI verze verze 2.0.4 nebo novější. Chcete-li najít nainstalovanou verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvoření skupiny prostředků pomocí [vytvořit skupinu az](/cli/azure/group#az_group_create) příkaz. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 


```azurecli-interactive 
az group create --name TestRG1 --location eastus
```

## <a name="vnet"></a>Vytvoření virtuální sítě

Vytvoření virtuální sítě pomocí [vytvoření sítě vnet az](/cli/azure/network/vnet#az_network_vnet_create) příkaz. Následující příklad vytvoří virtuální síť s názvem **VNet1** v **EastUS** umístění:

```azurecli-interactive 
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="gwsubnet"></a>Přidat podsíť brány

Podsíť brány obsahuje rezervovaných adres IP, které používají služby brány virtuální sítě. Chcete-li přidat podsíť brány použijte v následujících příkladech:

```azurepowershell-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="PublicIP"></a>Vyžádání veřejné IP adresy

Brána sítě VPN, musí mít dynamicky přidělené veřejnou IP adresu. Ke službě VPN gateway, které vytvoříte pro vaši virtuální síť se přidělí veřejnou IP adresu. Následující příklad použijte k vyžádání veřejné IP adresy:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="CreateGateway"></a>Vytvoření brány VPN

Vytvořte bránu VPN pomocí příkazu [az network vnet-gateway create](/cli/azure/group#az_network_vnet_gateway_create).

Pokud spustíte tento příkaz pomocí `--no-wait` parametr nevidíte žádné zpětnou vazbu nebo výstup. `--no-wait` Parametr povoluje bránu vytvořit na pozadí. Neznamená to okamžitě vytvoření brány VPN.

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

Brána sítě VPN může trvat 45 minut nebo déle vytvořit.

## <a name="viewgw"></a>Zobrazení služby VPN gateway

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Odpověď bude vypadat podobně jako tento:

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

### <a name="view-the-public-ip-address"></a>Zobrazení veřejnou IP adresu

Chcete-li zobrazit veřejnou IP adresu přiřadit k bráně, použijte následující příklad:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

Hodnota přidružená **ipAddress** pole je veřejnou IP adresu brány VPN.

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

Pokud již nepotřebujete prostředky, které jste vytvořili, použijte [odstranění skupiny az](/cli/azure/group#az_group_delete) se odstranit skupinu prostředků. Tato akce odstraní skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Další postup

Po dokončení vytvoření brány můžete vytvořit připojení mezi virtuální sítí a jiné virtuální síti. Nebo vytvořte připojení mezi virtuální sítí a místní umístění.

> [!div class="nextstepaction"]
> [Umožňuje vytvořit připojení site-to-site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Umožňuje vytvořit připojení point-to-site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Umožňuje vytvořit připojení k jiné virtuální síti](vpn-gateway-vnet-vnet-rm-ps.md)