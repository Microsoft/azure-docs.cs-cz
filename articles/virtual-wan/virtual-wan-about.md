---
title: Azure Virtual WAN – Přehled | Microsoft Docs
description: Přečtěte si o možnostech automatického škálovatelného propojení poboček pomocí virtuální sítě WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/25/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 54d177d7542d7501a91a955be20af776b16657a2
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182361"
---
# <a name="what-is-azure-virtual-wan"></a>Co je Azure Virtual WAN?

Azure Virtual WAN je síťová služba poskytující optimalizované a automatizované možnosti propojení jednotlivých poboček prostřednictvím Azure. Virtuální síť WAN umožňuje připojit a nakonfigurovat zařízení v pobočkách tak, aby komunikovaly přes Azure. Můžete to udělat buď ručně, nebo pomocí zařízení preferovaného partnera pro Virtual WAN. Podrobnosti najdete v článku o [preferovaných partnerech](https://go.microsoft.com/fwlink/p/?linkid=2019615). Využití zařízení preferovaného partnera vám zajistí snadné použití, jednoduché propojení a správu konfigurace. Integrovaný řídicí panel Azure WAN nabízí okamžité přehledy pro řešení potíží, díky kterým ušetříte čas a získáte snadný způsob zobrazení propojení ve velkém měřítku.

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan.png)

Tento článek obsahuje rychlý přehled o síťovém propojení úloh v prostředí Azure i mimo ně. Virtual WAN nabízí následující výhody:

* **Integrovaná řešení propojení ve hvězdicovém modelu:** Můžete zautomatizovat konfiguraci Site-to-Site a propojení mezi místními lokalitami a rozbočovačem Azure.
* **Automatizované vytvoření a konfigurace koncových sítí:** Připojte bez starostí své virtuální sítě a úlohy k rozbočovači Azure.
* **Intuitivní řešení potíží:** Můžete zobrazit celý tok dat v rámci Azure a získané informace použít k provedení potřebných akcí.

## <a name="s2s"></a>Propojení typu Site-to-Site

K vytvoření připojení typu Site-to-Site pomocí služby Virtual WAN můžete buď využít služeb [partnera pro Virtual WAN](https://go.microsoft.com/fwlink/p/?linkid=2019615), nebo vytvořit připojení ručně.

### <a name="s2spartner"></a>Práce s partnerem Virtual WAN

V případě spolupráce s partnerem pro Virtual WAN probíhá celý proces následujícím způsobem:

1. Řadič zařízení pobočky (VPN/SDWAN) se ověří pomocí [instančního objektu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md), aby mohl exportovat informace o lokalitách do Azure.
2. Řadič zařízení pobočky (VPN/SDWAN) získá informace o konfiguraci připojení k Azure a aktualizuje místní zařízení. To umožňuje automatizovat stažení konfigurace místního zařízení VPN i jeho úpravu a aktualizaci.
3. Jakmile má zařízení správnou konfiguraci Azure, je možné vytvořit propojení typu Site-to-Site (dva aktivní tunely) v rámci sítě Azure WAN. Azure podporuje IKEv1 i IKEv2. Protokol BGP je volitelný.


Pokud nechcete využít preferovaného partnera, můžete propojení nakonfigurovat ručně. Informace najdete v tématu věnovaném [vytvoření propojení typu Site-to-Site pomocí Virtual WAN](virtual-wan-site-to-site-portal.md).

## <a name="p2s"></a>Propojení typu Point-to-Site (Preview)

Propojení typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuálnímu rozbočovači z individuálního klientského počítače. Připojení P2S se vytvoří jeho zahájením z klientského počítače. Toto řešení je užitečné pro osoby pracující na dálku, které se chtějí připojit ze vzdáleného umístění, například z domova nebo konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN S2S, pokud máte pouze několik klientů, kteří se potřebují připojit.

Postup ručního vytvoření propojení typu Point-to-Site najdete v tématu o [vytvoření propojení typu Point-to-Site pomocí Virtual WAN](https://go.microsoft.com/fwlink/p/?linkid=2020051&clcid).

## <a name="er"></a>Propojení přes ExpressRoute (Preview)

Postup ručního vytvoření propojení najdete v tématu o [vytvoření propojení ExpressRoute pomocí Virtual WAN](https://go.microsoft.com/fwlink/p/?linkid=2020148&clcid).


## <a name="resources"></a>Prostředky pro Virtual WAN

Pro konfiguraci virtuální sítě WAN mezi koncovými body vytvořte následující prostředky:

* **virtualWAN:** Prostředek virtualWAN představuje virtuální překrytí vaší sítě Azure a jde o kolekci několika prostředků. Obsahuje odkazy na všechny vaše virtuální rozbočovače, které chcete mít v rámci virtuální sítě WAN. Prostředky Virtual WAN jsou navzájem izolované a nemohou obsahovat společný rozbočovač. Virtuální rozbočovače v síti Virtual WAN spolu navzájem nekomunikují. Vlastnost pro povolení přenosu mezi pobočkami umožňuje přenos mezi lokalitami VPN i mezi lokalitami s povoleným provozem z VPN do ExpressRoute. Je nutné upozornit, že ExpressRoute aktuálně v rámci Azure Virtual WAN funguje ve verzi Preview.

* **Síť:** Prostředek označovaný jako vpnsite představuje vaše místní zařízení VPN a jeho nastavení. Pokud využíváte služeb partnera pro Virtual WAN, máte integrované řešení, které automaticky exportuje tyto informace do Azure.

* **Rozbočovač:** Virtuální rozbočovač je virtuální síť spravovaná Microsoftem. Rozbočovač obsahuje různé koncové body služby, které umožňují připojení z vaší místní sítě (vpnsite). Rozbočovač je základem vaší sítě v oblasti. V každé oblasti Azure může existovat jen jeden rozbočovač. Když vytvoříte rozbočovač pomocí portálu Azure, automaticky se vytvoří virtuální síť virtuálního rozbočovače a jeho brána VPN (vpngateway).

  Brána rozbočovače není totéž, co brána virtuální sítě, kterou používáte pro ExpressRoute a VPN Gateway. Například při použití virtuální sítě WAN nevytváříte připojení mezi pobočkami z místní lokality přímo do vaší virtuální sítě. Místo toho vytváříte propojení s rozbočovačem. Provoz vždy prochází přes bránu rozbočovače. To znamená, že virtuální sítě nepotřebují své vlastní brány virtuální sítě. Virtual WAN umožňuje vašim virtuálním sítím využívat výhod snadného škálování prostřednictvím virtuálního rozbočovače a jeho brány. 

* **Připojení virtuální sítě k rozbočovači:** Prostředek připojení virtuální sítě k rozbočovači se používá k bezproblémovému propojení rozbočovače a virtuální sítě. V tuto chvíli můžete rozbočovač připojit pouze k virtuálním sítím, které jsou ve stejné geografické oblasti.

* **Směrovací tabulka rozbočovače:** Můžete vytvořit trasu virtuálního rozbočovače a použít ji ve směrovací tabulce virtuálního rozbočovače. V uvedené tabulce můžete použít více tras.

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]


## <a name="next-steps"></a>Další kroky

* Zobrazení [partnerů a míst pro Virtual WAN](https://aka.ms/virtualwan)