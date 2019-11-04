---
title: Vlastní selektory přenosu pro připojení Azure VPN Gateway | Microsoft Docs
description: Přečtěte si, jak používat vlastní selektory provozu při připojeních VPN Gateway.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: radwiv
ms.openlocfilehash: cf9401b21d4aa736a6edeae5146d1355f2d49d1d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512155"
---
# <a name="custom-traffic-selectors-for-vpn-gateway-connections"></a>Vlastní selektory přenosu pro VPN Gateway připojení

V připojeních k bráně VPN můžete nastavit vlastní selektory přenosu dat. Možnost nastavit selektory provozu vám umožní zúžit adresní prostory z obou stran tunelových propojení VPN, na které chcete odesílat přenosy. Vlastní selektory provozu jsou zvláště užitečné, pokud máte velký adresní prostor virtuální sítě, ale chcete použít jednu z podsítí pro vyjednávání IPsec/IKE.

Vlastní selektory přenosu dat se dají přidat při vytváření nového připojení nebo se dá aktualizovat pro existující připojení. Parametr `TrafficSelectorPolicies` se skládá z pole selektorů přenosu. Každý selektor provozu obsahuje kolekci místních a vzdálených rozsahů adres ve formátu CIDR.

## <a name="add-custom-traffic-selectors"></a>Přidat vlastní selektory provozu

Následující příklady ukazují, jak se dají vytvořit vlastní selektory přenosu pro připojení brány virtuální sítě pomocí příkazů PowerShellu. Nápovědu k vytvoření připojení brány virtuální sítě najdete v tématu [Konfigurace připojení typu Site-to-site](vpn-gateway-create-site-to-site-rm-powershell.md).

1. Nastavte hodnoty parametru *trafficselectorpolicies* v $trafficselectorpolicy. Při nastavování si všimněte, že hodnota *New-AzTrafficSelectorPolicy* přijímá v poli místní a vzdálené rozsahy adres.

   ```azurepowershell-interactive
   $trafficSelectorPolicy = New-AzTrafficSelectorPolicy `
   -LocalAddressRanges ("10.10.10.0/24", "20.20.20.0/24") `
   -RemoteAddressRanges ("30.30.30.0/24", "40.40.40.0/24")
   ```
2. Vytvoří nové připojení brány virtuální sítě. Upravte parametry hodnoty podle svých požadavků.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection `
   -ResourceGroupName $rgname `
   -name $vnetConnectionName `
   -location $location `
   -VirtualNetworkGateway1 $vnetGateway `
   -LocalNetworkGateway2 $localnetGateway `
   -ConnectionType IPsec `
   -RoutingWeight 3 `
   -SharedKey $sharedKey `
   -UsePolicyBasedTrafficSelectors $true `
   -TrafficSelectorPolicies ($trafficSelectorPolicy)
   ```

Můžete také aktualizovat vlastní selektory přenosu pro existující připojení brány virtuální sítě pomocí příkazu set-AzVirtualNetworkGatewayConnection. Hodnoty PowerShellu najdete v tématu [připojení brány virtuální sítě](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?).

## <a name="next-steps"></a>Další kroky

Další informace o branách VPN najdete v tématu [informace o VPN Gateway](vpn-gateway-about-vpngateways.md).