---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: 622a2f69c2e7b82f2df10d6120ee2dd466961915
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "77211390"
---
Starší (staré) skladové položky brány VPN jsou:

* Výchozí (základní)
* Standard
* Vysokovýkonné

VPN Gateway nepoužívá SKU brány UltraPerformance. Informace o SKU UltraPerformance najdete v dokumentaci k [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Při práci se staršími skladovými položkami mějte na paměti následující:

* Pokud chcete použít síť VPN typu PolicyBased, musíte použít SKU Basic. Sítě VPN typu PolicyBased (dříve nazývané Statické směrování) nejsou podporovány jinými SKU.
* Protokol BGP není podporován v základní SKU.
* Konfigurace současného fungování ExpressRoute a služby VPN Gateway nejsou podporované v základní SKU.
* Propojení VPN Gateway S2S aktivní-aktivní jde konfigurovat jenom v SKU HighPerformance.