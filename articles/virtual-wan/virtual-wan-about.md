---
title: Azure Virtual WAN – Přehled | Microsoft Docs
description: Seznamte se s automatizovaným škálovatelným připojením mezi větvemi virtuální sítě WAN, dostupnými oblastmi a partnery.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 9ac70252ce7c818ccbdecfd996b9970f011aa967
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241413"
---
# <a name="about-azure-virtual-wan"></a>O virtuální mašaleu Azure

Azure Virtual WAN je síťová služba, která poskytuje optimalizované a automatizované připojení poboček do Azure a jeho prostřednictvím. Oblasti Azure slouží jako centra, ke kterým můžete připojit pobočky. Páteřní síť Azure můžete využít k propojení větví a připojení k síti mezi větvemi. Máme seznam partnerů, kteří podporují automatizaci připojení pomocí Azure Virtual WAN VPN. Další informace naleznete v článku [virtuální chabé sítě partnerů a umístění.](virtual-wan-locations-partners.md)

Azure Virtual WAN sdružuje mnoho cloudových připojení Azure služby, jako je site-to-site VPN, User VPN (point-to-site) a ExpressRoute do jednoho provozního rozhraní. Připojení k virtuálním sítím Azure se navíjí pomocí připojení k virtuální síti. Umožňuje [architekturu globální tranzitní sítě](virtual-wan-global-transit-network-architecture.md) založenou na klasickém modelu připojení rozbočovače a paprsku, kde centrum sítě hostované v cloudu umožňuje přenosité připojení mezi koncovými body, které mohou být distribuovány mezi různými typy "paprsků".

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan1.png)

Tento článek poskytuje rychlý přehled o připojení k síti ve virtuální síti Azure. Virtual WAN nabízí následující výhody:

* **Integrovaná řešení konektivity v rozbočovači a paprsku:** Automatizujte konfiguraci mezi jednotlivými lokalitami a připojení mezi místními weby a centrem Azure.
* **Automatizované vytvoření a konfigurace koncových sítí:** Připojte bez starostí své virtuální sítě a úlohy k rozbočovači Azure.
* **Intuitivní řešení problémů:** Můžete zobrazit tok od konce v rámci Azure a pak tyto informace použít k provedení požadovaných akcí.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Základní a standardní virtuální wany

Existují dva typy virtuálních souborů WAN: Basic a Standard. V následující tabulce jsou uvedeny dostupné konfigurace pro každý typ.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Postup upgradu virtuální sítě WAN naleznete [v tématu Upgrade virtuální sítě WAN ze základní na standardní](upgrade-virtual-wan.md).

## <a name="architecture"></a><a name="architecture"></a>Architektura

Informace o architektuře virtuální sítě WAN a o migraci do virtuální sítě WAN naleznete v následujících článcích:

* [Architektura virtuální sítě WAN](migrate-from-hub-spoke-topology.md)
* [Architektura globální přenosové sítě](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Prostředky pro Virtual WAN

Pro konfiguraci virtuální sítě WAN mezi koncovými body vytvořte následující prostředky:

* **virtualWAN:** Prostředek virtualWAN představuje virtuální překrytí vaší sítě Azure a jde o kolekci několika prostředků. Obsahuje odkazy na všechny vaše virtuální rozbočovače, které chcete mít v rámci virtuální sítě WAN. Prostředky Virtual WAN jsou navzájem izolované a nemohou obsahovat společný rozbočovač. Virtuální rozbočovače napříč virtuální wan nekomunikují mezi sebou.

* **Rozbočovač:** Virtuální rozbočovač je virtuální síť spravovaná Microsoftem. Rozbočovač obsahuje různé koncové body služby pro povolení připojení. Z místní sítě (vpnsite) se můžete připojit k bráně VPN uvnitř virtuálního rozbočovače, připojit okruhy ExpressRoute k virtuálnímu rozbočovači nebo dokonce připojit mobilní uživatele k bráně point-to-site ve virtuálním rozbočovači. Rozbočovač je základem vaší sítě v oblasti. V každé oblasti Azure může existovat jen jeden rozbočovač.

  Brána rozbočovače není totéž, co brána virtuální sítě, kterou používáte pro ExpressRoute a VPN Gateway. Například při použití virtuální sítě WAN nevytváříte připojení mezi lokalitami z místního webu přímo do virtuální sítě. Místo toho můžete vytvořit připojení site-to-site k rozbočovači. Provoz vždy prochází přes bránu rozbočovače. To znamená, že virtuální sítě nepotřebují své vlastní brány virtuální sítě. Virtual WAN umožňuje vašim virtuálním sítím využívat výhod snadného škálování prostřednictvím virtuálního rozbočovače a jeho brány.

* **Připojení virtuální sítě k rozbočovači:** Prostředek připojení virtuální sítě k rozbočovači se používá k bezproblémovému propojení rozbočovače a virtuální sítě.

* **(Preview) Připojení rozbočovače k rozbočovačům** – rozbočovače jsou vzájemně propojeny ve virtuální síti WAN. To znamená, že větev, uživatel nebo virtuální síť připojená k místnímu rozbočovači může komunikovat s jinou větví nebo virtuální sítí pomocí architektury celé sítě připojených rozbočovačů. Virtuální sítě můžete také připojit v rámci rozbočovače, které prochází virtuálním rozbočovačem, stejně jako virtuální sítě napříč rozbočovačem, pomocí propojené houfu k rozbočovači.

* **Směrovací tabulka rozbočovače:** Můžete vytvořit trasu virtuálního rozbočovače a použít ji ve směrovací tabulce virtuálního rozbočovače. V uvedené tabulce můžete použít více tras.

**Další prostředky virtuální sítě WAN**

  * **Místo:** Tento prostředek se používá pouze pro připojení mezi lokalitami. Zdrojem webu je **vpnsite**. Představuje vaše místní zařízení VPN a jeho nastavení. Pokud využíváte služeb partnera pro Virtual WAN, máte integrované řešení, které automaticky exportuje tyto informace do Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Typy připojení

Virtuální síť WAN umožňuje následující typy připojení: VPN typu Site-to-Site, Uživatelská síť VPN (Point-to-Site) a ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Připojení VPN typu site-to-site

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan.png)

Při vytváření virtuálního připojení wan site-to-site můžete pracovat s dostupným partnerem. Pokud nechcete používat partnera, můžete připojení nakonfigurovat ručně. Další informace naleznete [v tématu Vytvoření připojení mezi lokalitami pomocí programu Virtual WAN](virtual-wan-site-to-site-portal.md).

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>Pracovní postup virtuálního partnera WAN

Při práci s partnerem virtuální sítě WAN je pracovní postup následující:

1. Řadič pobočkového zařízení (VPN/SDWAN) se ověřuje pro export informací zaměřených na lokalitu do Azure pomocí [hlavního povinného uživatele Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. Řadič zařízení pobočky (VPN/SDWAN) získá informace o konfiguraci připojení k Azure a aktualizuje místní zařízení. To umožňuje automatizovat stažení konfigurace místního zařízení VPN i jeho úpravu a aktualizaci.
3. Jakmile má zařízení správnou konfiguraci Azure, je možné vytvořit propojení typu Site-to-Site (dva aktivní tunely) v rámci sítě Azure WAN. Azure podporuje IKEv1 i IKEv2. Protokol BGP je volitelný.

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>Partneři pro virtuální připojení SÍTĚ WAN mezi lokalitami

Seznam dostupných partnerů a umístění naleznete v článku [Virtuální partneři wan a umístění.](virtual-wan-locations-partners.md)

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Připojení VPN uživatele (point-to-site)

Ke svým prostředkům v Azure se můžete připojit přes připojení IPsec/IKE (IKEv2) nebo OpenVPN. Tento typ připojení vyžaduje konfiguraci klienta VPN v klientském počítači. Další informace naleznete [v tématu Vytvoření připojení bodu k webu](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>Připojení ExpressRoute
ExpressRoute umožňuje připojit místní síť k Azure přes privátní připojení. Chcete-li vytvořit připojení, přečtěte si informace [o vytvoření připojení ExpressRoute pomocí programu Virtual WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a><a name="locations"></a>Umístění

Informace o poloze naleznete v článku [Virtuální partneři wan a umístění.](virtual-wan-locations-partners.md)

## <a name="faq"></a><a name="faq"></a>Nejčastější dotazy

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Další kroky

[Vytvoření připojení mezi lokalitami pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md)
