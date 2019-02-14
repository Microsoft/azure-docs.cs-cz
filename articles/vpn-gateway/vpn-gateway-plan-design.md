---
title: 'Plánování a návrh pro připojení mezi různými místy: Azure VPN Gateway | Dokumentace Microsoftu'
description: Další informace o VPN Gateway plánování a návrh pro více míst, hybridní a připojení VNet-to-VNet
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/27/2017
ms.author: yushwang
ms.openlocfilehash: 7802061ba09a30ca34ed3804ace846118c5edb9b
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235356"
---
# <a name="planning-and-design-for-vpn-gateway"></a>Plánování a návrh pro VPN Gateway

Plánování a navrhování mezi různými místy a VNet-to-VNet konfigurace může být jednoduché nebo složité, v závislosti na potřebách vaší sítě. Tento článek vás provede základní aspekty plánování a návrhu.

## <a name="planning"></a>Plánování

### <a name="compare"></a>Možnosti připojení mezi různými místy

Pokud chcete se navázat zabezpečené připojení místních lokalit k virtuální síti, je nutné třemi různými způsoby, Uděláte to tak: Site-to-Site, Point-to-Site a ExpressRoute. Porovnejte připojení různých míst, které jsou k dispozici. Vámi zvolené možnosti může záviset na různé aspekty, jako například:

* Jaký druh propustnosti vyžaduje vaše řešení?
* Chcete komunikovat přes veřejný internet prostřednictvím bezpečné VPN, nebo přes privátní připojení?
* Máte k dispozici veřejnou IP adresu?
* Plánujete použít zařízení VPN? Pokud ano, je kompatibilní?
* Připojujete pouze několik počítačů, nebo chcete trvalé připojení pro svůj server?
* Jaký typ brány VPN vyžaduje řešení, které chcete vytvořit?
* Které skladové položky brány byste měli použít?

### <a name="planningtable"></a>Plánovací tabulka

Následující tabulka vám může pomoci se zvolením nejlepší možnosti připojení pro vaše řešení.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="gwsku"></a>SKU brány

[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="wf"></a>Pracovní postup

Následující seznam popisuje běžné pracovní postup pro připojení ke cloudu:

1. Návrh a plánování topologie připojení a vytvořte seznam adresních prostorů pro všechny sítě, kterou chcete připojit.
2. Vytvoření virtuální sítě Azure. 
3. Vytvoření brány VPN pro virtuální síť.
4. Vytvořte a nakonfigurujte připojení k místním sítím nebo jiným virtuálním sítím (podle potřeby).
5. Vytvoření a konfigurace připojení typu Point-to-Site pro bránu Azure VPN (podle potřeby).

## <a name="design"></a>návrh
### <a name="topologies"></a>Topologie připojení

Začněte zobrazením diagramech [informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md) článku. Tento článek obsahuje základní diagramy, modely nasazení pro každou topologii a dostupné nástroje, které můžete použít k nasazení vaší konfigurace.

### <a name="designbasics"></a>Základy návrhu

Následující části popisují základní brána sítě VPN. 

#### <a name="servicelimits"></a>Síťová omezení služby

Projděte si tabulky zobrazíte [síťové služby omezení](../azure-subscription-service-limits.md#networking-limits). Výše uvedený může mít vliv na návrh.

#### <a name="subnets"></a>O podsítě

Při vytváření připojení, musíte zvážit rozsahů podsítě. Nemůže mít překrývající se rozsahy adres podsítě. Překrývající se podsítě je, když jedna virtuální síť nebo v místním umístění obsahuje stejné adresní prostor, který obsahuje jiné umístění. To znamená, že vám technici sítě pro vaše místní sítě a vyčlenit rozsah můžete použít pro Azure IP adresování prostoru a podsítě. Potřebujete adresní prostor, který se nepoužívá v místní síti.

Jak se vyhnout překrývající se podsítí je také důležité, když pracujete s připojení VNet-to-VNet. Pokud překrývat s podsítěmi a IP adresu existuje v odesílání i cílové virtuální sítě, selhání připojení VNet-to-VNet. Azure nelze směrování dat k druhé virtuální sítě, protože cílová adresa je součástí odesílání virtuální sítě.

Brány VPN vyžadují konkrétní podsíť nazývanou podsíť brány. Pro správné fungování všech podsítí brány je nutné, aby měly název GatewaySubnet. Se ujistěte, že název podsítě brány jiný název a podsíti brány nenasazujte virtuální počítače nebo cokoli jiného. Zobrazit [podsítě brány](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>O místní síťové brány

Brána místní sítě obvykle odkazuje na vaše místní umístění. V modelu nasazení classic bránu místní sítě se označuje jako místní síťovou lokalitu. Když konfigurujete bránu místní sítě, pojmenujte ho, zadejte veřejnou IP adresu místního zařízení VPN a zadáte předpony adres, které jsou v místním umístění. Azure zjistí pro síťový provoz předpony cílových adres consults konfiguraci, kterou jste zadali pro bránu místní sítě a směruje pakety odpovídajícím způsobem. Podle potřeby můžete upravit předpony adresy. Další informace najdete v tématu [brány místní sítě](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="gwtype"></a>O typech brány

Vyberte správný typ brány pro vaši topologii je velmi důležité. Pokud vyberete nesprávného typu, brána nebude správně fungovat. Typ brány určuje, jak se brána samotná připojuje, a jedná se o požadované nastavení pro model nasazení Resource Manager.

Typy brány jsou:

* Vpn
* ExpressRoute

#### <a name="connectiontype"></a>O typech připojení

Každá konfigurace vyžaduje určitý typ připojení. Typy připojení jsou:

* Protokol IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="vpntype"></a>Typy sítě VPN

Každá konfigurace vyžaduje určitý typ sítě VPN. Pokud kombinujete dvě konfigurace, například vytváříte-li připojení typu Site-to-Site a Point-to-Site ke stejné virtuální síti, musíte použít typ sítě VPN, který splňuje požadavky obou připojení.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Následující tabulky popisují typ sítě VPN, protože je namapován na každou konfiguraci připojení. Ujistěte se, že typ sítě VPN pro bránu odpovídá konfiguraci, kterou chcete vytvořit. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>Zařízení VPN pro připojení Site-to-Site

Konfigurace připojení Site-to-Site, bez ohledu na model nasazení, potřebujete následující položky:

* Zařízení VPN, který je kompatibilní s bránami Azure VPN Gateway
* Veřejná IP adresa protokolu IPv4 adresa, který není za službou NAT

Musíte mít prostředí pro konfiguraci zařízení VPN, nebo si nechejte, která můžete nakonfigurovat zařízení za vás.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="forcedtunnel"></a>Vezměte v úvahu vynucené tunelové propojení směrování

U většiny konfigurací můžete nakonfigurovat vynucené tunelování. Vynucené tunelování umožňuje přesměrování nebo "Vynutit" veškerý provoz směřující na Internet zpět do místního umístění prostřednictvím tunelu VPN typu Site-to-Site pro kontrolu a auditování. Toto je důležité zabezpečení požadavek pro většinu podnikových IT zásady. 

Bez vynucené tunelování, se internetový provoz z virtuálních počítačů v Azure přechod z Azure síťovou infrastrukturu přímo na Internetu, bez možnosti a umožňuje tak kontrolovat nebo auditování provozu vždy. Neoprávněný přístup k Internetu může potenciálně vést k zpřístupnění informací nebo jiných druhů porušení zabezpečení.

V obou modelů nasazení a s využitím různých nástrojů, je možné nakonfigurovat vynucené tunelování připojení. Další informace najdete v tématu [konfigurace vynuceného tunelování](vpn-gateway-forced-tunneling-rm.md).

**Vynucené tunelování diagramu**

![Azure VPN Gateway vynucené tunelování diagramu](./media/vpn-gateway-plan-design/forced-tunneling-diagram.png)

## <a name="next-steps"></a>Další postup

Zobrazit [VPN Gateway – nejčastější dotazy](vpn-gateway-vpn-faq.md) a [informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md) články pro víc informací, které vám pomůžou s návrhu.

Další informace o nastavení konkrétního brány najdete v tématu [o nastavení služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).
