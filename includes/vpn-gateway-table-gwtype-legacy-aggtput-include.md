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
ms.openlocfilehash: 9734859c0bf22201c146e5d8a220f3146f6051c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174897"
---
Následující tabulka ukazuje typy brány a odhadovanou agregovanou propustnost podle SKU brány. Tato tabulka se vztahuje na modely nasazení Správce prostředků a Classic. 

Ceny se liší pro jednotlivé SKU brány. Další informace najdete v tématu [VPN Gateway – ceny](https://azure.microsoft.com/pricing/details/vpn-gateway).

SKU brány UltraPerformance se v této tabulce neuvádí. Informace o SKU UltraPerformance najdete v dokumentaci k [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

|  | **Propustnost brány sítě VPN (1)** | **Maximální počet tunelových propojení IPsec brány sítě VPN (2)** | **Propustnost brány ExpressRoute** | **Brána sítě VPN a ExpressRoute vedle sebe** |
| --- | --- | --- | --- | --- |
| **Základní SKU (3)(5)(6)** |100 Mb/s |10 |500 Mb/s (6) |No |
| **Standardní SKU (4)(5)** |100 Mb/s |10 |1000 Mb/s |Yes |
| **SKU pro vysoký výkon (4)** |200 Mb/s |30 |2000 Mb/s |Yes |


(1) Propustnost sítě VPN představuje přibližný odhad na základě měření mezi sítěmi VNet ve stejné oblasti Azure. Není zaručena propustnost pro připojení mezi místními systémy přes internet. Jedná se o maximální možné měření propustnosti.

(2) Počet tunelových propojení se týká sítí VPN RouteBased. Sítě VPN PolicyBased můžou podporovat jen jeden tunel VPN typu S2S (Site-to-Site).

(3) Protokol BGP není podporován pro základní SKU.

(4) Sítě VPN typu PolicyBased nejsou pro tuto SKU podporované. Jsou podporované pouze pro základní SKU.

(5) Propojení VPN Gateway S2S aktivní-aktivní nejsou pro toto SKU podporovaná. Aktivní-aktivní je podporované jenom v SKU HighPerformance.

(6) základní SKU je zastaralé pro použití s ExpressRoute.
