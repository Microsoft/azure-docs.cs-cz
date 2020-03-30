---
title: Partnerský vztah virtuální sítě Azure
titlesuffix: Azure Virtual Network
description: Seznamte se s partnerskými vztahy virtuálních sítí v Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: 5fb54e812e72b9393ffdf632085d0f32ab8b1988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279543"
---
# <a name="virtual-network-peering"></a>Partnerské vztahy virtuálních sítí

Partnerský vztah virtuální chýše umožňuje bezproblémově připojovat sítě ve [virtuální síti Azure](virtual-networks-overview.md). Virtuální sítě se zobrazí jako jeden pro účely připojení. Provoz mezi virtuálními počítači používá páteřní infrastrukturu Microsoftu. Podobně jako provoz mezi virtuálními počítači ve stejné síti je provoz směrován pouze přes *privátní* síť společnosti Microsoft.

Azure podporuje následující typy partnerského vztahu:

* Partnerský vztah virtuální sítě: Připojte virtuální sítě ve stejné oblasti Azure.
* Partnerský vztah globální virtuální sítě: Připojení virtuálních sítí napříč oblastmi Azure.

Mezi výhody partnerských vztahů virtuálních sítí (místních i globálních) patří:

* Nízká latence a velká šířka pásma při propojení prostředků v různých virtuálních sítích.
* Možnost prostředků v jedné virtuální síti komunikovat s prostředky v jiné virtuální síti.
* Možnost přenosu dat mezi virtuálními sítěmi napříč předplatnými Azure, tenanty Azure Active Directory, modely nasazení a oblastmi Azure.
* Možnost partnerských virtuálních sítí vytvořených prostřednictvím Správce prostředků Azure.
* Možnost peer virtuální sítě vytvořené prostřednictvím Správce prostředků na jeden vytvořený prostřednictvím klasického modelu nasazení. Další informace o modelech nasazení Azure najdete v článku [Vysvětlení modelů nasazení Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Žádný výpadek prostředků ve virtuálních sítích při vytváření partnerského vztahu ani po jeho vytvoření.

Provoz mezi partnerskými virtuálními sítěmi je privátní. Provoz mezi virtuálními sítěmi probíhá na páteřní síti Microsoftu. Komunikace mezi partnerskými virtuálními sítěmi nevyžaduje veřejný internet, brány ani šifrování.

## <a name="connectivity"></a>Připojení

U virtuálních sítí s partnerským vztahem se prostředky v obou virtuálních sítích mohou přímo připojit k prostředkům ve virtuální síti partnerského vztahu.

Latence sítě mezi virtuálními počítači v partnerských virtuálních sítích ve stejné oblasti je stejná jako latence v rámci jedné virtuální sítě. Propustnost sítě závisí na šířce pásma, která je pro daný virtuální počítač povolená na základě jeho velikosti. S partnerským vztahem zde nejsou žádná další omezení týkající se šířky pásma.

Provoz mezi virtuálními počítači v partnerských virtuálních sítích je směrován přímo přes páteřní infrastrukturu Microsoftu, ne prostřednictvím brány ani přes veřejný internet.

Skupiny zabezpečení sítě můžete použít ve virtuální síti a blokovat tak přístup k jiným virtuálním sítím nebo podsítím.
Při konfiguraci partnerského vztahu virtuální sítě otevřete nebo zavřete pravidla skupiny zabezpečení sítě mezi virtuálními sítěmi. Pokud otevřete úplné připojení mezi partnerskými virtuálními sítěmi, můžete použít skupiny zabezpečení sítě k zablokování nebo odepření konkrétního přístupu. Výchozí možností je úplné připojení. Další informace o skupinách zabezpečení sítě naleznete v [tématu Skupiny zabezpečení](security-overview.md).

## <a name="service-chaining"></a>Řetězení služeb

Řetězení služeb umožňuje směrovat provoz z jedné virtuální sítě do virtuálního zařízení nebo brány v partnerské síti prostřednictvím uživatelem definovaných tras.

Chcete-li povolit zřetězení služeb, nakonfigurujte uživatelem definované trasy, které odkazují na virtuální počítače v partnerských virtuálních sítích jako další IP adresu *směrování.* Uživatelem definované trasy mohou také přejděte na brány virtuální sítě, které umožňují řetězení služeb.

Můžete nasadit sítě rozbočovače a paprsku, kde virtuální síť rozbočovače hostuje součásti infrastruktury, jako je síťové virtuální zařízení nebo brána VPN. Všechny uvedené virtuální sítě pak můžou vytvořit partnerský vztah s centrální virtuální sítí. Přenosy proudí přes síťová virtuální zařízení nebo brány VPN ve virtuální síti rozbočovače.

Partnerské vztahy virtuálních sítí umožňují, aby další segment směrování v uživatelsky definované trase byl IP adresou virtuálního počítače v partnerské virtuální síti nebo branou VPN. Mezi virtuálními sítěmi nelze směrovat pomocí uživatelem definované trasy, která určuje bránu Azure ExpressRoute jako další typ směrování. Další informace o uživatelsky definovaných trasách najdete v [přehledu uživatelsky definovaných tras](virtual-networks-udr-overview.md#user-defined). Informace o tom, jak vytvořit topologii sítě rozbočovače a paprsku, najdete [v tématu Topologie sítě hubových paprsků v Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Brány a místní připojení

Každá virtuální síť, včetně partnerské virtuální sítě, může mít vlastní bránu. Virtuální síť může používat svou bránu k připojení k místní síti. [Připojení virtuálních sítí k virtuální síti](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) můžete také nakonfigurovat pomocí bran, a to i pro virtuální sítě partnerského vztahu.

Při konfiguraci obou možností propojení virtuálních sítí, provoz mezi virtuálními sítěmi toky prostřednictvím konfigurace partnerského vztahu. Provoz používá páteřní sítě Azure.

Bránu můžete také nakonfigurovat ve virtuální síti partnerského vztahu jako tranzitní bod do místní sítě. V takovém případě virtuální síť, která používá vzdálenou bránu nemůže mít vlastní bránu. Virtuální síť má pouze jednu bránu. Brána je místní nebo vzdálená brána ve virtuální síti partnerského vztahu, jak je znázorněno na následujícím diagramu:

![přenos dat při partnerských vztazích virtuálních sítí](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Partnerský vztah virtuální sítě i globální partnerský vztah virtuální sítě podporují přenos brány podpory.

Je podporován přenos brány mezi virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Brána musí být ve virtuální síti v modelu Správce prostředků. Další informace o použití brány k průchodu najdete v tématu o [konfiguraci brány VPN pro průchod v partnerském vztahu virtuální sítě](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Když se střídáte virtuální sítě, které sdílejí jedno připojení Azure ExpressRoute, provoz mezi nimi prochází partnerský vztah. Tento provoz používá páteřní síť Azure. K připojení k místnímu okruhu lze v obou sítích nadále používat místní brány. V opačném případě můžete použít sdílenou bránu a nakonfigurovat přenos pro místní připojení.

## <a name="troubleshoot"></a>Řešení potíží

Chcete-li ověřit, že virtuální sítě jsou peered, můžete zkontrolovat efektivní trasy. Zkontrolujte trasy pro síťové rozhraní v libovolné podsíti ve virtuální síti. Pokud partnerský vztah virtuální sítě existuje, mají všechny podsítě virtuální sítě trasy s typem dalšího přechodu *VNet peering*, a to u každého adresního prostoru v každé partnerské virtuální síti. Další informace naleznete [v tématu Diagnostika problému směrování virtuálního počítače](diagnose-network-routing-problem.md).

Můžete také řešit potíže s připojením k virtuálnímu počítači ve virtuální síti s partnerským počítačem pomocí Nástroje sledování sítě Azure. Kontrola připojení umožňuje zobrazit způsob směrování provozu ze zdrojového síťového rozhraní virtuálního počítače do síťového rozhraní cílového virtuálního počítače. Další informace najdete [v tématu Poradce při potížích s připojením pomocí Azure Network Watcher pomocí portálu Azure](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Můžete také [zkusit poradce při potížích s partnerským vztahem virtuální sítě](virtual-network-troubleshoot-peering-issues.md).

## <a name="constraints-for-peered-virtual-networks"></a>Omezení pro partnerské virtuální sítě<a name="requirements-and-constraints"></a>

Následující omezení platí pouze v případě, že virtuální sítě jsou globálně partnerský:

* Prostředky v jedné virtuální síti nemohou komunikovat s front-endovou IP adresou základního nástroje pro vyrovnávání zatížení (ILB) v globálně partnerské virtuální síti.
* Některé služby, které používají základní nástroj pro vyrovnávání zatížení nefungují přes partnerský vztah globální virtuální sítě. Další informace najdete [v tématu Jaká jsou omezení související s globálním partnerským vztahem virtuální sítě a vykladači zatížení?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

Další informace naleznete v [tématu Požadavky a omezení](virtual-network-manage-peering.md#requirements-and-constraints). Další informace o podporovaném počtu partnerských stran naleznete v tématu [Omezení sítě](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>Oprávnění

Informace o oprávněních potřebných k vytvoření partnerského vztahu virtuální sítě naleznete v [tématu Oprávnění](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Ceny

Je nominální poplatek za příchozí a odchozí přenosy, které používá připojení partnerského vztahu virtuální sítě. Další informace naleznete v tématu [Ceny virtuální sítě](https://azure.microsoft.com/pricing/details/virtual-network).

Gateway Transit je vlastnost partnerského vztahu, která umožňuje virtuální síti využívat bránu VPN/ExpressRoute ve virtuální síti s partnerským vztahem. Přenos brány funguje jak pro připojení mezi místními prostory, tak pro připojení k síti do sítě. Provoz do brány (příchozí nebo odchozí) v partnerské virtuální síti účtuje poplatky za partnerský vztah virtuální sítě na virtuální síti pro paprsky (nebo virtuální sítě bez brány). Další informace najdete v [tématu ceny brány VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) pro poplatky za brány VPN a ceny brány ExpressRoute pro poplatky za bránu ExpressRoute.

>[!NOTE]
> Předchozí verze tohoto dokumentu uvádí, že poplatky za partnerský vztah virtuální sítě se nevztahují na virtuální síť pro paprsky (nebo virtuální síť bez brány) s přenosem brány. Nyní odráží přesné ceny na stránce s cenami.

## <a name="next-steps"></a>Další kroky

* Můžete vytvořit partnerský vztah mezi dvěma virtuálními sítěmi. Sítě mohou patřit do stejného předplatného, různých modelů nasazení ve stejném předplatném nebo různých předplatných. Dokončete kurz pro jeden z následujících scénářů:

    |Model nasazení Azure             | Předplatné  |
    |---------                          |---------|
    |Obě Resource Manager              |[Stejné](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Různé](create-peering-different-subscriptions.md)|
    |Jedna Resource Manager, druhá Classic  |[Stejné](create-peering-different-deployment-models.md)|
    |                                   |[Různé](create-peering-different-deployment-models-subscriptions.md)|

* Informace o tom, jak vytvořit topologii sítě rozbočovače a paprsku, najdete [v tématu Topologie sítě hubových paprsků v Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Informace o všech nastaveních partnerského vztahu virtuální sítě najdete v [tématu Vytvoření, změna nebo odstranění partnerského vztahu virtuální sítě](virtual-network-manage-peering.md).
* Odpovědi na společné partnerské vztahy virtuální sítě a otázky globálního partnerského vztahu virtuální sítě najdete v [tématu Partnerský vztah virtuální sítě](virtual-networks-faq.md#vnet-peering).
