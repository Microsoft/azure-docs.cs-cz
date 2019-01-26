---
title: Partnerský vztah Azure Virtual Network
titlesuffix: Azure Virtual Network
description: Seznamte se s partnerskými vztahy virtuálních sítí v Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: 348a37c4b2d23d7c58e0599b4717b97e83b683ea
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081530"
---
# <a name="virtual-network-peering"></a>Partnerské vztahy virtuálních sítí

Partnerské vztahy virtuálních sítí umožňují bezproblémové propojení dvou [virtuálních sítí](virtual-networks-overview.md) Azure. Po navázání partnerského vztahu se virtuální sítě pro účely připojení jeví jako jedna síť. Provoz mezi virtuálními počítači v partnerských virtuálních sítích je směrován přes páteřní infrastrukturu Microsoftu v zásadě stejně jako mezi virtuálními počítači ve stejné virtuální síti pouze přes *privátní* IP adresy. Azure podporuje:
* VNet peering – propojení virtuálních sítí ve stejné oblasti Azure
* Globální VNet peering – propojení virtuálních sítí z různých oblastí Azure

Mezi výhody partnerských vztahů virtuálních sítí (místních i globálních) patří:

* Provoz mezi partnerskými virtuálními sítěmi je privátní. Provoz mezi virtuálními sítěmi probíhá na páteřní síti Microsoftu. Komunikace mezi partnerskými virtuálními sítěmi nevyžaduje veřejný internet, brány ani šifrování.
* Nízká latence a velká šířka pásma při propojení prostředků v různých virtuálních sítích.
* Možnost komunikace prostředků v jedné virtuální síti s prostředky v jiné virtuální síti, jakmile je vytvořený partnerský vztah těchto virtuálních sítí.
* Možnost přenášet data mezi předplatnými Azure, modely nasazení a oblastmi Azure.
* Možnost navázání partnerského vztahu mezi virtuálními sítěmi vytvořenými prostřednictvím modelu nasazení Azure Resource Manager, nebo mezi jednou virtuální sítí vytvořenou prostřednictvím modelu nasazení Resource Manager a druhou vytvořenou prostřednictvím modelu nasazení Classic. Další informace o modelech nasazení Azure najdete v článku [Vysvětlení modelů nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Žádný výpadek prostředků ve virtuálních sítích při vytváření partnerského vztahu ani po jeho vytvoření.

## <a name="connectivity"></a>Připojení

Po navázání partnerského vztahu mezi virtuálními sítěmi se prostředky v některé z virtuálních sítí můžou přímo připojit k prostředkům v partnerské virtuální síti.

Latence sítě mezi virtuálními počítači v partnerských virtuálních sítích ve stejné oblasti je stejná jako latence v rámci jedné virtuální sítě. Propustnost sítě závisí na šířce pásma, která je pro daný virtuální počítač povolená na základě jeho velikosti. S partnerským vztahem zde nejsou žádná další omezení týkající se šířky pásma.

Provoz mezi virtuálními počítači v partnerských virtuálních sítích je směrován přímo přes páteřní infrastrukturu Microsoftu, ne prostřednictvím brány ani přes veřejný internet.

Skupiny zabezpečení sítě je možné v obou virtuálních sítích v případě potřeby použít k blokování přístupu do jiných virtuálních sítí nebo podsítí.
Při konfiguraci partnerského vztahu virtuálních sítí můžete otevřít nebo zavřít pravidla skupiny zabezpečení sítě mezi virtuálními sítěmi. Pokud otevřete úplné propojení mezi partnerskými virtuálními sítěmi (výchozí volba), můžete pomocí skupin zabezpečení sítě u konkrétních podsítí nebo virtuálních počítačů blokovat nebo odepřít specifický přístup. Další informace o skupinách zabezpečení sítě najdete v tématu [Přehled skupin zabezpečení sítě](security-overview.md).

## <a name="service-chaining"></a>Řetězení služeb

Pokud chcete povolit řetězení služeb, nakonfigurujte uživatelsky definované trasy, které míří k virtuálním počítačům v partnerských virtuálních sítích (jako IP adresy *dalšího směrování*) nebo k jejich branám. Řetězení služeb umožňuje směrovat přenos dat z jedné virtuální sítě do virtuálního zařízení nebo brány virtuální sítě v partnerské virtuální síti prostřednictvím uživatelsky definovaných tras.

Hvězdicové sítě můžete nasadit, když centrální virtuální síť hostuje součásti infrastruktury, jako je virtuální síťové zařízení nebo služba VPN Gateway. Všechny uvedené virtuální sítě pak můžou vytvořit partnerský vztah s centrální virtuální sítí. Provoz v centrální virtuální síti probíhá přes virtuální síťové zařízení nebo brány VPN. 

Partnerské vztahy virtuálních sítí umožňují, aby další segment směrování v uživatelsky definované trase byl IP adresou virtuálního počítače v partnerské virtuální síti nebo branou VPN. K vytvoření trasy mezi virtuálními sítěmi ale nemůžete použít uživatelsky definovanou trasu, ve které jako další typ segmentu směrování použijete bránu ExpressRoute. Další informace o uživatelsky definovaných trasách najdete v [přehledu uživatelsky definovaných tras](virtual-networks-udr-overview.md#user-defined). Informace o vytvoření hvězdicové síťové topologie najdete v tématu věnovaném [hvězdicové síťové topologii](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Brány a místní připojení

Každá virtuální síť, bez ohledu na to, jestli má navázaný partnerský vztah s jinou virtuální sítí, může mít stále svou vlastní bránu a používat ji pro připojení k místní síti. Můžete také nakonfigurovat [připojení mezi virtuálními sítěmi](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pomocí bran, a to i v situaci, kdy jsou tyto virtuální sítě v partnerském vztahu.

Když jsou nakonfigurovány obě možnosti pro propojení virtuálních sítí, přenos dat mezi těmito sítěmi probíhá s použitím konfigurace partnerského vztahu (t.j. přes páteřní síť Azure).

Když je mezi virtuálními sítěmi navázán partnerský vztah ve stejné oblasti, můžete také v partnerské virtuální síti nakonfigurovat bránu, která bude sloužit jako tranzitní bod pro místní síť. V tomto případě virtuální síť, která používá vzdálenou bránu, nemůže mít svou vlastní bránu. Virtuální síť může mít pouze jednu bránu. Může jí být buď místní brána, nebo vzdálená brána (v partnerské virtuální síti), jak ukazuje následující obrázek:

![přenos dat při partnerských vztazích virtuálních sítí](./media/virtual-networks-peering-overview/figure04.png)

Průchod branou není podporován v případě, že jde o partnerský vztah mezi virtuálními sítěmi vytvořenými v různých oblastech. Pokud chcete používat průchod branou, musí obě partnerské virtuální sítě existovat ve stejné oblasti. Průchod branou mezi virtuálními sítěmi vytvořený pomocí různých modelů nasazení (Resource Manager a klasický) je podporovaný pouze v případě, že je brána ve virtuální síti (Resource Manager). Další informace o použití brány k průchodu najdete v tématu o [konfiguraci brány VPN pro průchod v partnerském vztahu virtuální sítě](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Po nastavení partnerského vztahu mezi virtuálními sítěmi sdílejícími jedno připojení Azure ExpressRoute přenos dat prochází skrze partnerský vztah (tj. prostřednictvím páteřní sítě Azure). K připojení k místnímu okruhu lze v obou sítích nadále používat místní brány. Alternativním postupem je použití sdílené brány a konfigurace průchodu pro místní připojení.

## <a name="troubleshoot"></a>Řešení potíží

Pokud chcete partnerský vztah virtuální sítě potvrdit, musíte [zkontrolovat platné trasy](diagnose-network-routing-problem.md) síťového rozhraní v každé podsíti virtuální sítě. Pokud partnerský vztah virtuální sítě existuje, mají všechny podsítě virtuální sítě trasy s typem dalšího přechodu *VNet peering*, a to u každého adresního prostoru v každé partnerské virtuální síti.

K řešení problémů s připojením k virtuálnímu počítači v partnerské virtuální síti také můžete použít [kontrolu připojení](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve službě Network Watcher. Kontrola připojení zobrazí směrování provozu od síťového rozhraní zdrojového virtuálního počítače k síťovému rozhraní cílového virtuálního počítače.

[Můžete také zkusit naše troucleshooter pro VNet Peering problémy](https://support.microsoft.com/help/4486956/troubleshooter-for-virtual-network-peering-issues)

## <a name="requirements-and-constraints"></a>Požadavky a omezení

Pokud jsou virtuální sítě v globálním partnerském vztahu, platí následující omezení:
- Virtuální sítě můžou existovat v jakékoli oblasti veřejného cloudu Azure, ale ne v národních cloudech Azure.
- Prostředky v jedné virtuální síti nemůžou komunikovat s front-endovou IP adresou interního nástroje pro vyrovnávání zatížení Azure ve virtuální síti v globálním partnerském vztahu. Nástroj pro vyrovnávání zatížení a prostředky, které s ním komunikují, musí být ve stejné oblasti.
- Nemůžete používat vzdálené brány ani povolit průchod bránou. Pokud chcete používat vzdálené brány nebo povolit průchod bránou, virtuální sítě v partnerském vztahu musí být ve stejné oblasti.

Další informace o požadavcích a omezeních najdete v tématu [Požadavky a omezení partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md#requirements-and-constraints). Informace o omezeních počtu partnerských vztahů, které pro virtuální síť můžete vytvořit, najdete v tématu popisujícím [omezení sítí Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

## <a name="permissions"></a>Oprávnění

Informace o oprávněních požadovaných k vytvoření partnerského vztahu virtuálních sítí najdete v tématu popisujícím [oprávnění pro partnerské vztahy virtuálních sítí](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Ceny

Za příchozí a výchozí přenos využívající partnerské propojení virtuálních sítí se účtuje nominální poplatek. Další informace o cenách partnerských vztahů virtuálních sítí a globálních partnerských vztahů virtuálních sítí najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/virtual-network).

Průchod bránou je vlastnost partnerského uzlu, která umožňuje, aby virtuální síť využila bránu VPN v partnerské virtuální síti pro připojení mezi více umístěními nebo virtuálními sítěmi. Datové přenosy procházející v takové situaci vzdálenou bránou podléhají [poplatkům za bránu VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/), ale ne [poplatkům za partnerský vztah virtuální sítě](https://azure.microsoft.com/pricing/details/virtual-network). Například pokud má virtuální síť VNetA bránu VPN pro připojení k místní síti a virtuální síť VNetB je v partnerském vztahu k virtuální síti VNetA s nakonfigurovanými příslušnými vlastnostmi, datové přenosy ze sítě VNetB do místní sítě se účtují jenom podle ceníku výchozích přenosů na bránu VPN. V této situaci se neúčtují poplatky za partnerský vztah virtuálních sítí. Zjistěte, jak [nakonfigurovat průchod bránou VPN pro partnerský vztah virtuální sítě](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="next-steps"></a>Další postup

* Partnerský vztah virtuálních sítí se vytváří mezi virtuálními sítěmi vytvořenými prostřednictvím stejného modelu nebo různých modelů nasazení, které existují ve stejném předplatném nebo různých předplatných. Dokončete kurz pro jeden z následujících scénářů:

    |Model nasazení Azure             | Předplatné  |
    |---------                          |---------|
    |Obě Resource Manager              |[Stejné](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Různé](create-peering-different-subscriptions.md)|
    |Jedna Resource Manager, druhá Classic  |[Stejné](create-peering-different-deployment-models.md)|
    |                                   |[Různé](create-peering-different-deployment-models-subscriptions.md)|

* Zjistěte, jak vytvořit [hvězdicovou síťovou topologii](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Prostudujte si všechna [nastavení partnerského vztahu virtuálních sítí a jejich změny](virtual-network-manage-peering.md).
* V tématu věnovaném [nejčastějším dotazům k partnerským vztahům virtuálních sítí](virtual-networks-faq.md#vnet-peering) najdete odpovědi na časté otázky ohledně partnerských vztahů virtuálních sítí a globálních partnerských vztahů virtuálních sítí.
