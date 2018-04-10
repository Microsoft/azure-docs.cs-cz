---
title: Partnerské vztahy virtuálních sítí v Azure | Dokumentace Microsoftu
description: Seznamte se s partnerskými vztahy virtuálních sítí v Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: jdial
ms.openlocfilehash: 69264ac7ffe8124b964a10c11aacaca5c712fd63
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="virtual-network-peering"></a>Partnerské vztahy virtuálních sítí

Partnerské vztahy virtuálních sítí umožňují bezproblémové propojení dvou [virtuálních sítí](virtual-networks-overview.md) Azure. Po navázání partnerského vztahu se virtuální sítě pro účely připojení jeví jako jedna síť. Provoz mezi virtuálními počítači v partnerských virtuálních sítích je směrován přes páteřní infrastrukturu Microsoftu v zásadě stejně jako mezi virtuálními počítači ve stejné virtuální síti pouze přes *privátní* IP adresy. 

Mezi výhody použití partnerských vztahů virtuálních sítí patří:

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
Při konfiguraci partnerského vztahu virtuálních sítí můžete otevřít nebo zavřít pravidla skupiny zabezpečení sítě mezi virtuálními sítěmi. Pokud otevřete úplné propojení mezi partnerskými virtuálními sítěmi (výchozí volba), můžete pomocí skupin zabezpečení sítě u konkrétních podsítí nebo virtuálních počítačů blokovat nebo odepřít specifický přístup. Další informace o skupinách zabezpečení sítě najdete v tématu [Přehled skupin zabezpečení sítě](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Řetězení služeb

Pokud chcete povolit řetězení služeb, nakonfigurujte uživatelsky definované trasy, které míří k virtuálním počítačům v partnerských virtuálních sítích (jako IP adresy *dalšího směrování*) nebo k jejich branám. Řetězení služeb umožňuje směrovat přenos dat z jedné virtuální sítě do virtuálního zařízení nebo brány virtuální sítě v partnerské virtuální síti prostřednictvím uživatelsky definovaných tras.

Hvězdicové sítě můžete nasadit, když centrální virtuální síť hostuje součásti infrastruktury, jako je virtuální síťové zařízení nebo služba VPN Gateway. Všechny uvedené virtuální sítě pak můžou vytvořit partnerský vztah s centrální virtuální sítí. Provoz v centrální virtuální síti probíhá přes virtuální síťové zařízení nebo brány VPN. 

Partnerské vztahy virtuálních sítí umožňují, aby další segment směrování v uživatelsky definované trase byl IP adresou virtuálního počítače v partnerské virtuální síti nebo branou VPN. K vytvoření trasy mezi virtuálními sítěmi ale nemůžete použít uživatelsky definovanou trasu, ve které jako další typ segmentu směrování použijete bránu ExpressRoute. Další informace o uživatelsky definovaných trasách najdete v [přehledu uživatelsky definovaných tras](virtual-networks-udr-overview.md#user-defined). Informace o vytvoření hvězdicové síťové topologie najdete v tématu věnovaném [hvězdicové síťové topologii](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).

## <a name="gateways-and-on-premises-connectivity"></a>Brány a místní připojení

Každá virtuální síť, bez ohledu na to, jestli má navázaný partnerský vztah s jinou virtuální sítí, může mít stále svou vlastní bránu a používat ji pro připojení k místní síti. Můžete také nakonfigurovat [připojení mezi virtuálními sítěmi](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pomocí bran, a to i v situaci, kdy jsou tyto virtuální sítě v partnerském vztahu.

Když jsou nakonfigurovány obě možnosti pro propojení virtuálních sítí, přenos dat mezi těmito sítěmi probíhá s použitím konfigurace partnerského vztahu (t.j. přes páteřní síť Azure).

Když je mezi virtuálními sítěmi navázán partnerský vztah ve stejné oblasti, můžete také v partnerské virtuální síti nakonfigurovat bránu, která bude sloužit jako tranzitní bod pro místní síť. V tomto případě virtuální síť, která používá vzdálenou bránu, nemůže mít svou vlastní bránu. Virtuální síť může mít pouze jednu bránu. Může jí být buď místní brána, nebo vzdálená brána (v partnerské virtuální síti), jak ukazuje následující obrázek:

![přenos dat při partnerských vztazích virtuálních sítí](./media/virtual-networks-peering-overview/figure04.png)

Průchod branou se nepodporuje v případě, že jde o partnerský vztah mezi virtuálními sítěmi vytvořenými pomocí různých modelů nasazení nebo v různých oblastech. Pokud chcete používat průchod branou, je nutné, aby byly obě partnerské virtuální sítě vytvořené pomocí modelu nasazení Resource Manager a ve stejné oblasti.

Po nastavení partnerského vztahu mezi virtuálními sítěmi sdílejícími jedno připojení Azure ExpressRoute přenos dat prochází skrze partnerský vztah (tj. prostřednictvím páteřní sítě Azure). K připojení k místnímu okruhu lze v obou sítích nadále používat místní brány. Alternativním postupem je použití sdílené brány a konfigurace průchodu pro místní připojení.

## <a name="troubleshoot"></a>Řešení potíží

Pokud chcete partnerský vztah virtuální sítě potvrdit, musíte [zkontrolovat platné trasy](virtual-network-routes-troubleshoot-portal.md) síťového rozhraní v každé podsíti virtuální sítě. Pokud partnerský vztah virtuální sítě existuje, mají všechny podsítě virtuální sítě trasy s typem dalšího přechodu *VNet peering*, a to u každého adresního prostoru v každé partnerské virtuální síti.

K řešení problémů s připojením k virtuálnímu počítači v partnerské virtuální síti také můžete použít [kontrolu připojení](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve službě Network Watcher. Kontrola připojení zobrazí směrování provozu od síťového rozhraní zdrojového virtuálního počítače k síťovému rozhraní cílového virtuálního počítače.

## <a name="requirements-and-constraints"></a>Požadavky a omezení

Informace o požadavcích a omezeních najdete v tématu [Požadavky a omezení partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md#requirements-and-constraints). Informace o omezeních počtu partnerských vztahů, které pro virtuální síť můžete vytvořit, najdete v tématu popisujícím [omezení sítí Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

## <a name="permissions"></a>Oprávnění

Informace o oprávněních požadovaných k vytvoření partnerského vztahu virtuálních sítí najdete v tématu popisujícím [oprávnění pro partnerské vztahy virtuálních sítí](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Ceny

Za příchozí a výchozí přenos využívající partnerské propojení virtuálních sítí se účtuje nominální poplatek. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Další kroky

* Partnerský vztah virtuálních sítí se vytváří mezi virtuálními sítěmi vytvořenými prostřednictvím stejného modelu nebo různých modelů nasazení, které existují ve stejném předplatném nebo různých předplatných. Dokončete kurz pro jeden z následujících scénářů:

    |Model nasazení Azure             | Předplatné  |
    |---------                          |---------|
    |Obě Resource Manager              |[Stejné](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Různé](create-peering-different-subscriptions.md)|
    |Jedna Resource Manager, druhá Classic  |[Stejné](create-peering-different-deployment-models.md)|
    |                                   |[Různé](create-peering-different-deployment-models-subscriptions.md)|

* Zjistěte, jak vytvořit [hvězdicovou síťovou topologii](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).
* Prostudujte si všechna [nastavení partnerského vztahu virtuálních sítí a jejich změny](virtual-network-manage-peering.md).