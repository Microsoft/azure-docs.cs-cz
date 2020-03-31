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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74085229"
---
Při vytváření brány virtuální sítě musíte určit SKU brány, které chcete použít. Vyberte jednotku SKU, která splňuje vaše požadavky na základě typů úloh, propustnosti, funkcí a SLA. SKU virtuální chod brány sítě v zónách dostupnosti Azure najdete v [tématu Zóny dostupnosti Azure brány skutníny](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>SKU brány podle tunelu, připojení a propustnosti

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> VpnGw SKU (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 a VpnGw5AZ) jsou podporovány pouze pro model nasazení Resource Manager. Klasické virtuální sítě by měly nadále používat staré (starší) skum.
>  * Informace o práci se staršími virtuálními zařízeními brány (Basic, Standard a HighPerformance) naleznete v [tématu Práce s virtuálními zařízeními brány VPN (starší položky SKU).](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)
>  * Informace o soupoložkách brány ExpressRoute najdete v tématu [Brány virtuální sítě pro ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>SKU brány podle sady funkcí

Nové skupy brány VPN zjednodušují sady funkcí nabízené na branách:

| **Sku**| **Funkce**|
| ---    | ---         |
|**Základní** (**)   | **VPN založená na trase:** 10 tunelů pro S2S/připojení; žádné ověřování RADIUS pro P2S; no IKEv2 pro P2S<br>**Vpn založená na zásadách**: (IKEv1): 1 tunel S2S/connection; bez P2S|
| **Všechny generace1 a Generace2 SKU s výjimkou Basic** | **VPN založená na trase**: až 30 tunelů (*), P2S, BGP, aktivní aktivní, vlastní zásady IPsec/IKE, koexistence ExpressRoute/VPN |
|        |             |

(*) Můžete nakonfigurovat "PolicyBasedTrafficSelectors" pro připojení brány VPN založené na trase k více místním zařízením brány firewall založeným na zásadách. Podrobnosti najdete v tématu věnovaném [připojení bran VPN k několika místním zařízením VPN založeným na zásadách s využitím PowerShellu](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

(\*\*) Základní skladová položka je považována za starší skladovou položku. Základní skladová položka má určitá omezení funkcí. Nelze změnit velikost brány, která používá základní skladovou položku pro jednu z nových skladových míst brány brány, musíte místo toho změnit na novou skladovou položku, která zahrnuje odstranění a opětovné vytvoření brány VPN.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>SKU brány – produkční vs. úlohy pro dev-test

Vzhledem k rozdílům ve SLA a sadách funkcí doporučujeme pro produkci vs. vývoj a testování následující SKU:

| **Úloha**                       | **SKU**               |
| ---                                | ---                    |
| **Produkce, kritické úlohy** | Všechny generace1 a Generace2 SKU s výjimkou Basic |
| **Vývoj a testování nebo testování konceptu**   | Základní (**)                 |
|                                    |                        |

(\*\*) Základní skladová položka je považována za starší skladovou položku a má omezení funkcí. Před použitím základní skladové položky ověřte, zda je funkce, kterou potřebujete, podporována.

Pokud používáte staré skladové položky (starší verze), produkční skladové položky doporučení jsou standardní a highperformance. Informace a pokyny pro staré sloky naleznete v [tématu brána skum (starší verze)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
