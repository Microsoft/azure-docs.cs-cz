---
title: Přehled vyhrazených Center událostí – Azure Event Hubs | Microsoft Docs
description: Tento článek obsahuje přehled vyhrazených Event Hubs Azure, které nabízí nasazení s jedním tenantům pro centra událostí.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 70061b5dc4fe72c9fd2fd60dd8c67da31b1d1e6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85322448"
---
# <a name="overview-of-event-hubs-dedicated"></a>Přehled Event Hubs úrovně Dedicated

*Clustery Event Hubs* nabízejí nasazení s jedním nájemcem pro zákazníky s nejnáročnějšími požadavky na streamování. Tato nabídka pro jednoho tenanta má garantovanou smlouvu SLA 99,99% a je dostupná jenom na naší vyhrazené cenové úrovni. Cluster Event Hubs může příchozí miliony událostí za sekundu se zaručenou kapacitou a latencí druhé sekundy. Obory názvů a centra událostí vytvořené v rámci vyhrazeného clusteru zahrnují všechny funkce standardní nabídky a další, ale bez omezení pro příchozí přenosy dat. Zahrnuje také oblíbenou funkci [zachycení Event Hubs](event-hubs-capture-overview.md) bez dalších poplatků, což vám umožní automaticky dávkovat a zasílat datové proudy do Azure Storage nebo Azure Data Lake. 

Clustery se zřídí a účtují podle **kapacitních jednotek (kapacitní jednotky)**, což je předem přidělené množství prostředků procesoru a paměti. Pro každý cluster můžete koupit 1, 2, 4, 8, 12, 16 nebo 20 kapacitní jednotky. To, kolik z nich můžete ingestovat a streamovat na jednu jednotku, závisí na nejrůznějších faktorech, jako je počet výrobců a uživatelů, tvar datové části a rychlost přenosu dat (další podrobnosti najdete v tématu výsledky srovnávacích testů níže). 

> [!NOTE]
> Všechny clustery Event Hubs jsou ve výchozím nastavení povolené Kafka a podporují koncové body Kafka, které můžou používat vaše stávající aplikace založené na Kafka. Kafka v clusteru nemá vliv na neKafkaé případy použití; neexistuje možnost nebo není nutné zakázat Kafka v clusteru.

## <a name="why-dedicated"></a>Proč vyhrazené?

Vyhrazená Event Hubs nabízí tři působivé výhody pro zákazníky, kteří potřebují kapacitu na úrovni podniku:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Zaručená kapacita pro zajištění vyššího výkonu u jednorázové architektury

Vyhrazený cluster zaručuje kapacitu v plném měřítku a může zasílat až gigabajty streamovaná data s plně odolným úložištěm a latencí druhé sekundy tak, aby vyhovovala jakémukoli nárůstu provozu. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Úplný přístup k funkcím a exkluzivnímu přístupu 
Vyhrazená nabídka zahrnuje funkce, jako je například zachytávání bez dalších poplatků, jakož i exkluzivní přístup k nadcházejícím funkcím, jako je Bring Your Own Key (BYOK). Tato služba také spravuje vyrovnávání zatížení, aktualizace operačního systému, opravy zabezpečení a vytváření oddílů pro zákazníka, takže můžete strávit méně času na údržbu infrastruktury a další čas při sestavování funkcí na straně klienta.  

#### <a name="cost-savings"></a>Úspora nákladů
Na svazcích s vysokou úrovní příchozího přenosu dat (>100 počet propustnosti) se náklady na cluster výrazně méně za hodinu, než si koupíte srovnatelné množství jednotek propustnosti ve standardní nabídce.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs úrovně Dedicated kvóty a omezení

V nabídce Event Hubs úrovně Dedicated se účtuje pevná měsíční cena s minimálním počtem 4 hodin využití. Vyhrazená úroveň nabízí všechny funkce plánu Standard, ale kapacitu a omezení na úrovni podniku pro zákazníky s náročnými úlohami. 

| Příznak | Standard | Vyhrazená |
| --- |:---:|:---:|
| Šířka pásma | 20 počet propustnosti (až 40 počet propustnosti) | 20 kapacitní jednotky |
| Jmenné prostory |  1 | 50 za CU |
| Event Hubs |  10 na obor názvů | 1000 na obor názvů |
| Události příchozího přenosu dat | Plaťte za milion událostí | Zahrnuje |
| Velikost zprávy | 1 000 000 bajtů | 1 000 000 bajtů |
| Oddíly | 32 na centrum událostí | 1024 na centrum událostí |
| Skupiny uživatelů | 20 na centrum událostí | Bez omezení na CU, 1000 na každé centrum událostí |
| Zprostředkovaná připojení | 1 000 zahrnutý, 5 000 max | 100 K zahrnutí a maximum |
| Uchovávání zpráv | 7 dní, 84 GB zahrnutých za TU | 90 dní, zahrnutých 10 TB za CU |
| Zachytávání | Platba za hodinu | Zahrnuje |

## <a name="how-to-onboard"></a>Postup zprovoznění

Samoobslužné prostředí pro [vytváření Event Hubsho clusteru](event-hubs-dedicated-cluster-create-portal.md) prostřednictvím webu [Azure Portal](https://aka.ms/eventhubsclusterquickstart) je teď ve verzi Preview. Pokud máte nějaké dotazy nebo potřebujete pomáhat s připojováním k Event Hubs úrovně Dedicated, obraťte se prosím na [tým Event Hubs](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Nejčastější dotazy

#### <a name="what-can-i-achieve-with-a-cluster"></a>Co můžu s clusterem dosáhnout?

V případě Event Hubs clusteru závisí množství, které můžete ingestovat a streamování, na různých faktorech, jako jsou vaše producenti, spotřebitelé, rychlost přijímání a zpracování a mnohem víc. 

V následující tabulce jsou uvedeny výsledky srovnávacích testů, které jsme dosáhli během testování:

| Tvar datové části | Příjemců | Šířka pásma příchozího přenosu dat| Příchozí zprávy | Šířka pásma pro výstup | Odchozí zprávy | Celkem počet propustnosti | Počet propustnosti na CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Dávky 100x1KB | 2 | 400 MB/s | 400 tisíc zprávy za sekundu | 800 MB/s | 800k zprávy za sekundu | 400 počet propustnosti | 100 počet propustnosti | 
| Dávky 10x10KB | 2 | 666 MB/s | zprávy 66.6 k/s | 1,33 GB/s | 133k zprávy za sekundu | 666 počet propustnosti | 166 počet propustnosti |
| Dávky 6x32KB | 1 | 1,05 GB/s | 34k zprávy za sekundu | 1,05 GB/s | 34k zprávy za sekundu | 1000 počet propustnosti | 250 počet propustnosti |

Při testování se použila následující kritéria:

- Použil se cluster Event Hubs s vyhrazenou vrstvou, který má čtyři jednotky kapacity (kapacitní jednotky). 
- Centrum událostí používané pro ingestování mělo 200 oddílů. 
- Data, která byla ingestovaná, obdrží dvě aplikace přijímače, které přijímají ze všech oddílů.

#### <a name="can-i-scale-updown-my-cluster"></a>Můžu svůj cluster škálovat nahoru/dolů?

Po vytvoření se clustery účtují minimálně o 4 hodiny využití. Ve verzi Preview prostředí samoobslužného řízení můžete odeslat [žádost o podporu](https://ms.portal.azure.com/#create/Microsoft.Support) týmu Event Hubs v části *Technická > kvóta > žádosti o horizontální navýšení nebo snížení kapacity vyhrazeného clusteru* pro horizontální navýšení nebo snížení kapacity clusteru. Dokončení žádosti o horizontální navýšení kapacity clusteru může trvat až 7 dní. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Jak budou geografické DRy fungovat s clusterem?

Obor názvů můžete geograficky spárovat v rámci clusteru vyhrazené úrovně s jiným oborem názvů v rámci clusteru s vyhrazenou vrstvou. V naší standardní nabídce nedoporučujeme spárovat obor názvů vyhrazené úrovně s oborem názvů, protože limit propustnosti bude nekompatibilní a výsledkem bude chyba. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Můžu migrovat standardní obory názvů tak, aby patřily do clusteru vyhrazené úrovně?
V současné době nepodporujeme proces automatizované migrace pro migraci dat centra událostí ze standardního oboru názvů na vyhrazený. 

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další podrobnosti o Event Hubs úrovně Dedicated, obraťte se na obchodního zástupce Microsoftu nebo podpora Microsoftu. Můžete také vytvořit cluster nebo získat další informace o Event Hubs cenové úrovně, a to návštěvou následujících odkazů:

- [Vytvoření clusteru Event Hubs prostřednictvím webu Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs úrovně Dedicated ceny](https://azure.microsoft.com/pricing/details/event-hubs/). Můžete se také obrátit na prodejní zástupce Microsoftu nebo podpora Microsoftu a získat další podrobnosti o Event Hubs úrovně Dedicated kapacitě.
- [Event Hubs Nejčastější dotazy](event-hubs-faq.md) obsahují informace o cenách a odpovědi na nejčastější dotazy týkající se Event Hubs.
