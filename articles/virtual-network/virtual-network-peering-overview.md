---
title: Partnerský vztah Azure Virtual Network
titlesuffix: Azure Virtual Network
description: Přečtěte si o partnerském vztahu virtuálních sítí v Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2019
ms.author: anavin
ms.openlocfilehash: a53d5810b20aa8389c152889fed5d7f4e8cfc5b7
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177675"
---
# <a name="virtual-network-peering"></a>Partnerský vztah virtuální sítě

Partnerské vztahy virtuálních sítí umožňují bezproblémové připojení [virtuálních sítí](virtual-networks-overview.md)Azure. Po navázání partnerského vztahu se virtuální sítě zobrazí jako jedna pro účely připojení. Přenos dat mezi virtuálními počítači v partnerských virtuálních sítích je směrován přes páteřní infrastrukturu Microsoftu, podobně jako provoz se směruje mezi virtuálními počítači ve stejné virtuální síti, a to pouze pomocí *privátních* IP adres. Azure podporuje:
* VNet peering – propojení virtuální sítě ve stejné oblasti Azure
* Globální VNet peering – propojení virtuální sítě napříč oblastmi Azure

Výhody použití partnerského vztahu virtuálních sítí, ať už místní, nebo globální, zahrnují:

* Síťový provoz mezi partnerskými virtuálními sítěmi je privátní. Provoz mezi virtuálními sítěmi se udržuje v páteřní síti Microsoftu. V komunikaci mezi virtuálními sítěmi se nevyžaduje žádný veřejný Internet, brány ani šifrování.
* Síť s nízkou latencí a velkou šířkou pásma mezi prostředky v různých virtuálních sítích.
* Schopnost prostředků v jedné virtuální síti komunikovat s prostředky v jiné virtuální síti, jakmile se vytvoří partnerský vztah virtuálních sítí.
* Možnost přenášet data mezi předplatnými Azure, modely nasazení a různými oblastmi Azure.
* Možnost navázání partnerského vztahu virtuálních sítí vytvořených prostřednictvím Azure Resource Manager nebo pro partnerský vztah jedné virtuální sítě vytvořené prostřednictvím Správce prostředků do virtuální sítě vytvořené prostřednictvím modelu nasazení Classic. Další informace o modelech nasazení Azure najdete v tématu [Principy modelů nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Žádná výpadky prostředků v žádné virtuální síti při vytváření partnerského vztahu nebo po vytvoření partnerského vztahu.

## <a name="connectivity"></a>Připojení

Po navázání partnerského vztahu mezi virtuálními sítěmi se prostředky v některé z virtuálních sítí můžou přímo připojit k prostředkům v partnerské virtuální síti.

Latence sítě mezi virtuálními počítači v partnerských virtuálních sítích ve stejné oblasti je stejná jako latence v rámci jedné virtuální sítě. Propustnost sítě vychází z šířky pásma, která je pro virtuální počítač povolená, úměrná jeho velikosti. V rámci partnerského vztahu neexistují žádná další omezení šířky pásma.

Provoz mezi virtuálními počítači v partnerských virtuálních sítích je směrován přímo přes páteřní infrastrukturu Microsoftu, nikoli prostřednictvím brány nebo přes veřejný Internet.

Skupiny zabezpečení sítě je možné v obou virtuálních sítích použít k blokování přístupu do jiných virtuálních sítí nebo podsítí (v případě potřeby).
Při konfiguraci partnerského vztahu virtuálních sítí můžete buď otevřít nebo zavřít pravidla skupiny zabezpečení sítě mezi virtuálními sítěmi. Pokud otevřete úplné propojení mezi partnerskými virtuálními sítěmi (což je výchozí možnost), můžete použít skupiny zabezpečení sítě u konkrétních podsítí nebo virtuálních počítačů k blokování nebo odepření konkrétního přístupu. Další informace o skupinách zabezpečení sítě najdete v tématu [Přehled skupin zabezpečení sítě](security-overview.md).

## <a name="service-chaining"></a>Řetězení služeb

Můžete nakonfigurovat uživatelsky definované trasy, které odkazují na virtuální počítače v partnerských virtuálních sítích jako na IP adresu *dalšího segmentu směrování* , nebo na brány virtuální sítě, aby bylo možné řetězení služeb povolit. Řetězení služeb umožňuje směrovat provoz z jedné virtuální sítě do virtuálního zařízení nebo z brány virtuální sítě v partnerské virtuální síti prostřednictvím uživatelsky definovaných tras.

Můžete nasadit hvězdicové sítě, kde může rozbočovač virtuální síť hostovat součásti infrastruktury, jako je síťové virtuální zařízení nebo brána sítě VPN. Všechny virtuální sítě paprsků se pak můžou navázat na síť s rozbočovačem virtuální sítě. Provoz se může směrovat přes síťová virtuální zařízení nebo brány VPN ve virtuální síti rozbočovače. 

Partnerský vztah virtuálních sítí umožňuje dalšímu směrování v uživatelsky definované trase být IP adresa virtuálního počítače v partnerské virtuální síti nebo bráně VPN. Nemůžete ale směrovat mezi virtuálními sítěmi s uživatelem definovanou trasou, která jako typ dalšího segmentu směrování určuje ExpressRoute bránu. Další informace o trasách definovaných uživatelem najdete v tématu [Přehled uživatelsky definovaných tras](virtual-networks-udr-overview.md#user-defined). Informace o vytvoření hvězdicové síťové topologie najdete v tématu [topologie sítě centra a paprsků](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Brány a místní připojení

Každá virtuální síť, bez ohledu na to, jestli má partnerský vztah s jinou virtuální sítí, může mít stále svou vlastní bránu a používat ji pro připojení k místní síti. Můžete také nakonfigurovat [připojení virtuální sítě k virtuální síti](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pomocí bran, a to i v případě, že jsou tyto virtuální sítě v partnerském vztahu.

Když jsou nakonfigurované obě možnosti pro vzájemné propojení virtuální sítě, přenos mezi virtuálními sítěmi projde konfigurací partnerského vztahu (tj. prostřednictvím páteřní sítě Azure).

Při navázání partnerského vztahu virtuálních sítí můžete bránu nakonfigurovat také v partnerské virtuální síti jako tranzitní bod pro místní síť. V takovém případě virtuální síť, která používá vzdálenou bránu, nemůže mít svou vlastní bránu. Virtuální síť může mít pouze jednu bránu. Bránou může být buď místní brána, nebo vzdálená brána (v partnerské virtuální síti), jak je znázorněno na následujícím obrázku:

![přenos partnerských vztahů virtuálních sítí](./media/virtual-networks-peering-overview/figure04.png)

Přenos brány se podporuje jak pro partnerský vztah virtuálních sítí, tak pro globální partnerský vztah VNet. Přenos brány mezi virtuálními sítěmi vytvořenými pomocí různých modelů nasazení (Správce prostředků a klasický) se podporuje jenom v případě, že je brána ve virtuální síti (Správce prostředků). Další informace o používání brány pro přenos najdete v tématu [Konfigurace brány VPN pro přenos v partnerském vztahu virtuálních sítí](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Když jsou partnerské vztahy mezi virtuálními sítěmi, které sdílí jedno připojení Azure ExpressRoute, procházejí partnerským vztahem (to znamená prostřednictvím páteřní sítě Azure). V každé virtuální síti můžete pořád používat místní brány pro připojení k místnímu okruhu. Alternativně můžete použít sdílenou bránu a nakonfigurovat přenos pro místní připojení.

## <a name="troubleshoot"></a>Řešení potíží

Pro potvrzení partnerského vztahu virtuální sítě můžete [zkontrolovat efektivní trasy](diagnose-network-routing-problem.md) pro síťové rozhraní v libovolné podsíti ve virtuální síti. Pokud existuje partnerský vztah virtuální sítě, všechny podsítě v rámci virtuální sítě mají trasy s typem dalšího segmentu směrování *VNet peering*, a to pro každý adresní prostor v každé partnerské virtuální síti.

Můžete také řešit potíže s připojením k virtuálnímu počítači v partnerské virtuální síti pomocí [kontroly připojení](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Network Watcher. Kontroly připojení vám umožní zjistit, jak se provoz směruje ze síťového rozhraní zdrojového virtuálního počítače do síťového rozhraní cílového virtuálního počítače.

Můžete taky zkusit [Poradce při potížích s problémy s partnerským vztahem virtuální sítě](https://support.microsoft.com/help/4486956/troubleshooter-for-virtual-network-peering-issues).

## <a name="requirements-and-constraints"></a>Požadavky a omezení

Následující omezení platí pouze v případě, že jsou virtuální sítě globálně partnerského vztahu:
- Prostředky v jedné virtuální síti nemůžou komunikovat s front-end IP adresou základního interního nástroje pro vyrovnávání zatížení v globálně partnerské virtuální síti. Podpora pro základní Load Balancer existuje pouze ve stejné oblasti. Podpora Standard Load Balancer existuje jak pro, tak pro vytváření partnerských vztahů virtuálních sítí i globálních partnerských vztahů mezi virtuálními sítěmi. Služby, které používají základní nástroj pro vyrovnávání zatížení, který nefunguje přes globální partnerský vztah virtuálních sítí, jsou popsané [tady.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)

Další informace o požadavcích a omezeních najdete v tématu [požadavky a omezení pro partnerský vztah virtuálních sítí](virtual-network-manage-peering.md#requirements-and-constraints). Další informace o omezeních počtu partnerských vztahů, které můžete vytvořit pro virtuální síť, najdete v tématu [omezení sítě Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

## <a name="permissions"></a>Oprávnění

Další informace o oprávněních potřebných k vytvoření partnerského vztahu virtuálních sítí najdete v tématu věnovaném [oprávněním partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Ceny

Za příchozí a odchozí provoz, který využívá připojení partnerského vztahu virtuálních sítí, se účtuje nominální poplatek. Další informace o vytváření partnerských vztahů virtuálních sítí a globálních vztahů mezi virtuálními sítěmi najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/virtual-network).

Přenos brány je vlastnost partnerského vztahu, která umožňuje virtuální síti využívat bránu VPN/ExpressRoute v partnerské virtuální síti pro připojení mezi různými místy nebo připojení VNet-to-VNet. Provoz do brány (příchozí nebo odchozí) ve virtuální síti s partnerským vztahem bude mít za následek poplatky za vytváření partnerských vztahů virtuální sítě. Další podrobnosti najdete v tématu [poplatky za služby VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/) nebo poplatky za ExpressRoute a připojení k [partnerským sítím VNET.](https://azure.microsoft.com/pricing/details/virtual-network)

## <a name="next-steps"></a>Další kroky

* Partnerský vztah virtuální sítě se vytvoří mezi virtuálními sítěmi vytvořenými prostřednictvím stejných nebo různých modelů nasazení, které existují ve stejných nebo různých předplatných. Dokončete kurz pro jeden z následujících scénářů:

    |Model nasazení Azure             | Formě  |
    |---------                          |---------|
    |Jak Správce prostředků              |[Jedné](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Jiný](create-peering-different-subscriptions.md)|
    |Jeden Správce prostředků, jeden klasický  |[Jedné](create-peering-different-deployment-models.md)|
    |                                   |[Jiný](create-peering-different-deployment-models-subscriptions.md)|

* Naučte se vytvářet [síťové topologie hvězdicové a Paprskové sítě](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Přečtěte si o [nastaveních partnerského vztahu virtuálních sítí a o tom, jak je změnit](virtual-network-manage-peering.md).
* [Nejčastější](virtual-networks-faq.md#vnet-peering) dotazy k běžným partnerským partnerským vztahům virtuálních sítí a globálním partnerským partnerským vztahům k virtuální síti
