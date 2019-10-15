---
title: O branách virtuální sítě ExpressRoute – Azure | Microsoft Docs
description: Přečtěte si informace o branách virtuální sítě pro ExpressRoute. Tento článek obsahuje informace o SKU a typech bran.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: ba03d643c8d3770da60d4225d6c2b84d2a07766f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72325538"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>Brána virtuální sítě ExpressRoute a FastPath
Pokud chcete připojit virtuální síť Azure a místní síť přes ExpressRoute, musíte nejdřív vytvořit bránu virtuální sítě. Brána virtuální sítě slouží ke dvěma účelům: Exchange IP trasy mezi sítěmi a směrování síťového provozu. Tento článek vysvětluje typy bran, SKU brány a odhadovaný výkon podle SKU. Tento článek také vysvětluje ExpressRoute [FastPath](#fastpath), funkci, která umožňuje síťovému provozu z vaší místní sítě obejít bránu virtuální sítě, aby se zlepšil výkon.

## <a name="gateway-types"></a>Typy bran

Při vytváření brány virtuální sítě je třeba zadat několik nastavení. Jedno z požadovaných nastavení "-GatewayType" Určuje, jestli se brána používá pro přenosy ExpressRoute, nebo VPN. Existují dva typy brány:

* **VPN** – k posílání šifrovaného provozu mezi veřejným internetem slouží typ brány VPN. To se také označuje jako brána sítě VPN. Připojení typu Site-to-Site, Point-to-Site a VNet-to-VNet používají bránu VPN.

* **ExpressRoute** – Chcete-li odeslat síťový provoz na privátním připojení, použijte typ brány "ExpressRoute". To se také označuje jako brána ExpressRoute a jedná se o typ brány, který se používá při konfiguraci ExpressRoute.

Každá virtuální síť může mít pouze jednu bránu virtuální sítě pro každý typ brány. Například můžete mít jednu bránu virtuální sítě, která má typ -GatewayType Vpn a jednu, který má typ -GatewayType ExpressRoute.

## <a name="gwsku"></a>SKU brány
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Pokud chcete bránu upgradovat na výkonnější SKU brány, ve většině případů můžete použít rutinu prostředí PowerShell změny velikosti AzVirtualNetworkGateway. To bude fungovat pro upgrady na standardní a HighPerformance SKU. Pokud ale chcete upgradovat na UltraPerformance SKU, budete muset bránu znovu vytvořit. Opětovné vytvoření brány má za následek výpadky.

### <a name="aggthroughput"></a>Odhadované výkony podle SKU brány
V následující tabulce jsou uvedeny typy bran a odhadované výkony. Tato tabulka platí pro model nasazení Resource Manager i pro klasický model.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Výkon aplikace závisí na několika faktorech, například na koncové latenci, a na počtu toků přenosu, které aplikace otevírá. Čísla v tabulce znázorňují horní limit, který aplikace může teoreticky dosahovat v ideálním prostředí.
>
>

### <a name="zrgw"></a>Neredundantní SKU brány v zóně

V Zóny dostupnosti Azure můžete nasadit i brány ExpressRoute. Tím se fyzicky a logicky oddělují do různých Zóny dostupnosti, což zajistí ochranu místních síťových připojení k Azure před selháními na úrovni zóny.

![Zóna – redundantní brána ExpressRoute](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Redundantní brány v zóně používají pro bránu ExpressRoute konkrétní nové SKU brány.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Nové SKU brány podporují také další možnosti nasazení, které nejlépe vyhovují vašim potřebám. Při vytváření brány virtuální sítě pomocí nových SKU brány máte také možnost nasazení brány do konkrétní zóny. Tento postup se označuje jako brána pro oblast. Když nasadíte bránu pro oblast, všechny instance brány se nasadí ve stejné zóně dostupnosti.

## <a name="fastpath"></a>FastPath
Brána virtuální sítě ExpressRoute je navržená pro výměnu síťových tras a směrování síťového provozu. FastPath je navržená tak, aby vylepšila výkon datových cest mezi vaší místní sítí a virtuální sítí. Pokud je povoleno, FastPath odesílá síťový provoz přímo virtuálním počítačům ve virtuální síti a vynechá bránu. 

FastPath je k dispozici na všech okruhech ExpressRoute. Ještě vyžaduje vytvoření brány virtuální sítě pro výměnu tras mezi virtuální sítí a místní sítí. Brána virtuální sítě musí být buď Ultra Performance, nebo ErGw3AZ.

FastPath nepodporuje následující funkce:
* UDR v podsíti brány: Pokud použijete UDR k podsíti brány vaší virtuální sítě, bude se síťový provoz z vaší místní sítě dál posílat do brány virtuální sítě.
* Partnerský vztah virtuálních sítí: Pokud máte jiné virtuální sítě s partnerským vztahem, který je připojený k ExpressRoute síťového provozu z vaší místní sítě do ostatních virtuálních sítí (tj. virtuální sítě), bude se dál posílat do virtuální sítě. brány. Alternativním řešením je připojit všechny virtuální sítě k okruhu ExpressRoute přímo.
* Základní Load Balancer: Pokud nasadíte základní interní nástroj pro vyrovnávání zatížení ve vaší virtuální síti nebo ve službě Azure PaaS, kterou nasadíte ve vaší virtuální síti, použijete základní interní nástroj pro vyrovnávání zatížení, síťový provoz z vaší místní sítě na virtuální IP adresy hostované na Do brány virtuální sítě se pošle základní nástroj pro vyrovnávání zatížení. Řešením je upgradovat základní nástroj pro vyrovnávání zatížení na [standardní nástroj pro vyrovnávání zatížení](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview).
* Privátní odkaz: Pokud se připojíte k [privátnímu koncovému bodu](../private-link/private-link-overview.md) ve virtuální síti z místní sítě, připojení projde bránou virtuální sítě.
 
## <a name="resources"></a>Rozhraní REST API a rutiny PowerShellu
Další technické materiály a specifické požadavky na syntaxi při použití rozhraní REST API a rutin PowerShellu pro konfigurace brány virtuální sítě najdete na následujících stránkách:

| **Classic** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Další kroky
Další informace o dostupných konfiguracích připojení najdete v tématu [ExpressRoute Overview](expressroute-introduction.md) .

Další informace o vytváření bran ExpressRoute najdete v tématu [Vytvoření brány virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) .

Další informace o konfiguraci bran, které jsou v zóně redundantní, najdete v tématu [Vytvoření brány redundantní virtuální sítě](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) .

Další informace o tom, jak povolit FastPath, najdete v tématu [připojení virtuální sítě k ExpressRoute](expressroute-howto-linkvnet-arm.md) . 
