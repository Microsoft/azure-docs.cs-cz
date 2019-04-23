---
title: Azure Virtual WAN – Přehled | Microsoft Docs
description: Další informace o virtuální automatizovaných škálovatelných větve do větve připojení k síti WAN, dostupné oblasti a partnery.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 03/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 6dae42dcc8b74b682c7226916482228058db6154
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60460202"
---
# <a name="what-is-azure-virtual-wan"></a>Co je Azure Virtual WAN?

Azure virtuální síť WAN je síťová služba, která poskytuje větev optimalizované a automatické připojení k a prostřednictvím Azure. Oblasti Azure, které slouží jako rozbočovačů, které můžete zvolit připojení větvích. Po připojení se větve, můžete využít páteřní síť Azure k navázání připojení větev to-VNet a větve do větve. Seznam partnerů a umístění, které podporují virtuální sítě WAN VPN najdete v tématu [virtuální sítě WAN partneři a umístění](virtual-wan-locations-partners.md) článku.

Azure virtuální sítě WAN spojuje mnoho připojení k Azure cloudové služby jako site-to-site VPN (obecně dostupné), ExpressRoute (Preview), uživatel point-to-site VPN (Preview) do jediného provozní rozhraní. Navázat připojení k virtuálním sítím Azure pomocí připojení k virtuální síti.

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan1.png)

Tento článek obsahuje rychlý přehled připojení k síti v Azure virtuální sítě WAN. Virtual WAN nabízí následující výhody:

* **Integrované připojení řešení v hvězdicové:** Automatizace konfigurace site-to-site a připojení mezi místními servery a centra Azure.
* **Paprsky automatizované instalaci a konfiguraci:** Připojte virtuální sítě a úlohy k centru Azure bez problémů.
* **Intuitivní řešení potíží:** Můžete zobrazit začátku do konce tok v rámci Azure a pak tyto informace použít k provedení požadované akce.

## <a name="resources"></a>Prostředky pro Virtual WAN

Pokud chcete nakonfigurovat začátku do konce virtuální sítě WAN, vytvoříte následující prostředky:

* **virtualWAN:** Prostředek virtualWAN představuje virtuální překrytí sítí Azure a je kolekce z více zdrojů. Obsahuje odkazy na všechny vaše virtuální rozbočovače, které chcete mít v rámci virtuální sítě WAN. Prostředky Virtual WAN jsou navzájem izolované a nemohou obsahovat společný rozbočovač. Virtuální rozbočovače v síti Virtual WAN spolu navzájem nekomunikují. Vlastnost "Přenosy větve do větve" povolí provoz mezi servery VPN, stejně jako VPN na ExpressRoute (aktuálně ve verzi Preview) povolena weby.

* **Hub:** Virtuální rozbočovač je spravovaná Microsoftem virtuální sítě. Rozbočovač obsahuje různé koncové body služby, které umožňují připojení z vaší místní sítě (vpnsite). Rozbočovač je základem vaší sítě v oblasti. V každé oblasti Azure může existovat jen jeden rozbočovač. Když vytvoříte rozbočovač pomocí portálu Azure, automaticky se vytvoří virtuální síť virtuálního rozbočovače a jeho brána VPN (vpngateway).

  Brána rozbočovače není totéž, co brána virtuální sítě, kterou používáte pro ExpressRoute a VPN Gateway. Například při použití virtuální sítě WAN, není vytvoření připojení site-to-site z místní lokality přímo do vaší virtuální sítě. Místo toho vytvořte připojení site-to-site k rozbočovači. Provoz vždy prochází přes bránu rozbočovače. To znamená, že virtuální sítě nepotřebují své vlastní brány virtuální sítě. Virtual WAN umožňuje vašim virtuálním sítím využívat výhod snadného škálování prostřednictvím virtuálního rozbočovače a jeho brány.

* **Připojení k virtuální síti centra:** Prostředek připojení virtuální sítě centra se používá testy bezproblémového připojení k virtuální síti centra. V tuto chvíli můžete rozbočovač připojit pouze k virtuálním sítím, které jsou ve stejné geografické oblasti.

* **Centrum směrovací tabulky:**  Můžete vytvořit trasu virtuální rozbočovač a použít trasy do směrovací tabulky virtuální rozbočovač. V uvedené tabulce můžete použít více tras.

**Další prostředky virtuální sítě WAN**

  * **Lokalita:** Tento prostředek se používá pro připojení site-to-site jenom. Prostředek serveru je **vpnsite**. Představuje vaše místní zařízení VPN a jeho nastavení. Pokud využíváte služeb partnera pro Virtual WAN, máte integrované řešení, které automaticky exportuje tyto informace do Azure.

## <a name="connectivity"></a>Připojení

Virtuální síť WAN umožňuje tři typy připojení: site-to-site, point-to-site (Preview) a ExpressRoute (Preview).

### <a name="s2s"></a>Připojení VPN typu Site-to-site

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan.png)

Při vytváření virtuální sítě WAN připojení site-to-site můžete pracovat s partnerem k dispozici. Pokud už nechcete používat partnerem, můžete nakonfigurovat připojení ručně. Další informace najdete v tématu [vytvoření připojení typu site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Pracovní postup partnerské virtuální sítě WAN

Při práci s partnerem, virtuální sítě WAN, pracovní postup je:

1. Řadič zařízení pobočky (VPN/SDWAN) se ověří pomocí [instančního objektu Azure](../active-directory/develop/howto-create-service-principal-portal.md), aby mohl exportovat informace o lokalitách do Azure.
2. Řadič zařízení pobočky (VPN/SDWAN) získá informace o konfiguraci připojení k Azure a aktualizuje místní zařízení. To umožňuje automatizovat stažení konfigurace místního zařízení VPN i jeho úpravu a aktualizaci.
3. Jakmile má zařízení správnou konfiguraci Azure, je možné vytvořit propojení typu Site-to-Site (dva aktivní tunely) v rámci sítě Azure WAN. Azure podporuje IKEv1 i IKEv2. Protokol BGP je volitelný.

#### <a name="partners"></a>Partneři pro virtuální sítě WAN připojení site-to-site

Seznam dostupných partneři a umístění, najdete v článku [virtuální sítě WAN partneři a umístění](virtual-wan-locations-partners.md) článku.

### <a name="p2s"></a>Připojení k síti VPN Point-to-site (Preview)

Připojení point-to-site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální rozbočovač z jednotlivých klientských počítačů. Připojení P2S se vytvoří jeho zahájením z klientského počítače. Toto řešení je užitečné pro osoby pracující na dálku, které se chtějí připojit ze vzdáleného umístění, například z domova nebo konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN S2S, pokud máte pouze několik klientů, kteří se potřebují připojit.

Vytvoření připojení najdete v tématu [vytvoření připojení typu point-to-site pomocí virtuální sítě WAN](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>Propojení přes ExpressRoute (Preview)

ExpressRoute vám umožňuje připojit místní síť do Azure pomocí soukromého připojení. Vytvoření připojení najdete v tématu [vytvoření připojení ExpressRoute pomocí virtuální sítě WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>umístění

Informace o poloze, najdete v článku [virtuální sítě WAN partneři a umístění](virtual-wan-locations-partners.md) článku.

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Další postup

[Vytvoření připojení typu site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md)
