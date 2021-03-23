---
title: Partnerský vztah virtuální sítě Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si o partnerském vztahu virtuálních sítí v Azure, včetně toho, jak umožňuje připojit sítě v Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: altambaw
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: kumud
ms.openlocfilehash: 1356c896abff8c525df283251240d027123405fc
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778285"
---
# <a name="virtual-network-peering"></a>Peering virtuálních sítí

Partnerské vztahy virtuálních sítí umožňují bezproblémové připojení dvou nebo více [virtuálních sítí](virtual-networks-overview.md) v Azure. Virtuální sítě se zobrazí jako jedna pro účely připojení. Přenos dat mezi virtuálními počítači ve virtuálních sítích s navázaným partnerským vztahem používá páteřní infrastrukturu Microsoftu. Podobně jako provoz mezi virtuálními počítači ve stejné síti se provoz směruje jenom přes *privátní* síť Microsoftu.

Azure podporuje následující typy partnerských vztahů:

* **Partnerský vztah virtuální sítě**: Propojte virtuální sítě v rámci stejné oblasti Azure.
* **Globální partnerské vztahy virtuálních sítí**: propojení virtuálních sítí napříč oblastmi Azure.

Mezi výhody partnerských vztahů virtuálních sítí (místních i globálních) patří:

* Nízká latence a velká šířka pásma při propojení prostředků v různých virtuálních sítích.
* Schopnost prostředků v jedné virtuální síti komunikovat s prostředky v jiné virtuální síti.
* Možnost přenášet data mezi virtuálními sítěmi v rámci předplatných Azure, Azure Active Directory klienty, modely nasazení a oblastmi Azure.
* Možnost vytvoření partnerského vztahu virtuálních sítí vytvořených prostřednictvím Azure Resource Manager.
* Možnost navázání partnerského vztahu mezi virtuálními sítěmi vytvořenými prostřednictvím Správce prostředků, která je vytvořená prostřednictvím modelu nasazení Classic. Další informace o modelech nasazení Azure najdete v článku [Vysvětlení modelů nasazení Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Žádný výpadek prostředků ve virtuálních sítích při vytváření partnerského vztahu ani po jeho vytvoření.

Provoz mezi partnerskými virtuálními sítěmi je privátní. Provoz mezi virtuálními sítěmi probíhá na páteřní síti Microsoftu. Komunikace mezi partnerskými virtuálními sítěmi nevyžaduje veřejný internet, brány ani šifrování.

## <a name="connectivity"></a>Připojení

U partnerských virtuálních sítí se prostředky v některé z virtuálních sítí můžou přímo připojit k prostředkům v partnerské virtuální síti.

Latence sítě mezi virtuálními počítači v partnerských virtuálních sítích ve stejné oblasti je stejná jako latence v rámci jedné virtuální sítě. Propustnost sítě závisí na šířce pásma, která je pro daný virtuální počítač povolená na základě jeho velikosti. S partnerským vztahem zde nejsou žádná další omezení týkající se šířky pásma.

Provoz mezi virtuálními počítači v partnerských virtuálních sítích je směrován přímo přes páteřní infrastrukturu Microsoftu, ne prostřednictvím brány ani přes veřejný internet.

Skupiny zabezpečení sítě v obou virtuálních sítích můžete použít k blokování přístupu k ostatním virtuálním sítím nebo podsítím.
Při konfiguraci partnerského vztahu virtuálních sítí buď otevřete nebo zavřete pravidla skupiny zabezpečení sítě mezi virtuálními sítěmi. Pokud otevřete úplné propojení mezi partnerskými virtuálními sítěmi, můžete použít skupiny zabezpečení sítě a zablokovat nebo odepřít konkrétní přístup. Výchozí možností je úplné připojení. Další informace o skupinách zabezpečení sítě najdete v tématu [skupiny zabezpečení](./network-security-groups-overview.md).

## <a name="service-chaining"></a>Řetězení služeb

Řetězení služeb umožňuje směrovat provoz z jedné virtuální sítě do virtuálního zařízení nebo brány v partnerské síti prostřednictvím uživatelsky definovaných tras.

Pokud chcete povolit řetězení služeb, nakonfigurujte uživatelsky definované trasy, které odkazují na virtuální počítače v partnerských virtuálních sítích jako na IP adresu *dalšího segmentu směrování* . Trasy definované uživatelem můžou také odkazovat na brány virtuální sítě, aby se povolily řetězení služeb.

Můžete nasadit sítě rozbočovače a paprsků, ve kterých virtuální síť rozbočovače hostuje součásti infrastruktury, jako je síťové virtuální zařízení nebo brána sítě VPN. Všechny uvedené virtuální sítě pak můžou vytvořit partnerský vztah s centrální virtuální sítí. Přenos toků přes síťová virtuální zařízení nebo brány sítě VPN ve virtuální síti rozbočovače.

Partnerské vztahy virtuálních sítí umožňují, aby další segment směrování v uživatelsky definované trase byl IP adresou virtuálního počítače v partnerské virtuální síti nebo branou VPN. Mezi virtuálními sítěmi nelze směrovat pomocí uživatelsky definované trasy, která jako typ dalšího segmentu směrování určuje bránu Azure ExpressRoute. Další informace o uživatelsky definovaných trasách najdete v [přehledu uživatelsky definovaných tras](virtual-networks-udr-overview.md#user-defined). Informace o vytvoření hvězdicové síťové topologie najdete v tématu [Síťová topologie centra s paprsky v Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Brány a místní připojení

Každá virtuální síť, včetně partnerské virtuální sítě, může mít svou vlastní bránu. Virtuální síť může použít bránu pro připojení k místní síti. Můžete také nakonfigurovat [připojení virtuální sítě k virtuální síti](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pomocí bran, a to i u partnerských virtuálních sítí.

Při konfiguraci obou možností pro virtuální síť vzájemné propojení přenos dat mezi virtuálními sítěmi projde konfigurací partnerského vztahu. Přenos využívá páteřní síť Azure.

Bránu můžete také nakonfigurovat ve virtuální síti s partnerským vztahem jako tranzitní bod pro místní síť. V takovém případě virtuální síť, která používá vzdálenou bránu, nemůže mít svou vlastní bránu. Virtuální síť má pouze jednu bránu. Brána je místní nebo vzdálená brána v partnerské virtuální síti, jak je znázorněno v následujícím diagramu:

![přenos dat při partnerských vztazích virtuálních sítí](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Přenos brány podporuje partnerský vztah virtuálních sítí i globální partnerské vztahy virtuálních sítí.

Podporuje se přenos bran mezi virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Brána musí být ve virtuální síti v modelu Správce prostředků. Další informace o použití brány k průchodu najdete v tématu o [konfiguraci brány VPN pro průchod v partnerském vztahu virtuální sítě](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Při vytváření partnerského vztahu virtuálních sítí, které sdílejí jedno připojení Azure ExpressRoute, procházejí přenosy mezi nimi prostřednictvím vztahu partnerských vztahů. Tento provoz používá páteřní síť Azure. K připojení k místnímu okruhu lze v obou sítích nadále používat místní brány. V opačném případě můžete použít sdílenou bránu a nakonfigurovat přenos pro místní připojení.

## <a name="troubleshoot"></a>Řešení potíží

Pokud chcete potvrdit, že jsou virtuální sítě v partnerském vztahu, můžete zkontrolovat efektivní trasy. Podívejte se na trasy pro síťové rozhraní v jakékoli podsíti ve virtuální síti. Pokud partnerský vztah virtuální sítě existuje, mají všechny podsítě virtuální sítě trasy s typem dalšího přechodu *VNet peering*, a to u každého adresního prostoru v každé partnerské virtuální síti. Další informace najdete v tématu [Diagnostika problému s směrováním virtuálního počítače](diagnose-network-routing-problem.md).

Můžete také řešit potíže s připojením k virtuálnímu počítači v partnerské virtuální síti pomocí Azure Network Watcher. Při kontrole připojení se zobrazí informace o směrování provozu ze síťového rozhraní zdrojového virtuálního počítače do síťového rozhraní cílového virtuálního počítače. Další informace najdete v tématu [řešení potíží s připojením k Azure Network Watcher pomocí Azure Portal](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Můžete také zkusit [řešit problémy s partnerským vztahem virtuální sítě](virtual-network-troubleshoot-peering-issues.md).

## <a name="constraints-for-peered-virtual-networks"></a>Omezení pro virtuální sítě s partnerským vztahem<a name="requirements-and-constraints"></a>

Následující omezení platí pouze v případě, že jsou virtuální sítě globálně partnerského vztahu:

* Prostředky v jedné virtuální síti nemůžou komunikovat s front-end IP adresou základního interního Load Balancer (interního nástroje) v globálně partnerské virtuální síti.
* Některé služby, které používají základní nástroj pro vyrovnávání zatížení, nefungují prostřednictvím globálního partnerského vztahu virtuálních sítí. Další informace najdete v tématu [jaká omezení se týkají globálních partnerských vztahů mezi virtuálními sítěmi a vyrovnávání zatížení](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

Další informace najdete v tématu [požadavky a omezení](virtual-network-manage-peering.md#requirements-and-constraints). Další informace o podporovaném počtu partnerských vztahů najdete v tématu [omezení sítě](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>Oprávnění

Další informace o oprávněních potřebných k vytvoření partnerského vztahu virtuálních sítí najdete v tématu [oprávnění](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Ceny

U příchozího a odchozího provozu, který používá připojení partnerského vztahu virtuálních sítí, se účtuje nominální poplatek. Další informace najdete v tématu [Virtual Network ceny](https://azure.microsoft.com/pricing/details/virtual-network).

Přenos brány je vlastnost partnerského vztahu, která umožňuje virtuální síti využívat bránu VPN/ExpressRoute v partnerské virtuální síti. Přenos brány funguje jak pro připojení mezi různými místy, tak pro připojení k síti. Provoz do brány (příchozí nebo odchozí) v partnerské virtuální síti vychází z poplatků za partnerské vztahy virtuálních sítí ve virtuální síti rozbočovače (nebo virtuální sítě bez brány). Další informace najdete v tématu [ceny za VPN Gateway cen](https://azure.microsoft.com/pricing/details/vpn-gateway/) za poplatky za brány VPN a ceny ExpressRoute brány pro poplatky za ExpressRoute brány.

>[!NOTE]
> Předchozí verze tohoto dokumentu uvedla, že poplatky za partnerský vztah virtuálních sítí se nevztahují na virtuální síť rozbočovače (nebo virtuální síť mimo bránu) s přenosem brány. Nyní odráží přesné ceny na stránce s cenami.

## <a name="next-steps"></a>Další kroky

* Můžete vytvořit partnerský vztah mezi dvěma virtuálními sítěmi. Sítě můžou patřit do stejného předplatného, různých modelů nasazení ve stejném předplatném nebo různých předplatných. Dokončete kurz pro jeden z následujících scénářů:

    |Model nasazení Azure             | Předplatné  |
    |---------                          |---------|
    |Obě Resource Manager              |[Stejné](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Různé](create-peering-different-subscriptions.md)|
    |Jedna Resource Manager, druhá Classic  |[Stejné](create-peering-different-deployment-models.md)|
    |                                   |[Různé](create-peering-different-deployment-models-subscriptions.md)|

* Informace o vytvoření hvězdicové síťové topologie najdete v tématu [Síťová topologie centra s paprsky v Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Další informace o nastaveních partnerského vztahu virtuálních sítí najdete v tématu [Vytvoření, změna nebo odstranění partnerského vztahu virtuální sítě](virtual-network-manage-peering.md).
* Odpovědi na běžné otázky týkající se partnerského vztahu virtuální sítě a globálních vztahů mezi virtuálními sítěmi najdete v tématu [VNet peering](virtual-networks-faq.md#vnet-peering).
