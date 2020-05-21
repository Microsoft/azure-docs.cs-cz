---
title: Azure Virtual WAN – Přehled | Microsoft Docs
description: Seznamte se s automatizovaným škálovatelným připojením mezi větevmi, dostupnými oblastmi a partnery služby Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 05/14/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 8bdba64445212c564a3d4762bc8497be15f7d9a0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657009"
---
# <a name="about-azure-virtual-wan"></a>Informace o službě Azure Virtual WAN

Azure Virtual WAN je síťová služba, která přináší více funkcí směrování, zabezpečení a směrování, aby poskytovaly jediné provozní rozhraní. Mezi tyto funkce patří připojení větví (prostřednictvím automatizace připojení z virtuálních zařízení WAN, jako je SD-WAN nebo VPN CPE). připojení VPN typu Site-to-site, VPN vzdáleného uživatele (Point-to-site), konektivita privátního uživatele (ExpressRoute), připojení mezi cloudem (přenosné připojení k virtuálním sítím), připojení VPN ExpressRoute, směrování, Azure Firewall a šifrování pro privátní připojení. Nemusíte mít všechny tyto případy použití, abyste mohli začít používat virtuální síť WAN. Můžete jednoduše začít jenom s jedním případem použití a pak upravit síť tak, jak se vyvíjí.

Virtuální architektura sítě WAN je architektura rozbočovače a paprsku s integrovaným škálováním a výkonem pro větve (zařízení VPN/SD-WAN), uživatele (Azure VPN/OpenVPN/IKEv2), okruhy ExpressRoute a virtuální sítě. Umožňuje [globální přenosovou architekturu sítě](virtual-wan-global-transit-network-architecture.md), ve které cloudová hostovaná síť "centrum" umožňuje přenosné připojení mezi koncovými body, které mohou být distribuovány napříč různými typy "paprsků".

Oblasti Azure slouží jako centra, ke kterým se můžete rozhodnout pro připojení. Všechna centra jsou připojená v celé síti sítě WAN, která usnadňuje uživateli používání páteřní sítě Microsoftu pro připojení typu any-to-Any (jakékoli paprsky). Pro připojení paprsků se zařízeními SD-WAN/VPN je uživatelé mohli ručně nastavit ve službě Azure Virtual WAN nebo použít řešení partner Virtual WAN CPE (SD-WAN/VPN) k nastavení připojení k Azure. Máme seznam partnerů, kteří podporují automatizaci připojení (schopnost exportovat informace o zařízení do Azure, stáhnout konfiguraci Azure a vytvořit připojení) pomocí Azure Virtual WAN. Další informace najdete v článku věnovaném [virtuálním partnerům a umístěním sítě WAN](virtual-wan-locations-partners.md) .

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan1.png)

Tento článek poskytuje rychlý přehled o připojení k síti v Azure Virtual WAN. Virtual WAN nabízí následující výhody:

* **Integrovaná řešení pro připojení v centru a paprskech:** Automatizujte konfiguraci site-to-site a konektivitu mezi místními lokalitami a rozbočovačem Azure.
* **Automatizované vytvoření a konfigurace koncových sítí:** Připojte bez starostí své virtuální sítě a úlohy k rozbočovači Azure.
* **Intuitivní řešení potíží:** V rámci Azure se můžete podívat na koncový tok a pak tyto informace použít k provedení požadovaných akcí.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Virtuální sítě WAN úrovně Basic a Standard

Existují dva typy virtuálních sítí WAN: Basic a Standard. V následující tabulce jsou uvedeny dostupné konfigurace pro každý typ.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Postup upgradu virtuální sítě WAN najdete v tématu [upgrade virtuální sítě WAN z úrovně Basic na standard](upgrade-virtual-wan.md).

## <a name="architecture"></a><a name="architecture"></a>Architektura

Informace o architektuře Virtual WAN a o tom, jak migrovat na virtuální síť WAN, najdete v následujících článcích:

* [Architektura virtuální sítě WAN](migrate-from-hub-spoke-topology.md)
* [Architektura globální přenosové sítě](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Prostředky pro Virtual WAN

Pro konfiguraci virtuální sítě WAN mezi koncovými body vytvořte následující prostředky:

* **virtualWAN:** Prostředek virtualWAN představuje virtuální překrytí vaší sítě Azure a jde o kolekci několika prostředků. Obsahuje odkazy na všechny vaše virtuální rozbočovače, které chcete mít v rámci virtuální sítě WAN. Prostředky Virtual WAN jsou navzájem izolované a nemohou obsahovat společný rozbočovač. Virtuální rozbočovače na virtuální síti WAN mezi sebou vzájemně nekomunikují.

* **Rozbočovač:** Virtuální rozbočovač je virtuální síť spravovaná Microsoftem. Centrum obsahuje různé koncové body služby, aby bylo možné připojení povolit. Z vaší místní sítě (vpnsite) se můžete připojit k VPN Gateway uvnitř virtuálního rozbočovače, připojit ExpressRoute okruhy k virtuálnímu rozbočovači nebo připojit mobilní uživatele k bráně Point-to-site ve virtuálním centru. Rozbočovač je základem vaší sítě v oblasti. V každé oblasti Azure může existovat jen jeden rozbočovač.

  Brána rozbočovače není totéž, co brána virtuální sítě, kterou používáte pro ExpressRoute a VPN Gateway. Například při použití virtuální sítě WAN nevytvoříte připojení typu Site-to-Site z místní lokality přímo k vaší virtuální síti. Místo toho vytvoříte připojení typu Site-to-site k centru. Provoz vždy prochází přes bránu rozbočovače. To znamená, že virtuální sítě nepotřebují své vlastní brány virtuální sítě. Virtual WAN umožňuje vašim virtuálním sítím využívat výhod snadného škálování prostřednictvím virtuálního rozbočovače a jeho brány.

* **Připojení virtuální sítě k rozbočovači:** Prostředek připojení virtuální sítě k rozbočovači se používá k bezproblémovému propojení rozbočovače a virtuální sítě.

* **(Preview) připojení k rozbočovači** – rozbočovače jsou vzájemně připojeni ve virtuální síti WAN. To znamená, že větev, uživatel nebo virtuální síť, která je připojená k místnímu rozbočovači, může komunikovat s jinou větví nebo virtuální sítí pomocí úplné architektury sítě připojených Center. Virtuální sítě se taky můžete připojit v rámci centra, které se přenáší přes virtuální rozbočovač, a také virtuální sítě napříč centrem pomocí architektury připojeného k rozbočovači.

* **Směrovací tabulka rozbočovače:** Můžete vytvořit trasu virtuálního rozbočovače a použít ji ve směrovací tabulce virtuálního rozbočovače. V uvedené tabulce můžete použít více tras.

**Další prostředky virtuální sítě WAN**

  * **Lokalita:** Tento prostředek se používá jenom pro připojení Site-to-site. Prostředek sítě je **vpnsite**. Představuje vaše místní zařízení VPN a jeho nastavení. Pokud využíváte služeb partnera pro Virtual WAN, máte integrované řešení, které automaticky exportuje tyto informace do Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Typy připojení

Virtuální síť WAN umožňuje následující typy připojení: Site-to-Site VPN, User VPN (Point-to-site) a ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Připojení VPN typu site-to-site

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan.png)

Když vytváříte virtuální připojení WAN typu Site-to-site, můžete pracovat s dostupným partnerem. Pokud nechcete použít partnera, můžete nakonfigurovat připojení ručně. Další informace najdete v tématu [vytvoření připojení typu Site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md).

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>Pracovní postup partnera virtuální sítě WAN

Když pracujete s virtuálním partnerem WAN, je pracovní postup:

1. Řadič zařízení pobočky (VPN/SDWAN) je ověřený pro export informací orientovaných na lokalitu do Azure pomocí [instančního objektu Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. Řadič zařízení pobočky (VPN/SDWAN) získá informace o konfiguraci připojení k Azure a aktualizuje místní zařízení. To umožňuje automatizovat stažení konfigurace místního zařízení VPN i jeho úpravu a aktualizaci.
3. Jakmile má zařízení správnou konfiguraci Azure, je možné vytvořit propojení typu Site-to-Site (dva aktivní tunely) v rámci sítě Azure WAN. Azure podporuje IKEv1 i IKEv2. Protokol BGP je volitelný.

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>Partneři pro připojení typu Site-to-site k virtuální síti WAN

Seznam dostupných partnerů a umístění najdete v článku věnovaném [virtuálním partnerům a umístěním sítě WAN](virtual-wan-locations-partners.md) .

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Připojení uživatele VPN (Point-to-site)

K prostředkům v Azure se můžete připojit přes připojení IPsec/IKE (IKEv2) nebo OpenVPN. Tento typ připojení vyžaduje, aby byl na klientském počítači nakonfigurovaný klient VPN. Další informace najdete v tématu [vytvoření připojení typu Point-to-site](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>Připojení ExpressRoute
ExpressRoute umožňuje připojit místní síť k Azure prostřednictvím privátního připojení. Pokud chcete vytvořit připojení, přečtěte si téma [vytvoření připojení ExpressRoute pomocí virtuální sítě WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a><a name="locations"></a>Umístění

Informace o umístění naleznete v článku [virtuální partneři a umístění sítě WAN](virtual-wan-locations-partners.md) .

## <a name="faq"></a><a name="faq"></a>Nejčastější dotazy

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Další kroky

[Vytvoření připojení typu Site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md)
