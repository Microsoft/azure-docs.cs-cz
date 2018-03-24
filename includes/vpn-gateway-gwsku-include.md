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
ms.openlocfilehash: 05dc8ae48a9164e4f7118d378ab0eb7c30a4249e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
Při vytváření brány virtuální sítě musíte určit SKU brány, které chcete použít. Vyberte SKU, který splňuje vaše požadavky na základě typů úloh, propustnost, funkce a SLA.

###  <a name="benchmark"></a>SKU brány tunelové propojení, připojení a propustnosti

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>SKU brány podle sada funkcí

Nová brána VPN SKU zjednodušit sady funkcí nabízené na brány:

| **SKU**| **Funkce**|
| ---    | ---         |
|**Základní** (*)   | **Sítě VPN založené na trasách**: 10 tunelů s P2S; bez ověřování RADIUS pro P2S; žádné IKEv2 pro P2S<br>**Síť VPN založená na zásadách (IKEv1):** 1 tunel, bez P2S|
| **VpnGw1, VpnGw2 a VpnGw3** | **Sítě VPN založené na trasách**: až 30 tunelů (*), P2S, protokol BGP, aktivní aktivní, vlastní protokolu IPsec/IKE zásad, ExpressRoute nebo VPN koexistence |
|        |             |

( * ) Můžete nakonfigurovat PolicyBasedTrafficSelectors pro připojení brány sítě VPN založené na směrování (VpnGw1, VpnGw2, VpnGw3) k několika místním zařízením brány firewall založeným na zásadách. Podrobnosti najdete v tématu věnovaném [připojení bran VPN k několika místním zařízením VPN založeným na zásadách s využitím PowerShellu](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

(**) Základní SKU je považován za starší verze SKU. Základní SKU má určitá omezení funkce. Nelze změnit velikost bránu, která používá základní SKU pro jednu z nové SKU brány, musíte místo toho změnit na nový SKU, který zahrnuje odstranit a znovu vytvořit bránu VPN.

###  <a name="workloads"></a>SKU brány - produkční vs. vývojářské a testovací úlohy

Kvůli rozdílům ve SLA a sady funkcí doporučujeme následující SKU pro produkční prostředí oproti vývoje testování:

| **Úloha**                       | **SKU**               |
| ---                                | ---                    |
| **Produkce, kritické úlohy** | VpnGw1, VpnGw2, VpnGw3 |
| **Vývoj a testování nebo testování konceptu**   | Basic (*)                 |
|                                    |                        |

(**) Základní SKU je považován za starší verze SKU a má omezení funkce. Ověřte, zda je funkce, které potřebujete podporovat před použitím základní verze SKU.

Pokud používáte SKU původní (starší), jsou doporučení SKU produkční Standard a HighPerformance. Informace a pokyny pro původní SKU najdete v tématu [SKU brány (zastaralé)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).