---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 354015930170ca6466b3555c78d211c080232c82
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755464"
---
### <a name="to-view-local-network-gateways"></a>Zobrazení bran místní sítě

Pokud chcete zobrazit seznam místních síťových bran, použijte příkaz [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway).

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>Ověření hodnot sdíleného klíče

Ověřte, že hodnota sdíleného klíče je stejná jako hodnota, kterou jste použili pro konfiguraci zařízení VPN. Pokud není, buď spusťte připojení znovu s použitím hodnoty ze zařízení, nebo hodnotu v zařízení aktualizujte na vrácenou hodnotu. Tyto hodnoty se musí shodovat. Pokud chcete zobrazit sdílený klíč, použijte příkaz [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection).

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>Zobrazení veřejné IP adresy brány VPN

 Pokud chcete zjistit veřejnou IP adresu brány virtuální sítě, použijte příkaz [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip). Pro snadné čtení je výstup v tomto příkladu formátovaný, aby zobrazil seznam veřejných IP adres ve formátu tabulky.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
