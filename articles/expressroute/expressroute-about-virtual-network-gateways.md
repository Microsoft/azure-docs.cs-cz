---
title: O branách virtuálních sítí ExpressRoute – Azure | Dokumentace Microsoftu
description: Další informace o branách virtuálních sítí pro ExpressRoute. Tento článek obsahuje informace o SKU brány a typy.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 18615cf737eedcd188fd59d2aa98482210b9333a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991590"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>Brána virtuální sítě ExpressRoute a FastPath
Připojení vaší virtuální sítí Azure a vaší místní sítě prostřednictvím ExpressRoute, musíte nejprve vytvořte bránu virtuální sítě. Bránu virtuální sítě má dva účely: exchange IP trasy mezi sítěmi a směrovat síťový provoz. Tento článek vysvětluje typy bran, skladové položky brány a odhadovanou výkonu skladová jednotka potřebuje. Tento článek také popisuje ExpressRoute [FastPath](#fastpath), funkce, která umožňuje síťový provoz z vaší místní sítě k obejití brány virtuální sítě ke zlepšení výkonu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gateway-types"></a>Typy bran

Při vytváření brány virtuální sítě, budete muset zadat několik nastavení. Jeden z požadovaných nastavení "-GatewayType", určuje, jestli je brána používat pro ExpressRoute, nebo přenosy VPN. Dvě brány typy jsou:

* **VPN** – Pokud chcete posílat šifrovaný provoz přes veřejný Internet, použijte typ brány "Vpn". To se také označuje jako bránu sítě VPN. Připojení typu Site-to-Site, Point-to-Site a VNet-to-VNet používají bránu VPN.

* **ExpressRoute** – Pokud chcete posílat síťový provoz na privátní připojení, použijte typ brány "ExpressRoute". Tím se také označuje jako bránu ExpressRoute a je typ brány, kterou používá při konfiguraci ExpressRoute.

Každá virtuální síť může mít pouze jednu bránu virtuální sítě pro každý typ brány. Například můžete mít jednu bránu virtuální sítě, která má typ -GatewayType Vpn a jednu, který má typ -GatewayType ExpressRoute.

## <a name="gwsku"></a>SKU brány
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Pokud chcete provést upgrade brány na výkonnější skladové položky brány, ve většině případů můžete použít rutinu Powershellu "Změna velikosti AzVirtualNetworkGateway". To bude fungovat pro upgrade na Standard a HighPerformance SKU. Pokud chcete upgradovat na UltraPerformance SKU, musíte však znovu vytvořte bránu. Opětovné vytvoření brány způsobí výpadek.

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
Brána virtuální sítě ExpressRoute je navržená pro výměnu směrování sítě a směrování síťového provozu. FastPath slouží ke zlepšení výkonu cesty dat mezi vaší místní sítí a virtuální sítí. Při povolení FastPath odesílá síťový provoz přímo do virtuálních počítačů ve virtuální síti, obcházení brány. 

Je k dispozici na FastPath [ExpressRoute přímo](expressroute-erdirect-about.md) pouze. Jinými slovy, můžete povolit tuto funkci pouze tehdy, pokud jste [připojte svou virtuální síť](expressroute-howto-linkvnet-arm.md) pro okruh ExpressRoute vytvořený na port přímo ExpressRoute. FastPath nadále vyžaduje bránu virtuální sítě má být vytvořen pro výměnu tras mezi virtuální sítí a místní sítí. Brána virtuální sítě musí být Ultra výkon nebo ErGw3AZ.

FastPath nepodporuje následující funkce:
* Uživatelem definovaná TRASA v podsíti brány: Pokud použijete trasu UDR do podsítě brány virtuální sítě síťový provoz z vaší místní sítě bude pokračovat k odeslání do brány virtuální sítě.
* Partnerský vztah virtuální sítě: Pokud máte jiné virtuální sítě v partnerském vztahu s ten, který je připojená k ExpressRoute síťový provoz z vaší místní sítě k jiné virtuální sítě (například takzvané "Paprsků" virtuální sítě) budou i nadále odešlou do virtuální sítě brány. Alternativním řešením je se přímo připojit všechny virtuální sítě k okruhu ExpressRoute.

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

Zobrazit [propojení virtuální sítě pro ExpressRoute](expressroute-howto-linkvnet-arm.md) Další informace o tom, jak povolit FastPath. 
