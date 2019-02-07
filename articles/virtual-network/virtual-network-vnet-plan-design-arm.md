---
title: Plánování virtuální sítě Azure | Dokumentace Microsoftu
description: Zjistěte, jak naplánovat pro virtuální sítě na základě izolace, připojení a požadavků na umístění.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: jdial
ms.openlocfilehash: ef293b39d0e82cdd26e0c41af5d63d0459064017
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820789"
---
# <a name="plan-virtual-networks"></a>Plánování virtuální sítě

Vytvoření virtuální sítě můžete experimentovat s je docela jednoduché, ale je pravděpodobné, bude nasazovat více virtuálních sítí v čase pro podporu provozním potřebám vaší organizace. Pomocí plánování, budou moct nasazení virtuální sítě a připojení prostředky, které budete potřebovat efektivněji. Informace v tomto článku je nejužitečnější, pokud jste již obeznámeni s virtuálními sítěmi a máte nějaké zkušenosti, práci s nimi. Pokud nejste obeznámeni s virtuálními sítěmi, doporučujeme, abyste si přečetli [Přehled virtuálních sítí](virtual-networks-overview.md).

## <a name="naming"></a>Pojmenování

Všechny prostředky Azure mít název. Název musí být jedinečné v rámci oboru, které pro každý typ prostředku se můžou lišit. Například název virtuální sítě musí být jedinečný v rámci [skupiny prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), ale mohou být duplicitní v rámci [předplatné](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) nebo Azure [oblasti](https://azure.microsoft.com/regions/#services). Definování zásady vytváření názvů, který vám pomůže konzistentně při pojmenování prostředků je užitečné při správě několika síťových prostředků v čase. Návrhy, naleznete v tématu [zásady vytváření názvů](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#networking).

## <a name="regions"></a>Oblasti

Všechny prostředky Azure se vytvoří v oblasti Azure a předplatné. Prostředek možné vytvářet jenom ve virtuální síti, který existuje ve stejné oblasti a předplatném jako prostředek. Ale můžete propojit virtuální sítě, které existují v různých předplatných a oblastech. Další informace najdete v tématu [připojení](#connectivity). Při rozhodování o tom, jaké položky nakoupené k nasazení prostředků, zvažte, kde spotřebitelé prostředky se nacházejí fyzicky:

- Příjemci prostředků obvykle má nejnižší latenci sítě ke svým prostředkům. K určení relativní latence mezi zadaným umístěním a oblastí Azure, najdete v článku [zobrazit relativní latence](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Máte požadavky na rezidenci, suverenitu, dodržování předpisů nebo odolnosti dat? Pokud ano, je výběr oblasti, která odpovídá požadavkům na kritické. Další informace najdete v tématu [zeměpisných oblastech Azure](https://azure.microsoft.com/global-infrastructure/geographies/).
- Vyžadujete odolnost napříč zónami dostupnosti Azure v rámci stejné oblasti Azure pro prostředky, které můžete nasadit? Prostředky, jako jsou virtuální počítače (VM) můžete nasadit do různých zón dostupnosti ve stejné virtuální síti. Ne všechny oblasti Azure podporují zóny dostupnosti ale. Další informace o zónách dostupnosti a oblasti, které je podporují, najdete v článku [zóny dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Předplatná

Můžete nasadit tolik virtuálních sítí podle potřeby v každém předplatném, až [limit](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Některé organizace mají třeba různých předplatných pro různá oddělení. Další informace o kolem předplatná, najdete v článku [zásad správného řízení předplatné](/azure/architecture/cloud-adoption-guide/subscription-governance#define-your-hierarchy).

## <a name="segmentation"></a>Segmentace

Můžete vytvořit několik virtuálních sítí na předplatné a oblast. Můžete vytvořit více podsítí v rámci jedné virtuální sítě. Jaké jsou požadavky, které následují umožňují určit, kolik virtuálních sítí a podsítí, budete potřebovat:

### <a name="virtual-networks"></a>Virtuální sítě

Virtuální síť je virtuální a izolované část veřejné síti Azure. Každá virtuální síť je vyhrazená pro vaše předplatné. Co je třeba zvážit při rozhodování, jestli se má vytvořit jednu virtuální síť nebo vícenásobné virtuální sítě v předplatném:

- Neexistují žádné požadavky na zabezpečení organizace pro izolaci provozu do samostatné virtuální sítě? Můžete propojit virtuální sítě, nebo ne. Pokud se připojujete virtuální sítě, můžete implementovat virtuální síťové zařízení, jako je brána firewall pro řízení toku provozu mezi virtuálními sítěmi. Další informace najdete v tématu [zabezpečení](#security) a [připojení](#connectivity).
- Pro izolaci virtuálních sítí na samostatné splněné všechny požadavky organizace [předplatná](#subscriptions) nebo [oblastech](#regions)?
- A [síťové rozhraní](virtual-network-network-interface.md) umožňuje virtuálnímu počítači komunikovat s jiným prostředkům. Každé síťové rozhraní má jeden nebo více privátních IP adres přiřazené k němu. Kolik síťových rozhraní a [privátních IP adres](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) vyžadujete ve virtuální síti? Existují [omezení](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) počet síťových rozhraní a privátní IP adresy, které máte ve virtuální síti.
- Chcete pro připojení virtuální sítě s jinou virtuální sítí nebo v místní síti? Můžete se připojit k sobě navzájem nebo sítích na pracovišti, ale ne pro jiné některé virtuální sítě. Další informace najdete v tématu [připojení](#connectivity). Každá virtuální síť, která připojení k jiné virtuální síti nebo v místní síti, musí mít jedinečný adresní prostor. Každá virtuální síť má jeden nebo více rozsahů adres veřejné nebo soukromé přiřazené k jeho adresní prostor. Rozsah adres se zadává v classless internetové domény směrování (CIDR Classless) formátu, jako je například 10.0.0.0/16. Další informace o [rozsahy adres](manage-virtual-network.md#add-or-remove-an-address-range) pro virtuální sítě.
- Máte všechny požadavky organizace správy prostředků v různých virtuálních sítích? Pokud ano, vám může rozdělit prostředky do samostatné virtuální sítě pro zjednodušení [přiřazení oprávnění](#permissions) osobám ve vaší organizaci nebo pro přiřazení různých zásad do různých virtuálních sítí.
- Když nasadíte do virtuální sítě prostředkům některých služeb Azure, uživatel vytvořit své vlastní virtuální sítě. Pokud chcete zjistit, zda služba Azure vytvoří vlastní virtuální sítě, naleznete v tématu informace pro každý [služby Azure, který je možné nasadit do virtuální sítě](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Podsítě

Virtuální síť je možné segmentovat do jedné nebo několika podsítí až [omezení](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Co je třeba zvážit při rozhodování, jestli se má vytvořit jednu podsíť nebo vícenásobné virtuální sítě v předplatném:

- Každá podsíť musí mít jedinečný rozsah adres, zadaný ve formátu CIDR, v rámci adresního prostoru virtuální sítě. Rozsah adres se nesmí překrývat s jiné podsítě ve virtuální síti.
- Pokud plánujete nasazení prostředkům některých služeb Azure do virtuální sítě, se může vyžadovat, nebo vytvořit vlastní podsíti, tady musí být dost volného místa pro ně Uděláte to tak. Pokud chcete zjistit, zda služba Azure vytvoří vlastní podsíti, naleznete v tématu informace pro každý [služby Azure, který je možné nasadit do virtuální sítě](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Například pokud připojíte virtuální síť k místní síti pomocí služby Azure VPN Gateway, virtuální síť musí mít vyhrazenou podsíť brány. Další informace o [podsítě brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Azure směruje síťový provoz mezi všemi podsítěmi ve virtuální síti, ve výchozím nastavení. Azure pro výchozí směrování Azure směrování mezi podsítěmi zabránit, nebo pro směrování provozu mezi podsítěmi přes síťové virtuální zařízení, třeba můžete přepsat. Pokud požadujete, aby přenosy mezi prostředky ve stejné virtuální síti flow přes síťové virtuální zařízení (NVA), nasaďte prostředky do různých podsítí. Další informace najdete v [zabezpečení](#security).
- Můžete omezit přístup k prostředkům Azure, například účet služby Azure storage nebo Azure SQL database do konkrétních podsítí s koncový bod služby virtuální sítě. Kromě toho můžete odepřít přístup k prostředkům z Internetu. Můžete vytvořit více podsítí a povolit koncový bod služby pro některé podsítě, ale ne pro jiné. Další informace o [koncové body služby](virtual-network-service-endpoints-overview.md), a prostředků Azure můžete povolit je.
- Můžete přiřadit žádnou nebo jednu skupinu zabezpečení sítě ke každé podsíti ve virtuální síti. Můžete přiřadit stejné nebo jiné, síťové skupiny zabezpečení do každé podsítě. Každá skupina zabezpečení sítě obsahuje pravidla, která povolují nebo zakazují provoz do a ze zdroje a cíle. Další informace o [skupiny zabezpečení sítě](#traffic-filtering).

## <a name="security"></a>Zabezpečení

Můžete filtrovat síťový provoz do a z prostředků ve virtuální síti pomocí skupin zabezpečení sítě a síťových virtuálních zařízení. Můžete řídit, jak Azure směruje provoz z podsítě. Můžete také omezit, kdo ve vaší organizaci můžete pracovat s prostředky ve virtuálních sítích.

### <a name="traffic-filtering"></a>Filtrování provozu

- Můžete filtrovat síťový provoz mezi prostředky ve virtuální síti pomocí skupiny zabezpečení sítě, síťové virtuální zařízení, která filtruje provoz sítě, nebo obojí. Filtrování provozu sítě nasadit síťové virtuální zařízení, jako je brána firewall, najdete v článku [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Při použití síťového virtuálního zařízení, můžete také vytvořit vlastní trasy pro směrování provozu z podsítě do síťového virtuálního zařízení. Další informace o [směrování provozu](#traffic-routing).
- Skupina zabezpečení sítě obsahuje několik výchozích pravidel zabezpečení, která povolují nebo zakazují provoz do nebo z prostředků. Skupina zabezpečení sítě lze přidružit k síťovému rozhraní a podsíti, síťové rozhraní se nachází v. Aby se zjednodušila správa pravidel zabezpečení, se doporučuje že přidružíte skupinu zabezpečení sítě pro jednotlivé podsítě, spíše než jednotlivé síťová rozhraní v rámci podsítě, kdykoli je to možné.
- Pokud různé virtuální počítače v podsíti, pravidla zabezpečení použitá jim, můžete přidružit síťové rozhraní ve virtuálním počítači na jeden nebo více skupin zabezpečení aplikací. Pravidlo zabezpečení můžete určit skupinu zabezpečení aplikace v její zdroj a cíl. Toto pravidlo potom vztahuje pouze na síťová rozhraní, které jsou členy skupiny zabezpečení aplikace. Další informace o [skupiny zabezpečení sítě](security-overview.md) a [skupiny zabezpečení aplikací](security-overview.md#application-security-groups).
- Azure vytvoří několik výchozích pravidel zabezpečení v rámci jednotlivých skupin zabezpečení sítě. Jedno výchozí pravidlo umožňuje veškerý provoz mezi všechny prostředky ve virtuální síti. Chcete-li toto chování přepsat, použijte zabezpečení sítě ve skupině, vlastního směrování směrovat provoz na síťové virtuální zařízení, nebo obojí. Doporučuje se, že je seznámit se všemi Azure [výchozích pravidlech zabezpečení](security-overview.md#default-security-rules) a pochopit, jak se používají pravidla skupiny zabezpečení sítě k prostředku.

Můžete zobrazit vzorové návrhy pro implementace DMZ mezi Azure a internet pomocí [síťové virtuální zařízení](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) nebo [skupiny zabezpečení sítě](virtual-networks-dmz-nsg.md).

### <a name="traffic-routing"></a>Směrování provozu

Azure vytvoří několik výchozích tras pro odchozí provoz z podsítě. Můžete přepsat výchozí Azure pro směrování vytvořením směrovací tabulku a přidružíte ji k podsíti. Běžné důvody pro přepsání Azure výchozí směrování jsou:
- Protože chcete, aby provoz mezi podsítěmi probíhat přes síťové virtuální zařízení. Další informace o tom, jak [konfigurovat směrovací tabulky pro vynucení provozu přes síťové virtuální zařízení](tutorial-create-route-table-portal.md).
- Vzhledem k tomu, že budete chtít vynutit veškerý provoz směřující na internet přes síťové virtuální zařízení, nebo na místě, prostřednictvím služby Azure VPN gateway. Vynucení internetových přenosů do místní pro kontrolu a protokolování se často označuje jako vynucené tunelování. Další informace o tom, jak nakonfigurovat [vynucené tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Pokud potřebujete implementovat vlastní směrování, se doporučuje, aby měli seznámit s [směrování v Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Připojení

Virtuální sítě můžete připojit k jiným virtuálním sítím pomocí partnerského vztahu virtuálních sítí nebo k vaší místní síti pomocí služby Azure VPN gateway.

### <a name="peering"></a>Partnerské vztahy

Při použití [partnerský vztah virtuální sítě](virtual-network-peering-overview.md), virtuální sítě můžou být ve stejném, nebo jiné, podporovaných oblastech Azure. Virtuální sítě může být ve stejném nebo jiném předplatná Azure (patří do různých tenantů Azure Active Directory i předplatná). Před vytvořením partnerského vztahu, se doporučuje, aby měli seznámit se všemi vytvoření partnerského vztahu [požadavky a omezení](virtual-network-manage-peering.md#requirements-and-constraints). Šířka pásma mezi prostředky ve virtuálních sítích s partnerskými uzly ve stejné oblasti je stejná, jako kdyby byly ve stejné virtuální síti.

### <a name="vpn-gateway"></a>VPN Gateway

Můžete použít Azure [VPN Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro připojení k místní síti pomocí virtuální sítě [site-to-site VPN](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json), nebo Azure pomocí vyhrazeného připojení [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Můžete kombinovat partnerský vztah a bránu VPN k vytvoření [hvězdicové sítě](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), kde uvedené virtuální sítě připojit k virtuální síti centra a Centrum připojí k místní síti, třeba.

### <a name="name-resolution"></a>Překlad adres

Prostředky v jedné virtuální síti nelze přeložit názvy prostředků v partnerské virtuální síti Azure pomocí [integrovanou DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md). K překladu názvů v partnerské virtuální síti, [nasazení serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server), nebo použít Azure DNS [privátních domén](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Řešení názvů mezi prostředky ve virtuální síti a místními sítěmi také vyžaduje, abyste k nasazení serveru DNS.

## <a name="permissions"></a>Oprávnění

Azure využívá [řízení přístupu podle rolí](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) k prostředkům. Oprávnění se přiřazují [oboru](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) v následující hierarchie: Předplatné, skupina pro správu, skupinu prostředků a jednotlivých prostředků. Další informace o hierarchii, najdete v článku [uspořádání prostředků](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pro práci s virtuálními sítěmi Azure a všechny jejich související funkce, jako jsou vytvoření partnerského vztahu, skupiny zabezpečení sítě, koncové body služby a směrovací tabulky, můžete přiřadit členové vaší organizace do vestavěné [vlastníka](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [Přispěvatel](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor), nebo [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí a pak přiřadit roli, kterou chcete vhodným rozsahem. Pokud chcete přiřadit specifické oprávnění pro podmnožinu funkcí virtuální sítě, vytvořte [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a přiřadit konkrétní oprávnění požadovaná pro [virtuálních sítí](manage-virtual-network.md#permissions), [ podsítě a koncové body služby](virtual-network-manage-subnet.md#permissions), [síťová rozhraní](virtual-network-network-interface.md#permissions), [partnerský vztah](virtual-network-manage-peering.md#permissions), [skupiny zabezpečení sítě a aplikace](manage-network-security-group.md#permissions), nebo [směrovací tabulky](manage-route-table.md#permissions) k roli.

## <a name="policy"></a>Zásada

Služba Azure Policy umožňuje vytváříte, přiřazujete a spravujete definice zásad. Definice zásad u vašich prostředků vynucují různá pravidla, takže prostředky zůstanou splňovat standardy organizace a smlouvy o úrovni služeb. Azure Policy provádí hodnocení vašich prostředků, skenování pro prostředky, které nejsou kompatibilní s definicemi zásad, které máte. Můžete například definovat a použije zásadu, která umožňuje vytvářet virtuální sítě v jenom konkrétní skupině prostředků nebo oblasti. Další zásady můžete vyžadovat, že má každý podsíť skupinu zabezpečení sítě přidruženou k němu. Zásady se následně vyhodnocují při vytváření a aktualizaci prostředků.

Zásady se použijí u následující hierarchie: Předplatné, skupina pro správu a skupiny prostředků. Další informace o [Azure policy](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo nasadit virtuální sítí [šablonu zásad](policy-samples.md) ukázky.

## <a name="next-steps"></a>Další postup

Další informace o úlohách, nastavení a možnosti pro [virtuální sítě](manage-virtual-network.md), [podsítě a služby koncového bodu](virtual-network-manage-subnet.md), [síťové rozhraní](virtual-network-network-interface.md), [partnerského vztahu](virtual-network-manage-peering.md), [skupiny zabezpečení sítě a aplikace](manage-network-security-group.md), nebo [směrovací tabulka](manage-route-table.md).
