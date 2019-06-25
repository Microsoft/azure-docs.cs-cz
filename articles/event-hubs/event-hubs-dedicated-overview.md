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
ms.openlocfilehash: 52e092e6e48f004656860cb5d078e780039584ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730244"
---
# <a name="overview-of-event-hubs-dedicated"></a>Přehled služby Event Hubs Dedicated

*Clustery centra událostí* nabízejí nasazení jednoho tenanta pro zákazníky s nejnáročnější požadavky na datových proudů. Tuto nabídku jednoho tenanta má zaručené smlouvy SLA 99,99 % a jsou dostupné jenom na naše Dedicated cenovou úroveň. Cluster služby Event Hubs můžete příchozího přenosu dat milionů událostí za sekundu s sekunda latence a zaručené kapacity. Obory názvů a centra událostí v rámci vyhrazeného clusteru zahrnují všechny funkce standardní nabídky a další, ale žádné neomezené příchozí. Zahrnuje také oblíbené [Event Hubs Capture](event-hubs-capture-overview.md) funkce bez dalších poplatků, abyste mohli automaticky služby batch a protokolu datových proudů do služby Azure Storage nebo Azure Data Lake. 

Clustery jsou zřízené a účtuje se podle **kapacitních jednotek (CUs)** , předběžně přidělit objem prostředků procesoru a paměti. Si můžete koupit 1, 2, 4, 8, 12, 16 nebo 20 kapacitní jednotky pro každý cluster. Kolik může ingestovat a streamování na kapacitní jednotku závisí na různých faktorech, jako je počet producenti a spotřebitelé, datová část obrazce, výchozí přenos dat přenosové rychlosti (výsledky srovnávacích testů pod další podrobnosti najdete v článku). 

> [!NOTE]
> Všechny služby Event Hubs clustery Kafka povolenými ve výchozím nastavení a podporují Kafka koncové body, které lze použít ve stávajících Kafka na základě aplikací. S povolené Kafka v clusteru nemá vliv na vaše případy použití jiných Kafka; neexistuje žádná možnost nebo třeba zakázat Kafka v clusteru.

## <a name="why-dedicated"></a>Proč vyhrazená?

Vyhrazená Služba Event Hubs nabízí tři poutavé výhody pro zákazníky, kteří potřebují podnikové úrovni kapacity:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Jedním tenantů zaručuje kapacity pro zajištění lepšího výkonu

Vyhrazený cluster zaručuje kapacity v plném rozsahu a můžete příchozí přenos dat až po gigabajtech streamovaných dat s plně trvalého úložiště a sekunda latencí tak, aby vyhovovaly žádné zvyšování provoz. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Zahrnuté a výhradní přístup k funkcím 
Vyhrazené nabídka zahrnuje funkcí, jako jsou zachytávání v žádné další poplatky, stejně jako výhradní přístup k chystaných funkcí jako BYOK. Služba také spravuje Vyrovnávání zatížení, operační systém aktualizací, oprav zabezpečení a dělení zákazníka, takže strávíte méně času na údržbu infrastruktury a získejte víc času na tvorbu funkcí na straně klienta.  

#### <a name="cost-savings"></a>Úspora nákladů
Na svazcích vysokou příchozího přenosu dat (> 100 jednotek propustnosti), cluster náklady výrazně menší za hodinu zakoupení srovnatelné množství jednotek propustnosti ve standardní nabídce.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Vyhrazená Služba Event Hubs kvóty a omezení

Event hubs úrovně Dedicated nabídka se účtuje za pevnou měsíční cenu s minimálně 4 hodin využití. Vyhrazené úroveň nabízí všechny funkce plánu Standard, ale s enterprise škálování kapacity a omezení pro zákazníky s náročné úlohy. 

| Funkce | Standard | Vyhrazený |
| --- |:---:|:---:|
| Šířka pásma | 20 jednotek propustnosti (až 40 jednotek propustnosti, které) | 20 kapacitní jednotky |
| Obory názvů |  1 | 50 na kapacitní jednotku |
| Event Hubs |  10 za obor názvů | 1 000 na obor názvů |
| Události příchozího přenosu dat | Platba za milion událostí | Zahrnuje |
| Velikost zprávy | 1 milion bajtů | 1 milion bajtů |
| Oddíly | 40 na obor názvů | 2000 na kapacitní jednotku |
| Skupiny příjemců | 20 za centra událostí | Bez omezení na kapacitní jednotku, 1 000 na Centrum událostí |
| Zprostředkovaná připojení | zahrnuté 1 000, maximálně 5 000 | 100 tisíc v základu a maximální počet |
| Uchovávání zpráv | 7 dní, 84 GB zahrnuto na jednotek Propustnosti | 90 dnů, 10 TB, které jsou zahrnuty na kapacitní jednotku |
| Zachycování | Platba za hodinu | Zahrnuje |

## <a name="how-to-onboard"></a>Jak se zapojit

Samoobslužné prostředí pro [vytvoření clusteru služby Event Hubs](event-hubs-dedicated-cluster-create-portal.md) prostřednictvím [webu Azure Portal](https://aka.ms/eventhubsclusterquickstart) je teď ve verzi Preview. Pokud máte nějaké dotazy nebo potřebujete nápovědu připojování k Event hubs úrovně Dedicated, obraťte se prosím [tým služby Event Hubs](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Nejčastější dotazy

#### <a name="what-can-i-achieve-with-a-cluster"></a>Co mám dělat s clusterem?

Pro cluster služby Event Hubs kolik ingestování a streamování závisí na různých faktorech, jako je například vaše výrobci, příjemci, rychlost, jakou jsou ingestování a zpracování a spoustu dalších věcí. 

Následující tabulka ukazuje výsledky srovnávacích testů jsme dosáhli během naše testování:

| Datová část obrazce | Příjemci | Šířka pásma příchozího přenosu dat| Příchozí zprávy | Šířka pásma pro výchozí přenos dat | Odchozí zprávy | Celkový počet jednotek propustnosti | Jednotek propustnosti, které na kapacitní jednotku |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Dávky 100x1KB | 2 | 400 MB/s | 400 tisíc zpráv za sekundu | 800 MB/s | 800 tisíc zpráv za sekundu | 400 jednotek propustnosti | 100 jednotek propustnosti | 
| Dávky 10x10KB | 2 | 666 MB/s | 66.6 tisíc zpráv za sekundu | 1.33 GB/s | 133 tisíc zpráv za sekundu | 666 jednotek propustnosti | 166 jednotek propustnosti |
| Dávky 6x32KB | 1 | 1,05 GB/s | 34 tisíc zpráv za sekundu | 1,05 GB/s | 34 tisíc zpráv za sekundu | 1000 jednotek propustnosti | 250 jednotek propustnosti |

Při testování, použil následující kritéria:

- Cluster služby Event Hubs úrovně dedicated s čtyři kapacitních jednotek (CUs) byl použit. 
- Centra událostí používá pro příjem bylo 200 oddíly. 
- Který se ingestuje data byla přijata dvě aplikace příjemce přijímají ze všech oddílů.

#### <a name="can-i-scale-updown-my-cluster"></a>Můžete škálovat nahoru/dolů clusteru?

Po vytvoření clusterů se účtují po dobu minimálně 4 hodin využití. Ve verzi Preview samoobslužné prostředí, můžete odeslat [žádost o podporu](https://ms.portal.azure.com/#create/Microsoft.Support) týmu služby Event Hubs v rámci *technické > kvóty > žádost o vertikálně navýšit kapacitu nebo škálování dolů vyhrazeného clusteru* škálování clusteru směrem nahoru nebo dolů. To může trvat až 7 dnů, dokončete žádost o vertikálně snížit kapacitu vašeho clusteru. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Jak fungují Geo-DR pomocí clusteru?

Je to možné geo pár oboru názvů v cluster úrovně Dedicated pomocí jiného oboru názvů v rámci cluster úrovně Dedicated. Doporučujeme není, párování oboru názvů úrovně Dedicated s oborem názvů do našich standardní nabídky, protože limit propustnosti bude kompatibilní se budou účtovat chyby. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Můžete migrovat Moje standardních názvových prostorů patří cluster úrovně Dedicated?
Nepodporujeme aktuálně procesu automatické migrace pro migraci event hubs data do Dedicated, jeden z obor názvů Standard. 

## <a name="next-steps"></a>Další postup

Kontaktujte zástupce Microsoftu nebo Microsoft Support a získejte další podrobnosti o Event hubs úrovně Dedicated. Můžete také vytvořit cluster nebo Další informace o službě Event Hubs cenové úrovně návštěvou následujících odkazů:

- [Vytvoření clusteru služby Event Hubs pomocí webu Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Cenách služby Event hubs úrovně Dedicated](https://azure.microsoft.com/pricing/details/event-hubs/). Můžete také kontaktovat zástupce Microsoftu nebo Microsoft Support a získejte další podrobnosti o kapacita vyhrazená Služba Event Hubs.
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md) obsahuje informace o cenách a odpovědi na některé nejčastější dotazy o službě Event Hubs.
