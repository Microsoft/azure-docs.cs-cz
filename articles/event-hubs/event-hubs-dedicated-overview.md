---
title: Přehled vyhrazených center událostí – Azure Event Hubs | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled vyhrazených center událostí Azure, která nabízejí jednoklientská nasazení centra událostí.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72516741"
---
# <a name="overview-of-event-hubs-dedicated"></a>Přehled vyhrazených centra událostí

*Clustery Event Hubs* nabízejí nasazení pro jednoho klienta pro zákazníky s nejnáročnějšími potřebami streamování. Tato nabídka pro jednoho tenanta má garantovanou 99,99% sla a je k dispozici pouze na naší vyhrazené cenové úrovni. Cluster centra událostí může příchozí přenos milionů událostí za sekundu s garantovanou kapacitou a latencí podsekund. Obory názvů a centra událostí vytvořená v rámci vyhrazeného clusteru zahrnují všechny funkce standardní nabídky a další, ale bez omezení příchozího přenosu dat. Obsahuje také oblíbenou funkci [zachycení centra událostí](event-hubs-capture-overview.md) bez dalších nákladů, což vám umožní automaticky dávkovat a protokolovat datové proudy do Azure Storage nebo Azure Data Lake. 

Clustery jsou zřízeny a fakturovány **jednotkami kapacity (CU)**, předem přidělené množství prostředků procesoru a paměti. Pro každý cluster můžete zakoupit 1, 2, 4, 8, 12, 16 nebo 20 CU. Kolik můžete ingestovat a streamovat na CU, závisí na různých faktorech, jako je počet výrobců a spotřebitelů, tvar užitečného zatížení, rychlost odchozího přenosu (další podrobnosti viz výsledky benchmarku níže). 

> [!NOTE]
> Všechny clustery event hubů jsou ve výchozím nastavení povoleny kafka a podporují koncové body Kafka, které mohou být použity stávajícími aplikacemi založenými na Kafce. Povolení kafky v clusteru nemá vliv na případy použití mimo Kafka; neexistuje žádná možnost nebo je třeba zakázat Kafka v clusteru.

## <a name="why-dedicated"></a>Proč věnováno?

Vyhrazená centra událostí nabízejí tři přesvědčivé výhody pro zákazníky, kteří potřebují kapacitu na podnikové úrovni:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Jednonájem zaručuje kapacitu pro lepší výkon

Vyhrazený cluster zaručuje kapacitu v plném rozsahu a může příchozí až gigabajty streamovaných dat s plně trvanlivé úložiště a sub-druhá latence pro všechny burst v provozu. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inkluzivní a exkluzivní přístup k funkcím 
Vyhrazená nabídka obsahuje funkce, jako je capture bez dalších nákladů, stejně jako exkluzivní přístup k nadcházejícím funkcím, jako je Bring Your Own Key (BYOK). Služba také spravuje vyrovnávání zatížení, aktualizace operačního systému, opravy zabezpečení a dělení pro zákazníka, takže můžete strávit méně času na údržbu infrastruktury a více času na vytváření funkcí na straně klienta.  

#### <a name="cost-savings"></a>Úspora nákladů
Při vysokých objemech příchozích dat (>100 jednotek tu), cluster udává podstatně méně za hodinu než nákup srovnatelného množství jednotek propustnosti v nabídce Standard.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Vyhrazená kvóta a limity centra událostí

Vyhrazená nabídka Event Hubs se účtuje za pevnou měsíční cenu s minimálně 4 hodinami používání. Úroveň Dedicated nabízí všechny funkce standardního plánu, ale s kapacitou podnikového škálování a omezením pro zákazníky s náročnými úlohami. 

| Funkce | Standard | Vyhrazená |
| --- |:---:|:---:|
| Šířka pásma | 20 Tu (až 40 Tu) | 20 Cu |
| Jmenné prostory |  1 | 50 za CU |
| Event Hubs |  10 na obor názvů | 1000 na obor názvů |
| Události příchozího přenosu dat | Plaťte za milion událostí | Zahrnuje |
| Velikost zprávy | 1 milion bajtů | 1 milion bajtů |
| Oddíly | 32 na Centrum událostí | 1024 na centrum událostí |
| Skupiny příjemců | 20 na Centrum událostí | Bez omezení na CU, 1000 na centrum událostí |
| Zprostředkovaná připojení | 1 000 v ceně, 5 000 max | 100 K v ceně a max |
| Uchovávání zpráv | 7 dní, 84 GB včetně na TU | 90 dní, 10 TB včetně CU |
| Zachycování | Platba za hodinu | Zahrnuje |

## <a name="how-to-onboard"></a>Jak na palubě

Samoobslužné prostředí pro [vytvoření clusteru Event Hubs](event-hubs-dedicated-cluster-create-portal.md) prostřednictvím [portálu Azure Portal](https://aka.ms/eventhubsclusterquickstart) je teď ve verzi Preview. Máte-li jakékoli dotazy nebo potřebujete pomoc s nástupem do centra událostí vyhrazená, obraťte se na [tým Centra událostí](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Nejčastější dotazy

#### <a name="what-can-i-achieve-with-a-cluster"></a>Čeho mohu dosáhnout pomocí clusteru?

V clusteru Centra událostí závisí na tom, kolik můžete ingestovat a streamovat, na různých faktorech, jako jsou výrobci, spotřebitelé, rychlost ingestování a zpracování a mnoho dalšího. 

Následující tabulka ukazuje výsledky srovnávacích testů, kterých jsme dosáhli během testování:

| Obrazec datové části | Přijímače | Šířka pásma příchozího přenosu dat| Příchozí zprávy o přenosu dat | Šířka pásma odchozího přenosu | Odchozí zprávy | Celkem TUs | TU na CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Dávky 100x1KB | 2 | 400 MB/s | 400 tisíc zpráv/s | 800 MB/s | 800 tisíc zpráv/s | 400 tuu | 100 tuu | 
| Dávky 10x10KB | 2 | 666 MB/s | 66.6k zpráv/s | 1,33 GB/s | 133 kS zpráv/s | 666 TU | 166 TU |
| Dávky 6x32KB | 1 | 1,05 GB/s | 34k zprávy / s | 1,05 GB/s | Zprávy 34 kS/s | 1000 tuu | 250 tuu |

Při testování byla použita následující kritéria:

- Byl použit cluster centra událostí s vyhrazenou úrovní se čtyřmi jednotkami kapacity (CU). 
- Centrum událostí používané pro ingestování mělo 200 oddílů. 
- Data, která byla přijata byla přijata dvěma aplikacemi přijímače přijímajícími ze všech oddílů.

#### <a name="can-i-scale-updown-my-cluster"></a>Můžu vertikálně navýšit/snížit kapacitu clusteru?

Po vytvoření clustery se účtují minimálně 4 hodiny používání. Ve verzi Preview samoobslužného prostředí můžete odeslat [žádost o podporu](https://ms.portal.azure.com/#create/Microsoft.Support) týmu Centra událostí v části *Technická > kvóta > požadavek na škálování na výši nouzi nebo škálování vyhrazeného clusteru* pro škálování clusteru nahoru nebo dolů. Dokončení požadavku na horizontální navýšení kapacity clusteru může trvat až 7 dní. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Jak bude Geo-DR pracovat s mým clusterem?

Obor názvů můžete geograficky spárovat v clusteru vyhrazené vrstvy s jiným oborem názvů v clusteru vyhrazené vrstvy. Nedoporučujeme spárovat obor názvů vyhrazené vrstvy s oborem názvů v naší standardní nabídce, protože limit propustnost bude nekompatibilní, což bude mít za následek chyby. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Můžu migrovat standardní obory názvů tak, aby patřily do clusteru s vyhrazenou vrstvou?
V současné době nepodporujeme automatizovaný proces migrace pro migraci dat centra událostí ze standardního oboru názvů na vyhrazený. 

## <a name="next-steps"></a>Další kroky

Další podrobnosti o vyhrazených službách Event Hubs získáte od svého obchodního zástupce společnosti Microsoft nebo podpory společnosti Microsoft. Můžete také vytvořit cluster nebo získat další informace o cenových úrovních Centra událostí na následujících odkazech:

- [Vytvoření clusteru Event Hubs prostřednictvím portálu Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Centra událostí Vyhrazené ceny](https://azure.microsoft.com/pricing/details/event-hubs/). Můžete se také obrátit na svého obchodního zástupce společnosti Microsoft nebo na podporu společnosti Microsoft a získat další podrobnosti o vyhrazené kapacitě centra událostí.
- [Nejčastější dotazy k centru událostí](event-hubs-faq.md) obsahují informace o cenách a odpovídají na některé nejčastější dotazy týkající se centra událostí.
