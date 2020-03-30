---
title: O virtuálních síťových branách ExpressRoute – Azure| Dokumenty společnosti Microsoft
description: Přečtěte si o privátních síťových branách pro ExpressRoute. Tento článek obsahuje informace o skumu brány a typy.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281415"
---
# <a name="about-expressroute-virtual-network-gateways"></a>O branách virtuálnísítě ExpressRoute

Chcete-li připojit virtuální síť Azure a místní síť přes ExpressRoute, musíte nejprve vytvořit bránu virtuální sítě. Brána virtuální sítě slouží dvěma účelům: výměna tras IP mezi sítěmi a provoz sítě tras. Tento článek vysvětluje typy bran, skladové položky brány a odhadovaný výkon skladové položky. Tento článek také vysvětluje ExpressRoute [FastPath](#fastpath), funkce, která umožňuje síťový provoz z místní sítě obejít bránu virtuální sítě ke zlepšení výkonu.

## <a name="gateway-types"></a>Typy bran

Při vytváření brány virtuální sítě je třeba zadat několik nastavení. Jedno z požadovaných nastavení ,-GatewayType', určuje, zda je brána používána pro ExpressRoute nebo přenosy VPN. Dva typy brány jsou:

* **VPN** - Chcete-li odeslat šifrovaný provoz přes veřejný internet, použijte typ brány "Vpn". To se také označuje jako brána VPN. Připojení typu Site-to-Site, Point-to-Site a VNet-to-VNet používají bránu VPN.

* **ExpressRoute** - Chcete-li odeslat síťový provoz na soukromé připojení, použijte typ brány 'ExpressRoute'. To se také označuje jako brána ExpressRoute a je typ brány používané při konfiguraci ExpressRoute.

Každá virtuální síť může mít pouze jednu bránu virtuální sítě pro každý typ brány. Například můžete mít jednu bránu virtuální sítě, která má typ -GatewayType Vpn a jednu, který má typ -GatewayType ExpressRoute.

## <a name="gateway-skus"></a><a name="gwsku"></a>SKU brány
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Pokud chcete upgradovat bránu na výkonnější bránu SKU, ve většině případů můžete použít rutinu prostředí PowerShell Pro měnit velikost AzVirtualNetworkGateway. To bude fungovat pro upgrady na standardní a vysoce výkonné skutény. Chcete-li však upgradovat na skladovou položku UltraPerformance, budete muset bránu znovu vytvořit. Při opětovném vytvoření brány budou prostojována.

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>Odhadované výkony podle skladové položky brány
V následující tabulce jsou uvedeny typy bran a odhadované výkony. Tato tabulka platí pro model nasazení Resource Manager a pro model nasazení Classic.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Výkon aplikace závisí na několika faktorech, jako je například latence od konce na konec a počet toků provozu, které aplikace otevře. Čísla v tabulce představují horní limit, který aplikace může teoreticky dosáhnout v ideálním prostředí.
>
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>Podsíť brány

Před vytvořením brány ExpressRoute je nutné vytvořit podsíť brány. Podsíť brány obsahuje IP adresy, které virtuální počítače a služby brány virtuální sítě používají. Když vytvoříte bránu virtuální sítě, virtuální počítače brány se nasadí do podsítě brány a nakonfigurují je s požadovaným nastavením brány ExpressRoute. Nikdy nenasazujte nic jiného (například další virtuální počítače) do podsítě brány. Aby fungovala podsíť brány, musí být pojmenována GatewaySubnet. Pojmenování podsítě brány "GatewaySubnet" umožňuje Azure vědět, že se jedná o podsíť pro nasazení virtuálních počítačů a služeb brány virtuální sítě virtuální sítě virtuální sítě.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Při vytváření podsítě brány zadáte počet IP adres, které podsíť obsahuje. IP adresy v podsíti brány jsou přiděleny virtuálním počítačům brány a službám brány. Některé konfigurace vyžadují víc IP adres než jiné. 

Při plánování velikosti podsítě brány se podívejte do dokumentace ke konfiguraci, kterou plánujete vytvořit. Například konfigurace expressroute/VPN gateway vyžaduje větší podsíť brány než většina ostatních konfigurací. Kromě toho můžete chtít, aby vaše podsíť brány obsahuje dostatek IP adres pro případné budoucí další konfigurace. Zatímco můžete vytvořit podsíť brány tak malé jako /29, doporučujeme vytvořit podsíť brány /27 nebo větší (/27, /26 atd.), pokud máte k dispozici adresní prostor k tomu. To bude vyhovovat většině konfigurací.

Následující příklad prostředí Resource Manager PowerShell zobrazuje podsíť brány s názvem GatewaySubnet. Můžete vidět, že zápis CIDR určuje /27, což umožňuje dostatek IP adres pro většinu konfigurací, které v současné době existují.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>Skutě vidoucí brány redundantní zóny

Brány ExpressRoute můžete také nasadit v zónách dostupnosti Azure. To je fyzicky a logicky odděluje do různých zón dostupnosti a chrání místní síťové připojení k Azure před selháními na úrovni zóny.

![Zónově redundantní brána ExpressRoute](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zóny redundantní brány používají konkrétní nové brány su pro bránu ExpressRoute.

* Ergw1Az
* Ergw2Az
* Ergw3Az

Nové virtuální počítače brány také podporují další možnosti nasazení, které nejlépe odpovídají vašim potřebám. Při vytváření brány virtuální sítě pomocí nové brány skum, máte také možnost nasadit bránu v určité zóně. To se označuje jako zonální brána. Když nasadíte zónovou bránu, všechny instance brány jsou nasazeny ve stejné zóně dostupnosti.

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

Brána virtuální sítě ExpressRoute je určena k výměně síťových tras a síťovým přenosům. FastPath je navržen tak, aby zlepšil výkon datové cesty mezi místní sítí a virtuální sítí. Pokud je povoleno, FastPath odešle síťový provoz přímo do virtuálních počítačů ve virtuální síti, obcházet bránu.

Další informace o fastpathu, včetně omezení a požadavků, naleznete [v tématu O fastpathu](about-fastpath.md).

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST API a rutiny prostředí PowerShell
Další technické prostředky a specifické požadavky na syntaxi při použití rozhraní REST API a rutin prostředí PowerShell pro konfigurace brány virtuální sítě najdete na následujících stránkách:

| **Classic** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [ROZHRANÍ API PRO ODPOČINEK](https://msdn.microsoft.com/library/jj154113.aspx) |[ROZHRANÍ API PRO ODPOČINEK](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Další kroky

Další informace o dostupných konfiguracích připojení naleznete v tématu [Přehled expressroute](expressroute-introduction.md).

Další informace o vytváření bran ExpressRoute najdete [v tématu Vytvoření brány virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

Další informace o konfiguraci zónově redundantních bran naleznete [v tématu Vytvoření zóny redundantní brány virtuální sítě](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

Další informace o rychlé cestě naleznete v [tématu O rychlé cestě](about-fastpath.md).