---
title: Plánování Azure Virtual Networks | Microsoft Docs
description: Naučte se plánovat virtuální sítě na základě vašich požadavků na umístění, izolaci a připojení.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: kumud
ms.openlocfilehash: 416ca556e298fa088916a554860d05725bc1cf72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045497"
---
# <a name="plan-virtual-networks"></a>Plánování virtuálních sítí

Vytvoření virtuální sítě pro experimentování s je dostatečně snadné, ale je pravděpodobné, že v průběhu času nasadíte několik virtuálních sítí, které budou podporovat požadavky vaší organizace na produkční prostředí. V případě určitého plánování budete moci nasadit virtuální sítě a propojovat prostředky, které budete potřebovat efektivněji. Informace v tomto článku jsou nejužitečnější, pokud už jste obeznámeni s virtuálními sítěmi a máte nějaké zkušenosti s nimi pracovat. Pokud nejste obeznámeni s virtuálními sítěmi, doporučujeme si přečíst [Přehled virtuální sítě](virtual-networks-overview.md).

## <a name="naming"></a>Pojmenování

Všechny prostředky Azure mají název. Název musí být jedinečný v rámci oboru, který se může u každého typu prostředku lišit. Například název virtuální sítě musí být v rámci [skupiny prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)jedinečný, ale může být duplikován v rámci [předplatného](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) nebo [oblasti](https://azure.microsoft.com/regions/#services)Azure. Definování konvence vytváření názvů, kterou můžete použít konzistentně, když je pojmenování prostředků užitečné při správě několika síťových prostředků v průběhu času. Návrhy najdete v tématu zásady [vytváření názvů](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork).

## <a name="regions"></a>Oblasti

Všechny prostředky Azure se vytvářejí v oblasti a předplatném Azure. Prostředek se dá vytvořit jenom ve virtuální síti, která existuje ve stejné oblasti a předplatném jako prostředek. Můžete ale propojit virtuální sítě, které existují v různých předplatných a oblastech. Další informace najdete v tématu věnovaném [připojení](#connectivity). Při rozhodování o tom, které oblasti se mají v nástroji nasazovat prostředky, zvažte, kde se fyzicky nacházejí spotřebitelé těchto prostředků:

- Spotřebitelé prostředků obvykle chtějí nejnižší latenci sítě pro své prostředky. Informace o tom, jak určit relativní latence mezi zadaným umístěním a oblastmi Azure, najdete v tématu [zobrazení relativních latencí](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Máte k dispozici nároky na data, svrchovanost, dodržování předpisů nebo odolnost proti chybám? Pokud ano, vyberte oblast, která je zarovnána k požadavkům, je kritická. Další informace najdete v tématu [geografických oblastí Azure](https://azure.microsoft.com/global-infrastructure/geographies/).
- Vyžadujete odolnost napříč Zóny dostupnosti Azure v rámci stejné oblasti Azure pro prostředky, které nasadíte? V rámci jedné virtuální sítě můžete nasadit prostředky, jako jsou virtuální počítače, do různých zón dostupnosti. Ne všechny oblasti Azure ale podporují zóny dostupnosti. Další informace o zónách dostupnosti a oblastech, které je podporují, najdete v tématu [zóny dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Předplatná

V rámci každého předplatného můžete nasadit libovolný počet virtuálních sítí, a to až do [limitu](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Některé organizace mají různá předplatná pro různá oddělení, například. Další informace a požadavky týkající se předplatných najdete v tématu zásady [správného řízení předplatného](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy).

## <a name="segmentation"></a>Segmentation

Můžete vytvořit více virtuálních sítí pro každé předplatné a jednotlivé oblasti. V každé virtuální síti můžete vytvořit několik podsítí. Následující pokyny vám pomůžou určit, kolik virtuálních sítí a podsítí budete potřebovat:

### <a name="virtual-networks"></a>Virtuální sítě

Virtuální síť je virtuální izolovaná část veřejné sítě Azure. Každá virtuální síť je vyhrazena pro vaše předplatné. Co je potřeba vzít v úvahu při rozhodování, zda vytvořit jednu virtuální síť nebo více virtuálních sítí v rámci předplatného:

- Existují všechny požadavky na zabezpečení organizace pro izolaci provozu do samostatných virtuálních sítí? Můžete se rozhodnout připojit virtuální sítě nebo ne. Pokud propojíte virtuální sítě, můžete implementovat síťové virtuální zařízení, jako je třeba brána firewall, abyste mohli řídit tok přenosů mezi virtuálními sítěmi. Další informace najdete v tématu [zabezpečení](#security) a [připojení](#connectivity).
- Existují všechny požadavky organizace pro izolaci virtuálních sítí do samostatných [předplatných](#subscriptions) nebo [oblastí](#regions)?
- [Síťové rozhraní](virtual-network-network-interface.md) umožňuje virtuálním počítačům komunikovat s dalšími prostředky. Každé síťové rozhraní má přiřazenou jednu nebo více privátních IP adres. Kolik síťových rozhraní a [privátních IP adres](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) vyžadujete ve virtuální síti? Existují [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) počtu síťových rozhraní a privátních IP adres, které můžete mít v rámci virtuální sítě.
- Chcete virtuální síť připojit k jiné virtuální síti nebo místní síti? Můžete se rozhodnout připojit některé virtuální sítě k ostatním nebo místním sítím, ale ne k ostatním. Další informace najdete v tématu věnovaném [připojení](#connectivity). Každá virtuální síť, kterou se připojujete k jiné virtuální síti nebo místní síti, musí mít jedinečný adresní prostor. Každá virtuální síť má jeden nebo více rozsahů veřejných nebo privátních adres přiřazených k adresnímu prostoru. Rozsah adres je určený ve formátu CIDR (Internet Domain Routing), například 10.0.0.0/16. Přečtěte si další informace o [rozsahu adres](manage-virtual-network.md#add-or-remove-an-address-range) pro virtuální sítě.
- Máte k prostředkům v různých virtuálních sítích nějaké požadavky na správu organizace? V takovém případě můžete prostředky oddělit do samostatné virtuální sítě, aby se zjednodušilo [přiřazení oprávnění](#permissions) jednotlivcům ve vaší organizaci nebo k přiřazování různých zásad k různým virtuálním sítím.
- Když nasadíte některé prostředky služeb Azure do virtuální sítě, vytvoří se jejich vlastní virtuální síť. Pokud chcete zjistit, jestli služba Azure vytvoří svou vlastní virtuální síť, přečtěte si informace pro každou [službu Azure, kterou je možné nasadit do virtuální sítě](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Podsítě

Virtuální síť je možné rozdělit do jedné nebo víc podsítí až do [limitů](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Co je potřeba vzít v úvahu při rozhodování, zda vytvořit jednu podsíť nebo více virtuálních sítí v rámci předplatného:

- Každá podsíť musí mít jedinečný rozsah adres, který je určený ve formátu CIDR, v rámci adresního prostoru virtuální sítě. Rozsah adres se nesmí překrývat s ostatními podsítěmi ve virtuální síti.
- Pokud plánujete nasadit některé prostředky služeb Azure do virtuální sítě, může to vyžadovat nebo vytvořit vlastní podsíť, takže pro ně musí být dostatek volného místa. Pokud chcete zjistit, jestli služba Azure vytvoří svou vlastní podsíť, přečtěte si informace pro každou [službu Azure, kterou je možné nasadit do virtuální sítě](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Pokud například připojujete virtuální síť k místní síti pomocí VPN Gateway Azure, musí mít virtuální síť vyhrazenou podsíť pro bránu. Přečtěte si další informace o [podsítích brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Azure ve výchozím nastavení směruje síťový provoz mezi všemi podsítěmi ve virtuální síti. Můžete přepsat výchozí směrování Azure a zabránit tak směrování Azure mezi podsítěmi nebo směrování provozu mezi podsítěmi přes síťové virtuální zařízení, například. Pokud vyžadujete, aby provoz mezi prostředky ve stejné virtuální síti procházel přes síťové virtuální zařízení (síťové virtuální zařízení), nasaďte prostředky do různých podsítí. Další informace najdete v [zabezpečení](#security).
- Přístup k prostředkům Azure, jako je například účet Azure Storage nebo Azure SQL Database, můžete omezit na konkrétní podsítě s koncovým bodem služby virtuální sítě. Dále můžete odepřít přístup k prostředkům z Internetu. Můžete vytvořit několik podsítí a povolit koncový bod služby pro některé podsítě, ale ne jiné. Přečtěte si další informace o [koncových bodech služby](virtual-network-service-endpoints-overview.md)a o prostředcích Azure, pro které je můžete povolit.
- Ke každé podsíti ve virtuální síti můžete přidružit žádnou nebo jednu skupinu zabezpečení sítě. Ke každé podsíti můžete přiřadit stejnou nebo jinou skupinu zabezpečení sítě. Každá skupina zabezpečení sítě obsahuje pravidla, která povolují nebo zakazují provoz do a ze zdrojů a cílů. Přečtěte si další informace o [skupinách zabezpečení sítě](#traffic-filtering).

## <a name="security"></a>Zabezpečení

Můžete filtrovat síťový provoz do a z prostředků ve virtuální síti pomocí skupin zabezpečení sítě a síťových virtuálních zařízení. Můžete řídit, jak Azure směruje provoz z podsítí. Můžete také omezit, kdo ve vaší organizaci může pracovat s prostředky ve virtuálních sítích.

### <a name="traffic-filtering"></a>Filtrování provozu

- Můžete filtrovat síťový provoz mezi prostředky ve virtuální síti pomocí skupiny zabezpečení sítě, síťové virtuální zařízení, která filtruje síťový provoz nebo obojí. Chcete-li nasadit síťové virtuální zařízení, jako je brána firewall, pro filtrování síťového provozu, přečtěte si [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Při použití síťové virtuální zařízení můžete také vytvořit vlastní trasy ke směrování provozu z podsítí do síťové virtuální zařízení. Přečtěte si další informace o [směrování provozu](#traffic-routing).
- Skupina zabezpečení sítě obsahuje několik výchozích pravidel zabezpečení, která povolují nebo zakazují provoz do nebo z prostředků. Skupina zabezpečení sítě může být přidružena k síťovému rozhraní, podsíti, ve které je síťové rozhraní, nebo obojí. Aby se zjednodušila Správa pravidel zabezpečení, doporučujeme, abyste skupinu zabezpečení sítě spojili s jednotlivými podsítěmi, a to i přes jednotlivá síťová rozhraní v rámci podsítě, kdykoli to bude možné.
- Pokud se na různé virtuální počítače v podsíti vztahují odlišná pravidla zabezpečení, můžete síťové rozhraní ve virtuálním počítači přidružit k jedné nebo více skupinám zabezpečení aplikace. Pravidlo zabezpečení může určit skupinu zabezpečení aplikace v jejím zdroji, cíli nebo obojím. Toto pravidlo pak platí pouze pro síťová rozhraní, která jsou členy skupiny zabezpečení aplikace. Přečtěte si další informace o [skupinách zabezpečení sítě](security-overview.md) a [skupinách zabezpečení aplikací](security-overview.md#application-security-groups).
- Azure vytvoří v každé skupině zabezpečení sítě několik výchozích pravidel zabezpečení. Jedno výchozí pravidlo povoluje tok provozu mezi všemi prostředky ve virtuální síti. Chcete-li toto chování přepsat, použijte skupiny zabezpečení sítě, vlastní směrování pro směrování provozu do síťové virtuální zařízení nebo obojí. Doporučujeme, abyste se seznámili se všemi [výchozími pravidly zabezpečení](security-overview.md#default-security-rules) Azure a seznámili se s tím, jak se pravidla skupiny zabezpečení sítě aplikují na prostředek.

Můžete si prohlédnout ukázkové návrhy pro implementaci hraniční sítě (označované také jako DMZ) mezi Azure a Internetem pomocí [síťové virtuální zařízení](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

### <a name="traffic-routing"></a>Směrování provozu

Azure vytvoří několik výchozích tras pro odchozí provoz z podsítě. Výchozí směrování Azure můžete přepsat tak, že vytvoříte směrovací tabulku a přidružíte ji k podsíti. Mezi běžné důvody pro přepsání výchozího směrování Azure patří:
- Vzhledem k tomu, že chcete, aby tok dat mezi podsítěmi síťové virtuální zařízení. Další informace o tom, jak [nakonfigurovat směrovací tabulky pro vynucení provozu přes síťové virtuální zařízení](tutorial-create-route-table-portal.md).
- Vzhledem k tomu, že chcete vynutit všechny přenosy vázané na Internet prostřednictvím síťové virtuální zařízení nebo místního prostředí prostřednictvím služby Azure VPN Gateway. Pro kontrolu a protokolování se často označuje jako vynucené tunelové propojení v místním prostředí pro Internet. Přečtěte si další informace o tom, jak nakonfigurovat [vynucené tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Pokud potřebujete implementovat vlastní směrování, doporučujeme, abyste se seznámili se [směrováním v Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Připojení

Virtuální síť můžete připojit k jiným virtuálním sítím pomocí partnerského vztahu virtuálních sítí nebo k místní síti pomocí Azure VPN Gateway.

### <a name="peering"></a>Partnerské vztahy

Při použití [partnerského vztahu virtuálních sítí](virtual-network-peering-overview.md)můžou být virtuální sítě ve stejné nebo jiné podporované oblasti Azure. Virtuální sítě můžou být ve stejném nebo jiném předplatném Azure (dokonce i v předplatných, která patří různým Azure Active Directorym klientům). Před vytvořením partnerského vztahu doporučujeme, abyste se seznámili se všemi [požadavky a omezeními](virtual-network-manage-peering.md#requirements-and-constraints)partnerských vztahů. Šířka pásma mezi prostředky ve virtuálních sítích partnerských ve stejné oblasti je stejná, jako kdyby byly prostředky ve stejné virtuální síti.

### <a name="vpn-gateway"></a>VPN Gateway

Pomocí [VPN Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure můžete připojit virtuální síť k místní síti pomocí [sítě VPN typu Site-to-site](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json)nebo pomocí vyhrazeného připojení s Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Partnerský vztah a bránu sítě VPN můžete kombinovat a vytvořit tak [sítě rozbočovačů a paprsků](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), ve kterých se virtuální sítě s paprsky připojují k virtuální síti rozbočovače, a rozbočovač se připojí k místní síti, například.

### <a name="name-resolution"></a>Překlad adres

Prostředky v jedné virtuální síti nemůžou přeložit názvy prostředků v partnerské virtuální síti pomocí [integrované DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md)Azure. Chcete-li přeložit názvy v partnerské virtuální síti, [Nasaďte vlastní server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)nebo použijte Azure DNS [privátní domény](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Překlad názvů mezi prostředky ve virtuální síti a v místních sítích taky vyžaduje, abyste nasadili vlastní server DNS.

## <a name="permissions"></a>Oprávnění

Azure využívá [řízení přístupu na základě role](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) k prostředkům. Oprávnění jsou přiřazena k [oboru](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) v následující hierarchii: skupina pro správu, předplatné, skupina prostředků a jednotlivé prostředky. Další informace o hierarchii najdete v tématu [uspořádání prostředků](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud chcete pracovat s virtuálními sítěmi Azure a všemi jejich souvisejícími možnostmi, jako je například partnerský vztah, skupiny zabezpečení sítě, koncové body služby a směrovací tabulky, můžete členům vaší organizace přiřadit předdefinované role [Owner](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner) [přispěvatele](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) , [přispěvatele](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)nebo sítě a potom přiřadit roli příslušnému oboru. Pokud chcete přiřadit konkrétní oprávnění k podmnožině možností virtuální sítě, vytvořte [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a přiřaďte specifická oprávnění požadovaná pro [virtuální sítě](manage-virtual-network.md#permissions), [podsítě a koncové body služby](virtual-network-manage-subnet.md#permissions), [Síťová rozhraní](virtual-network-network-interface.md#permissions), [partnerské vztahy](virtual-network-manage-peering.md#permissions), [skupiny zabezpečení sítě a aplikace](manage-network-security-group.md#permissions)nebo [směrovací tabulky](manage-route-table.md#permissions) k roli.

## <a name="policy"></a>Zásady

Azure Policy vám umožní vytvářet, přiřazovat a spravovat definice zásad. Definice zásad pro vaše prostředky vynutila různá pravidla, takže prostředky zůstávají v souladu se standardy vaší organizace a smlouvami o úrovni služeb. Azure Policy spouští vyhodnocení vašich prostředků, hledá prostředky, které nejsou kompatibilní s definicemi zásad, které máte. Můžete například definovat a použít zásadu, která umožňuje vytváření virtuálních sítí jenom v konkrétní skupině prostředků nebo oblasti. Jiná zásada může vyžadovat, aby každá podsíť měla přidruženou skupinu zabezpečení sítě. Zásady se pak vyhodnotí při vytváření a aktualizaci prostředků.

Zásady jsou aplikovány na následující hierarchii: skupina pro správu, předplatné a skupina prostředků. Přečtěte si další informace o [Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo nasazení některých [Azure Policy definic](policy-samples.md)virtuálních sítí.

## <a name="next-steps"></a>Další kroky

Přečtěte si o všech úlohách, nastaveních a možnostech pro [virtuální síť](manage-virtual-network.md), [podsíť a koncový bod služby](virtual-network-manage-subnet.md), [síťové rozhraní](virtual-network-network-interface.md), [partnerský vztah](virtual-network-manage-peering.md),  [síť a skupinu zabezpečení aplikace](manage-network-security-group.md)nebo [směrovací tabulku](manage-route-table.md).
