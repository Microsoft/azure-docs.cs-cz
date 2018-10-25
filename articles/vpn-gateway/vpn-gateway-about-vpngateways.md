---
title: Azure VPN Gateway | Microsoft Docs
description: Zjistěte, co je brána VPN a jak se pomocí ní můžete připojit k virtuálním sítím Azure. Zahrnuje řešení IPSec/IKE typu Site-to-Site mezi místními sítěmi a IPSec/IKE typu VNet-to-VNet a sítě VPN typu Point-to-Site.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 10/19/2018
ms.author: cherylmc
ms.openlocfilehash: d35e7cfbf55d0dc53214bbb7b1153c3270c611c2
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471318"
---
# <a name="what-is-vpn-gateway"></a>Co je VPN Gateway?

Brána VPN je specifický typ brány virtuální sítě, která se používá k posílání šifrovaného provozu mezi virtuální sítí Azure a místním umístěním přes veřejný internet. Bránu VPN můžete použít také k posílání šifrovaného provozu mezi virtuálními sítěmi Azure po síti Microsoftu. Každá virtuální síť může mít pouze jednu bránu VPN. K jedné bráně VPN však můžete vytvořit několik připojení. Když vytvoříte několik připojení ke stejné bráně VPN, všechny tunely VPN sdílejí dostupnou šířku pásma.

## <a name="whatis"></a>Co je brána virtuální sítě?

Bránu virtuální sítě tvoří nejméně dva virtuální počítače nasazené v konkrétní podsíti, kterou si vytvoříte. Tato brána se označuje jako *podsíť brány*. Virtuální počítače, které se nachází v podsíti brány, se vytvoří při vytvoření brány virtuální sítě. Virtuální počítače brány virtuální sítě jsou nakonfigurovány tak, aby obsahovaly tabulky směrování a služby brány, které jsou pro bránu specifické. Virtuální počítače, které jsou součástí brány virtuální sítě, není možné konfigurovat přímo a do podsítě brány byste nikdy neměli nasazovat další prostředky.

Vytvoření vytváření brány virtuální sítě může trvat až 45 minut. Při vytvoření brány virtuální sítě se virtuální počítače brány nasadí do podsítě brány a nakonfigurují s použitím nastavení, která zadáte. Jedním z nastavení, která konfigurujete, je typ brány. Typ brány „vpn“ určuje, že typem vytvořené brány virtuální sítě je brána VPN. Po vytvoření brány VPN můžete vytvořit tunelové propojení IPsec/IKE mezi touto bránou VPN a jinou bránou VPN (VNet-to-VNet) nebo tunelové propojení IPsec/IKE mezi místními sítěmi mezi bránou VPN a místním zařízením VPN (Site-to-Site). Můžete vytvořit také připojení VPN typu Point-to-Site (připojení VPN přes protokol IKEv2 nebo SSTP), které umožňuje připojení k virtuální síti ze vzdáleného umístění, například z konference nebo z domova.

## <a name="configuring"></a>Konfigurace služby VPN Gateway

Připojení brány VPN se spoléhá na několik prostředků nakonfigurovaných se specifickými nastaveními. Většinu prostředků je možné nakonfigurovat jednotlivě, nicméně některé prostředky je potřeba konfigurovat v určitém pořadí.

### <a name="settings"></a>Nastavení

Nastavení, která jste pro jednotlivé zdroje zvolili, jsou pro vytvoření úspěšného připojení zásadní. Informace o jednotlivých prostředcích a nastaveních služby VPN Gateway najdete v tématu [Informace o nastavení služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md). Tento článek obsahuje informace, které vám pomůžou pochopit typy bran, skladové položky bran, typy sítí VPN, typy připojení, podsítě brány, místní síťové brány a různá další nastavení prostředků, o kterých možná uvažujete.

### <a name="tools"></a>Nástroje pro nasazení

Prostředky můžete začít vytvářet a konfigurovat pomocí konfiguračního nástroje, jako je například Azure Portal. Později se můžete rozhodnout používat ke konfiguraci dalších prostředků nebo úpravám stávajících prostředků jiný nástroj, třeba PowerShell. V současné době nelze konfigurovat všechny prostředky a nastavení prostředků pomocí webu Azure Portal. Pokyny v článcích pro každou topologii připojení určují, kdy je zapotřebí specifický konfigurační nástroj. 

### <a name="models"></a>Model nasazení

Pro Azure aktuálně existují dva modely nasazení. Kroky při konfiguraci brány VPN se budou lišit v závislosti na modelu nasazení, který jste použili k vytvoření virtuální sítě. Například pokud jste virtuální síť vytvořili pomocí modelu nasazení Classic, budete při vytváření a konfiguraci brány VPN postupovat podle pokynů pro model nasazení Classic. Další informace o modelech nasazení najdete v tématu [Pochopení modelů nasazení Resource Manager a Classic](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="planningtable"></a>Plánovací tabulka

Následující tabulka vám může pomoci se zvolením nejlepší možnosti připojení pro vaše řešení.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## <a name="gwsku"></a>SKU brány

Při vytváření brány virtuální sítě zadáváte jednotku SKU brány, kterou chcete použít. Vyberte jednotku SKU, která splňuje vaše požadavky na základě typů úloh, propustnosti, funkcí a SLA. Další informace o SKU brány, včetně podporovaných funkcí, produkce, vývoje a testování a konfiguračních postupů, najdete v tématu [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="benchmark"></a>SKU brány podle tunelu, připojení a propustnosti

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="diagrams"></a>Diagramy topologie připojení

Je důležité vědět, že pro připojení brány VPN jsou dostupné různé konfigurace. Musíte určit, která konfigurace bude nejlépe vyhovovat vašim potřebám. V následujících oddílech si můžete prohlédnout diagramy topologie a informace o následujících připojení brány sítě VPN. Následující oddíly obsahují tabulky, které uvádějí:

* dostupný model nasazení,
* dostupné konfigurační nástroje,
* odkazy na příslušný článek, pokud existuje.

Diagramy a popisy vám pomohou s výběrem topologie připojení, která bude odpovídat vašim požadavkům. Diagramy popisují základní topologie, ale je možné vytvořit komplexnější konfigurace s použitím diagramů jako vodítek.

## <a name="s2smulti"></a>Site-to-Site a Multi-Site (tunel VPN IPsec/IKE)

### <a name="S2S"></a>Site-to-Site

Připojení brány VPN typu Site-to-Site (S2S) je připojení přes tunel VPN prostřednictvím protokolu IPsec/IKE (IKEv1 nebo IKEv2). Připojení S2S můžete použít pro konfigurace mezi různými místy a pro hybridní konfigurace. Připojení typu Site-to-Site vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu a není umístěné za překladem adres (NAT). Informace o výběru zařízení VPN najdete v tématu [Nejčastější dotazy k branám VPN – Zařízení VPN](vpn-gateway-vpn-faq.md#s2s).

![Příklad propojení Site-to-Site pomocí Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>Multi-Site

Tento typ připojení je variací připojení Site-to-Site. Z brány virtuální sítě vytvoříte několik připojení VPN, obvykle pro připojení k několika místním lokalitám. Při práci s několika připojeními je nutné použít typ sítě VPN RouteBased (při práci s klasickými virtuálními sítěmi se označuje jako dynamická brána). Vzhledem k tomu, že virtuální síť může mít jenom jednu bránu virtuální sítě, všechna připojení prostřednictvím brány sdílejí dostupnou šířku pásma. Tento typ připojení se často označuje jako připojení „s více lokalitami“ (multi-site).

![Příklad propojení Multi-Site pomocí Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modely nasazení a metody pro Site-to-Site a Multi-Site

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>Point-to-Site (VPN přes IKEv2 nebo SSTP)

Připojení brány VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení P2S se vytvoří jeho zahájením z klientského počítače. Toto řešení je užitečné pro osoby pracující z domova, které se chtějí k virtuálním sítím Azure připojit ze vzdáleného umístění, například z domova nebo z místa konání konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN S2S, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti.

Na rozdíl od připojení S2S nevyžadují připojení P2S místní veřejnou IP adresu ani zařízení VPN. Připojení typu P2S je možné použít s připojeními typu S2S prostřednictvím stejné brány VPN za předpokladu, že všechny požadavky na konfiguraci obou připojení jsou kompatibilní. Další informace o připojení Point-to-Site najdete v tématu věnovaném [síti VPN typu Point-to-Site](point-to-site-about.md).


![Příklad propojení Point-to-Site pomocí Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Modely a metody nasazení pro P2S

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="V2V"></a>Připojení typu VNet-to-VNet (tunel VPN IPsec/IKE)

Propojení virtuální sítě s jinou virtuální sítí (VNet-to-VNet) je podobné propojení virtuální sítě s místním serverem. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE. Dokonce je možné kombinovat komunikaci typu VNet-to-VNet s konfiguracemi připojení více lokalit. Díky tomu je možné vytvářet topologie sítí, ve kterých se používá propojování více míst i propojování virtuálních sítí.

Virtuální sítě, které propojujete, můžou být:

* v jedné nebo několika oblastech,
* v jednom nebo několika předplatných, 
* v jednom nebo několika modelech nasazení.

![Příklad propojení VNet-to-VNet pomocí Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Připojení mezi různými modely nasazení

Azure v současné době nabízí dva modely nasazení: Classic a Resource Manager. Pokud již Azure nějakou dobu používáte, pravděpodobně vaše virtuální počítače a role instancí Azure fungují ve virtuální síti Classic. Vaše novější virtuální počítače a role instancí však mohou používat virtuální síť vytvořenou v nástroji Resource Manager. Můžete vytvořit připojení mezi virtuálními sítěmi umožňující prostředkům v jedné virtuální síti přímo komunikovat s prostředky v jiné.

### <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

Pokud virtuální síť splňuje určité požadavky, je možné k vytvoření připojení využít metodu VNet peering. VNet peering nepoužívá bránu virtuální sítě. Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modely nasazení a metody pro VNet-to-VNet

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute (soukromé připojení)

ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 a CRM Online. Co se týká připojení, může se jednat o síť typu any-to-any (IP VPN), síť Ethernet typu point-to-point nebo virtuální křížové připojení prostřednictvím poskytovatele připojení ve společném umístění.

Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. To dovoluje připojením ExpressRoute poskytovat větší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes internet.

Připojení ExpressRoute využívá jako součást požadované konfigurace bránu virtuální sítě. U připojení ExpressRoute je brána virtuální sítě nakonfigurovaná s typem ExpressRoute (a ne Vpn). I když se provoz přenášený přes okruh ExpressRoute ve výchozím nastavení nešifruje, je možné vytvořit řešení, které vám umožní přes okruh ExpressRoute posílat šifrovaný provoz. Další informace o ExpressRoute najdete v [Technickém přehledu ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="coexisting"></a>Současně existující připojení typu Site-to-Site a ExpressRoute

ExpressRoute je vyhrazené soukromé připojení ke službám Microsoftu, včetně Azure, z vaší sítě WAN (nikoli prostřednictvím veřejného internetu). Provoz VPN typu Site-to-Site je přenášen zašifrovaně prostřednictvím veřejného internetu. Možnost konfigurace VPN typu Site-to-Site a připojení ExpressRoute pro stejnou virtuální síť má několik výhod.

Můžete nakonfigurovat síť VPN typu Site-to-Site jako zabezpečenou cestu převzetí služeb při selhání pro ExpressRoute, nebo použít VPN typu Site-to-Site pro připojení k webům, které nejsou součástí vaší sítě, ale jsou připojené prostřednictvím ExpressRoute. Tato konfigurace vyžaduje dvě brány virtuální sítě pro stejnou virtuální síť, jednu typu Vpn a druhou typu ExpressRoute.

![Příklad současné existence ExpressRoute a VPN Gateway](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Modely a metody nasazení pro S2S a ExpressRoute existují současně

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Ceny

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Další informace o skladových jednotkách (SKU) brány pro službu VPN Gateway najdete v tématu [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="faq"></a>Nejčastější dotazy

Nejčastější dotazy týkající se služby VPN Gateway najdete v tématu [Nejčastější dotazy ke službě VPN Gateway](vpn-gateway-vpn-faq.md).

## <a name="next-steps"></a>Další kroky

- Plánování konfigurace brány VPN. Viz [Plánování a návrh pro VPN Gateway](vpn-gateway-plan-design.md).
- Další informace najdete v tématu věnovaném [nejčastějším dotazům k VPN Gateway](vpn-gateway-vpn-faq.md).
- Přečtěte si téma [Předplatné a omezení služeb](../azure-subscription-service-limits.md#networking-limits).
- Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
