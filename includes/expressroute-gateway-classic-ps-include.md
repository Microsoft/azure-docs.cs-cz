---
title: zahrnout soubor
description: zahrnout soubor
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 11/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fab00e5281bb91bce10228b3bc2e9cfd503d5d5b
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219813"
---
Virtuální síť a podsíť brány musíte vytvořit předtím, než začnete pracovat na následující úlohy.

> [!NOTE]
> Tyto příklady se nevztahují na S2S a ExpressRoute existovat vedle sebe konfigurace.
> Další informace o práci s bránami v konfiguraci coexist najdete v tématu [konfigurace současně existujících připojení](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Přidání brány

Pomocí následujícího příkazu vytvořte bránu. Nezapomeňte nahradit všechny hodnoty vlastními.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Ověřte, že vytvoření brány

Pomocí následujícího příkazu Pokud chcete ověřit vytvoření brány. Tento příkaz načte také ID brány, které jsou nutné pro další operace.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Změňte velikost brány

Několik položek [skladové položky brány](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Následující příkaz můžete kdykoli změnit SKU brány.

> [!IMPORTANT]
> Tento příkaz nefunguje pro brány UltraPerformance. Chcete-li změnit brána brány UltraPerformance, nejprve odeberte existující bránu ExpressRoute a pak vytvořte nové brány UltraPerformance. Přejít na nižší verzi z brány UltraPerformance bránu, nejprve odeberte brány UltraPerformance a pak vytvořit novou bránu. 
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Odebrání brány

Pomocí následujícího příkazu k odebrání brány

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```
