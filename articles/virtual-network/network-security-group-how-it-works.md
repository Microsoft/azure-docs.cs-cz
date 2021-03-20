---
title: Skupina zabezpečení sítě – jak funguje
titlesuffix: Azure Virtual Network
description: Přečtěte si, jak skupiny zabezpečení sítě pomůžou filtrovat síťový provoz mezi prostředky Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 9510c4b0940a0a03ae9a232c3329817468aaf358
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99537894"
---
# <a name="how-network-security-groups-filter-network-traffic"></a>Jak skupiny zabezpečení sítě filtr síťového provozu
<a name="network-security-groups"></a>

Skupinu zabezpečení sítě Azure můžete použít k filtrování síťového provozu do a z prostředků Azure ve službě Azure Virtual Network. Skupina zabezpečení sítě obsahuje [pravidla zabezpečení](./network-security-groups-overview.md#security-rules) , která povolují nebo odmítají příchozí síťový provoz nebo odchozí síťový provoz z několika typů prostředků Azure. Pro každé pravidlo můžete určit zdroj a cíl, port a protokol.

Do virtuální sítě Azure můžete nasadit prostředky z několika služeb Azure. Úplný seznam najdete v tématu popisujícím [služby, které je možné nasadit do virtuální sítě](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Ke každé [podsíti](virtual-network-manage-subnet.md#change-subnet-settings) virtuální sítě a každému [síťovému rozhraní](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) na virtuálním počítači můžete přiřadit jednu nebo žádnou skupinu zabezpečení sítě. Stejnou skupinu zabezpečení sítě můžete přidružit k libovolnému počtu podsítí a síťových rozhraní.

Následující obrázek ukazuje různé scénáře nasazení skupin zabezpečení sítě pro povolení síťového provozu do a z internetu na portu TCP 80:

![Zpracování NSG](./media/network-security-group-how-it-works/network-security-group-interaction.png)

Předchozí obrázek společně s následujícím textem vám pomůže porozumět způsobu, jakým Azure zpracovává příchozí a odchozí pravidla pro skupiny zabezpečení sítě:

## <a name="inbound-traffic"></a>Příchozí provoz

V případě příchozího provozu zpracuje Azure nejprve pravidla ve skupině zabezpečení sítě přidružené k příslušné podsíti, pokud taková skupina existuje, a pak pravidla ve skupině zabezpečení sítě přidružené k síťovému rozhraní, pokud taková skupina existuje.

- **VM1:** Zpracují se pravidla ve skupině *NSG1*, protože je přidružená k podsíti *Subnet1* a virtuální počítač *VM1* se nachází v podsíti *Subnet1*. Pokud jste nevytvořili pravidlo povolující příchozí provoz na portu 80, výchozí pravidlo zabezpečení [DenyAllInbound](./network-security-groups-overview.md#denyallinbound) provoz odepře a skupina *NSG2* ho nikdy nevyhodnotí, protože skupina *NSG2* je přidružená k síťovému rozhraní. Pokud skupina *NSG1* obsahuje pravidlo zabezpečení povolující port 80, provoz se pak zpracuje skupinou *NSG2*. Pokud pro virtuální počítač chcete povolit příchozí provoz přes port 80, skupina *NSG1* i *NSG2* musí obsahovat pravidlo povolující příchozí provoz přes port 80 z internetu.
- **VM2:** Zpracují se pravidla ve skupině *NSG1*, protože virtuální počítač *VM2* se také nachází v podsíti *Subnet1*. Vzhledem k tomu, že k síťovému rozhraní virtuálního počítače *VM2* není přidružená žádná skupina zabezpečení sítě, přijme toto rozhraní veškerý provoz povolený skupinou *NSG1* nebo se mu odepře veškerý provoz zakázaný skupinou *NSG1*. Pokud je skupina zabezpečení sítě přidružená k podsíti, povolí se nebo se odepře provoz do všech prostředků ve stejné podsíti.
- **VM3:** Vzhledem k tomu, že k podsíti *Subnet2* není přidružená žádná skupina zabezpečení sítě, povolí se příchozí provoz do této podsítě a zpracuje se skupinou *NSG2*, protože skupina *NSG2* je přidružená k síťovému rozhraní připojenému k virtuálnímu počítači *VM3*.
- **VM4:** Provoz do virtuálního počítače *VM4* se povolí, protože k podsíti *Subnet3* ani k síťovému rozhraní na virtuálním počítači není přidružená žádná skupina zabezpečení sítě. Pokud k podsíti a síťovému rozhraní není přidružená žádná skupina zabezpečení sítě, povolí se přes ně průchod veškerého síťového provozu.

## <a name="outbound-traffic"></a>Odchozí provoz

V případě odchozího provozu zpracuje Azure nejprve pravidla ve skupině zabezpečení sítě přidružené k příslušnému síťovému rozhraní, pokud taková skupina existuje, a pak pravidla ve skupině zabezpečení sítě přidružené k podsíti, pokud taková skupina existuje.

- **VM1:** Zpracují se pravidla zabezpečení ve skupině *NSG2*. Pokud nevytvoříte pravidlo zabezpečení zakazující odchozí provoz přes port 80 do internetu, výchozí pravidlo zabezpečení [AllowInternetOutbound](./network-security-groups-overview.md#allowinternetoutbound) ve skupině *NSG1* i *NSG2* provoz povolí. Pokud skupina *NSG2* obsahuje pravidlo zabezpečení zakazující port 80, provoz se odepře a skupina *NSG1* ho nikdy nevyhodnotí. Pokud chcete na virtuálním počítači zakázat odchozí provoz přes port 80, jedna ze skupin zabezpečení sítě nebo obě musí obsahovat pravidlo zakazující odchozí provoz přes port 80 do internetu.
- **VM2:** Veškerý provoz se odešle přes síťové rozhraní do podsítě, protože k síťovému rozhraní připojenému k virtuálnímu počítači *VM2* není přidružená žádná skupina zabezpečení sítě. Zpracují se pravidla ve skupině *NSG1*.
- **VM3:** Pokud skupina *NSG2* obsahuje pravidlo zabezpečení zakazující port 80, provoz se odepře. Pokud skupina *NSG2* obsahuje pravidlo zabezpečení povolující port 80, povolí se odchozí provoz přes port 80 do internetu, protože k podsíti *Subnet2* není přidružená žádná skupina zabezpečení sítě.
- **VM4:** Veškerý odchozí provoz z virtuálního počítače *VM4* se povolí, protože k síťovému rozhraní připojenému k virtuálnímu počítači ani k podsíti *Subnet3* není přidružená žádná skupina zabezpečení sítě.


## <a name="intra-subnet-traffic"></a>Intra-Subnet provoz

Je důležité si uvědomit, že pravidla zabezpečení v NSG přidružené k podsíti můžou mít vliv na připojení mezi virtuálním počítačem v rámci tohoto virtuálního počítače. Například pokud je do *NSG1* přidáno pravidlo, které zakazuje veškerý příchozí a odchozí provoz, *VM1* a *VM2* už nebudou moct vzájemně komunikovat. Další pravidlo by se muselo přidat konkrétně, aby to bylo možné. 

Agregovaná pravidla použitá na síťové rozhraní můžete snadno zobrazit v [platných pravidlech zabezpečení](virtual-network-network-interface.md#view-effective-security-rules) pro síťové rozhraní. Pomocí funkce [Ověření toku protokolu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) v nástroji Azure Network Watcher můžete také určit, jestli je povolená komunikace směřující do síťového rozhraní nebo z něj. Při ověřování toků IP adres se dozvíte, jestli je komunikace povolená nebo zakázaná, a které pravidlo zabezpečení sítě povoluje nebo zakazuje provoz.

> [!NOTE]
> Skupiny zabezpečení sítě jsou přidruženy k podsítím nebo virtuálním počítačům a cloudovým službám nasazeným v modelu nasazení Classic a k podsítím nebo síťovým rozhraním v modelu nasazení Správce prostředků. Další informace o modelech nasazení Azure najdete v článku [Vysvětlení modelů nasazení Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> Pokud nemáte konkrétní důvod, doporučujeme přidružit skupinu zabezpečení sítě k podsíti nebo síťovému rozhraní, ale ne obojímu. Vzhledem k tomu, že může docházet ke konfliktům mezi pravidly ve skupině zabezpečení sítě přidružené k podsíti a pravidly ve skupině zabezpečení sítě přidružené k síťovému rozhraní, můžou nastat neočekávané problémy s komunikací vyžadující řešení.

## <a name="next-steps"></a>Další kroky

* Informace o prostředcích Azure, které je možné nasadit do virtuální sítě a ke kterým je možné přidružit skupiny zabezpečení sítě, najdete v tématu [Integrace virtuální sítě pro služby Azure](virtual-network-for-azure-services.md).
* Pokud jste ještě nikdy skupinu zabezpečení sítě nevytvářeli, můžete si projít rychlý [kurz](tutorial-filter-network-traffic.md), ve kterém se seznámíte s jejím vytvořením.
* Pokud už skupiny zabezpečení sítě znáte a potřebujete je spravovat, přečtěte si téma [Správa skupiny zabezpečení sítě](manage-network-security-group.md). 
* Pokud máte problémy s komunikací a potřebujete řešit potíže se skupinami zabezpečení sítě, přečtěte si téma [Diagnostika potíží s filtrováním síťového provozu virtuálních počítačů](diagnose-network-traffic-filter-problem.md). 
* Naučte se, jak povolit [protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) k analýze síťového provozu do a z prostředků, které mají přidruženou skupinu zabezpečení sítě.