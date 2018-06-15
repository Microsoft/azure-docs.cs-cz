---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 865f4a963dfbaa7b8392865f8b0c468734673ba0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197631"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Úprava IP adresy brány místní sítě (gatewayIpAddress)

Pokud zařízení VPN, ke kterému se chcete připojit, změnilo svou veřejnou IP adresu, musíte upravit bránu místní sítě, aby odrážela tuto změnu. IP adresu brány je možné změnit bez odebrání existujícího připojení brány VPN (pokud nějaké máte). Pokud chcete upravit IP adresu brány, nahraďte hodnoty „Site2“ a „TestRG1“ vlastními hodnotami pomocí příkazu [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_update).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Na výstupu ověřte správnost IP adresy:

```
"gatewayIpAddress": "23.99.222.170",
```