---
title: Azure Virtual WAN – Přehled | Microsoft Docs
description: Další informace o virtuální automatizovaných škálovatelných větve do větve připojení k síti WAN, dostupné oblasti a partnery.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 03/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 6a6fc9df2b102fd16bba03f26df4e24a1c946875
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409988"
---
# <a name="what-is-azure-virtual-wan"></a>Co je Azure Virtual WAN?

Azure Virtual WAN je síťová služba poskytující optimalizované a automatizované možnosti propojení jednotlivých poboček prostřednictvím Azure. Virtuální síť WAN umožňuje připojit a nakonfigurovat zařízení v pobočkách tak, aby komunikovaly přes Azure. To můžete udělat buď ručně, nebo pomocí partnerských zařízení prostřednictvím virtuální sítě WAN partnera. Pomocí partnerských zařízení umožňuje že snadné použití, zjednodušení připojení a správa konfigurace. Další informace najdete v tématu [umístění a partneři](virtual-wan-locations-partners.md) článku. Kromě toho Azure WAN integrovaný řídicí panel poskytuje okamžitý přehled o řešení problémů, které dokáže šetřit čas a poskytuje snadný způsob, jak zobrazit ve velkém měřítku připojení.

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan.png)

Tento článek obsahuje rychlý přehled o síťovém propojení úloh v prostředí Azure i mimo ně. Virtual WAN nabízí následující výhody:

* **Integrované připojení řešení v hvězdicové:** Automatizace konfigurace Site-to-Site a připojení mezi místními servery a centra Azure.
* **Paprsky automatizované instalaci a konfiguraci:** Připojte virtuální sítě a úlohy k centru Azure bez problémů.
* **Intuitivní řešení potíží:** Můžete zobrazit začátku do konce tok v rámci Azure a tyto informace slouží k provedení požadované akce.

## <a name="s2s"></a>Propojení typu Site-to-Site

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

## <a name="partner-region"></a>Partneři a umístění

### <a name="partner"></a>Partneři

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

### <a name="locations"></a>umístění

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Další postup

Zobrazení [virtuální sítě WAN partneři a umístění](virtual-wan-locations-partners.md) stránky.
