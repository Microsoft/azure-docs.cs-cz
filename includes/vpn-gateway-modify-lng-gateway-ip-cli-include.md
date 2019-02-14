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
ms.openlocfilehash: e368b1590f263618969423d57cdf0531fc2bb54d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246157"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Úprava IP adresy brány místní sítě (gatewayIpAddress)

Pokud zařízení VPN, ke kterému se chcete připojit, změnilo svou veřejnou IP adresu, musíte upravit bránu místní sítě, aby odrážela tuto změnu. IP adresu brány je možné změnit bez odebrání existujícího připojení brány VPN (pokud nějaké máte). Pokud chcete upravit IP adresu brány, nahraďte hodnoty „Site2“ a „TestRG1“ vlastními hodnotami pomocí příkazu [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Na výstupu ověřte správnost IP adresy:

```
"gatewayIpAddress": "23.99.222.170",
```
