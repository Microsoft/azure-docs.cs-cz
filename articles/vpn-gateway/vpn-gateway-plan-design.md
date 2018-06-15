---
title: 'Plánování a návrhu pro připojení mezi různými místy: Azure VPN Gateway | Microsoft Docs'
description: Další informace o službě VPN Gateway plánování a návrh mezi různými místy, hybridního i připojení VNet-to-VNet
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: d5aaab83-4e74-4484-8bf0-cc465811e757
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2017
ms.author: cherylmc
ms.openlocfilehash: 0ebc3ef4a64432e993dd6ed69766bb64544fe433
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884381"
---
# <a name="planning-and-design-for-vpn-gateway"></a>Plánování a návrh pro VPN Gateway

Plánování a návrhu mezi různými místy a VNet-to-VNet konfigurace může být jednoduchý nebo složitá, v závislosti na vašich sítí. Tento článek vás provede základní aspekty plánování a návrhu.

## <a name="planning"></a>Plánování

### <a name="compare"></a>Možnosti připojení mezi různými místy

Pokud se chcete připojit místními servery bezpečně k virtuální síti, máte tři různé způsoby, jak udělat: Site-to-Site, Point-to-Site a ExpressRoute. Porovnejte připojení různých mezi různými místy, které jsou k dispozici. Možnosti, kterou zvolíte může záviset na různé aspekty, jako například:

* Jaký druh propustnosti vyžaduje vaše řešení?
* Chcete komunikovat přes veřejný internet prostřednictvím bezpečné VPN, nebo přes privátní připojení?
* Máte k dispozici veřejnou IP adresu?
* Plánujete použít zařízení VPN? Pokud ano, je kompatibilní?
* Připojujete pouze několik počítačů, nebo chcete trvalé připojení pro svůj server?
* Jaký typ brány VPN vyžaduje řešení, které chcete vytvořit?
* Které skladová položka brány mám použít?

### <a name="planningtable"></a>Plánovací tabulka

V následující tabulce vám pomohou rozhodnout se nejlepší možnosti připojení pro vaše řešení.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="gwsku"></a>SKU brány

[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="wf"></a>Pracovní postup

Následující seznam popisuje běžné pracovní postup pro připojení k síti cloudu:

1. Návrhu a plánování topologie připojení a vytvořte seznam adresních prostorů pro všechny sítě, které se chcete připojit.
2. Vytvoření virtuální sítě Azure. 
3. Vytvořte bránu VPN virtuální sítě.
4. Vytvořit a nakonfigurovat připojení k místní sítě nebo jiných virtuálních sítí (podle potřeby).
5. Vytvořit a nakonfigurovat připojení Point-to-Site pro bránu Azure VPN (podle potřeby).

## <a name="design"></a>Návrh
### <a name="topologies"></a>Topologie připojení ke službě

Začít hledáním v diagramech [o službě VPN Gateway](vpn-gateway-about-vpngateways.md) článku. Článek obsahuje základní diagramy, modely nasazení pro každý topologie a nástroje pro nasazení k dispozici, které můžete použít k nasazení vaší konfigurace.

### <a name="designbasics"></a>Základní informace o návrhu

Následující části popisují základy brány VPN. 

#### <a name="servicelimits"></a>Omezení služby sítě

Procházení tabulky, které chcete zobrazit [sítě služby omezení](../azure-subscription-service-limits.md#networking-limits). Omezení uvedené může mít vliv na návrh vašeho.

#### <a name="subnets"></a>O podsítě

Při vytváření připojení, musíte zvážit vaší rozsahy podsítě. Nemůže mít překrývající se rozsahy adres podsítě. Překrývající se podsítí je stejné adresní prostor, který obsahuje jiné umístění obsahuje jednu virtuální síť nebo místní umístění. To znamená, je nutné, aby technici sítě pro vaše místní sítě do vyčlenit oblasti, budete moci použít pro Azure na IP adresování místa nebo podsítě. Je nutné adresní prostor, který se nepoužívá v místní síti.

Zamezení překrývající se podsítí je také důležité, když pracujete s připojení VNet-to-VNet. Pokud podsítě překrývajících se a IP adresu v odesílání i cílové virtuální sítě existuje, připojení VNet-to-VNet se nezdaří. Azure nelze směrovat data do jiné virtuální sítě, protože cílová adresa je součástí odesílání virtuální sítě.

Brány sítě VPN vyžaduje konkrétní podsíť s názvem podsíť brány. Pro správné fungování všech podsítí brány je nutné, aby měly název GatewaySubnet. Ujistěte se, aby název podsítě brány jiný název a podsíti brány nenasazujte virtuální počítače nebo cokoliv jiného. V tématu [podsítě brány](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>O brány místní sítě

Brána místní sítě obvykle odkazuje na vaše místní umístění. V modelu nasazení classic bránu místní sítě se označuje jako místní síťové lokality. Při konfiguraci brány místní sítě, zadejte jeho název, zadejte veřejnou IP adresu místního zařízení VPN a zadáte předpony adres, které se nacházejí v místní umístění. Azure zjistí předpony cílových adres pro síťový provoz, zajímají konfiguraci, která jste zadali pro bránu místní sítě a směruje pakety odpovídajícím způsobem. Předpony adres můžete upravit podle potřeby. Další informace najdete v tématu [brány místní sítě](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="gwtype"></a>O typech brány

Výběr typu správné brány pro vaše topologie je velmi důležité. Pokud vyberete chybný typ, brána nebude fungovat správně. Typ brány určuje, jak se brána samotná připojuje, a jedná se o požadované nastavení pro model nasazení Resource Manager.

Typy brány jsou:

* Vpn
* ExpressRoute

#### <a name="connectiontype"></a>O typech připojení

Každá konfigurace vyžaduje určitý typ připojení. Typy připojení jsou:

* Protokol IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="vpntype"></a>O typy sítě VPN

Každá konfigurace vyžaduje určitý typ sítě VPN. Pokud kombinujete dvě konfigurace, například vytváříte-li připojení typu Site-to-Site a Point-to-Site ke stejné virtuální síti, musíte použít typ sítě VPN, který splňuje požadavky obou připojení.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Následující tabulky popisují typ sítě VPN, jak se mapuje na každé konfiguraci připojení. Ujistěte se, že typ sítě VPN pro bránu odpovídá konfiguraci, kterou chcete vytvořit. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>Zařízení VPN pro připojení Site-to-Site

Konfigurace připojení Site-to-Site, bez ohledu na modelu nasazení, potřebujete následující položky:

* Zařízení VPN, který je kompatibilní s Azure VPN Gateway
* Veřejnou adresu IPv4 IP, který není za zařízení NAT

Budete muset prostředí konfiguraci zařízení VPN nebo někdo, která můžete nakonfigurovat zařízení pro vás k dispozici.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="forcedtunnel"></a>Vezměte v úvahu vynucené tunelového propojení směrování

Pro většinu konfiguraci můžete konfigurovat vynucené tunelování. Vynucené tunelování vám umožní přesměrování nebo "Vynutit" veškerý provoz vázaný na Internet zpět na místní umístění prostřednictvím tunelu Site-to-Site VPN pro kontrolu a auditování. Požadavek kritické zabezpečení pro většinu organizace IT zásad. 

Bez vynucené tunelování, se internetový provoz z virtuálních počítačů v Azure procházení od Azure síťové infrastruktury přímo se k Internetu, bez možnosti a umožní vám na svoji provoz vždy. Neoprávněný přístup k Internetu může potenciálně vést k informacím nebo jiné typy narušení zabezpečení.

V obou modelech nasazení a pomocí jiných nástrojů se dá konfigurovat vynucené tunelové připojení. Další informace najdete v tématu [konfigurace vynuceného tunelování](vpn-gateway-forced-tunneling-rm.md).

**Vynutit tunelové diagram**

![Vynutit tunelové diagram služby Azure VPN Gateway](./media/vpn-gateway-plan-design/forced-tunneling-diagram.png)

## <a name="next-steps"></a>Další kroky

Najdete v článku [VPN Gateway – nejčastější dotazy](vpn-gateway-vpn-faq.md) a [o službě VPN Gateway](vpn-gateway-about-vpngateways.md) články pro další informace vám pomohou při návrhu.

Další informace o nastavení konkrétní brány najdete v tématu [o nastavení brány sítě VPN](vpn-gateway-about-vpn-gateway-settings.md).