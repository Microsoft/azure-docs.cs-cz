---
title: Přehled vyhrazených Center událostí – Azure Event Hubs | Microsoft Docs
description: Tento článek obsahuje přehled vyhrazených Event Hubs Azure, které nabízí nasazení s jedním tenantům pro centra událostí.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 721acf354c7d14c1362b4f760982af37d59115f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715611"
---
# <a name="overview-of-event-hubs-dedicated"></a>Přehled Event Hubs úrovně Dedicated

*Clustery Event Hubs* nabízejí nasazení s jedním nájemcem pro zákazníky s nejnáročnějšími požadavky na streamování. Tato nabídka pro jednoho tenanta má garantovanou smlouvu SLA 99,99% a je dostupná jenom na naší vyhrazené cenové úrovni. Cluster Event Hubs může příchozí miliony událostí za sekundu se zaručenou kapacitou a latencí druhé sekundy. Obory názvů a centra událostí vytvořené v rámci vyhrazeného clusteru zahrnují všechny funkce standardní nabídky a další, ale bez omezení pro příchozí přenosy dat. Zahrnuje také oblíbenou funkci [Event Hubs Capture](event-hubs-capture-overview.md) bez dalších poplatků. Tato funkce umožňuje automaticky dávkovat a zaznamenávat datové proudy Azure Storage nebo Azure Data Lake. 

Clustery se zřídí a účtují podle **kapacitních jednotek (kapacitní jednotky)**, což je předem přidělené množství prostředků procesoru a paměti. Pro každý cluster můžete koupit 1, 2, 4, 8, 12, 16 nebo 20 kapacitní jednotky. Kolik možností ingestování a streamování za sekundu závisí na nejrůznějších faktorech, jako jsou následující: 

- Počet výrobců a příjemců
- Tvar datové části
- Míra odchozích dat

> [!NOTE]
> Všechny clustery Event Hubs jsou ve výchozím nastavení povolené Kafka a podporují koncové body Kafka, které můžou používat vaše stávající aplikace založené na Kafka. Kafka v clusteru nemá vliv na neKafkaé případy použití; neexistuje možnost nebo není nutné zakázat Kafka v clusteru.

## <a name="why-dedicated"></a>Proč vyhrazené?

Vyhrazená Event Hubs nabízí tři působivé výhody pro zákazníky, kteří potřebují kapacitu na úrovni podniku:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Zaručená kapacita pro zajištění vyššího výkonu u jednorázové architektury

Vyhrazený cluster zaručuje kapacitu v plném rozsahu. Může zasílat až gigabajty streamování dat pomocí plně odolného úložiště a čekací latence, která bude vyhovovat jakémukoli nárůstu provozu. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Úplný přístup k funkcím a exkluzivnímu přístupu 
Vyhrazená nabídka zahrnuje funkce, jako je například zachytávání bez dalších poplatků, a exkluzivní přístup k nadcházejícím funkcím, jako je Bring Your Own Key (BYOK). Tato služba také spravuje vyrovnávání zatížení, aktualizace operačního systému, opravy zabezpečení a vytváření oddílů. Takže můžete strávit méně času při údržbě infrastruktury a další čas při vytváření funkcí na straně klienta.  

#### <a name="cost-savings"></a>Úspora nákladů
U svazků s vysokou úrovní příchozího přenosu dat (>100 jednotek propustnosti) se náklady na cluster výrazně méně za hodinu, než si koupíte srovnatelné množství jednotek propustnosti ve standardní nabídce.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs úrovně Dedicated kvóty a omezení

V nabídce Event Hubs úrovně Dedicated se účtuje pevná měsíční cena s minimálním počtem 4 hodin využití. Vyhrazená úroveň nabízí všechny funkce standardního plánu, ale kapacitu a omezení na podnikové úrovni pro zákazníky s náročnými úlohami. 

| Funkce | Standard | Vyhrazená |
| --- |:---|:---|
| Šířka pásma | 20 počet propustnosti (až 40 počet propustnosti) | 20 kapacitní jednotky |
| Obory názvů |  1 | 50 za CU |
| Event Hubs |  10 na obor názvů | 1000 na obor názvů |
| Události příchozího přenosu dat | Plaťte za milion událostí | Zahrnuje |
| Velikost zprávy | 1 000 000 bajtů | 1 000 000 bajtů |
| Oddíly | 32 na centrum událostí | 1024 na centrum událostí<br/>2000 za CU |
| Skupiny uživatelů | 20 na centrum událostí | Bez omezení na CU, 1000 na každé centrum událostí |
| Zprostředkovaná připojení | 1 000 zahrnutý, 5 000 max | 100 K zahrnutí a maximum |
| [Uchovávání událostí](event-hubs-features.md#event-retention) | 7 dní, 84 GB zahrnutých za TU | 90 dní, zahrnutých 10 TB za CU |
| Zachytávání | Platba za hodinu | Zahrnuje |

Další kvóty a omezení najdete v tématu [Event Hubs kvót a omezení](event-hubs-quotas.md) .

## <a name="how-to-onboard"></a>Postup zprovoznění

Prostředí pro samoobslužné [vytváření Event Hubsého clusteru](event-hubs-dedicated-cluster-create-portal.md) prostřednictvím [Azure Portal](https://aka.ms/eventhubsclusterquickstart) je teď ve verzi Preview. Pokud máte nějaké dotazy nebo potřebujete pomáhat s připojováním k Event Hubs úrovně Dedicated, obraťte se na [tým Event Hubs](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Nejčastější dotazy

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další podrobnosti o Event Hubs úrovně Dedicated, obraťte se na obchodního zástupce Microsoftu nebo podpora Microsoftu. Můžete také vytvořit cluster nebo získat další informace o Event Hubs cenové úrovně, a to návštěvou následujících odkazů:

- [Vytvoření clusteru Event Hubs pomocí Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs úrovně Dedicated ceny](https://azure.microsoft.com/pricing/details/event-hubs/). Můžete se také obrátit na prodejní zástupce Microsoftu nebo podpora Microsoftu a získat další podrobnosti o Event Hubs úrovně Dedicated kapacitě.
- [Event Hubs Nejčastější dotazy](event-hubs-faq.md) obsahují informace o cenách a odpovědi na nejčastější dotazy týkající se Event Hubs.
