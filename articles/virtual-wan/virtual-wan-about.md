---
title: Azure Virtual WAN – Přehled | Microsoft Docs
description: Seznamte se s automatizovaným škálovatelným připojením mezi větevmi, dostupnými oblastmi a partnery služby Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/22/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: f1576e963f9c25821b5e3f57907662e3d86df4e0
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406344"
---
# <a name="what-is-azure-virtual-wan"></a>Co je Azure Virtual WAN?

Azure Virtual WAN je síťová služba, která poskytuje optimalizované a automatizované připojení větví k Azure. Oblasti Azure slouží jako centra, ke kterým se můžete připojit ke svým větvím. Můžete využít páteřní síť Azure a taky připojit větve a využít připojení k virtuální síti. Máme seznam partnerů, kteří podporují automatizaci připojení pomocí sítě VPN Azure Virtual WAN. Další informace najdete v článku věnovaném [virtuálním partnerům a umístěním sítě WAN](virtual-wan-locations-partners.md) .

Azure Virtual WAN přináší dohromady spoustu služeb cloudového připojení Azure, jako je VPN typu Site-to-site a ExpressRoute, do jediného provozního rozhraní. Připojení ke službě Azure virtuální sítě se vytváří pomocí připojení k virtuální síti.

ExpressRoute pro virtuální síť WAN je aktuálně ve verzi Preview.

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan1.png)

Tento článek poskytuje rychlý přehled o připojení k síti v Azure Virtual WAN. Virtual WAN nabízí následující výhody:

* **Integrovaná řešení pro připojení v centru a paprskech:** Automatizujte konfiguraci site-to-site a konektivitu mezi místními lokalitami a rozbočovačem Azure.
* **Nastavení a konfigurace automatizovaného paprsku:** Propojte své virtuální sítě a úlohy do centra Azure bez problémů.
* **Intuitivní řešení potíží:** V rámci Azure se můžete podívat na koncový tok a pak tyto informace použít k provedení požadovaných akcí.

## <a name="resources"></a>Prostředky pro Virtual WAN

Pokud chcete nakonfigurovat koncovou virtuální síť WAN, vytvoříte následující prostředky:

* **virtualWAN:** Prostředek virtualWAN představuje virtuální překryv vaší sítě Azure a je kolekce několika prostředků. Obsahuje odkazy na všechny vaše virtuální rozbočovače, které chcete mít v rámci virtuální sítě WAN. Prostředky Virtual WAN jsou navzájem izolované a nemohou obsahovat společný rozbočovač. Virtuální rozbočovače v síti Virtual WAN spolu navzájem nekomunikují. Vlastnost Povolit větev do větve provozu umožňuje přenosy mezi lokalitami VPN a sítěmi VPN a ExpressRoute (aktuálně ve verzi Preview) povolenými weby.

* **Zdroj** Virtuální rozbočovač je virtuální síť spravovaná Microsoftem. Rozbočovač obsahuje různé koncové body služby, které umožňují připojení z vaší místní sítě (vpnsite). Rozbočovač je základem vaší sítě v oblasti. V každé oblasti Azure může existovat jen jeden rozbočovač. Když vytvoříte rozbočovač pomocí portálu Azure, automaticky se vytvoří virtuální síť virtuálního rozbočovače a jeho brána VPN (vpngateway).

  Brána rozbočovače není totéž, co brána virtuální sítě, kterou používáte pro ExpressRoute a VPN Gateway. Například při použití virtuální sítě WAN nevytvoříte připojení typu Site-to-Site z místní lokality přímo k vaší virtuální síti. Místo toho vytvoříte připojení typu Site-to-site k centru. Provoz vždy prochází přes bránu rozbočovače. To znamená, že virtuální sítě nepotřebují své vlastní brány virtuální sítě. Virtual WAN umožňuje vašim virtuálním sítím využívat výhod snadného škálování prostřednictvím virtuálního rozbočovače a jeho brány.

* **Připojení k virtuální síti rozbočovače:** Prostředek připojení k virtuální síti rozbočovače se používá k bezproblémovému připojení centra k vaší virtuální síti. V tuto chvíli můžete rozbočovač připojit pouze k virtuálním sítím, které jsou ve stejné geografické oblasti.

* **Tabulka směrování centra:**  Můžete vytvořit trasu virtuálního rozbočovače a použít ji pro směrovací tabulku virtuálního rozbočovače. V uvedené tabulce můžete použít více tras.

**Další prostředky virtuální sítě WAN**

  * **Webovém** Tento prostředek se používá jenom pro připojení Site-to-site. Prostředek sítě je **vpnsite**. Představuje vaše místní zařízení VPN a jeho nastavení. Pokud využíváte služeb partnera pro Virtual WAN, máte integrované řešení, které automaticky exportuje tyto informace do Azure.

## <a name="connectivity"></a>Komunikační

Virtuální síť WAN umožňuje dva typy připojení: Site-to-site a ExpressRoute (Preview).

### <a name="s2s"></a>Připojení VPN typu Site-to-site

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan.png)

Když vytváříte virtuální připojení WAN typu Site-to-site, můžete pracovat s dostupným partnerem. Pokud nechcete použít partnera, můžete nakonfigurovat připojení ručně. Další informace najdete v tématu [vytvoření připojení typu Site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Pracovní postup partnera virtuální sítě WAN

Když pracujete s virtuálním partnerem WAN, je pracovní postup:

1. Řadič zařízení pobočky (VPN/SDWAN) se ověří pomocí [instančního objektu Azure](../active-directory/develop/howto-create-service-principal-portal.md), aby mohl exportovat informace o lokalitách do Azure.
2. Řadič zařízení pobočky (VPN/SDWAN) získá informace o konfiguraci připojení k Azure a aktualizuje místní zařízení. To umožňuje automatizovat stažení konfigurace místního zařízení VPN i jeho úpravu a aktualizaci.
3. Jakmile má zařízení správnou konfiguraci Azure, je možné vytvořit propojení typu Site-to-Site (dva aktivní tunely) v rámci sítě Azure WAN. Azure podporuje IKEv1 i IKEv2. Protokol BGP je volitelný.

#### <a name="partners"></a>Partneři pro připojení typu Site-to-site k virtuální síti WAN

Seznam dostupných partnerů a umístění najdete v článku věnovaném virtuálním [partnerům a umístěním sítě WAN](virtual-wan-locations-partners.md) .


### <a name="er"></a>Propojení přes ExpressRoute (Preview)

ExpressRoute umožňuje připojit místní síť k Azure prostřednictvím privátního připojení. Pokud chcete vytvořit připojení, přečtěte si téma [vytvoření připojení ExpressRoute pomocí virtuální sítě WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Polohy

Informace o umístění naleznete v článku [virtuální partneři a umístění sítě WAN](virtual-wan-locations-partners.md) .

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Další postup

[Vytvoření připojení typu Site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md)
