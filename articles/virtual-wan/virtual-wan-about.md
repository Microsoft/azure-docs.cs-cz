---
title: Azure Virtual WAN – Přehled | Microsoft Docs
description: Seznamte se s automatizovaným škálovatelným připojením mezi větevmi, dostupnými oblastmi a partnery služby Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 9ac70252ce7c818ccbdecfd996b9970f011aa967
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77056338"
---
# <a name="about-azure-virtual-wan"></a>Informace o službě Azure Virtual WAN

Azure Virtual WAN je síťová služba, která poskytuje optimalizované a automatizované připojení větví k Azure. Oblasti Azure slouží jako centra, ke kterým se můžete připojit ke svým větvím. Můžete využít páteřní síť Azure a taky připojit větve a využít připojení k virtuální síti. Máme seznam partnerů, kteří podporují automatizaci připojení pomocí sítě VPN Azure Virtual WAN. Další informace najdete v článku věnovaném [virtuálním partnerům a umístěním sítě WAN](virtual-wan-locations-partners.md) .

Azure Virtual WAN přináší dohromady spoustu služeb cloudového připojení Azure, jako je například síť VPN typu Site-to-site, síť VPN uživatele (Point-to-site) a ExpressRoute do jediného provozního rozhraní. Připojení ke službě Azure virtuální sítě se vytváří pomocí připojení k virtuální síti. Umožňuje [globální přenosovou architekturu sítě](virtual-wan-global-transit-network-architecture.md) založenou na klasickém modelu připojení hub, kde cloudová hostovaná síť "centrum" umožňuje přenosné připojení mezi koncovými body, které mohou být distribuovány napříč různými typy "paprsků".

![Diagram služby Virtual WAN](./media/virtual-wan-about/virtualwan1.png)

Tento článek poskytuje rychlý přehled o připojení k síti v Azure Virtual WAN. Virtual WAN nabízí následující výhody:

* **Integrovaná řešení pro připojení v centru a paprskech:** Automatizujte konfiguraci site-to-site a konektivitu mezi místními lokalitami a rozbočovačem Azure.
* **Automatizované vytvoření a konfigurace koncových sítí:** Připojte bez starostí své virtuální sítě a úlohy k rozbočovači Azure.
* **Intuitivní řešení potíží:** V rámci Azure se můžete podívat na koncový tok a pak tyto informace použít k provedení požadovaných akcí.

## <a name="basicstandard"></a>Virtuální sítě WAN úrovně Basic a Standard

Existují dva typy virtuálních sítí WAN: Basic a Standard. V následující tabulce jsou uvedeny dostupné konfigurace pro každý typ.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Postup upgradu virtuální sítě WAN najdete v tématu [upgrade virtuální sítě WAN z úrovně Basic na standard](upgrade-virtual-wan.md).

## <a name="architecture"></a>Architektura

Informace o architektuře Virtual WAN a o tom, jak migrovat na virtuální síť WAN, najdete v následujících článcích:

* [Architektura virtuální sítě WAN](migrate-from-hub-spoke-topology.md)
* [Architektura globální tranzitní sítě](virtual-wan-global-transit-network-architecture.md)

## <a name="resources"></a>Prostředky pro Virtual WAN

Pro konfiguraci virtuální sítě WAN mezi koncovými body vytvořte následující prostředky:

* **virtualWAN:** Prostředek virtualWAN představuje virtuální překrytí vaší sítě Azure a jde o kolekci několika prostředků. Obsahuje odkazy na všechny vaše virtuální rozbočovače, které chcete mít v rámci virtuální sítě WAN. Prostředky Virtual WAN jsou navzájem izolované a nemohou obsahovat společný rozbočovač. Virtuální rozbočovače na virtuální síti WAN mezi sebou vzájemně nekomunikují.

* **Rozbočovač:** Virtuální rozbočovač je virtuální síť spravovaná Microsoftem. Centrum obsahuje různé koncové body služby, aby bylo možné připojení povolit. Z vaší místní sítě (vpnsite) se můžete připojit k VPN Gateway uvnitř virtuálního rozbočovače, připojit ExpressRoute okruhy k virtuálnímu rozbočovači nebo připojit mobilní uživatele k bráně Point-to-site ve virtuálním centru. Rozbočovač je základem vaší sítě v oblasti. V každé oblasti Azure může existovat jen jeden rozbočovač.

  Brána rozbočovače není totéž, co brána virtuální sítě, kterou používáte pro ExpressRoute a VPN Gateway. Například při použití virtuální sítě WAN nevytvoříte připojení typu Site-to-Site z místní lokality přímo k vaší virtuální síti. Místo toho vytvoříte připojení typu Site-to-site k centru. Provoz vždy prochází přes bránu rozbočovače. To znamená, že virtuální sítě nepotřebují své vlastní brány virtuální sítě. Virtual WAN umožňuje vašim virtuálním sítím využívat výhod snadného škálování prostřednictvím virtuálního rozbočovače a jeho brány.

* **Připojení virtuální sítě k rozbočovači:** Prostředek připojení virtuální sítě k rozbočovači se používá k bezproblémovému propojení rozbočovače a virtuální sítě.

* **(Preview) připojení k rozbočovači** – rozbočovače jsou vzájemně připojeni ve virtuální síti WAN. To znamená, že větev, uživatel nebo virtuální síť, která je připojená k místnímu rozbočovači, může komunikovat s jinou větví nebo virtuální sítí pomocí úplné architektury sítě připojených Center. Virtuální sítě se taky můžete připojit v rámci centra, které se přenáší přes virtuální rozbočovač, a také virtuální sítě napříč centrem pomocí architektury připojeného k rozbočovači.

* **Směrovací tabulka rozbočovače:** Můžete vytvořit trasu virtuálního rozbočovače a použít ji ve směrovací tabulce virtuálního rozbočovače. V uvedené tabulce můžete použít více tras.

**Další prostředky virtuální sítě WAN**

  * **Lokalita:** Tento prostředek se používá jenom pro připojení Site-to-site. Prostředek sítě je **vpnsite**. Představuje vaše místní zařízení VPN a jeho nastavení. Pokud využíváte služeb partnera pro Virtual WAN, máte integrované řešení, které automaticky exportuje tyto informace do Azure.

## <a name="connectivity"></a>Typy připojení

Virtuální síť WAN umožňuje následující typy připojení: Site-to-Site VPN, User VPN (Point-to-site) a ExpressRoute.

### <a name="s2s"></a>Připojení VPN typu Site-to-site

![Diagram služby Virtual WAN](./media/virtual-wan-about/virtualwan.png)

Když vytváříte virtuální připojení WAN typu Site-to-site, můžete pracovat s dostupným partnerem. Pokud nechcete použít partnera, můžete nakonfigurovat připojení ručně. Další informace najdete v tématu [vytvoření připojení typu Site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Pracovní postup partnera virtuální sítě WAN

Když pracujete s virtuálním partnerem WAN, je pracovní postup:

1. Řadič zařízení pobočky (VPN/SDWAN) se ověří pomocí [instančního objektu Azure](../active-directory/develop/howto-create-service-principal-portal.md), aby mohl exportovat informace o lokalitách do Azure.
2. Řadič zařízení pobočky (VPN/SDWAN) získá informace o konfiguraci připojení k Azure a aktualizuje místní zařízení. To umožňuje automatizovat stažení konfigurace místního zařízení VPN i jeho úpravu a aktualizaci.
3. Jakmile má zařízení správnou konfiguraci Azure, je možné vytvořit propojení typu Site-to-Site (dva aktivní tunely) v rámci sítě Azure WAN. Azure podporuje IKEv1 i IKEv2. Protokol BGP je volitelný.

#### <a name="partners"></a>Partneři pro připojení typu Site-to-site k virtuální síti WAN

Seznam dostupných partnerů a umístění najdete v článku věnovaném [virtuálním partnerům a umístěním sítě WAN](virtual-wan-locations-partners.md) .

### <a name="uservpn"></a>Připojení uživatele VPN (Point-to-site)

K prostředkům v Azure se můžete připojit přes připojení IPsec/IKE (IKEv2) nebo OpenVPN. Tento typ připojení vyžaduje, aby byl na klientském počítači nakonfigurovaný klient VPN. Další informace najdete v tématu [vytvoření připojení typu Point-to-site](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>Připojení ExpressRoute
ExpressRoute umožňuje připojit místní síť k Azure prostřednictvím privátního připojení. Pokud chcete vytvořit připojení, přečtěte si téma [vytvoření připojení ExpressRoute pomocí virtuální sítě WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Polohy

Informace o umístění naleznete v článku [virtuální partneři a umístění sítě WAN](virtual-wan-locations-partners.md) .

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Další kroky

[Vytvoření připojení typu Site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md)
