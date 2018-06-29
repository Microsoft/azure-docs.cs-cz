---
title: Architektura sítě Azure
description: Tento článek obsahuje obecné popis sítě infrastruktury Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 67781f196b445c9330e0dcb1fc7d8b0a1a53cbc0
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102115"
---
# <a name="azure-network-architecture"></a>Architektura sítě Azure
Architektura sítě Azure následuje upravenou verzi odvětví standardní jádra nebo distribuční nebo přístup k modelu, s vrstvami odlišné hardwaru. Vrstvy, které patří:

- Základní (Datacenter směrovače)
- Distribuce (přístup směrovače a agregace L2) - vrstvě distribuční odděluje L3 směrování v L2 přepínání
- Přístup (přepínače hostitele L2)

Architektura sítě má dvě úrovně vrstvy 2 přepínače, jedné vrstvy agregování provoz z jiných a vrstvy 2 smyčky, abyste zapracovali redundance. To poskytuje výhod, třeba flexibilnější nároků sítě VLAN a zlepšuje port škálování. Architektura udržuje Ú2 a Ú3 jedinečné, což umožňuje použití hardwaru v každé z různých vrstev v síti a minimalizuje se při selhání v jedné vrstvy, ze které mají vliv na ostatní vrstvy. Použití šachty umožňuje sdílení, jako je například připojení k infrastruktuře L3 prostředků.

## <a name="network-configuration"></a>Konfigurace sítě
Architektura sítě clusteru Microsoft Azure v rámci datového centra se skládá z následujících zařízení:

- Směrovače (Datacenter, směrovač přístup a okrajové směrovače listu)
- Přepínače (agregace a horní části přepínače Rack)
- Digi CMs
- Jednotky PDU nebo Nucleons

Následující obrázek poskytuje podrobný přehled architektury sítě Azure v rámci clusteru. Azure má dva samostatné architektury. Některé stávající zákazníky služby Azure a sdílených služeb nacházet v architektuře výchozí LAN (DLA), zatímco nové oblasti a virtuální zákazníci budou mít přístup prostřednictvím architektury Quantum 10 (Otázka č. 10). Architektura DLA je návrh tradiční stromu s active pasivní přístup směrovače a přístupu (ACL) použity na směrovači přístup. Architektura 10 Quantum je návrh clos nebo OK směrovačů kde seznamy řízení přístupu se nepoužije na směrovači, ale pod směrování prostřednictvím Vyrovnávání zatížení softwaru (SLB), nebo softwarově definované sítě VLAN.

![Síť Azure][1]

### <a name="quantum-10-devices"></a>Zařízení quantum 10
Návrh Quantum 10 provede vrstvy 3 přepínání šíření více zařízení v návrhu CLOS nebo OK. Výhody návrhu Otázka č. 10 zahrnují větší schopnosti a lepší možnosti škálování stávající síťové infrastruktuře. Návrh využívá ohraničení listu směrovače, přepínače spine a horní Rack směrovače mají být předány provoz clusterů mezi víc tras, aby vám umožnil odolnost proti chybám. Zabezpečení služeb, jako je překlad síťových adres jsou zpracovávány prostřednictvím Vyrovnávání zatížení softwaru místo v hardwarových zařízeních.

### <a name="access-routers"></a>Přístup směrovače
Směrovače nebo přístup k distribuce L3 (ARs) provést primární funkci směrování pro vrstvy distribuce a přístup. Tato zařízení jsou nasazeny jako pár a jsou výchozí brána pro podsítě. Každý pár AR může podporovat více párů přepínač L2 agregace, v závislosti na kapacitu. Maximální počet závisí na kapacitu na zařízení a také domény selhání. Typické čísel na základě očekávané kapacitou by tři dvojice přepínač L2 agregace za pár AR.

### <a name="l2-aggregation-switches"></a>L2 Přepínače agregace  
Tato zařízení sloužit jako agregačního bodu L2 provozu. Jsou vrstvy distribuce pro prostředek infrastruktury L2 a může zpracovávat velké objemy přenosů. Vzhledem k tomu, že tato zařízení agregovat provoz, 802.1Q funkce je povinná a velkou šířku pásma technologie, jako je port agregaci a 10GE používají.

### <a name="l2-host-switches"></a>L2 Přepínače hostitele
Hostitelé připojit přímo k tyto přepínače. Mohou být přepínače rack připojené nebo skříň nasazení. Standard 802.1Q umožňuje označení jednu síť VLAN jako nativní sítě VLAN, považuje tento VLAN jako normální (vyžadující) rámce sítě Ethernet. Za normálních okolností jsou odeslaných a přijatých vyžadující na 802.1Q rámce na nativní síť VLAN trunk portu. Tato funkce byla navržena pro migraci 802.1Q a kompatibilitu s jiný-802.1Q zařízením. V této architektuře síťové infrastruktury využívá nativní síť VLAN.

Tato architektura Určuje standard pro nativní výběr sítě VLAN, které zajišťuje, kde je to možné, že zařízení AR mají jedinečné nativní síť VLAN pro každý trunk a L2Aggregation k L2Aggregation šachty. L2Aggregation k přepínači L2Host šachty mít jiné než výchozí nativní síť VLAN.

### <a name="link-aggregation-8023ad"></a>Odkaz agregace (802.3ad)
Odkaz agregace (PRODLEVA) umožňuje více jednotlivých odkazy na spojeno dohromady a považován za jedné logické linky. Číslo používaný k označení portu kanál rozhraní musí být standardizované, aby provozní ladění snadnější, zbytku sítě bude používat stejné číslo na obou koncích port kanál. To vyžaduje standardní určit které konec kanál portu k definování dalším dostupným číslem.

Čísla zadaná pro L2Agg k přepínači L2Host jsou čísla portu kanál používá na straně L2Agg. Rozsah čísel je více omezená na stranu L2Host, a proto je standardní použití čísla 1 a 2 na straně L2Host odkazovat na port kanál, který má na straně "a" a "b".

### <a name="vlans"></a>Sítě VLAN
Architektura sítě pomocí sítí VLAN skupiny serverů společně do jedné domény všesměrového vysílání. Čísla VLAN 802.1Q odpovídat standardy, které podporuje sítě VLAN číslované 1 – 4094.

### <a name="customer-vlans"></a>Zákazník sítí VLAN
Zákazníci, mají různé možnosti sítě VLAN implementace můžete nasadit pomocí portálu Azure, aby vyhovovaly oddělení a architektura jejich řešení. Tato řešení se nasazují přes virtuální počítače. Odkaz na odběratele najdete příklady architektura [Azure referenční architektury](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Architektura Edge
Datových centrech Azure jsou založena na vysoce redundantní a dobře zřízené síťové infrastruktury. Sítě v datových centrech Azure jsou implementované v "nutné plus jedna" (N + 1) redundance architektur nebo lepší. Úplné převzetí služeb při selhání funkce v rámci i mezi datovými centry pomáhají k zajištění dostupnosti sítě a služby. Externě jsou datová centra obsloužených okruhy vyhrazené, velkou šířku pásma sítě, které redundantně připojit vlastnosti s více než 1200 poskytovatelům internetových služeb globálně na více bodů partnerského vztahu, poskytuje více než 2 000 GB/s potenciální edge kapacity v síti.

Filtrování směrovačů ve vrstvě okraj a přístupu k síti Microsoft Azure poskytuje dobře známou zabezpečení na úrovni paketů, aby se zabránilo neoprávněné pokusy o připojení k Azure. Mohou pomoci zajistit, že skutečný obsah paketů obsahují data v očekávaném formátu a schéma komunikace očekávané klienta nebo serveru v souladu s. Azure implementuje vrstvené architektura skládající se z následujících oddělení sítě a součásti řízení přístupu:

- Okraj směrovače – oddělit prostředí aplikace z Internetu. Hraniční směrovače jsou navrženy pro zajištění ochrany proti falešná identita a omezit přístup pomocí seznamů řízení přístupu (ACL).
- Distribuce (přístup k) směrovače - směrovače přístupu jsou navrženy pro povolit pouze Microsoft schválena IP adresy, poskytují ochranu proti falšování a zavedené připojení pomocí seznamů řízení přístupu.

### <a name="a10-ddos-mitigation-architecture"></a>A10 Architektura zmírnění DDOS
Útoků DOS i nadále k dispozici skutečnou hrozbou pro spolehlivost online službách společnosti Microsoft. Více stát cílem útoků, složitější a jako služby společnosti Microsoft k více geograficky různorodá schopnost poskytovat efektivní mechanismy pro identifikaci a minimalizovat dopad útoků těchto je s vysokou prioritou.

Následující podrobnosti vysvětlují, jak je implementována systému zmírnění A10 DDOS z hlediska architektury sítě.  
Microsoft Azure používá v DCR (směrovač Datacenter), které poskytují automatické zjišťování a ke zmírnění A10 síťových zařízení. A10 řešení využívá monitorování sítě Azure k ukázkové Net toku paketů a zjistěte, jestli je útok. Jakmile se detekuje útoku, zařízení A10 slouží jako šachtové pece zmírnit útoky. Po snížení rizika, další čistou provoz je povolený v datovém centru Azure přímo z DCR. A10 řešení se používá k ochraně Azure síťové infrastruktury.

Ochrana DDoS v řešení A10 patří:

- UDP IPv4 a IPv6 vyplnění ochrany
- ICMP IPv4 a IPv6 vyplnění ochrany
- TCP IPv4 a IPv6 vyplnění ochrany
- Prevence útoků TCP SYN pro protokol IPv4 a IPv6
- Útok fragmentace

> [!NOTE]
> Ochrana proti útoku DDoS zajišťuje ve výchozím nastavení pro všechny zákazníky Azure.
>
>

## <a name="network-connection-rules"></a>Pravidla pro připojení sítě
Azure nasadí hraniční směrovače ve své síti, které poskytují zabezpečení na úrovni paketů, aby se zabránilo neoprávněné pokusy o připojení k Microsoft Azure. Jejich zajistěte, aby skutečný obsah paketů obsahují data v očekávaném formátu a v souladu s schéma komunikace očekávané klienta nebo serveru.

Hraniční směrovače oddělit prostředí aplikace z Internetu. Hraniční směrovače jsou navrženy pro zajištění ochrany proti falešná identita a omezit přístup pomocí seznamů řízení přístupu (ACL). Tyto hraniční směrovače jsou konfigurováni pomocí vrstvený přístup seznam ACL k limit síťové protokoly, které jsou povoleny přenosu hraniční směrovače a přístup k směrovače.

Síťová zařízení jsou umístěny v přístup a hraniční umístění a fungovat jako hranic body, které jsou použity filtry příchozí nebo odchozí.

## <a name="next-steps"></a>Další postup
Další informace o funkci Microsoft pro zabezpečení infrastruktury Azure najdete v tématu:

- [Azure zařízení, místní a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Zabezpečení funkce Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Azure produkční operace a Správa](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integritu infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníka v Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
