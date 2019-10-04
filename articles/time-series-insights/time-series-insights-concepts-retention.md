---
title: Pochopení uchovávání dat ve vašem Azure Time Series Insights prostředí | Microsoft Docs
description: Tento článek popisuje dvě nastavení, která řídí uchovávání dat ve vašem Azure Time Series Insightsovém prostředí.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 5799974581ba74d3265f0a5a66f9b081ded9f800
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948212"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Pochopení uchovávání dat v Azure Time Series Insights

Tento článek popisuje dvě nastavení, která mají vliv na uchovávání dat ve vašem Azure Time Series Insightsovém prostředí.

## <a name="video"></a>Obrazový

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Následující video shrnuje Time Series Insights uchovávání dat a jejich plánování.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Každé prostředí Azure Time Series má nastavení, které řídí **dobu uchovávání dat**. Hodnota zahrnuje 1 až 400 dní. Data se odstraňují na základě kapacity úložiště v prostředí nebo doby uchování, podle toho, co nastane dřív.

Kromě toho má prostředí Azure Time Series nastavení **chování při překročení limitu úložiště** . Řídí chování vstupu a vyprázdnění při dosažení maximální kapacity prostředí. Při konfiguraci si můžete vybrat ze dvou příznaků:

- **Vyprázdnit stará data** (výchozí)  
- **Pozastavit příchozí přenos dat**

> [!NOTE]
> Ve výchozím nastavení platí, že při vytváření nového prostředí se uchování nakonfiguruje tak, aby **vymazala stará data**. Toto nastavení se dá po vytvoření pomocí Azure Portal přepínat podle potřeby, a to na stránce **konfigurace** Time Series Insights prostředí.

Informace o přepnutí chování uchovávání najdete [v Time Series Insights konfigurace uchovávání v](time-series-insights-how-to-configure-retention.md)nástroji.

Porovnání chování uchovávání dat:

## <a name="purge-old-data"></a>Vyprázdnit stará data

- Toto chování je výchozím chováním pro Time Series Insights prostředí.  
- Toto chování je preferováno, když uživatelé chtějí vždy zobrazit nejnovější *data* ve svém Time Series InsightSM prostředí.
- Toto chování *vymaže* data po dosažení limitů prostředí (doba uchování, velikost nebo počet, podle toho, co nastane dřív). Doba uchování je ve výchozím nastavení nastavená na 30 dní.
- Nejstarší ingestovaná data se nejprve vyprázdní (přístup FIFO).

### <a name="example-one"></a>Příklad jedné

Vezměte v úvahu ukázkové prostředí s chováním uchovávání **a vyprázdnit stará data**:

**Doba uchovávání dat** je nastavená na 400 dní. **Kapacita** je nastavená na jednotku S1, která obsahuje 30 GB celkové kapacity.   Vybereme, že příchozí data se za průměrně nashromáždí do 500 MB každého dne. Toto prostředí může uchovávat jenom 60 dní, které mají za sekundu množství příchozích dat, protože maximální kapacita dosáhne 60 dnů. Příchozí data se sčítají takto: 500 MB každý den × 60 dní = 30 GB.

V 61stém dni se v prostředí zobrazují data o aktuálnosti, ale vyprázdní nejstarší data starší než 60 dní. Vyprázdnit vytvoří místo pro nové streamování dat v, aby se nová data mohla dál prozkoumat. Pokud si uživatel přeje zachovat data déle, může zvětšit velikost prostředí přidáním dalších jednotek nebo může doručovat méně dat.  

### <a name="example-two"></a>Příklad 2

Vezměte v úvahu i prostředí, ve kterém se nakonfigurují i chování uchovávání **, a vyprázdnit stará data**. V tomto příkladu je **Doba uchovávání dat** nastavená na nižší hodnotu 180 dní. **Kapacita** je nastavená na jednotku S1, která obsahuje 30 GB celkové kapacity. Aby bylo možné ukládat data po celých 180 dní, denní příchozí přenos dat nemůže překročit 0,166 GB (166 MB) za den.  

Když se denní rychlost příchozího přenosu v tomto prostředí překročí 0,166 GB za den, data se nedají uložit na 180 dnů, protože se některá data vyprázdní. Během zaneprázdněného časového rámce zvažte stejné prostředí. Předpokládat, že se míra příchozího přenosu v prostředí může zvýšit na průměr 0,189 GB za den. V tomto časovém rámci je zachováno přibližně 158 dní dat (30 GB/0.189 = 158,73 dnů uchovávání). Tato doba je kratší než časový rámec požadované doby uchovávání dat.

## <a name="pause-ingress"></a>Pozastavit příchozí přenos dat

- Nastavení **pozastavit** příjem dat je navrženo tak, aby se zajistilo, že se data neodstraní, pokud jsou dosažena omezení velikosti a počtu před jejich dobou uchování.  
- **Pozastaví** příchozí přenos dat, aby uživatelé zvýšili kapacitu svého prostředí předtím, než se data vyprázdní kvůli porušení doby uchování.
- Pomáhá chránit před ztrátou dat, ale může vytvořit příležitost pro ztrátu vašich nejnovějších dat, pokud se příchozí přenos dat pozastaví mimo dobu uchování vašeho zdroje událostí.
- Jakmile však dosáhnete maximální kapacity prostředí, prostředí pozastaví příchozí přenos dat, dokud neproběhne následující další akce:

   - Maximální kapacitu prostředí můžete zvýšit tak, aby se přidaly další jednotky škálování, jak je popsáno v tématu [Jak škálovat Time Series Insights prostředí](time-series-insights-how-to-scale-your-environment.md).
   - Doba uchovávání dat je dosažená a data se vyprázdní a přinášejí prostředí pod jeho maximální kapacitu.

### <a name="example-three"></a>Příklad tři

Zvažte prostředí s chováním uchovávání dat nakonfigurovaným pro **pozastavení**příchozího přenosu dat. V tomto příkladu je **Doba uchovávání dat** nakonfigurovaná na 60 dní. **Kapacita** je nastavená na tři (3) jednotky S1. Předpokládejme, že toto prostředí má každý den příchozí přenos dat na 2 GB. V tomto prostředí se příchozí přenos dat pozastaví, jakmile se dosáhne maximální kapacity.

V tuto chvíli prostředí zobrazuje stejnou datovou sadu, dokud se neobnoví nebo dokud nebude zapnuté příchozí **přenosy** (což by vymazalo starší data, aby uvolnila místo pro nová data).

Po obnovení příchozího přenosu dat:

- Toky dat v pořadí, ve kterém byly přijaty zdrojem událostí
- Události jsou indexovány na základě časového razítka, pokud jste nepřekročili zásady uchovávání informací ve zdroji událostí. Další informace o konfiguraci uchovávání informací o zdrojích událostí [Event Hubs Nejčastější dotazy](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Měli byste nastavit výstrahy, které vám poskytnou informace, které vám pomůžou zabránit pozastavenému přenosu. Může dojít ke ztrátě dat, protože výchozí doba uchování je 1 den pro zdroje událostí Azure. Proto když je příchozí příjem pozastaven, pravděpodobně ztratíte nejaktuálnější data, pokud se neprovede žádná další akce. Abyste se vyhnuli potenciálním ztrátám dat, je nutné zvýšit kapacitu nebo přepnout chování, aby se **vymazala stará data** .

V ovlivněných Event Hubs zvažte úpravu vlastnosti **uchovávání zpráv** , aby se minimalizovala ztráta dat, když dojde k pozastavení vstupu v Time Series Insights.

[@no__t – uchovávání zpráv centra 1Event.](media/time-series-insights-contepts-retention/event-hub-retention.png)](media/time-series-insights-contepts-retention/event-hub-retention.png#lightbox)

Pokud nejsou u zdroje událostí nakonfigurované žádné vlastnosti (`timeStampPropertyName`), Time Series Insights ve výchozím nastavení časové razítko doručení do centra událostí jako osu X. Pokud je `timeStampPropertyName` nakonfigurovaná tak, aby byla něco jiného, prostředí vyhledá nakonfigurované `timeStampPropertyName` v datovém paketu, když se události analyzují.

Pokud potřebujete škálovat prostředí tak, aby vyhovovalo další kapacitě nebo aby se zvýšila doba uchovávání, přečtěte si téma [Jak škálovat Time Series Insights prostředí](time-series-insights-how-to-scale-your-environment.md) , kde najdete další informace.  

## <a name="next-steps"></a>Další kroky

- Informace o konfiguraci a změně nastavení uchovávání dat najdete [v Time Series Insights konfigurace uchovávání v](time-series-insights-how-to-configure-retention.md)nástroji.
