---
title: O branách virtuálních sítí ExpressRoute – Azure | Dokumentace Microsoftu
description: Další informace o branách virtuálních sítí pro ExpressRoute. Tento článek obsahuje informace o SKU brány a typy.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 5b74e387c6bee58acbbb7bae320a9bc72a4dda1c
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376275"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>Brána virtuální sítě ExpressRoute a FastPath
Pokud chcete připojit virtuální síť Azure a místní síť přes ExpressRoute, musíte nejdřív vytvořit bránu virtuální sítě. Brána virtuální sítě slouží ke dvěma účelům: Exchange IP trasy mezi sítěmi a směrování síťového provozu. Tento článek vysvětluje typy bran, SKU brány a odhadovaný výkon podle SKU. Tento článek také vysvětluje ExpressRoute [FastPath](#fastpath), funkci, která umožňuje síťovému provozu z vaší místní sítě obejít bránu virtuální sítě, aby se zlepšil výkon.

## <a name="gateway-types"></a>Typy bran

Při vytváření brány virtuální sítě, budete muset zadat několik nastavení. Jeden z požadovaných nastavení "-GatewayType", určuje, jestli je brána používat pro ExpressRoute, nebo přenosy VPN. Dvě brány typy jsou:

* **VPN** – Pokud chcete posílat šifrovaný provoz přes veřejný Internet, použijte typ brány "Vpn". To se také označuje jako bránu sítě VPN. Připojení typu Site-to-Site, Point-to-Site a VNet-to-VNet používají bránu VPN.

* **ExpressRoute** – Pokud chcete posílat síťový provoz na privátní připojení, použijte typ brány "ExpressRoute". Tím se také označuje jako bránu ExpressRoute a je typ brány, kterou používá při konfiguraci ExpressRoute.

Každá virtuální síť může mít pouze jednu bránu virtuální sítě pro každý typ brány. Například můžete mít jednu bránu virtuální sítě, která má typ -GatewayType Vpn a jednu, který má typ -GatewayType ExpressRoute.

## <a name="gwsku"></a>SKU brány
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Pokud chcete bránu upgradovat na výkonnější SKU brány, ve většině případů můžete použít rutinu prostředí PowerShell změny velikosti AzVirtualNetworkGateway. To bude fungovat pro upgrade na Standard a HighPerformance SKU. Pokud chcete upgradovat na UltraPerformance SKU, musíte však znovu vytvořte bránu. Opětovné vytvoření brány způsobí výpadek.

### <a name="aggthroughput"></a>Odhadované výkonů podle SKU brány
Následující tabulka ukazuje typy brány a odhadovanou funkční. Tato tabulka platí pro model nasazení Resource Manager i pro klasický model.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Výkon aplikace závisí na několika faktorech, například na celkovou latenci a počet přenosové toky, které aplikace se otevře. Čísla v tabulce představují horní mez, které může aplikace dosáhnout teoreticky v prostředí ideální.
>
>

### <a name="zrgw"></a>Zónově redundantní SKU brány

Můžete také nasadit brány ExpressRoute v zónách dostupnosti Azure. To fyzicky a logicky je odděluje do různých zón dostupnosti, chrání před výpadky na úrovni zóny připojení k místní síti do Azure.

![Zónově redundantní brány ExpressRoute](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zónově redundantní brány používají konkrétní nové SKU brány pro bránu ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Nové SKU brány podporovat i jiné možnosti nasazení, aby co nejlépe odpovídaly vašim potřebám. Při vytváření brány virtuální sítě pomocí nové SKU brány, máte také možnost k nasazení brány v konkrétní zóně. To se označuje jako brána oblastmi. Při nasazení oblastmi brány všechny instance brány jsou nasazené ve stejné zóně dostupnosti.

## <a name="fastpath"></a>FastPath
Brána virtuální sítě ExpressRoute je navržená pro výměnu síťových tras a směrování síťového provozu. FastPath je navržená tak, aby vylepšila výkon datových cest mezi vaší místní sítí a virtuální sítí. Pokud je povoleno, FastPath odesílá síťový provoz přímo virtuálním počítačům ve virtuální síti a vynechá bránu. 

FastPath je k dispozici pouze na [ExpressRoute Direct](expressroute-erdirect-about.md) . Jinými slovy, tuto funkci můžete povolit pouze v případě, že [připojíte virtuální síť](expressroute-howto-linkvnet-arm.md) k okruhu ExpressRoute vytvořenému na portu ExpressRoute Direct. FastPath pořád vyžaduje vytvoření brány virtuální sítě pro výměnu tras mezi virtuální sítí a místní sítí. Brána virtuální sítě musí být buď Ultra Performance, nebo ErGw3AZ.

FastPath nepodporuje následující funkce:
* UDR v podsíti brány: Pokud použijete UDR k podsíti brány vaší virtuální sítě, bude se síťový provoz z vaší místní sítě dál posílat do brány virtuální sítě.
* Partnerský vztah virtuálních sítí: Pokud máte jiné virtuální sítě s partnerským vztahem, který je připojený k ExpressRoute síťového provozu z vaší místní sítě do ostatních virtuálních sítí (tj. virtuální sítě), bude se dál posílat do virtuální sítě. brány. Alternativním řešením je připojit všechny virtuální sítě k okruhu ExpressRoute přímo.
* Základní zátěžový Balander: Pokud nasadíte základní interní nástroj pro vyrovnávání zatížení ve vaší virtuální síti nebo službu Azure PaaS, kterou nasadíte ve vaší virtuální síti, použijete základní interní nástroj pro vyrovnávání zatížení, síťový provoz z vaší místní sítě do virtuálních IP adres hostovaný na Do brány virtuální sítě se pošle základní nástroj pro vyrovnávání zatížení. Řešením je upgradovat základní nástroj pro vyrovnávání zatížení na [standardní nástroj pro vyrovnávání zatížení](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview). 
 
## <a name="resources"></a>Rutiny Powershellu a rozhraní REST API
Pro další zdroje technických informací a požadavky na konkrétní syntaxe při použití rozhraní REST API a rutin prostředí PowerShell pro konfiguraci brány virtuální sítě naleznete na následujících stránkách:

| **Classic** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Další postup
Zobrazit [přehled ExpressRoute](expressroute-introduction.md) pro další informace o konfiguracích dostupné připojení.

Zobrazit [vytvořit bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) pro další informace o vytváření brány ExpressRoute.

Zobrazit [vytvořit bránu virtuální sítě zónově redundantní](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) pro další informace o konfiguraci brány zónově redundantní.

Další informace o tom, jak povolit FastPath, najdete v tématu [připojení virtuální sítě k ExpressRoute](expressroute-howto-linkvnet-arm.md) . 
