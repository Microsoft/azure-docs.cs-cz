---
title: Architektura sítě Azure
description: Tento článek obsahuje obecný popis síťové infrastruktury Microsoft Azure.
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
ms.openlocfilehash: 5a0f0594a9ccb27a0f76a679e454e9a3ffe19a43
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505378"
---
# <a name="azure-network-architecture"></a>Architektura sítě Azure
Architektura sítě Azure se řídí upravenou verzi oboru standardní core / / přístup k distribuce modelu s vrstvami odlišné hardwaru. Tyto vrstvy patří:

- Jádro (datacenter směrovače)
- Distribuce (přístup směrovače a agregací Ú2). Vrstvy distribuce odděluje L3 směrování od přepínání L2.
- Přístup (L2 hostitele přepínače)

Architektura sítě má dvě úrovně přepínače vrstvy 2. Jedna vrstva agregace provoz z jiných vrstev. Druhá vrstva smyčky začlenit redundance. Flexibilnější nároky na síť VLAN a vylepšuje škálování portu. Architektura udržuje L2 a L3 distinct, která umožňuje používat hardware v každé z různých vrstev v síti a minimalizuje selhání v jedné vrstvě výstrahy neovlivňovaly další vrstvy. Použití šachty umožňuje sdílení, jako je například připojení L3 infrastruktury prostředků.

## <a name="network-configuration"></a>Konfigurace sítě
Architektura sítě clusteru služby Azure v datacentru se skládá z následujících zařízení:

- Směrovače (datacenter, směrovač přístupu a hraniční směrovače listu)
- Přepínače (přepínače agregace a top-of-rack)
- Digi CMs
- Jednotek pro distribuci napájení

Azure má dva samostatné architektury. Některé stávající zákazníky Azure a sdílené služby umístěné v architektuře sítě LAN výchozí (DLA), zatímco nové oblasti a virtuální zákazníky umístěné v architektuře Quantum 10 (10). DLA architektura je tradiční stromu návrh, s použitím směrovače přístup aktivní/pasivní vysoká dostupnost a zabezpečení seznamy řízení přístupu (ACL) použitý k přístupu k směrovačům. Architektura Quantum 10 je návrh zavřít/síť směrovače, kde seznamů ACL nejsou aplikována na směrovači. Místo toho seznamy řízení přístupu se použijí následující směrování, prostřednictvím služby Vyrovnávání zatížení softwaru (SLB) nebo softwarově definovaných sítí VLAN.

Následující diagram představuje podrobný přehled síťové architektury v Azure cluster:

![Diagram sítě Azure][1]

### <a name="quantum-10-devices"></a>Quantum 10 zařízení
Návrh Quantum 10 provádí přepínání rozšíření vrstvy 3 přes různá zařízení v návrhu Clos nebo síť. Výhody návrh 10 patří schopnost větší a lepší možnosti škálování stávající síťové infrastruktuře. Návrh využívá hraniční směrovače listu, spine přepínače a směrovače top-of-rack přenášena clustery ve více tras, povolení pro odolnost proti chybám. Softwarové Vyrovnávání zatížení, namísto hardwarových zařízení zpracovává zabezpečení služeb, jako je překlad síťových adres.

### <a name="access-routers"></a>Přístup směrovače
Rozdělení/přístup L3 směrovače (ARs) provést primární funkce směrování vrstvy distribuce a přístup. Tato zařízení jsou nasazeny jako dvojici a jsou výchozí brána pro podsítě. Každý pár AR může podporovat více párů přepínač agregace L2, v závislosti na kapacitu. Maximální počet závisí na objemu zařízení, jakož i domén selhání. Typické číslo je za pár AR tři páry přepínač agregace L2.

### <a name="l2-aggregation-switches"></a>Přepínače agregace L2  
Tato zařízení sloužit jako agregačního bodu pro provoz L2. Jsou vrstvy distribuce pro L2 prostředky infrastruktury a může zpracovávat velké objemy přenosů. Vzhledem k tomu, že tato zařízení agregovat provoz, vyžadují 802.1Q funkce a velkou šířku pásma technologie, jako je agregace portů a 10GE.

### <a name="l2-host-switches"></a>Přepínače hostitele L2
Hostitelé připojení přímo k tyto přepínače. Mohou být přepínače racku nebo skříně nasazení. Standard 802.1Q umožňuje označení jednoho VLAN jako nativní síť VLAN, zpracuje síť VLAN jako normální (neoznačených) rámce sítě Ethernet. Za normálních okolností jsou odeslaných a přijatých neoznačených na 802.1Q snímků na nativní síť VLAN trunk portu. Tato funkce je navržená pro migraci do 802.1Q a kompatibilitu s bez-802.1Q zařízení. V této architektuře síťové infrastruktury používá nativní síť VLAN.

Tato architektura Určuje standard pro výběr nativní síť VLAN. Standardní zajistí, kde je to možné, AR zařízení jedinečné, nativní síť VLAN pro každý trunk a L2Aggregation k šachty L2Aggregation. L2Aggregation k přepínači L2Host šachty mají jiné než výchozí nativní síť VLAN.

### <a name="link-aggregation-8023ad"></a>Odkaz agregace (802.3ad)
Odkaz agregace povoluje více jednotlivé odkazy na spojeny dohromady a považován za jediný odkaz na logickou. K usnadnění provozní ladění, by měl standardizované číslo používané k určení portu kanál rozhraní. Zbytku sítě používá stejné číslo na obou koncích port kanálu.

Čísla zadané pro L2Agg k přepínači L2Host jsou čísla portu kanál používá na straně L2Agg. Vzhledem k tomu, že je rozsah čísel na straně L2Host omezenější, je pomocí čísla 1 a 2 na straně L2Host standardní. Jde o portu kanál má straně "b" a "a" strana v uvedeném pořadí.

### <a name="vlans"></a>Sítě VLAN
Síťovou architekturu používají sítě VLAN na skupinu serverů dohromady do jedné domény všesměrového vysílání. Čísla VLAN 802.1Q odpovídat 1 – 4094 číslované standardy, které podporuje sítě VLAN.

### <a name="customer-vlans"></a>Virtuální místní sítě zákazníka
Existují různé možnosti implementace sítě VLAN, můžete nasadit pomocí webu Azure portal pro oddělení a architektura potřeby vašeho řešení. Tato řešení prostřednictvím virtuálních počítačů nasadit. Zákazník referenční architektury příklady najdete v tématu [referenční architektury Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Architektura Edge
Datacentra Azure jsou postavené na vysoce redundantní a dobře zřízené síťové infrastruktury. Microsoft implementuje sítěmi v rámci datových centrech Azure "potřebu plus jeden" (N + 1) redundance architektury nebo vyšší. Úplné převzetí služeb při selhání funkce v rámci a mezi datacentry, pomáhají zajistit dostupnost sítě a služby. Externě datacentry obsluhuje vyhrazené a velkou šířku pásma sítě okruhů. Vlastnosti těchto okruhů redundantně připojení s víc než 1200 poskytovatelů internetových služeb globálně v několika bodech partnerského vztahu. Tímto způsobem přesahující 2 000 GB/s potenciální edge kapacity přes síť.

Filtrování směrovače ve vrstvě edge a přístup sítě Azure poskytují zavedené zabezpečení na úrovni paketů. To pomáhá zabránit neoprávněné pokusy o připojení k Azure. Směrovače pomáhají zajistit, že obsahují data v očekávaném formátu skutečný obsah pakety a v souladu s komunikační schéma očekávané klient/server. Implementace vrstvené architektury, skládající se z následujících oddělení sítí a součástí řízení přístupu v Azure:

- **Hraniční směrovače.** Toto oddělení prostředí aplikace z Internetu. Hraniční směrovače slouží k zajištění ochrany proti falešného a omezení přístupu pomocí seznamů řízení přístupu.
- **Směrovače distribuce (access).** Tyto povolit že IP adresy schválené jenom Microsoft poskytuje ochranu proti falšování identity a připojení pomocí seznamů řízení přístupu.

### <a name="a10-ddos-mitigation-architecture"></a>Architektura zmírnění a10 před útoky DDOS
Útoky na dostupnost služby i nadále k dispozici skutečnou hrozbou spolehlivosti služeb online services. Útoky stát cílové a propracované a jako služby Microsoft poskytuje další zeměpisně odlišných identifikující a minimalizovat dopad útoků těchto je s vysokou prioritou. Následující postup podrobně vysvětluje, jak je implementovaná systému před útoky DDOS A10 omezení rizik z hlediska architektury sítě.

Azure používá A10 síťových zařízení v datovém centru směrovač (DCR), které poskytují automatické detekci a zmírnění distribuovaných útoků. Řešení A10 využívá monitorování sítě Azure k ukázkový tok paketů a určení, zda je útok. Pokud se detekuje útok, přesouváním A10 zařízení ke zmírnění útoků. Teprve pak je čistá je povolený provoz do datacentra Azure přímo z DCR. Společnost Microsoft používá A10 řešení pro ochranu Azure síťovou infrastrukturu.

Ochrana před útoky DDoS v řešení A10 patří:

- UDP IPv4 a IPv6 vyplnění ochrany
- ICMP IPv4 a IPv6 vyplnění ochrany
- TCP IPv4 a IPv6 vyplnění ochrany
- Ochrany útoky TCP SYN pro protokoly IPv4 a IPv6
- Fragmentace útoku

> [!NOTE]
> Společnost Microsoft poskytuje pro všechny zákazníky Azure DDoS protection ve výchozím nastavení.
>
>

## <a name="network-connection-rules"></a>Pravidla síťových připojení
Ve své síti Azure nasadí hraniční směrovače, které poskytují zabezpečení na úrovni paketů zabránit neoprávněné pokusy o připojení k Azure. Hraniční směrovače zkontrolujte, zda obsahují data v očekávaném formátu skutečný obsah paketů a odpovídat schématu komunikace očekávané klient/server.

Hraniční směrovače oddělit prostředí aplikace z Internetu. Tyto směrovače, jestliže jsou navržené pro zajištění ochrany proti falešného a omezení přístupu pomocí seznamů řízení přístupu. Microsoft nakonfiguruje hraniční směrovače s použitím vrstveného přístupu seznamu ACL pro omezení síťových protokolů, které jsou povoleny přenosu hraniční směrovače a přistupovat k nim směrovače.

Microsoft se umístí na přístup a hraniční umístění jako hranice body, ve kterém jsou použity filtry příchozí a výchozí přenos dat síťových zařízení.

## <a name="next-steps"></a>Další postup
Další informace o co společnost Microsoft k zajištění infrastruktury Azure, najdete v tématech:

- [Zařízení Azure, místním prostředí a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Funkce zabezpečení Azure SQL Database](azure-infrastructure-sql.md)
- [Operace Azure výroby a správu](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integrity infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníků Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
