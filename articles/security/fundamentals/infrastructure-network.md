---
title: Architektura sítě Azure
description: Tento článek obsahuje obecný popis infrastrukturní sítě Microsoft Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727178"
---
# <a name="azure-network-architecture"></a>Architektura sítě Azure
Architektura sítě Azure se řídí upravenou verzí standardního modelu jádra/distribuce/přístupu s odlišnými hardwarovými vrstvami. Vrstvy zahrnují:

- Jádro (směrovače datového centra)
- Distribuce (přístupové směrovače a agregace L2). Distribuční vrstva odděluje směrování L3 od přepínání L2.
- Přístup (hostitelské přepínače L2)

Síťová architektura má dvě úrovně přepínačů vrstvy 2. Jedna vrstva agreguje provoz z druhé vrstvy. Druhá vrstva smyčky začlenit redundance. Architektura poskytuje flexibilnější nároky na virtuální síť VLAN a zlepšuje škálování portů. Architektura udržuje L2 a L3 odlišné, což umožňuje použití hardwaru v každé z různých vrstev v síti a minimalizuje poruchy v jedné vrstvě z ovlivnění druhé vrstvy. Použití kmenů umožňuje sdílení prostředků, jako je například připojení k infrastruktuře L3.

## <a name="network-configuration"></a>Konfigurace sítě
Síťová architektura clusteru Azure v rámci datového centra se skládá z následujících zařízení:

- Směrovače (datové centrum, přístupový směrovač a hraniční směrovače)
- Přepínače (agregace a přepínače nahoře v racku)
- Digi CM
- Distribuční jednotky

Azure má dvě samostatné architektury. Někteří stávající zákazníci Azure a sdílené služby se nacházejí na výchozí architektuře LAN (DLA), zatímco nové oblasti a virtuální zákazníci jsou umístěny na architektuře Quantum 10 (Q10). Architektura DLA je tradiční návrh stromu s aktivními/pasivními přístupovými směrovači a seznamy řízení přístupu zabezpečení (ACL) aplikovanými na přístupové směrovače. Architektura Quantum 10 je návrh směrovačů Close/mesh, kde se akly na směrovačích nepoužívají. Místo toho jsou akly použity pod směrováním, prostřednictvím služby SLB (Software Load Balancing) nebo softwarově definovaných sítěMi VLAN.

Následující diagram poskytuje podrobný přehled síťové architektury v rámci clusteru Azure:

![Diagram sítě Azure](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Quantum 10 zařízení
Design Quantum 10 provádí přepínání vrstvy 3 rozložené na více zařízeních v designu Clos / mesh. Mezi výhody návrhu Q10 patří větší možnosti a větší schopnost škálovat stávající síťovou infrastrukturu. Návrh využívá hraniční listové směrovače, přepínače páteří a směrovače s horním stavem pro přenos clusterů napříč více trasami, což umožňuje odolnost proti chybám. Softwarové vyrovnávání zatížení namísto hardwarových zařízení zpracovává služby zabezpečení, jako je překlad síťových adres.

### <a name="access-routers"></a>Přístup k směrovačům
Směrovače L3 (ARs) pro distribuci a přístup k nim provádějí primární funkci směrování pro distribuční a přístupové vrstvy. Tato zařízení jsou nasazena jako pár a jsou výchozí bránou pro podsítě. Každá dvojice AR může podporovat více párů přepínačů agregace L2 v závislosti na kapacitě. Maximální počet závisí na kapacitě zařízení, stejně jako selhání domén. Typické číslo je tři páry přepínačů agregace L2 na dvojici AR.

### <a name="l2-aggregation-switches"></a>Agregační spínače L2  
Tato zařízení slouží jako bod agregace pro provoz L2. Jedná se o distribuční vrstvu pro infrastrukturu L2 a zvládnou velké množství provozu. Vzhledem k tomu, že tato zařízení agregují provoz, vyžadují funkce 802.1q a technologie s velkou šířkou pásma, jako je agregace portů a 10GE.

### <a name="l2-host-switches"></a>Hostitelské přepínače L2
Hostitelé se připojují přímo k těmto přepínačům. Mohou se jedná o přepínače montované do racku nebo nasazení šasi. Standard 802.1q umožňuje označení jedné sítě VLAN jako nativní sítě VLAN a považuje tuto síť VLAN za normální (netagovované) rámování sítě Ethernet. Za normálních okolností jsou rámce nativní síti VLAN přenášeny a přijímány netagované na portu kmene 802.1q. Tato funkce byla navržena pro migraci na 802.1q a kompatibilitu se zařízeními, která nejsou schopna 802.1q. V této architektuře používá nativní síť VLAN pouze síťová infrastruktura.

Tato architektura určuje standard pro nativní výběr sítě VLAN. Standard zajišťuje, pokud je to možné, že ar zařízení mají jedinečnou, nativní VLAN pro každý kmen a L2Aggregation na L2Aggregation kmeny. The L2Aggregation to L2Host Switch trunks have a non-default native VLAN.

### <a name="link-aggregation-8023ad"></a>Agregace odkazů (802.3ad)
Agregace odkazů umožňuje více jednotlivých odkazů, které mají být spojeny dohromady a považovány za jeden logický odkaz. Pro usnadnění provozního ladění by mělo být standardizováno číslo použité k označení rozhraní kanálu portů. Zbytek sítě používá stejné číslo na obou koncích kanálu portu.

Čísla určená pro přepínač L2Agg na L2Host jsou čísla kanálů portů používaná na straně L2Agg. Vzhledem k tomu, že rozsah čísel je omezenější na straně L2Host, standardem je použití čísel 1 a 2 na straně L2Host. Ty se vztahují k port-kanál jít na stranu "a" a "b" straně, resp.

### <a name="vlans"></a>Vlan
Síťová architektura používá sítě VLAN k seskupení serverů do jedné domény vysílání. Čísla VLAN odpovídají standardu 802.1q, který podporuje sítě VLAN s číslem 1–4094.

### <a name="customer-vlans"></a>Seznamy VLAN odběratele
Máte různé možnosti implementace sítě VLAN, které můžete nasadit prostřednictvím portálu Azure, abyste vyhověli potřebám oddělení a architektury vašeho řešení. Tato řešení nasadíte prostřednictvím virtuálních počítačů. Příklady architektury pro reference zákazníků najdete v tématu [referenční architektury Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Hrana architektura
Datová centra Azure jsou postavená na vysoce redundantních a dobře zřízených síťových infrastrukturách. Microsoft implementuje sítě v rámci datových center Azure s architekturami redundance "need plus one" (N+1) nebo lepšími. Úplné funkce převzetí služeb při selhání v datových centrech a mezi nimi pomáhají zajistit dostupnost sítě a služeb. Datacentra jsou externě obsluhována vyhrazenými síťovými obvody s velkou šířkou pásma. Tyto obvody redundantně propojují vlastnosti s více než 1200 poskytovateli internetových služeb po celém světě na více partnerských bodech. To poskytuje více než 2 000 Gb/s potenciální kapacity okrajů v síti.

Filtrování směrovačů na hraniční a přístupové vrstvě sítě Azure poskytuje zavedené zabezpečení na úrovni paketů a pomáhá zabránit neoprávněným pokusům o připojení k Azure. Směrovače pomáhají zajistit, aby skutečný obsah paketů obsahoval data v očekávaném formátu a odpovídal očekávanému schématu komunikace mezi klientem a serverem. Azure implementuje vrstvenou architekturu, která se skládá z následujících součástí oddělení sítě a řízení přístupu:

- **Hraniční směrovače.** Tyto oddělit aplikační prostředí od internetu. Hraniční směrovače jsou navrženy tak, aby poskytovaly ochranu proti falšování a omezovaly přístup pomocí aklů.
- **Distribuční (přístupové) směrovače.** Ty umožňují pouze adresy IP schválené společností Microsoft, poskytují anti-spoofing a navazují připojení pomocí aklů.

### <a name="ddos-mitigation"></a>Zmírnění DDOS
Distribuované útoky odmítnutí služby (DDoS) nadále představují skutečnou hrozbu pro spolehlivost online služeb. Jak se útoky stávají cílenějšími a sofistikovanějšími a služby, které společnost Microsoft poskytuje, jsou geograficky rozmanitější, identifikace a minimalizace dopadu těchto útoků je vysokou prioritou.

[Azure DDoS Protection Standard](../../virtual-network/ddos-protection-overview.md) poskytuje ochranu proti útokům DDoS. Další informace najdete v [tématu Azure DDoS Protection: Doporučené postupy a referenční architektury.](ddos-best-practices.md)

> [!NOTE]
> Microsoft poskytuje ochranu DDoS ve výchozím nastavení pro všechny zákazníky Azure.
>
>

## <a name="network-connection-rules"></a>Pravidla síťového připojení
Azure ve své síti nasazuje hraniční směrovače, které poskytují zabezpečení na úrovni paketů, aby zabránily neoprávněným pokusům o připojení k Azure. Hraniční směrovače zajišťují, že skutečný obsah paketů obsahuje data v očekávaném formátu a odpovídá očekávanému schématu komunikace mezi klientem a serverem.

Hraniční směrovače oddělují aplikační prostředí od internetu. Tyto směrovače jsou navrženy tak, aby poskytovaly ochranu proti falšování a omezovaly přístup pomocí aklů. Společnost Microsoft konfiguruje hraniční směrovače pomocí stupňovitého přístupu acl, aby omezila síťové protokoly, které mohou projíždět hraničními směrovači a přístupovými směrovači.

Společnost Microsoft umístí síťová zařízení na umístění přístupu a okrajů, aby působila jako hraniční body, kde jsou použity filtry příchozího nebo odchozího přenosu dat.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá, aby zabezpečil infrastrukturu Azure, najdete v tématu:

- [Zařízení Azure, prostory a fyzické zabezpečení](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Součásti a hranice informačního systému Azure](infrastructure-components.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Produkční operace a správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)


