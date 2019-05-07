---
title: Přehled vyhrazených event hubs – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek poskytuje přehled o vyhrazený Azure Event Hubs, která nabízí nasazení jednoho tenanta služby event hubs.
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
ms.openlocfilehash: e8a2d8321a42e8b3d090c1ce1fdb3fd9a7ee3714
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138657"
---
# <a name="overview-of-event-hubs-dedicated"></a>Přehled služby Event Hubs Dedicated

Clustery Azure Event Hubs nabízí nasazení jednoho tenanta pro zákazníky s nejnáročnější požadavky na datových proudů. Tuto nabídku jednoho tenanta má garantovanou smlouvou SLA 99,99 %. Je k dispozici pouze na cenovou úroveň Dedicated.

Cluster služby Event Hubs můžete příchozího přenosu dat milionů událostí za sekundu s subsecond latence a zaručené kapacity. Obory názvů a centra událostí v rámci vyhrazeného clusteru zahrnout všechny funkce standardní nabídky a další, ale žádné neomezené příchozí. Zahrnuje také [Event Hubs Capture](event-hubs-capture-overview.md) funkce bez dalších poplatků. Můžete ho automaticky služby batch a protokolu datových proudů do služby Azure Storage nebo Azure Data Lake.

Vyhrazené clustery jsou zřízené a účtuje se po kapacitních jednotkách (CUs). Kapacitní jednotky jsou předběžně přidělené objem prostředků procesoru a paměti. Můžete koupit 1, 2, 4, 8, 16, 12 nebo 20 kapacitní jednotky pro každý cluster. Kolik vám umožní ingestovat a datový proud na kapacitní jednotku závisí na faktorech, jako je počet producenti a příjemci, datová část obrazce a frekvence odchozího přenosu dat.

Další informace najdete v tabulce s výsledky srovnávacích testů.

## <a name="why-use-event-hubs-dedicated"></a>Proč používat Event hubs úrovně Dedicated?

Vyhrazená Služba Event Hubs nabízí tři výhody pro zákazníky, kteří potřebují kapacitu na podnikové úrovni.

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Jedním tenantů zaručuje kapacity pro zajištění lepšího výkonu

Vyhrazený cluster zaručuje kapacity v plném rozsahu. Může příchozí přenos dat až po gigabajtech streamovaných dat s plně trvalého úložiště a subsecond latencí tak, aby vyhovovaly nápory na provoz.

#### <a name="inclusive-and-exclusive-access-to-features"></a>Zahrnuté a výhradní přístup k funkcím 
Vyhrazené nabídka zahrnuje funkce, jako je zachytávání bez dalších poplatků. Nabízí taky exkluzivní přístup k chystaných funkcí jako BYOK. Služba také spravuje Vyrovnávání zatížení, operační systém aktualizací, oprav zabezpečení a dělení. S těmito možnostmi může strávit míň času na údržbu infrastruktury a získejte víc času na tvorbu funkcí na straně klienta.

#### <a name="cost-savings"></a>Úspora nákladů
Na svazcích vysokou příchozího přenosu dat clusteru náklady výrazně méně za hodinu, než v případě nákupu srovnatelné množství jednotek propustnosti (jednotek propustnosti) nabídka Standard. Je k velkému > 100 jednotek propustnosti.


## <a name="event-hubs-standard-vs-dedicated"></a>Event Hubs úrovně Standard vs. Vyhrazený

Následující tabulka porovnává dostupné úrovně služby Event Hubs. Event hubs úrovně Dedicated nabídka se účtuje podle pevnou měsíční cenu ve srovnání s využití ceny pro většinu funkcí úrovně Standard. Vyhrazené úroveň nabízí všechny funkce plánu Standard, ale s kapacitou na podnikové úrovni pro zákazníky s náročné úlohy.

| Funkce | Standard | Vyhrazený |
| --- |:---:|:---:|
| Události příchozího přenosu dat | Platba za milion událostí | Zahrnuje |
| Jednotka propustnosti (1 MB/s příchozí, 2 MB/s odchozí) | Platba za hodinu | Zahrnuje |
| Velikost zpráv | 1 MB | 1 MB |
| Oddíly | 40 na obor názvů | 2 000 na kapacitní jednotku |
| Skupiny příjemců | 20 za centra událostí | 1 000 na Centrum událostí |
| Maximální šířka pásma | 20 jednotek propustnosti (až 40 jednotek propustnosti, které) | 20 kapacitní jednotky |
| Zprostředkovaná připojení | 1 000 zahrnuté | 100 000 zahrnuté |
| Uchovávání zpráv | Jeden den v základu | Až sedmi dnů v ceně |
| Zachycování | Platba za hodinu | Zahrnuje |

## <a name="what-can-i-achieve-with-a-cluster"></a>Co mám dělat s clusterem?

Pro cluster služby Event Hubs kolik ingestování a streamování závisí na vaší výrobci, zákazníky, rychlost, jakou přijímat a zpracovávat a spoustu dalších věcí.

V následující tabulce jsou uvedeny výsledky srovnávacích testů, které bylo dosaženo během testování.

| Datová část obrazce | Příjemci | Šířka pásma příchozího přenosu dat| Příchozí zprávy | Šířka pásma pro výchozí přenos dat | Odchozí zprávy | Celkový počet jednotek propustnosti | Jednotek propustnosti, které na kapacitní jednotku |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Dávky 100x1KB | 2 | 400 MB/s | 400 000 zpráv za sekundu | 800 MB/s | 800 000 zpráv za sekundu | 400 jednotek propustnosti | 100 jednotek propustnosti | 
| Dávky 10x10KB | 2 | 666 MB/s | 66,600 zpráv za sekundu | 1.33 GB/s | 133,000 zpráv za sekundu | 666 jednotek propustnosti | 166 jednotek propustnosti |
| Dávky 6x32KB | 1 | 1,05 GB/s | 34 000 zpráv za sekundu | 1,05 GB/s | 34 000 zpráv za sekundu | 1 000 jednotek propustnosti | 250 jednotek propustnosti |

Při testování, byly použity následující kritéria:

- Byl použit clusteru služby Event Hubs úrovně Dedicated s čtyři jednotky kapacity.
- Centra událostí používá pro příjem bylo 200 oddíly.
- Který se ingestuje data byla přijata dvě aplikace příjemce. Dostali data ze všech oddílů.

## <a name="use-event-hubs-dedicated"></a>Použít vyhrazené služby Event Hubs

Chcete-li používat Event hubs úrovně Dedicated, [požádat podporu fakturace](https://ms.portal.azure.com/#create/Microsoft.Support) nebo zástupce společnosti Microsoft. Kapacitu můžete škálovat směrem nahoru nebo dolů v průběhu měsíce přizpůsobit svým potřebám přidáváním nebo odebíráním kapacitní jednotky. Produktový tým služby Event Hubs vám umožní získat flexibilní nasazení, který je pro vás nejvhodnější.

## <a name="next-steps"></a>Další postup

Kontaktujte zástupce Microsoftu nebo Microsoft Support získáte další informace o Event hubs úrovně Dedicated kapacity. Další informace o cenových úrovních služby Event Hubs, použijte následující odkazy:

- [Cenách služby Event hubs úrovně Dedicated](https://azure.microsoft.com/pricing/details/event-hubs/). Můžete také kontaktovat zástupce Microsoftu nebo Microsoft Support získáte další informace o Event hubs úrovně Dedicated kapacity.
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md) obsahuje informace o cenách a odpovědi na některé nejčastější dotazy o službě Event Hubs.
