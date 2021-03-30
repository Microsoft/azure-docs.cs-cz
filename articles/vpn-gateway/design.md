---
title: O návrhu Azure VPN Gateway
description: Přečtěte si o způsobech návrhu topologie brány VPN pro připojení k virtuálním sítím Azure.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 61732f66aef58f5a9edcb9e095782e19e8aaffdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91397211"
---
# <a name="vpn-gateway-design"></a>VPN Gateway návrh

Je důležité vědět, že pro připojení brány VPN jsou dostupné různé konfigurace. Musíte určit, která konfigurace bude nejlépe vyhovovat vašim potřebám. V níže uvedených částech můžete zobrazit informace o návrhu a diagramy topologie pro následující připojení brány VPN. Diagramy a popisy vám pomohou s výběrem topologie připojení, která bude odpovídat vašim požadavkům. Diagramy znázorňují hlavní topologie standardních hodnot, ale je možné vytvořit složitější konfigurace s použitím diagramů jako pokynů.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>Site-to-Site a Multi-Site (tunel VPN IPsec/IKE)

### <a name="site-to-site"></a><a name="S2S"></a>Site-to-site

Připojení brány VPN typu Site-to-Site (S2S) je připojení přes tunel VPN prostřednictvím protokolu IPsec/IKE (IKEv1 nebo IKEv2). Připojení S2S můžete použít pro konfigurace mezi různými místy a pro hybridní konfigurace. Připojení S2S vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Informace o výběru zařízení VPN najdete v tématu [Nejčastější dotazy k branám VPN – Zařízení VPN](vpn-gateway-vpn-faq.md#s2s).

![Příklad propojení Site-to-Site pomocí Azure VPN Gateway](./media/design/vpngateway-site-to-site-connection-diagram.png)

V aktivním pohotovostním režimu můžete VPN Gateway nakonfigurovat pomocí jedné veřejné IP adresy nebo v režimu aktivní-aktivní pomocí dvou veřejných IP adres. V aktivním pohotovostním režimu je jeden tunel IPsec aktivní a druhý tunel je v pohotovostním režimu. V tomto nastavení přenos toků přes aktivní tunel a pokud s tímto tunelem dojde k nějakému problému, přepíná přenos do úsporného tunelu. Nastavení VPN Gateway v režimu aktivní-aktivní se *doporučuje* , protože tunely IPSec jsou současně aktivní a data se přenášejí mezi tunely současně. Další výhodou režimu aktivní-aktivní je to, že se zákazníci setkávají s vyšší propustností.

### <a name="multi-site"></a><a name="Multi"></a>Více lokalit

Tento typ připojení je variací připojení Site-to-Site. Z brány virtuální sítě vytvoříte několik připojení VPN, obvykle pro připojení k několika místním lokalitám. Při práci s několika připojeními je nutné použít typ sítě VPN RouteBased (při práci s klasickými virtuálními sítěmi se označuje jako dynamická brána). Vzhledem k tomu, že virtuální síť může mít jenom jednu bránu virtuální sítě, všechna připojení prostřednictvím brány sdílejí dostupnou šířku pásma. Tento typ připojení se často označuje jako připojení „s více lokalitami“ (multi-site).

![Příklad propojení Multi-Site pomocí Azure VPN Gateway](./media/design/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modely nasazení a metody pro Site-to-Site a Multi-Site

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>SÍŤ VPN typu Point-to-site

Připojení brány VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení P2S se vytvoří jeho zahájením z klientského počítače. Toto řešení je užitečné pro osoby pracující z domova, které se chtějí k virtuálním sítím Azure připojit ze vzdáleného umístění, například z domova nebo z místa konání konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN S2S, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti.

Na rozdíl od připojení S2S nevyžadují připojení P2S místní veřejnou IP adresu ani zařízení VPN. Připojení typu P2S je možné použít s připojeními typu S2S prostřednictvím stejné brány VPN za předpokladu, že všechny požadavky na konfiguraci obou připojení jsou kompatibilní. Další informace o připojení Point-to-Site najdete v tématu věnovaném [síti VPN typu Point-to-Site](point-to-site-about.md).

![Příklad propojení Point-to-Site pomocí Azure VPN Gateway](./media/design/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Modely a metody nasazení pro P2S

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>Připojení typu VNet-to-VNet (tunel VPN IPsec/IKE)

Propojení virtuální sítě s jinou virtuální sítí (VNet-to-VNet) je podobné propojení virtuální sítě s místním serverem. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE. Dokonce je možné kombinovat komunikaci typu VNet-to-VNet s konfiguracemi připojení více lokalit. Díky tomu je možné vytvářet topologie sítí, ve kterých se používá propojování více míst i propojování virtuálních sítí.

Virtuální sítě, které propojujete, můžou být:

* v jedné nebo několika oblastech,
* v jednom nebo několika předplatných, 
* v jednom nebo několika modelech nasazení.

![Příklad propojení VNet-to-VNet pomocí Azure VPN Gateway](./media/design/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Připojení mezi různými modely nasazení

Azure v současné době nabízí dva modely nasazení: Classic a Resource Manager. Pokud již Azure nějakou dobu používáte, pravděpodobně vaše virtuální počítače a role instancí Azure fungují ve virtuální síti Classic. Vaše novější virtuální počítače a role instancí však mohou používat virtuální síť vytvořenou v nástroji Resource Manager. Můžete vytvořit připojení mezi virtuálními sítěmi umožňující prostředkům v jedné virtuální síti přímo komunikovat s prostředky v jiné.

### <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

Pokud virtuální síť splňuje určité požadavky, je možné k vytvoření připojení využít metodu VNet peering. VNet peering nepoužívá bránu virtuální sítě. Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modely nasazení a metody pro VNet-to-VNet

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute (soukromé připojení)

ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute můžete navázat připojení ke cloudovým službám Microsoftu, jako jsou Microsoft Azure, Microsoft 365 a CRM Online. Připojení může být ze sítě mezi libovolnými body (IP VPN), Ethernetové sítě mezi dvěma body nebo virtuálního křížového připojení přes poskytovatele připojení v kolokačním zařízení.

Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. To dovoluje připojením ExpressRoute poskytovat větší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes internet.

Připojení ExpressRoute využívá jako součást požadované konfigurace bránu virtuální sítě. U připojení ExpressRoute je brána virtuální sítě nakonfigurovaná s typem ExpressRoute (a ne Vpn). I když se provoz přenášený přes okruh ExpressRoute ve výchozím nastavení nešifruje, je možné vytvořit řešení, které vám umožní přes okruh ExpressRoute posílat šifrovaný provoz. Další informace o ExpressRoute najdete v [Technickém přehledu ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>Současně existující připojení typu Site-to-Site a ExpressRoute

ExpressRoute je vyhrazené soukromé připojení ke službám Microsoftu, včetně Azure, z vaší sítě WAN (nikoli prostřednictvím veřejného internetu). Provoz VPN typu Site-to-Site je přenášen zašifrovaně prostřednictvím veřejného internetu. Možnost konfigurace VPN typu Site-to-Site a připojení ExpressRoute pro stejnou virtuální síť má několik výhod.

Můžete nakonfigurovat síť VPN typu Site-to-Site jako zabezpečenou cestu převzetí služeb při selhání pro ExpressRoute, nebo použít VPN typu Site-to-Site pro připojení k webům, které nejsou součástí vaší sítě, ale jsou připojené prostřednictvím ExpressRoute. Tato konfigurace vyžaduje dvě brány virtuální sítě pro stejnou virtuální síť, jednu typu Vpn a druhou typu ExpressRoute.

![Příklad současné existence ExpressRoute a VPN Gateway](./media/design/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Modely a metody nasazení pro S2S a ExpressRoute existují současně

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="highly-available-connections"></a><a name="highly-available"></a>Připojení s vysokou dostupností

Informace o plánování a návrhu pro připojení s vysokou dostupností najdete v tématu s [vysokou dostupností připojení](vpn-gateway-highlyavailable.md).

## <a name="next-steps"></a>Další kroky

* Další informace najdete v tématu věnovaném [nejčastějším dotazům k VPN Gateway](vpn-gateway-vpn-faq.md).

* Přečtěte si další informace o [nastavení konfigurace VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).

* Informace o protokolu BGP VPN Gateway najdete v tématu [o protokolu BGP](vpn-gateway-bgp-overview.md).

* Přečtěte si téma [Předplatné a omezení služeb](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

* Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
