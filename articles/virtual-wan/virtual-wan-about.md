---
title: Azure Virtual WAN – Přehled | Microsoft Docs
description: Seznamte se s automatizovaným škálovatelným připojením mezi větevmi, dostupnými oblastmi a partnery služby Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 69525173aa5c4af536105a4e897ee4860f021b6c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267427"
---
# <a name="what-is-azure-virtual-wan"></a>Co je Azure Virtual WAN?

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

* **Připojení k rozbočovači:** Rozbočovače jsou všechny vzájemně propojené ve virtuální síti WAN. To znamená, že větev, uživatel nebo virtuální síť, která je připojená k místnímu rozbočovači, může komunikovat s jinou větví nebo virtuální sítí pomocí úplné architektury sítě připojených Center. Virtuální sítě se taky můžete připojit v rámci centra, které se přenáší přes virtuální rozbočovač, a také virtuální sítě napříč centrem pomocí architektury připojeného k rozbočovači.

* **Směrovací tabulka rozbočovače:** Můžete vytvořit trasu virtuálního rozbočovače a použít ji ve směrovací tabulce virtuálního rozbočovače. V uvedené tabulce můžete použít více tras.

**Další prostředky virtuální sítě WAN**

* **Lokalita:** Tento prostředek se používá jenom pro připojení Site-to-site. Prostředek sítě je **vpnsite**. Představuje vaše místní zařízení VPN a jeho nastavení. Pokud využíváte služeb partnera pro Virtual WAN, máte integrované řešení, které automaticky exportuje tyto informace do Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Typy připojení

Virtuální síť WAN umožňuje následující typy připojení: Site-to-Site VPN, User VPN (Point-to-site) a ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Připojení VPN typu site-to-site

K prostředkům v Azure se můžete připojit přes připojení typu Site-to-site IPsec/IKE (IKEv2). Další informace najdete v tématu [vytvoření připojení typu Site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md). 

Tento typ připojení vyžaduje zařízení VPN nebo virtuální partnerská zařízení WAN. Virtuální partneři sítě WAN poskytují automatizaci pro připojení, což je schopnost exportovat informace o zařízení do Azure, stáhnout konfiguraci Azure a navázat připojení ke službě Azure Virtual WAN hub. Seznam dostupných partnerů a umístění najdete v článku věnovaném [virtuálním partnerům a umístěním sítě WAN](virtual-wan-locations-partners.md) . Pokud váš poskytovatel zařízení VPN/SD-WAN není uvedený na zmíněném odkazu, můžete zjednodušit pomocí podrobného pokynu [vytvořit připojení typu Site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md) a nastavit připojení.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Připojení uživatele VPN (Point-to-site)

K prostředkům v Azure se můžete připojit přes připojení IPsec/IKE (IKEv2) nebo OpenVPN. Tento typ připojení vyžaduje, aby byl na klientském počítači nakonfigurovaný klient VPN. Další informace najdete v tématu [vytvoření připojení typu Point-to-site](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>Připojení ExpressRoute
ExpressRoute umožňuje připojit místní síť k Azure prostřednictvím privátního připojení. Pokud chcete vytvořit připojení, přečtěte si téma [vytvoření připojení ExpressRoute pomocí virtuální sítě WAN](virtual-wan-expressroute-portal.md).

### <a name="hub-to-vnet-connections"></a><a name="hub"></a>Připojení z centra na virtuální síť

Virtuální síť Azure se dá připojit k virtuálnímu rozbočovači. Další informace najdete v tématu [připojení virtuální sítě k rozbočovači](virtual-wan-site-to-site-portal.md#vnet).

### <a name="transit-connectivity"></a><a name="transit"></a>Přenosové připojení

#### <a name="transit-connectivity-between-vnets"></a><a name="transit-vnet"></a>Tranzitní připojení mezi virtuální sítě

Virtuální síť WAN umožňuje přenosové připojení mezi virtuální sítě. Virtuální sítě se připojit k virtuálnímu rozbočovači prostřednictvím připojení k virtuální síti. Přenosová konektivita mezi virtuální sítě ve **standardní virtuální síti WAN** je povolená v důsledku přítomnosti směrovače v každém virtuálním rozbočovači. Tento směrovač je vytvořen při prvním vytvoření virtuálního rozbočovače.

Směrovač může mít čtyři stavy směrování: zřizování, zřizování, neúspěšné nebo žádné. **Stav směrování** je umístěný v Azure Portal tak, že přejdete na stránku virtuálního centra.

* Stav **žádný** označuje, že virtuální rozbočovač nezřídil směrovač. K tomu může dojít, pokud je virtuální síť WAN typu *Basic*nebo pokud byl virtuální rozbočovač nasazen před zpřístupněním služby.
* Stav **selhání** indikuje selhání při vytváření instance. Chcete-li vytvořit instanci nebo resetovat směrovač, můžete najít možnost **resetovat směrovač** tak, že přejdete na stránku Přehled virtuálního centra v Azure Portal.

Každý směrovač virtuálního rozbočovače podporuje agregovanou propustnost až do 50 GB/s. Připojení mezi virtuálními síťovými připojeními předpokládá celkem 2000 úloh virtuálních počítačů v rámci všech virtuální sítě připojených k jednomu virtuálnímu rozbočovači.

#### <a name="transit-connectivity-between-vpn-and-expressroute"></a><a name="transit-er"></a>Tranzitní připojení mezi VPN a ExpressRoute

Virtuální síť WAN umožňuje přenosové připojení mezi VPN a ExpressRoute. To znamená, že weby připojené k síti VPN nebo vzdálení uživatelé můžou komunikovat s ExpressRoute lokalitami. Implicitně se předpokládá, že **příznak větvení na větev** je povolený a že je v připojeních VPN a ExpressRoute podporovaná podpora protokolu BGP. Tento příznak může být umístěný v nastavení Azure Virtual WAN v Azure Portal. Veškerou správu tras poskytuje směrovač virtuálního rozbočovače, který taky umožňuje přenosové připojení mezi virtuálními sítěmi.

### <a name="custom-routing"></a><a name="routing"></a>Vlastní směrování

Virtual WAN nabízí pokročilá vylepšení směrování. Možnost nastavení vlastních směrovacích tabulek, optimalizaci směrování virtuální sítě pomocí přiřazování a šíření tras, logických skupin směrování tabulek s popisky a zjednodušením scénářů směrování síťových virtuálních zařízení (síťové virtuální zařízení) nebo sdílených služeb.

### <a name="global-vnet-peering"></a><a name="global"></a>Globální partnerský vztah virtuální sítě

Globální VNet peering poskytuje mechanismus pro propojení dvou virtuální sítě v různých oblastech. Virtuální síťová připojení ve virtuální síti WAN připojují virtuální sítě k virtuálním rozbočovačům. Uživatel nemusí explicitně nastavovat globální partnerský vztah virtuální sítě. Virtuální sítě připojené k virtuálnímu rozbočovači ve stejné oblasti účtují poplatky za partnerské vztahy virtuální sítě. Virtuální sítě připojené k virtuálnímu rozbočovači v jiné oblasti účtují globální poplatky VNet peering.

### <a name="expressroute-traffic-encryption"></a><a name="encryption"></a>Šifrování provozu ExpressRoute

Azure Virtual WAN poskytuje možnost šifrovat ExpressRoute provoz. Technika poskytuje zašifrovaný přenos mezi místními sítěmi a virtuálními sítěmi Azure přes ExpressRoute, a to bez toho, aby prochází veřejným internetem nebo pomocí veřejných IP adres. Další informace najdete v tématu [protokol IPSec přes ExpressRoute pro virtuální síť WAN](vpn-over-expressroute.md).

## <a name="locations"></a><a name="locations"></a>Umístění

Informace o umístění naleznete v článku [virtuální partneři a umístění sítě WAN](virtual-wan-locations-partners.md) .

## <a name="route-tables-for-basic-and-standard-virtual-wans"></a><a name="route"></a>Směrovací tabulky pro virtuální sítě WAN úrovně Basic a Standard

Směrovací tabulky teď mají funkce pro přidružení a šíření. Již existující směrovací tabulka je směrovací tabulka, která nemá tyto funkce. Pokud máte již existující trasy ve směrování centra a chcete používat nové funkce, vezměte v úvahu následující skutečnosti:

* **Standardní virtuální zákazníci sítě WAN s již existujícími trasami ve virtuálním centru**: Pokud máte již existující trasy v části směrování pro centrum v Azure Portal, budete je muset nejdřív odstranit a pak se pokusit vytvořit nové směrovací tabulky (k dispozici v části směrovací tabulky pro centrum v Azure Portal).

* **Základní virtuální zákazníci sítě WAN s již existujícími trasami ve virtuálním centru**: Pokud máte již existující trasy v části směrování pro centrum v Azure Portal, budete je muset nejdřív odstranit a pak **upgradovat** základní virtuální síť WAN na standardní virtuální síť WAN. Viz [upgrade virtuální sítě WAN z úrovně Basic na standard](upgrade-virtual-wan.md).

## <a name="faq"></a><a name="faq"></a>Časté otázky

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="whats-new"></a><a name="new"></a>Co je nového?

Přihlaste se k odběru informačního kanálu RSS a zobrazte nejnovější aktualizace funkcí Virtual WAN na stránce s [aktualizacemi Azure](https://azure.microsoft.com/updates/?category=networking&query=VIRTUAL%20WAN) .

## <a name="next-steps"></a>Další kroky

[Vytvoření připojení typu Site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md)
