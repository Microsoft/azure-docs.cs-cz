---
title: O branách virtuálních sítí ExpressRoute – Azure | Dokumentace Microsoftu
description: Další informace o branách virtuálních sítí pro ExpressRoute. Tento článek obsahuje informace o SKU brány a typy.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894374"
---
# <a name="about-expressroute-virtual-network-gateways"></a>O branách virtuální sítě ExpressRoute

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

## <a name="gwsub"></a>Podsíť brány

Než vytvoříte bránu ExpressRoute, musíte vytvořit podsíť brány. Podsíť brány obsahuje IP adresy, které používají virtuální počítače a služby brány virtuální sítě. Když vytvoříte bránu virtuální sítě, virtuální počítače brány se nasadí do podsítě brány a nakonfigurují s požadovaným nastavením ExpressRoute brány. Nikdy nesaďte nic jiného (například další virtuální počítače) do podsítě brány. Aby bylo možné správně pracovat, podsíť brány musí mít název "GatewaySubnet". Pojmenování podsítě brány "GatewaySubnet" umožňuje službě Azure zjistit, že se jedná o podsíť pro nasazení virtuálních počítačů a služeb brány virtuální sítě do.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Při vytváření podsítě brány zadáte počet IP adres, které podsíť obsahuje. IP adresy v podsíti brány jsou přiděleny virtuálním počítačům brány a službám brány. Některé konfigurace vyžadují víc IP adres než jiné. 

Při plánování velikosti podsítě brány si přečtěte dokumentaci ke konfiguraci, kterou plánujete vytvořit. Například konfigurace s ExpressRoute/VPN Gateway vyžaduje větší podsíť brány než většina ostatních konfigurací. Kromě toho můžete chtít zajistit, aby podsíť brány obsahovala dostatek IP adres, aby mohla pojmout možné budoucí další konfigurace. I když můžete vytvořit podsíť brány, která je menší než/29, doporučujeme vytvořit podsíť brány o velikosti/27 nebo větší (/27,/26 atd.), pokud máte dostupný adresní prostor. To bude vyhovovat většině konfigurací.

Následující příklad Správce prostředků PowerShell ukazuje podsíť brány s názvem GatewaySubnet. Můžete vidět, že zápis CIDR určuje/27, což umožňuje dostatek IP adres pro většinu konfigurací, které aktuálně existují.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

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

Další informace o FastPath, včetně omezení a požadavků, najdete v tématu [o FastPath](about-fastpath.md).

## <a name="resources"></a>Rutiny Powershellu a rozhraní REST API
Pro další zdroje technických informací a požadavky na konkrétní syntaxe při použití rozhraní REST API a rutin prostředí PowerShell pro konfiguraci brány virtuální sítě naleznete na následujících stránkách:

| **Classic** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Další kroky

Další informace o dostupných konfiguracích připojení najdete v tématu [ExpressRoute Overview](expressroute-introduction.md).

Další informace o vytváření bran ExpressRoute najdete v tématu [Vytvoření brány virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

Další informace o konfiguraci bran – redundantní brány najdete v tématu [Vytvoření brány redundantní virtuální sítě v zóně](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

Další informace o FastPath najdete v tématu [o FastPath](about-fastpath.md).