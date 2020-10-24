---
title: Přehled vyhrazených Center událostí – Azure Event Hubs | Microsoft Docs
description: Tento článek obsahuje přehled vyhrazených Event Hubs Azure, které nabízí nasazení s jedním tenantům pro centra událostí.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 20b153c9093c96e7357a8e439b6655f1db80bd46
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516987"
---
# <a name="overview-of-event-hubs-dedicated"></a>Přehled Event Hubs úrovně Dedicated

*Clustery Event Hubs* nabízejí nasazení s jedním nájemcem pro zákazníky s nejnáročnějšími požadavky na streamování. Tato nabídka pro jednoho tenanta má garantovanou smlouvu SLA 99,99% a je dostupná jenom na naší vyhrazené cenové úrovni. Cluster Event Hubs může příchozí miliony událostí za sekundu se zaručenou kapacitou a latencí druhé sekundy. Obory názvů a centra událostí vytvořené v rámci vyhrazeného clusteru zahrnují všechny funkce standardní nabídky a další, ale bez omezení pro příchozí přenosy dat. Zahrnuje také oblíbenou funkci [zachycení Event Hubs](event-hubs-capture-overview.md) bez dalších poplatků, což vám umožní automaticky dávkovat a zasílat datové proudy do Azure Storage nebo Azure Data Lake. 

Clustery se zřídí a účtují podle **kapacitních jednotek (kapacitní jednotky)**, což je předem přidělené množství prostředků procesoru a paměti. Pro každý cluster můžete koupit 1, 2, 4, 8, 12, 16 nebo 20 kapacitní jednotky. To, kolik z nich můžete ingestovat a streamovat na jednu jednotku, závisí na nejrůznějších faktorech, jako je počet výrobců a uživatelů, tvar datové části a rychlost přenosu dat (další podrobnosti najdete v tématu výsledky srovnávacích testů níže). 

> [!NOTE]
> Všechny clustery Event Hubs jsou ve výchozím nastavení povolené Kafka a podporují koncové body Kafka, které můžou používat vaše stávající aplikace založené na Kafka. Kafka v clusteru nemá vliv na neKafkaé případy použití; neexistuje možnost nebo není nutné zakázat Kafka v clusteru.

## <a name="why-dedicated"></a>Proč vyhrazené?

Vyhrazená Event Hubs nabízí tři působivé výhody pro zákazníky, kteří potřebují kapacitu na úrovni podniku:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Zaručená kapacita pro zajištění vyššího výkonu u jednorázové architektury

Vyhrazený cluster garantuje kapacitu v plném měřítku a může zasílat až gigabajty streamovaná data s plně odolným úložištěm a druhou latencí, aby vyhovovala jakémukoli nárůstu provozu. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Úplný přístup k funkcím a exkluzivnímu přístupu 
Vyhrazená nabídka zahrnuje funkce, jako je například zachytávání bez dalších poplatků, a exkluzivní přístup k nadcházejícím funkcím, jako je Bring Your Own Key (BYOK). Tato služba také spravuje vyrovnávání zatížení, aktualizace operačního systému, opravy zabezpečení a vytváření oddílů pro zákazníka, takže můžete strávit méně času na údržbu infrastruktury a více času na vytváření klientských funkcí.  

#### <a name="cost-savings"></a>Úspora nákladů
U svazků s vysokou úrovní příchozího přenosu dat (>100 jednotek propustnosti) se náklady na cluster výrazně méně za hodinu, než si koupíte srovnatelné množství jednotek propustnosti ve standardní nabídce.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs úrovně Dedicated kvóty a omezení

V nabídce Event Hubs úrovně Dedicated se účtuje pevná měsíční cena s minimálním počtem 4 hodin využití. Vyhrazená úroveň nabízí všechny funkce plánu Standard, ale kapacitu a omezení na úrovni podniku pro zákazníky s náročnými úlohami. 

| Funkce | Standard | Vyhrazená |
| --- |:---:|:---:|
| Šířka pásma | 20 počet propustnosti (až 40 počet propustnosti) | 20 kapacitní jednotky |
| Obory názvů |  1 | 50 za CU |
| Event Hubs |  10 na obor názvů | 1000 na obor názvů |
| Události příchozího přenosu dat | Plaťte za milion událostí | Zahrnuje |
| Velikost zprávy | 1 000 000 bajtů | 1 000 000 bajtů |
| Oddíly | 32 na centrum událostí | 1024 na centrum událostí |
| Skupiny uživatelů | 20 na centrum událostí | Bez omezení na CU, 1000 na každé centrum událostí |
| Zprostředkovaná připojení | 1 000 zahrnutý, 5 000 max | 100 K zahrnutí a maximum |
| Uchovávání zpráv | 7 dní, 84 GB zahrnutých za TU | 90 dní, zahrnutých 10 TB za CU |
| Zachytávání | Platba za hodinu | Zahrnuje |

## <a name="how-to-onboard"></a>Postup zprovoznění

Prostředí pro samoobslužné [vytváření Event Hubsého clusteru](event-hubs-dedicated-cluster-create-portal.md) prostřednictvím [Azure Portal](https://aka.ms/eventhubsclusterquickstart) je teď ve verzi Preview. Pokud máte nějaké dotazy nebo potřebujete pomáhat s připojováním k Event Hubs úrovně Dedicated, obraťte se na [tým Event Hubs](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Nejčastější dotazy

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další podrobnosti o Event Hubs úrovně Dedicated, obraťte se na obchodního zástupce Microsoftu nebo podpora Microsoftu. Můžete také vytvořit cluster nebo získat další informace o Event Hubs cenové úrovně, a to návštěvou následujících odkazů:

- [Vytvoření clusteru Event Hubs pomocí Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs úrovně Dedicated ceny](https://azure.microsoft.com/pricing/details/event-hubs/). Můžete se také obrátit na prodejní zástupce Microsoftu nebo podpora Microsoftu a získat další podrobnosti o Event Hubs úrovně Dedicated kapacitě.
- [Event Hubs Nejčastější dotazy](event-hubs-faq.md) obsahují informace o cenách a odpovědi na nejčastější dotazy týkající se Event Hubs.
