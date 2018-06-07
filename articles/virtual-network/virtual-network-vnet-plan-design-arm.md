---
title: Plánování virtuální sítě Azure | Microsoft Docs
description: Informace o plánování pro virtuální sítě na základě vašeho izolace, připojení a požadavky na umístění.
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
ms.openlocfilehash: fd290420c2c755e07f6949750e3a88bcb64682f3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656903"
---
# <a name="plan-virtual-networks"></a>Plánování virtuální sítě

Vytvoření virtuální sítě a experimentovat s je dostatečně snadno, ale pravděpodobné, budete nasazovat více virtuálních sítí v čase pro podporu provozním potřebám vaší organizace. Díky některé plánování, bude možné nasadit virtuální sítě a připojení prostředků, které potřebujete efektivněji. Informace v tomto článku je užitečný zejména, pokud jste již obeznámeni s virtuálními sítěmi a máte nějaké zkušenosti s práce s nimi. Pokud nejste obeznámeni s virtuálními sítěmi, je doporučeno, abyste si přečetli [Přehled virtuálních sítí](virtual-networks-overview.md).

## <a name="naming"></a>Pojmenování

Všechny prostředky Azure mít název. Název musí být jedinečný v rámci oboru, který se mohou lišit pro každý typ prostředku. Název virtuální sítě, například musí být jedinečný v rámci [skupiny prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), ale mohou být duplicitní v rámci [předplatné](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) nebo Azure [oblast](https://azure.microsoft.com/regions/#services). Definování zásady vytváření názvů, který můžete použít konzistentně při pojmenování prostředků je užitečné, pokud správa několika síťovým prostředkům v čase. Návrhy, najdete v části [konvence vytváření názvů](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#networking).

## <a name="regions"></a>Oblasti

Všechny prostředky Azure jsou vytvořeny do oblasti Azure a předplatné. Prostředek lze vytvořit pouze ve virtuální síti, který již existuje ve stejné oblasti a předplatné jako prostředek. Ale můžete propojit virtuální sítě, které existují v různých předplatných a oblastech. Další informace najdete v tématu [připojení](#connectivity). Při rozhodování, které oblasti k prostředkům v nasazení, zvažte, kde spotřebitelé prostředků fyzicky nacházejí:

- Obvykle je vhodné spotřebiteli prostředků nejnižší latenci sítě ke svým prostředkům. K určení relativních latenci mezi do zadaného umístění a oblastech Azure, najdete v části [zobrazení relativní latence](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Máte požadavky dat sídle suverenity, dodržování předpisů nebo odolnosti? Pokud ano, je důležité výběr oblasti, aby požadavky. Další informace najdete v tématu [Azure zeměpisných](https://azure.microsoft.com/global-infrastructure/geographies/).
- Vyžadujete odolnosti napříč Azure dostupnost zóny v rámci stejné oblasti Azure pro prostředky, které můžete nasadit? Prostředky, jako jsou virtuální počítače (VM) můžete nasadit do různých dostupnost zóny v rámci stejné virtuální síti. Ne všechny oblasti Azure ale podporují dostupnost zóny. Další informace o dostupnosti zóny a oblasti, které je podporují, najdete v části [dostupnost zóny](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Předplatná

Můžete nasadit tolik virtuální sítě podle potřeby v rámci každé předplatné, až [limit](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Některé organizace mají třeba různých předplatných pro různá oddělení. Další informace a pokyny ohledně odběry, najdete v části [zásad správného řízení předplatné](/azure/architecture/cloud-adoption-guide/subscription-governance#define-your-hierarchy).

## <a name="segmentation"></a>Segmentace

Můžete vytvořit několik virtuálních sítí podle předplatného a každou oblast. Můžete vytvořit několik podsítí v rámci jedné virtuální sítě. Důležité informace, které vám pomohou určit, kolik virtuální sítě a podsítě, budete potřebovat:

### <a name="virtual-networks"></a>Virtuální sítě

Virtuální síť se virtuální a izolované část Azure veřejné síti. Každá virtuální síť je vyhrazen pro vaše předplatné. Co je třeba zvážit při rozhodování, zda vytvořit jednu virtuální síť, nebo více virtuálních sítí v předplatném:

- Neexistují žádné požadavky na zabezpečení organizace pro izolaci provozu do samostatné virtuální sítě? Můžete propojit virtuální sítě, nebo ne. Pokud připojíte virtuální sítě, můžete implementovat virtuální zařízení sítě, jako je například Brána firewall, pokud chcete řídit tok přenosů mezi virtuálními sítěmi. Další informace najdete v tématu [zabezpečení](#security) a [připojení](#connectivity).
- Existují nějaké organizační požadavky pro izolaci virtuální sítě na samostatné [odběry](#subscriptions) nebo [oblasti](#regions)?
- A [síťové rozhraní](virtual-network-network-interface.md) umožňuje virtuálních počítačů komunikovat s jiným prostředkům. Každé rozhraní sítě má jeden nebo více privátních IP adres přiřazené. Kolik síťových rozhraní a [privátních IP adres](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) vyžadujete ve virtuální síti? Existují [omezení](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) počet síťových rozhraní a privátní IP adresy, které může mít v rámci virtuální sítě.
- Chcete pro připojení virtuální sítě s jinou virtuální sítí nebo místní sítě? Můžete se připojit k sobě navzájem nebo místní sítě, ale jiné ne některé virtuální sítě. Další informace najdete v tématu [připojení](#connectivity). Každý virtuální síť, kterou můžete připojit k jiné virtuální síti, nebo do místní sítě, musí mít jedinečný adresní prostor. Každá virtuální síť má jeden nebo více rozsahů veřejného nebo soukromého adresního přiřazené k jeho adresní prostor. Rozsah adres je zadat v notaci internetové domény směrování (CIDR) formátu, například 10.0.0.0/16. Další informace o [rozsahy adres](manage-virtual-network.md#add-or-remove-an-address-range) pro virtuální sítě.
- Máte všechny požadavky organizace správy pro prostředky v různých virtuálních sítích? Pokud ano, může být oddělení prostředků do samostatné virtuální sítě, aby se zjednodušila [přiřazení oprávnění](#permissions) pro jednotlivce ve vaší organizaci nebo přiřadit různé [zásady](#policies) na různé virtuální sítě.
- Pokud nasazujete některé prostředky služby Azure do virtuální sítě, uživatel může vytvořit své vlastní virtuální síti. Pokud chcete zjistit, zda služba Azure vytvoří vlastní virtuální síti, najdete v části informace pro každý [služba Azure, které můžou být nasazené do virtuální sítě](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Podsítě

Virtuální sítě mohou být segmentovány do jedné nebo více podsítí až [omezení](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Co je třeba zvážit při rozhodování, zda vytvořit jednu podsíť, nebo více virtuálních sítí v předplatném:

- Každá podsíť musí mít jedinečnou adresu rozsahu, zadaný ve formátu CIDR, v rámci adresního prostoru virtuální sítě. Rozsah adres se nesmí překrývat s jiných podsítí ve virtuální síti.
- Pokud plánujete nasadit některé prostředky služby Azure do virtuální sítě, se může vyžadovat, nebo vytvořit vlastní podsíti, tedy s sebou musí být dostatečně nepřidělené místo pro ně Uděláte to tak. Pokud chcete zjistit, zda služba Azure vytvoří vlastní podsíti, najdete v části informace pro každý [služba Azure, které můžou být nasazené do virtuální sítě](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Například pokud se virtuální sítě připojit k místní síti pomocí služby Azure VPN Gateway, virtuální síť musí mít podsíť vyhrazené pro bránu. Další informace o [podsítě brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Azure trasy síťový provoz mezi všech podsítí ve virtuální síti, ve výchozím nastavení. Azure výchozí směrování, aby se zabránilo Azure směrování mezi podsítěmi nebo směrovat provoz mezi podsítěmi prostřednictvím sítě virtuálního zařízení, například můžete přepsat. Pokud požadujete, aby provoz mezi prostředky ve stejné virtuální sítě probíhaly přes virtuální síťové zařízení (hodnocení chyb zabezpečení), nasaďte tyto prostředky do různých podsítí. Další informace v [zabezpečení](#security).
- Můžete omezit přístup k prostředkům Azure například účtu úložiště Azure nebo Azure SQL database, ke konkrétním podsítím s koncového bodu služby virtuální sítě. Navíc můžete odepřít přístup k prostředkům z Internetu. Můžete vytvořit několik podsítí a povolení koncového bodu služby pro některé podsítě, ale jiné ne. Další informace o [koncové body služby](virtual-network-service-endpoints-overview.md), a prostředky Azure můžete povolit je.
- Můžete přidružit žádnou nebo jednu skupinu zabezpečení sítě pro každou podsíť ve virtuální síti. Můžete přidružit stejné, nebo na jiný, sítě skupinu zabezpečení pro každou podsíť. Každá skupina zabezpečení sítě obsahuje pravidla, která povolí nebo zakážou provoz do a z zdroje a cíle. Další informace o [skupin zabezpečení sítě](#traffic-filtering).

## <a name="security"></a>Zabezpečení

Můžete filtrovat síťový provoz do a z prostředků ve virtuální síti pomocí skupin zabezpečení sítě a virtuálních síťových zařízení. Můžete řídit, jak Azure směruje provoz z podsítě. Můžete také omezit, kdo ve vaší organizaci můžete pracovat s prostředky ve virtuálních sítích.

### <a name="traffic-filtering"></a>Filtrování provozu

- Můžete filtrovat síťový provoz mezi prostředky ve virtuální síti pomocí skupiny zabezpečení sítě, hodnocení chyb zabezpečení, který filtruje síťový provoz, nebo obojí. Nasazení hodnocení chyb zabezpečení, jako je například Brána firewall, filtrování provozu sítě najdete v tématu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Pokud používáte hodnocení chyb zabezpečení, je také vytvořit vlastní trasy pro směrování provozu z podsítě k hodnocení chyb zabezpečení. Další informace o [směrování provozu](#traffic-routing).
- Skupina zabezpečení sítě obsahuje několik výchozích pravidel zabezpečení, která povolí nebo zakážou provoz do nebo z prostředků. Skupina zabezpečení sítě může být přidružena k rozhraní sítě, podsítě, které je v rozhraní sítě nebo obojí. Aby se zjednodušila správa pravidel zabezpečení, se doporučuje přidružíte skupinu zabezpečení sítě pro jednotlivé podsítě, že místo jednotlivým síťovým rozhraní v rámci podsítě, kdykoli je to možné.
- Pokud jiné virtuální počítače v podsíti potřebovat jiné bezpečnostní pravidla na ně použity, můžete přidružit rozhraní sítě ve virtuálním počítači na jeden nebo více skupin zabezpečení aplikace. Pravidlo zabezpečení můžete zadat skupiny zabezpečení služby aplikace v jeho zdrojový, cílový nebo obojí. Toto pravidlo pak platí jenom pro rozhraní sítě, které jsou členy skupiny zabezpečení aplikace. Další informace o [skupin zabezpečení sítě](security-overview.md) a [skupin zabezpečení aplikací](security-overview.md#application-security-groups).
- Azure vytvoří několik výchozích pravidel zabezpečení v rámci jednotlivých skupin zabezpečení sítě. Jeden výchozí pravidlo umožňuje všechny přenosu mezi všechny prostředky ve virtuální síti. Chcete-li toto chování potlačit, zabezpečení sítě pomocí skupin, vlastní směrování směrovat provoz do hodnocení chyb zabezpečení, nebo do obou. Je doporučeno, seznamte se se všemi Azure [výchozí pravidla zabezpečení](security-overview.md#default-security-rules) a pochopit, jak se používají pravidla skupiny zabezpečení sítě k prostředku.

Můžete zobrazit vzorové návrhy pro implementaci DMZ mezi Azure a internet pomocí [hodnocení chyb zabezpečení](/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) nebo [skupin zabezpečení sítě](virtual-networks-dmz-nsg.md).

### <a name="traffic-routing"></a>směrování provozu

Azure vytvoří několik výchozích tras odchozího provozu z podsítě. Můžete přepsat výchozí Azure směrování vytvořením směrovací tabulku a jeho přidružením k podsíti. Běžných příčin pro přepsání Azure výchozí směrování jsou:
- Protože chcete, aby provoz mezi podsítěmi, chcete-li procházet skrz hodnocení chyb zabezpečení. Další informace o tom, jak [konfiguraci směrovací tabulky vynutit přenos prostřednictvím hodnocení chyb zabezpečení](tutorial-create-route-table-portal.md).
- Vzhledem k tomu, že budete chtít vynutit veškerý provoz vázaný na internet prostřednictvím hodnocení chyb zabezpečení, nebo na místní, prostřednictvím služby Azure VPN gateway. Vynucení internetové přenosy místní pro kontroly a protokolování se často označuje jako vynuceného tunelování. Další informace o tom, jak nakonfigurovat [vynuceného tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Pokud potřebujete implementovat vlastní směrování, doporučuje se, že jsou Seznamte se s [směrování v Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Připojení

Virtuální síť můžete připojit k jiné virtuální sítě pomocí virtuální sítě partnerský vztah, nebo k místní síti, pomocí služby Azure VPN gateway.

### <a name="peering"></a>Partnerské vztahy

Při použití [partnerský vztah virtuální sítě](virtual-network-peering-overview.md), virtuální sítě může být ve stejném, nebo jiné, podporované oblasti Azure. Virtuální sítě může být ve stejné nebo různých předplatných Azure, tak dlouho, dokud oba odběry jsou přiřazeny k stejné klienta Azure Active Directory. Před vytvořením partnerský vztah, se doporučuje, seznamte se se všemi partnerského vztahu [požadavky a omezení](virtual-network-manage-peering.md#requirements-and-constraints). Šířku pásma mezi prostředky ve virtuálních sítích peered ve stejné oblasti je stejný, jako kdyby byly prostředky ve stejné virtuální síti.

### <a name="vpn-gateway"></a>VPN Gateway

Můžete použít Azure [brány VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro připojení k místní síti pomocí virtuální sítě [site-to-site VPN](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json), nebo pomocí vyhrazené připojení Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Zkombinováním partnerský vztah a brány VPN k vytvoření [hvězdicové sítě](/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), kde ramenem virtuální sítě připojit k virtuální síti rozbočovače a rozbočovače připojí k místní síti, například.

### <a name="name-resolution"></a>Překlad adres

Prostředky v jedné virtuální síti nelze přeložit názvy prostředků v peered virtuální sítě pomocí Azure [integrovanou DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md). Překlad názvů ve virtuální síti peered, [nasazení serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server), nebo použijte Azure DNS [privátní domény](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Řešení názvů mezi prostředky ve virtuální síti a místní sítě také vyžaduje, abyste k nasazení serveru DNS.

## <a name="permissions"></a>Oprávnění

Využívá Azure [řízení přístupu podle rolí](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) k prostředkům. Jsou přiřazeny oprávnění [oboru](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) v následující hierarchie: předplatné, skupinu pro správu, skupinu prostředků a jednotlivých prostředků. Další informace o hierarchii, najdete v části [uspořádání prostředků](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pro práci s virtuálních sítí Azure a všechny jejich souvisejících funkcí, jako je vytvoření partnerského vztahu, skupiny zabezpečení sítě, koncové body služby a směrovací tabulky, můžete přiřadit členy vaší organizace integrované [vlastníka](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [Přispěvatel](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor), nebo [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) role a potom přiřadit roli tak, aby je vhodný rozsah. Pokud chcete přiřadit specifické oprávnění pro podmnožinu funkcí virtuální sítě, vytvořte [vlastní role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a přiřadit konkrétní oprávnění požadovaná pro [virtuální sítě](manage-virtual-network.md#permissions), [ podsítě a koncové body služby](virtual-network-manage-subnet.md#permissions), [síťových rozhraní](virtual-network-network-interface.md#permissions), [partnerský vztah](virtual-network-manage-peering.md#permissions), [skupin zabezpečení sítě a aplikace](manage-network-security-group.md#permissions), nebo [směrovacích tabulek](manage-route-table.md#permissions) k roli.

## <a name="policy"></a>Zásada

Azure zásady umožňuje vytvořit, přiřadit a spravovat definice zásady. Definice zásad vynutit různá pravidla přes vaše prostředky, takže prostředky zůstanou kompatibilní s vaší organizační standardy a smlouvy o úrovni služeb. Azure zásad běží zkušební verzi vašich prostředků hledání prostředků, které nejsou kompatibilní s definice zásady, které máte. Můžete například definovat a použít zásadu, která umožňuje vytváření virtuálních sítí v jenom určité skupiny zdrojů nebo oblasti. Jiné zásady může vyžadovat, že všechny podsítě má skupinu zabezpečení sítě spojenou k němu. Zásady jsou proto vyhodnocována při vytváření nebo aktualizaci prostředky.

Zásady platí pro následující hierarchie: předplatné, skupinu pro správu a skupina prostředků. Další informace o [Azure zásad](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo nasadit některé virtuální sítě [šablony zásad](policy-samples.md) ukázky.

## <a name="next-steps"></a>Další postup

Další informace o všechny úlohy, nastavení a možnosti [virtuální sítě](manage-virtual-network.md), [podsítě a služby koncový bod](virtual-network-manage-subnet.md), [síťové rozhraní](virtual-network-network-interface.md), [partnerského vztahu](virtual-network-manage-peering.md), [skupinu zabezpečení sítě a aplikace](manage-network-security-group.md), nebo [směrovací tabulku](manage-route-table.md).
