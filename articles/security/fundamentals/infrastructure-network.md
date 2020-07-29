---
title: Architektura sítě Azure
description: Tento článek poskytuje obecný popis Microsoft Azure sítě infrastruktury.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: c4756c36c2243840df69f3696e7ddac3628f3a00
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "68727178"
---
# <a name="azure-network-architecture"></a>Architektura sítě Azure
Architektura sítě Azure se řídí upravenou verzí modelu Core/Distribution/Access úrovně Standard, s odlišnými hardwarovými vrstvami. Mezi vrstvy patří:

- Jádro (směrovače Datacenter)
- Distribuce (přístup k směrovačům a agregaci L2). Distribuční vrstva odděluje směrování L3 od přepínání L2.
- Přístup (přepínače hostitele L2)

Architektura sítě má dvě úrovně přepínačů vrstvy 2. Jedna vrstva agreguje provoz z jiné vrstvy. Druhá smyčka vrstev pro začlenění redundance. Architektura nabízí pružnější nároky sítě VLAN a vylepšuje škálování portů. Architektura udržuje omezení L2 a L3 odlišně, která umožňuje použití hardwaru v každé z různých vrstev v síti a minimalizuje chyby v jedné vrstvě z vlivu na ostatní vrstvy. Používání šachty umožňuje sdílení prostředků, jako je připojení k infrastruktuře L3.

## <a name="network-configuration"></a>Konfigurace sítě
Architektura sítě clusteru Azure v rámci datového centra se skládá z následujících zařízení:

- Směrovače (datacentrum, směrovač přístupu a hraniční směrovače ohraničení)
- Přepínače (agregace a přepínače Top-of-rack)
- Adaptéry Digi CMs
- Jednotky distribuce napájení

Azure má dvě samostatné architektury. Někteří stávající zákazníci Azure a sdílené služby se nacházejí ve výchozí architektuře LAN (DLA), zatímco nové oblasti a virtuální zákazníci se nacházejí v architektuře 10 (Q10). Architektura DLA je tradiční návrh stromu, se směrovači aktivního/pasivního přístupu a seznamy řízení přístupu (ACL), které se vztahují na směrovače přístupu. Ta 10 – 10 – architektura je návrhem směrovačů, kde se na směrovačích nevztahují seznamy řízení přístupu (ACL). Místo toho se seznamy ACL používají pod směrováním, prostřednictvím služby Vyrovnávání zatížení softwaru (SLB) nebo pomocí softwarově definovaných sítí VLAN.

Následující diagram poskytuje podrobný přehled architektury sítě v rámci clusteru Azure:

![Diagram sítě Azure](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>10 – 10 zařízení
10. – 2. návrh provádí přepínání vrstvy 3 na více zařízení v návrhu sítí Clos/mřížky. Výhody návrhu Q10 zahrnují větší možnosti a větší možnost škálování stávající síťové infrastruktury. Návrh pracuje s hraničními směrovači, přepínači páteře a směrovači na úrovni racku k předávání provozu do clusterů napříč více trasami, což umožňuje odolnost proti chybám. Vyrovnávání zatížení softwaru, místo hardwarových zařízení, zpracovává služby zabezpečení, jako je například překlad síťových adres.

### <a name="access-routers"></a>Přístupové směrovače
Směrovače distribuce/přístup k L3 (ARs) provádějí funkci primárního směrování pro vrstvy distribuce a přístupu. Tato zařízení se nasazují jako dvojice a jsou výchozí bránou pro podsítě. Každý pár AR může v závislosti na kapacitě podporovat více párů přepínačů agregace L2. Maximální počet závisí na kapacitě zařízení a také na doménách selhání. Typický počet je tři páry přepínačů agregace L2 na jednu dvojici AR.

### <a name="l2-aggregation-switches"></a>Agregační přepínače L2  
Tato zařízení slouží jako agregovaný bod pro přenos L2. Jsou to distribuční vrstva pro prostředky infrastruktury L2 a můžou zpracovávat velké objemy přenosů. Vzhledem k tomu, že tato zařízení shromažďují přenos dat, vyžadují funkce q 802.1 a technologie vysoké šířky pásma, jako jsou agregace portů a 10GE.

### <a name="l2-host-switches"></a>Přepínače hostitele L2
Hostitelé se připojují přímo k těmto přepínačům. Můžou to být přepínače připojené k racku nebo nasazení skříně. 802.1 q Standard umožňuje označení jedné sítě VLAN jako nativní sítě VLAN, což považuje tuto síť VLAN za normální (netagované) rámce sítě Ethernet. Za normálních okolností se rámce v nativní síti VLAN přenáší a přijímají bez příznaku na portu 802.1 q. Tato funkce byla navržena pro migraci na 802.1 q a kompatibilitu se zařízeními, která nejsou schopná 802.1 q. V této architektuře pouze síťová infrastruktura používá nativní síť VLAN.

Tato architektura určuje standard pro výběr nativní sítě VLAN. Standard zajišťuje, pokud je to možné, že zařízení AR mají jedinečnou, nativní síť VLAN pro všechny šachty a L2Aggregation L2Aggregation šachty. L2Host přepínače L2Aggregation pro mají jinou než výchozí nativní síť VLAN.

### <a name="link-aggregation-8023ad"></a>Agregace propojení (802.3 AD)
Agregace propojení umožňuje seskupit více jednotlivých odkazů a zacházet jako jeden logický odkaz. Aby se usnadnilo provozní ladění, musí být číslo používané k určení rozhraní portu portů standardizované. Zbytek sítě používá stejné číslo na obou koncích kanálu portů.

Čísla zadaná pro přepínač L2Agg to L2Host jsou čísla portů portu, která se používají na straně L2Agg. Vzhledem k tomu, že rozsah čísel je více omezený na straně L2Host, je standardem použití čísel 1 a 2 na straně L2Host. Tyto odkazy odkazují na kanál "a" na straně "a", respektive "b".

### <a name="vlans"></a>Sítě VLAN
Síťová architektura používá sítě VLAN k seskupení serverů do jedné domény všesměrového vysílání. Čísla VLAN vyhovují standardu 802.1 q, který podporuje sítě VLAN s čísly 1 – 4094.

### <a name="customer-vlans"></a>Zákaznické sítě VLAN
Máte různé možnosti implementace sítě VLAN, které můžete nasadit prostřednictvím Azure Portal, abyste splnili požadavky na oddělení a architekturu vašeho řešení. Tato řešení se nasazují prostřednictvím virtuálních počítačů. Příklady referenčních architektur zákazníka najdete v tématu [referenční architektury Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Architektura Edge
Datová centra Azure jsou postavená na vysoce redundantní a dobře zřízené síťové infrastruktuře. Společnost Microsoft implementuje sítě v datových centrech Azure s využitím "potřeby a jedné" (N + 1) redundantních architektur nebo lepších. Úplné funkce převzetí služeb při selhání v rámci a mezi datovými centry pomůžou zajistit dostupnost sítě a služeb. V externím případě jsou datová centra obsluhována vyhrazenými síťovými okruhy s vysokou šířkou pásma. Tyto okruhy mají redundantní propojení vlastností s více než 1200 poskytovateli internetových služeb globálně na několika partnerských bodech. Tato funkce poskytuje vyšší úroveň 2 000 GB/s potenciální hraniční kapacity v síti.

Filtrování směrovačů na úrovni Edge a accessové vrstvy sítě Azure zajišťuje dobře zavedené zabezpečení na úrovni paketů a pomáhá zabránit neoprávněným pokusům o připojení k Azure. Směrovače vám pomůžou zajistit, že skutečný obsah paketů obsahuje data v očekávaném formátu a že odpovídají očekávanému komunikačnímu schématu klienta/serveru. Azure implementuje vrstvenou architekturu, která se skládá z následujících komponent pro oddělení a řízení přístupu v síti:

- **Hraniční směrovače.** Oddělit prostředí aplikace od Internetu. Hraniční směrovače jsou navržené tak, aby poskytovaly ochranu proti falšování identity a omezily přístup pomocí seznamů řízení přístupu (ACL).
- **Distribuční směrovače (Access).** Tyto povolují pouze IP adresy schválené společností Microsoft, poskytují ochranu proti falšování a navázání připojení pomocí seznamů ACL.

### <a name="ddos-mitigation"></a>Zmírnění DDOS
Útoky DDoS (Distributed Denial of Service) i nadále představují skutečnou hrozbu pro spolehlivost online služby. Protože se útoky stanou cíleny a promyšlenější a protože služby společnosti Microsoft jsou lépe geograficky různorodé, identifikace a minimalizace dopadu těchto útoků je vysoká priorita.

[Azure DDoS Protection Standard](../../virtual-network/ddos-protection-overview.md) poskytuje ochranu před útoky DDoS. Další informace najdete v tématu [Azure DDoS Protection: osvědčené postupy a referenční architektury](ddos-best-practices.md) .

> [!NOTE]
> Microsoft poskytuje standardně DDoS Protection pro všechny zákazníky Azure.
>
>

## <a name="network-connection-rules"></a>Pravidla síťového připojení
V jeho síti Azure nasadí hraniční směrovače, které poskytují zabezpečení na úrovni paketů, aby se předešlo neoprávněným pokusům o připojení k Azure. Hraniční směrovače zajišťují, že skutečný obsah paketů obsahuje data v očekávaném formátu a odpovídá očekávanému komunikačnímu schématu klienta/serveru.

Hraniční směrovače oddělí prostředí aplikace od Internetu. Tyto směrovače jsou navržené tak, aby poskytovaly ochranu proti falšování identity, a omezují přístup pomocí seznamů ACL. Společnost Microsoft konfiguruje hraniční směrovače pomocí víceúrovňového přístupu přes vrstvený přístup k omezení síťových protokolů, které mají povoleno přenos hraničních směrovačů a přístupových směrovačů.

Společnost Microsoft umisťuje síťová zařízení do umístění Access a Edge, aby fungovala jako hraniční body, ve kterých se používají příchozí nebo výstupní filtry.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure, najdete tady:

- [Zařízení, místní a fyzické zabezpečení Azure](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Komponenty a hranice informačních systémů Azure](infrastructure-components.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Provozní provoz a Správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)


