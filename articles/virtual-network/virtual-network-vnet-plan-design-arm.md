---
title: Plánování virtuálních sítí Azure | Dokumenty společnosti Microsoft
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
ms.openlocfilehash: 95dd7be118e869aed02bb55918ab0cefa0d05d03
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998871"
---
# <a name="plan-virtual-networks"></a>Plánování virtuálních sítí

Vytvoření virtuální sítě pro experimentování je dost snadné, ale je pravděpodobné, že v průběhu času nasadíte více virtuálních sítí, abyste podpořili produkční potřeby vaší organizace. S některými plánování, budete moci nasadit virtuální sítě a připojit prostředky, které potřebujete efektivněji. Informace v tomto článku jsou velmi užitečné, pokud jste již obeznámeni s virtuálními sítěmi a máte nějaké zkušenosti s jejich práce. Pokud nejste obeznámeni s virtuálními sítěmi, doporučujeme si přečíst [přehled virtuální sítě](virtual-networks-overview.md).

## <a name="naming"></a>Pojmenování

Všechny prostředky Azure mají název. Název musí být jedinečný v rámci oboru, který se může lišit pro každý typ prostředku. Například název virtuální sítě musí být jedinečný v rámci [skupiny prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), ale může být duplikován v rámci [předplatného](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) nebo [oblasti](https://azure.microsoft.com/regions/#services)Azure . Definování konvence pojmenování, kterou lze použít konzistentně při pojmenování prostředků, je užitečné při správě několika síťových prostředků v průběhu času. Návrhy naleznete v [tématu Konvence pojmenování](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork).

## <a name="regions"></a>Oblasti

Všechny prostředky Azure se vytvářejí v oblasti Azure a předplatné. Prostředek lze vytvořit pouze ve virtuální síti, která existuje ve stejné oblasti a předplatné jako prostředek. Můžete však připojit virtuální sítě, které existují v různých předplatných a oblastech. Další informace naleznete v [tématu connectivity](#connectivity). Při rozhodování o tom, do kterých oblastí chcete nasadit prostředky, zvažte, kde jsou fyzicky umístěni příjemci prostředků:

- Spotřebitelé prostředků obvykle chtějí nejnižší latenci sítě pro své prostředky. Pokud chcete určit relativní latence mezi zadaným umístěním a oblastmi Azure, přečtěte si hlavní informace o [zobrazení relativních latencí](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Máte požadavky na rezidenci dat, suverenitu, dodržování předpisů nebo odolnost? Pokud ano, výběr oblasti, která je zarovnána s požadavky, je kritická. Další informace najdete v [tématu Azure geographies](https://azure.microsoft.com/global-infrastructure/geographies/).
- Požadujete odolnost proti chybám napříč zónami dostupnosti Azure ve stejné oblasti Azure pro prostředky, které nasadíte? Prostředky, jako jsou virtuální počítače (VM), můžete nasadit do různých zón dostupnosti v rámci stejné virtuální sítě. Ne všechny oblasti Azure však podporují zóny dostupnosti. Další informace o zónách dostupnosti a oblastech, které je podporují, najdete v [tématu Zóny dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Předplatná

V rámci každého předplatného můžete nasadit libovolný počet virtuálních sítí, a to až do [limitu](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Některé organizace mají například různá předplatná pro různá oddělení. Další informace a důležité informace týkající se předplatných najdete v [tématu Zásadsprávné hořání předplatného](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy).

## <a name="segmentation"></a>Segmentation

Můžete vytvořit více virtuálních sítí na jedno předplatné a na oblast. V rámci každé virtuální sítě můžete vytvořit více podsítí. Následující aspekty vám pomohou určit, kolik virtuálních sítí a podsítí požadujete:

### <a name="virtual-networks"></a>Virtuální sítě

Virtuální síť je virtuální, izolovaná část veřejné sítě Azure. Každá virtuální síť je vyhrazena pro vaše předplatné. Co je třeba zvážit při rozhodování, zda vytvořit jednu virtuální síť nebo více virtuálních sítí v předplatném:

- Existují nějaké požadavky na zabezpečení organizace pro izolaci provozu do samostatných virtuálních sítí? Můžete se rozhodnout připojit virtuální sítě, nebo ne. Pokud připojíte virtuální sítě, můžete implementovat síťové virtuální zařízení, jako je brána firewall, a řídit tak tok provozu mezi virtuálními sítěmi. Další informace naleznete v [tématu zabezpečení](#security) a [připojení](#connectivity).
- Existují nějaké organizační požadavky pro izolaci virtuálních sítí do [samostatných předplatných](#subscriptions) nebo [oblastí](#regions)?
- [Síťové rozhraní](virtual-network-network-interface.md) umožňuje virtuálnímu virtuálnímu zařízení komunikovat s jinými prostředky. Každé síťové rozhraní má přiřazenou jednu nebo více privátních ADRES IP. Kolik síťových rozhraní a [privátních IP adres](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) požadujete ve virtuální síti? Počet síťových rozhraní a privátních IP adres, které můžete mít v rámci virtuální sítě, je [tento](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) počet limitů.
- Chcete připojit virtuální síť k jiné virtuální síti nebo místní síti? Můžete se rozhodnout připojit některé virtuální sítě k sobě navzájem nebo k místním sítím, ale ne k jiným. Další informace naleznete v [tématu connectivity](#connectivity). Každá virtuální síť, kterou připojíte k jiné virtuální síti nebo místní síti, musí mít jedinečný adresní prostor. Každá virtuální síť má jeden nebo více oblastí veřejných nebo privátní adres přiřazených k adresnímu prostoru. Rozsah adres je určen ve formátu CIDR (Classless Internet Domain Routing), například 10.0.0.0/16. Přečtěte si další informace o [rozsahu adres](manage-virtual-network.md#add-or-remove-an-address-range) pro virtuální sítě.
- Máte nějaké požadavky na správu organizace pro prostředky v různých virtuálních sítích? Pokud ano, můžete oddělit prostředky do samostatné virtuální sítě, abyste [zjednodušili přiřazení oprávnění](#permissions) jednotlivcům ve vaší organizaci nebo přiřadili různé zásady různým virtuálním sítím.
- Když nasadíte některé prostředky služby Azure do virtuální sítě, vytvoří vlastní virtuální síť. Pokud chcete zjistit, jestli služba Azure vytvoří vlastní virtuální síť, přečtěte si informace o každé [službě Azure, kterou lze nasadit do virtuální sítě](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Podsítě

Virtuální síť lze rozdělit do jedné nebo více podsítí až do [limitů](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Při rozhodování, jestli chcete v předplatném vytvořit jednu podsíť nebo více virtuálních sítí, je třeba zvážit:

- Každá podsíť musí mít jedinečný rozsah adres zadaný ve formátu CIDR v adresním prostoru virtuální sítě. Rozsah adres se nemůže překrývat s jinými podsítěmi ve virtuální síti.
- Pokud plánujete nasadit některé prostředky služby Azure do virtuální sítě, mohou vyžadovat nebo vytvořit vlastní podsíť, takže musí být dostatek nepřiděleného místa, aby tak učinily. Pokud chcete zjistit, jestli služba Azure vytvoří vlastní podsíť, přečtěte si informace o každé [službě Azure, kterou lze nasadit do virtuální sítě](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Pokud například připojíte virtuální síť k místní síti pomocí brány Azure VPN Gateway, musí mít virtuální síť vyhrazenou podsíť pro bránu. Další informace o [podsítích brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Azure ve výchozím nastavení směruje síťový provoz mezi všemi podsítěmi ve virtuální síti. Můžete přepsat výchozí směrování Azure zabránit směrování Azure mezi podsítěmi nebo směrovat provoz mezi podsítěmi prostřednictvím síťového virtuálního zařízení, například. Pokud požadujete, aby provoz mezi prostředky ve stejné virtuální síti tok prostřednictvím síťového virtuálního zařízení (NVA), nasadit prostředky do různých podsítí. Další informace o [zabezpečení](#security).
- Přístup k prostředkům Azure, jako je účet úložiště Azure nebo databáze Azure SQL, můžete omezit na konkrétní podsítě s koncovým bodem služby virtuální sítě. Dále můžete odepřít přístup ke zdrojům z internetu. Můžete vytvořit více podsítí a povolit koncový bod služby pro některé podsítě, ale ne pro jiné. Přečtěte si další informace o [koncových bodech služby](virtual-network-service-endpoints-overview.md)a o prostředcích Azure, pro které je můžete povolit.
- Ke každé podsíti ve virtuální síti můžete přidružit nulovou nebo jednu skupinu zabezpečení sítě. Ke každé podsíti můžete přidružit stejnou nebo jinou skupinu zabezpečení sítě. Každá skupina zabezpečení sítě obsahuje pravidla, která umožňují nebo zapírají provoz do zdrojů a cílů a z nich. Další informace o [skupinách zabezpečení sítě](#traffic-filtering).

## <a name="security"></a>Zabezpečení

Síťový provoz do a z prostředků ve virtuální síti můžete filtrovat pomocí skupin zabezpečení sítě a síťových virtuálních zařízení. Můžete řídit, jak Azure směruje provoz z podsítí. Můžete také omezit, kdo ve vaší organizaci může pracovat s prostředky ve virtuálních sítích.

### <a name="traffic-filtering"></a>Filtrování provozu

- Síťový provoz mezi prostředky ve virtuální síti můžete filtrovat pomocí skupiny zabezpečení sítě, síťového virtuálního zařízení, které filtruje síťový provoz, nebo obojího. Pokud chcete nasadit síťové virtuální připojení, jako je brána firewall, abyste filtrovat síťový provoz, přečtěte si informace o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Při použití síťového virtuálního provozu můžete také vytvořit vlastní trasy pro směrování provozu z podsítí do síťového virtuálního provozu. Další informace o [směrování provozu](#traffic-routing).
- Skupina zabezpečení sítě obsahuje několik výchozích pravidel zabezpečení, která povolují nebo odepírají provoz do prostředků nebo z prostředků. Skupina zabezpečení sítě může být přidružena k síťovému rozhraní, podsíti, ve které se síťové rozhraní nachází, nebo obojí. Chcete-li zjednodušit správu pravidel zabezpečení, doporučujeme přidružit skupinu zabezpečení sítě k jednotlivým podsítím, nikoli k jednotlivým síťovým rozhraním v rámci podsítě, kdykoli je to možné.
- Pokud různé virtuální aplikace v rámci podsítě potřebují použít různá pravidla zabezpečení, můžete přidružit síťové rozhraní ve virtuálním jazyce k jedné nebo více skupinám zabezpečení aplikací. Pravidlo zabezpečení může určit skupinu zabezpečení aplikace ve zdroji, cíli nebo v obou případech. Toto pravidlo se pak vztahuje pouze na síťová rozhraní, která jsou členy skupiny zabezpečení aplikace. Další informace o [skupinách zabezpečení sítě](security-overview.md) a [skupinách zabezpečení aplikací](security-overview.md#application-security-groups).
- Azure vytvoří několik výchozích pravidel zabezpečení v rámci každé skupiny zabezpečení sítě. Jedno výchozí pravidlo umožňuje tok veškerého provozu mezi všemi prostředky ve virtuální síti. Chcete-li toto chování přepsat, použijte skupiny zabezpečení sítě, vlastní směrování pro směrování provozu do síťového virtuálního počítače nebo obojí. Doporučujeme, abyste se seznámili se všemi [výchozími pravidly zabezpečení](security-overview.md#default-security-rules) Azure a pochopili, jak se na prostředek vztahují pravidla skupiny zabezpečení sítě.

Můžete zobrazit ukázkové návrhy pro implementaci hraniční sítě (označované také jako DMZ) mezi Azure a internetem pomocí [síťového virtuálního zařízení](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

### <a name="traffic-routing"></a>Směrování provozu

Azure vytvoří několik výchozích tras pro odchozí provoz z podsítě. Výchozí směrování Azure můžete přepsat vytvořením směrovací tabulky a jejím přisunutím k podsíti. Běžné důvody pro přepsání výchozího směrování Azure jsou:
- Protože chcete, aby přenos mezi podsítěmi tok přes síťové virtuální sítě. Další informace o [konfiguraci tabulek tras pro vynucení přenosu prostřednictvím služby NVA](tutorial-create-route-table-portal.md).
- Protože chcete vynutit veškerý internetový provoz prostřednictvím služby NVA nebo místní prostřednictvím brány Azure VPN. Vynucení místního internetového provozu pro kontrolu a protokolování se často označuje jako nucené tunelování. Další informace o konfiguraci [vynuceného tunelového propojení](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Pokud potřebujete implementovat vlastní směrování, doporučujeme, abyste se seznámili s [směrováním v Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Připojení

Virtuální síť můžete připojit k jiným virtuálním sítím pomocí partnerského vztahu virtuální sítě nebo k místní síti pomocí brány Azure VPN.

### <a name="peering"></a>Partnerské vztahy

Při použití [partnerského vztahu virtuální sítě](virtual-network-peering-overview.md)mohou být virtuální sítě ve stejných nebo jiných podporovaných oblastech Azure. Virtuální sítě můžou být ve stejném nebo jiném předplatných Azure (dokonce i předplatná patřící různým tenantům Služby Azure Active Directory). Před vytvořením partnerského vztahu doporučujeme seznámit se se všemi [požadavky a omezeními](virtual-network-manage-peering.md#requirements-and-constraints)partnerského vztahu . Šířka pásma mezi prostředky ve virtuálních sítích partnerských ve stejné oblasti je stejná, jako kdyby byly ve stejné virtuální síti.

### <a name="vpn-gateway"></a>VPN Gateway

[Bránu](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure VPN můžete použít k připojení virtuální sítě k místní síti pomocí [sítě SITE-to-site VPN](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json)nebo pomocí vyhrazeného připojení s Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Můžete kombinovat partnerský vztah a bránu VPN a vytvořit [tak rozbočovací a paprskové sítě](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), kde se virtuální sítě paprsku připojují k virtuální síti rozbočovače a rozbočovač se připojuje například k místní síti.

### <a name="name-resolution"></a>Překlad adres

Prostředky v jedné virtuální síti nemohou přeložit názvy prostředků ve virtuální síti s partnerským vztahem pomocí [integrovaného DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md)Azure . Chcete-li přeložit názvy ve virtuální síti partnerského vztahu, [nasaďte vlastní server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)nebo použijte [privátní domény](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Azure DNS . Překlad názvů mezi prostředky ve virtuální síti a místními sítěmi také vyžaduje nasazení vlastního serveru DNS.

## <a name="permissions"></a>Oprávnění

Azure využívá [řízení přístupu na základě rolí](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) k prostředkům. Oprávnění jsou přiřazena [oboru](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) v následující hierarchii: skupina pro správu, předplatné, skupina prostředků a jednotlivé prostředky. Další informace o hierarchii naleznete v tématu [Uspořádání prostředků](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Chcete-li pracovat s virtuálními sítěmi Azure a všemi souvisejícími funkcemi, jako je partnerský vztah, skupiny zabezpečení sítě, koncové body služby a tabulky tras, můžete přiřadit členy vaší organizace k rolím [integrovaného vlastníka](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [přispěvatele](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)nebo [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) a pak přiřadit roli příslušnému oboru. Pokud chcete přiřadit určitá oprávnění pro podmnožinu možností virtuální sítě, vytvořte [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a přiřaďte konkrétní oprávnění požadovaná pro [virtuální sítě](manage-virtual-network.md#permissions), [podsítě a koncové body služby](virtual-network-manage-subnet.md#permissions), [síťová rozhraní](virtual-network-network-interface.md#permissions), partnerský [vztah](virtual-network-manage-peering.md#permissions), [skupiny zabezpečení sítě a aplikací](manage-network-security-group.md#permissions)nebo směrování [tabulek](manage-route-table.md#permissions) do role.

## <a name="policy"></a>Zásada

Azure Policy umožňuje vytvářet, přiřazovat a spravovat definice zásad. Definice zásad vynucují různá pravidla nad prostředky, aby prostředky zůstaly v souladu s vašimi organizačními standardy a smlouvami o úrovni služeb. Azure Policy spouští vyhodnocení vašich prostředků a vyhledá prostředky, které nejsou kompatibilní s definicemi zásad, které máte. Můžete například definovat a použít zásadu, která umožňuje vytváření virtuálních sítí pouze v určité skupině prostředků nebo oblasti. Jiná zásada může vyžadovat, aby každá podsíť byla přidružena skupina zabezpečení sítě. Zásady jsou pak vyhodnoceny při vytváření a aktualizaci prostředků.

Zásady se používají pro následující hierarchii: skupina pro správu, předplatné a skupina prostředků. Přečtěte si další informace o [zásadách Azure](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo nasaďte některé ukázky [šablon zásad](policy-samples.md) virtuální sítě.

## <a name="next-steps"></a>Další kroky

Informace o všech úkolech, nastaveních a možnostech [pro virtuální síť](manage-virtual-network.md), koncový bod [podsítě a služby](virtual-network-manage-subnet.md), [síťové rozhraní](virtual-network-network-interface.md), partnerský [vztah](virtual-network-manage-peering.md), [skupinu zabezpečení sítě a aplikací](manage-network-security-group.md)nebo [směrovací tabulku](manage-route-table.md).
