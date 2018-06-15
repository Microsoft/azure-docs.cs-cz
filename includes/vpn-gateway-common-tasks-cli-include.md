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
ms.openlocfilehash: e021a1b109f735dee16d75c05c26ab35e599a3d9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197699"
---
### <a name="to-view-local-network-gateways"></a>Zobrazení bran místní sítě

Pokud chcete zobrazit seznam místních síťových bran, použijte příkaz [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_list).

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>Ověření hodnot sdíleného klíče

Ověřte, že hodnota sdíleného klíče je stejná jako hodnota, kterou jste použili pro konfiguraci zařízení VPN. Pokud není, buď spusťte připojení znovu s použitím hodnoty ze zařízení, nebo hodnotu v zařízení aktualizujte na vrácenou hodnotu. Tyto hodnoty se musí shodovat. Pokud chcete zobrazit sdílený klíč, použijte příkaz [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection#az_network_vpn_connection_list).

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>Zobrazení veřejné IP adresy brány VPN

Pokud chcete zjistit veřejnou IP adresu brány virtuální sítě, použijte příkaz [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_list). Pro snadné čtení je výstup v tomto příkladu formátovaný, aby zobrazil seznam veřejných IP adres ve formátu tabulky.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
