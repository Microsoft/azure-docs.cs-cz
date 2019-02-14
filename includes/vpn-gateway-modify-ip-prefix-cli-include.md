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
ms.openlocfilehash: b381c5ad8fd81cd9b7411e1f4679b3f5214e6de9
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246178"
---
### <a name="noconnection"></a>Úprava předpon IP adres místní síťové brány – žádné připojení brány

Pokud nemáte připojení brány a chcete přidat nebo odebrat předpony IP adres, můžete použít stejný příkaz, který používáte k vytvoření brány místní sítě, tedy [az network local-gateway create](https://docs.microsoft.com/cli/azure/network/local-gateway). Tento příkaz můžete použít také k aktualizaci IP adresy brány pro zařízení VPN. K přepsání aktuálního nastavení použijte existující název brány místní sítě. Pokud použijete jiný název, vytvoříte novou bránu místní sítě, místo abyste přepsali tu stávající.

Při každé změně je nutné zadat celý seznam předpon, ne jenom předpony, které chcete změnit. Zadejte pouze předpony, které chcete ponechat. V tomto případě 10.0.0.0/24 a 20.0.0.0/24.

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="withconnection"></a>Úprava předpon IP adres místní síťové brány – existující připojení brány

Pokud máte připojení brány a chcete přidat nebo odebrat předpony IP adres, můžete předpony aktualizovat pomocí příkazu [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway). Způsobí to určitý výpadek připojení VPN. Při upravování předpon IP adres není potřeba odstraňovat bránu VPN.

Při každé změně je nutné zadat celý seznam předpon, ne jenom předpony, které chcete změnit. V tomto příkladu jsou již přítomny předpony 10.0.0.0/24 a 20.0.0.0/24. Přidáme předpony 30.0.0.0/24 a 40.0.0.0/24 a při aktualizaci zadáme všechny 4 předpony.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```
