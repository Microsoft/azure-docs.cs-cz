---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010812"
---
Při vytváření brány virtuální sítě musíte určit SKU brány, které chcete použít. Vyberte jednotku SKU, která splňuje vaše požadavky na základě typů úloh, propustnosti, funkcí a SLA. Skladové jednotky brány virtuální sítě v Zóny dostupnosti Azure najdete v tématu [skladové položky brány zóny dostupnosti Azure](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>SKU brány podle tunelu, připojení a propustnosti

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> Skladové položky VpnGw (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 a VpnGw5AZ) se podporují jenom pro model nasazení Správce prostředků. Klasické virtuální sítě by měly dál používat staré (zastaralé) SKU.
>  * Informace o práci se staršími SKU brány (Basic, Standard a HighPerformance) najdete v tématu [práce s SKU služby VPN Gateway (starší skladové položky)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
>  * Skladové položky ExpressRoute brány najdete v tématu [Virtual Network Branch pro ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>SKU brány podle sady funkcí

Nové skladové položky brány VPN zefektivňují sady funkcí nabízené na branách:

| **SKU**| **Funkce**|
| ---    | ---         |
|**Základní** (* *)   | **Síť VPN založená na směrování**: 10 tunelů pro S2S/připojení; žádné ověřování RADIUS pro P2S; žádná IKEv2 pro P2S<br>**Síť VPN založená na zásadách**: (IKEv1): 1 tunelové připojení S2S/připojení; žádné P2S|
| **Všechny skladové položky Generation1 a Generation2 s výjimkou úrovně Basic** | **Síť VPN založená na směrování**: až 30 tunelů (*), P2S, BGP, aktivní-aktivní, vlastní zásady IPSec/IKE, koexistence EXPRESSROUTE/VPN |
|        |             |

(*) Můžete nakonfigurovat "PolicyBasedTrafficSelectors" pro připojení brány sítě VPN založené na trasách k několika místním zařízením brány firewall na základě zásad. Podrobnosti najdete v tématu věnovaném [připojení bran VPN k několika místním zařízením VPN založeným na zásadách s využitím PowerShellu](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

( \* \* ) Základní SKU se považuje za starší SKU. Základní skladová položka má určitá omezení funkcí. Nemůžete změnit velikost brány, která používá základní SKU na jednu z nových SKU brány, musíte místo toho přejít na novou SKU, která zahrnuje odstranění a opětovné vytvoření brány VPN.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>SKU brány – produkční a Dev-Test úlohy

Vzhledem k rozdílům ve SLA a sadách funkcí doporučujeme pro produkci vs. vývoj a testování následující SKU:

| **Úloha**                       | **Skladové položky**               |
| ---                                | ---                    |
| **Produkce, kritické úlohy** | Všechny skladové položky Generation1 a Generation2 s výjimkou úrovně Basic |
| **Vývoj a testování nebo testování konceptu**   | Základní (* *)                 |
|                                    |                        |

( \* \* ) Základní SKU se považuje za starší položku SKU a má omezení funkcí. Před použitím základní SKU ověřte, zda je funkce, kterou potřebujete, podporována.

Pokud používáte staré SKU (starší verze), doporučení pro produkční skladová jednotka jsou standardní a HighPerformance. Informace a pokyny pro staré skladové položky naleznete v tématu [SKU brány (starší verze)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
