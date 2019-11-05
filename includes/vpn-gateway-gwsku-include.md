---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 75152fabfc33dda0494d871fbdf9a388f4260c0d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495748"
---
Při vytváření brány virtuální sítě musíte určit SKU brány, které chcete použít. Vyberte jednotku SKU, která splňuje vaše požadavky na základě typů úloh, propustnosti, funkcí a SLA. Skladové jednotky brány virtuální sítě v Zóny dostupnosti Azure najdete v tématu [skladové položky brány zóny dostupnosti Azure](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="benchmark"></a>SKU brány podle tunelu, připojení a propustnosti

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>SKU brány podle sady funkcí

Nové skladové položky brány VPN zefektivňují sady funkcí nabízené na branách:

| **SKU**| **Funkce**|
| ---    | ---         |
|**Základní** (* *)   | **Síť VPN založená na směrování**: 10 tunelů pro S2S/připojení; žádné ověřování RADIUS pro P2S; žádná IKEv2 pro P2S<br>**Síť VPN založená na zásadách**: (IKEv1): 1 tunelové připojení S2S/připojení; žádné P2S|
| **Všechny skladové položky Generation1 a Generation2 s výjimkou úrovně Basic** | **Síť VPN založená na směrování**: až 30 tunelů (*), P2S, BGP, aktivní-aktivní, vlastní zásady IPSec/IKE, koexistence EXPRESSROUTE/VPN |
|        |             |

(*) Můžete nakonfigurovat "PolicyBasedTrafficSelectors" pro připojení brány sítě VPN založené na trasách k několika místním zařízením brány firewall na základě zásad. Podrobnosti najdete v tématu věnovaném [připojení bran VPN k několika místním zařízením VPN založeným na zásadách s využitím PowerShellu](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

(\*\*) Základní SKU se považuje za starší SKU. Základní skladová položka má určitá omezení funkcí. Nemůžete změnit velikost brány, která používá základní SKU na jednu z nových SKU brány, musíte místo toho přejít na novou SKU, která zahrnuje odstranění a opětovné vytvoření brány VPN.

###  <a name="workloads"></a>SKU brány – provozní a vývojové a testovací úlohy

Z důvodu rozdílů v SLA a sadách funkcí doporučujeme následující SKU pro produkční prostředí vs. vývoj a testování:

| **Úloha**                       | **SKU**               |
| ---                                | ---                    |
| **Produkce, kritické úlohy** | Všechny skladové položky Generation1 a Generation2 s výjimkou úrovně Basic |
| **Vývoj a testování nebo testování konceptu**   | Základní (* *)                 |
|                                    |                        |

(\*\*) Základní SKU se považuje za starší verzi SKU a má omezení funkcí. Před použitím základní SKU ověřte, zda je funkce, kterou potřebujete, podporována.

Pokud používáte staré SKU (starší verze), doporučení pro produkční skladová jednotka jsou standardní a HighPerformance. Informace a pokyny pro staré skladové položky naleznete v tématu [SKU brány (starší verze)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
