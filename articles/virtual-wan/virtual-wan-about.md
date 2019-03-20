---
title: Azure Virtual WAN – Přehled | Microsoft Docs
description: Další informace o virtuální automatizovaných škálovatelných větve do větve připojení k síti WAN, dostupné oblasti a partnery.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 03/19/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 5c6e69e05eaa036e140d7275b4e66930a3e5be7a
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225293"
---
# <a name="what-is-azure-virtual-wan"></a>Co je Azure Virtual WAN?

Azure virtuální síť WAN je síťová služba, která poskytuje větev optimalizované a automatické připojení k a prostřednictvím Azure. Oblasti Azure, které slouží jako rozbočovačů, které můžete zvolit připojení větvích. Po připojení se větve, můžete využít páteřní síť Azure k navázání připojení větev to-VNet a větve do větve.

Azure virtuální sítě WAN spojuje mnoho připojení k Azure cloudové služby jako Site-to-Site VPN (obecně dostupné), ExpressRoute (Preview), uživatel Point-to-Site VPN (Preview) do jediného provozní rozhraní. Navázání připojení k virtuálním sítím Azure pomocí připojení k virtuální síti.

![Diagram virtuální sítě WAN](./media/virtual-wan-about/vwangraphic.png)

Tento článek obsahuje rychlý přehled připojení k síti v Azure virtuální sítě WAN. Virtual WAN nabízí následující výhody:

* **Integrované připojení řešení v hvězdicové:** Automatizace konfigurace Site-to-Site a připojení mezi místními servery a centra Azure.
* **Paprsky automatizované instalaci a konfiguraci:** Připojte virtuální sítě a úlohy k centru Azure bez problémů.
* **Intuitivní řešení potíží:** Můžete zobrazit začátku do konce tok v rámci Azure a tyto informace slouží k provedení požadované akce.

## <a name="partner-region"></a>Partneři a umístění

Další informace najdete v tématu [virtuální sítě WAN partneři a umístění](virtual-wan-locations-partners.md) článku.

### <a name="partner"></a>Partneři

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

### <a name="locations"></a>umístění

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="s2s"></a>Propojení typu Site-to-Site

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan.png)

K vytvoření připojení typu Site-to-Site pomocí služby Virtual WAN můžete buď využít služeb [partnera pro Virtual WAN](virtual-wan-locations-partners.md), nebo vytvořit připojení ručně.

### <a name="s2spartner"></a>Pracovní postup partnera

Při práci s partnerem, virtuální sítě WAN, pracovní postup je:

1. Řadič zařízení pobočky (VPN/SDWAN) se ověří pomocí [instančního objektu Azure](../active-directory/develop/howto-create-service-principal-portal.md), aby mohl exportovat informace o lokalitách do Azure.
2. Řadič zařízení pobočky (VPN/SDWAN) získá informace o konfiguraci připojení k Azure a aktualizuje místní zařízení. To umožňuje automatizovat stažení konfigurace místního zařízení VPN i jeho úpravu a aktualizaci.
3. Jakmile má zařízení správnou konfiguraci Azure, je možné vytvořit propojení typu Site-to-Site (dva aktivní tunely) v rámci sítě Azure WAN. Azure podporuje IKEv1 i IKEv2. Protokol BGP je volitelný.

Pokud už nechcete používat partnerem, můžete konfigurovat připojení ručně, naleznete v tématu [vytvoření připojení typu Site-to-Site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md).

## <a name="p2s"></a>Propojení typu Point-to-Site (Preview)

Propojení typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuálnímu rozbočovači z individuálního klientského počítače. Připojení P2S se vytvoří jeho zahájením z klientského počítače. Toto řešení je užitečné pro osoby pracující na dálku, které se chtějí připojit ze vzdáleného umístění, například z domova nebo konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN S2S, pokud máte pouze několik klientů, kteří se potřebují připojit.

Postup ručního vytvoření propojení typu Point-to-Site najdete v tématu o [vytvoření propojení typu Point-to-Site pomocí Virtual WAN](virtual-wan-point-to-site-portal.md).

## <a name="er"></a>Propojení přes ExpressRoute (Preview)

Postup ručního vytvoření propojení najdete v tématu o [vytvoření propojení ExpressRoute pomocí Virtual WAN](virtual-wan-expressroute-portal.md).

## <a name="resources"></a>Prostředky pro Virtual WAN

Pro konfiguraci virtuální sítě WAN mezi koncovými body vytvořte následující prostředky:

* **virtualWAN:** Prostředek virtualWAN představuje virtuální překrytí sítí Azure a je kolekce z více zdrojů. Obsahuje odkazy na všechny vaše virtuální rozbočovače, které chcete mít v rámci virtuální sítě WAN. Prostředky Virtual WAN jsou navzájem izolované a nemohou obsahovat společný rozbočovač. Virtuální rozbočovače v síti Virtual WAN spolu navzájem nekomunikují. Vlastnost pro povolení přenosu mezi pobočkami umožňuje přenos mezi lokalitami VPN i mezi lokalitami s povoleným provozem z VPN do ExpressRoute. Je nutné upozornit, že ExpressRoute aktuálně v rámci Azure Virtual WAN funguje ve verzi Preview.

* **Lokalita:** Síťovému prostředku, známá jako vpnsite představuje vaše místní zařízení VPN a jeho nastavení. Pokud využíváte služeb partnera pro Virtual WAN, máte integrované řešení, které automaticky exportuje tyto informace do Azure.

* **Hub:** Virtuální rozbočovač je spravovaná Microsoftem virtuální sítě. Rozbočovač obsahuje různé koncové body služby, které umožňují připojení z vaší místní sítě (vpnsite). Rozbočovač je základem vaší sítě v oblasti. V každé oblasti Azure může existovat jen jeden rozbočovač. Když vytvoříte rozbočovač pomocí portálu Azure, automaticky se vytvoří virtuální síť virtuálního rozbočovače a jeho brána VPN (vpngateway).

  Brána rozbočovače není totéž, co brána virtuální sítě, kterou používáte pro ExpressRoute a VPN Gateway. Například při použití virtuální sítě WAN nevytváříte připojení mezi pobočkami z místní lokality přímo do vaší virtuální sítě. Místo toho vytváříte propojení s rozbočovačem. Provoz vždy prochází přes bránu rozbočovače. To znamená, že virtuální sítě nepotřebují své vlastní brány virtuální sítě. Virtual WAN umožňuje vašim virtuálním sítím využívat výhod snadného škálování prostřednictvím virtuálního rozbočovače a jeho brány. 

* **Připojení k virtuální síti centra:** Prostředek připojení virtuální sítě centra se používá testy bezproblémového připojení k virtuální síti centra. V tuto chvíli můžete rozbočovač připojit pouze k virtuálním sítím, které jsou ve stejné geografické oblasti.

* **Centrum směrovací tabulky:**  Můžete vytvořit trasu virtuální rozbočovač a použít trasy do směrovací tabulky virtuální rozbočovač. V uvedené tabulce můžete použít více tras.

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Další postup

Zobrazení [virtuální sítě WAN partneři a umístění](virtual-wan-locations-partners.md) stránky pro další informace o umístění a partnerům virtuální sítě WAN.
