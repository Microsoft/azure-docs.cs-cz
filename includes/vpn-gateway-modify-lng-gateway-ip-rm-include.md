---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 06df27b5eeb74f75bc49a848881b56aa4e81c57b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380477"
---
Pokud zařízení VPN, ke kterému se chcete připojit, změnilo svou veřejnou IP adresu, musíte upravit bránu místní sítě, aby odrážela tuto změnu. Při upravování této hodnoty můžete také zároveň upravit předpony adres. Ujistěte se, že k přepsání aktuálního nastavení používáte existující název brány místní sítě. Pokud použijete jiný název, vytvoříte novou bránu místní sítě, místo abyste přepsali tu stávající.

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```
