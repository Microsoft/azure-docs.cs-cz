---
title: Pochopení uchovávání dat ve vašem prostředí – Azure Time Series Insight | Microsoft Docs
description: Tento článek popisuje dvě nastavení, která řídí uchovávání dat ve vašem Azure Time Series Insightsovém prostředí.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 4f236679d0662df852581a6a8408ed6bc0d4e3fe
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535685"
---
# <a name="understand-data-retention-in-azure-time-series-insights-gen1"></a>Pochopení uchovávání dat v Azure Time Series Insights Gen1

> [!CAUTION]
> Toto je Gen1 článek.

Tento článek popisuje dvě primární nastavení, která mají vliv na uchovávání dat ve vašem Azure Time Series Insightsovém prostředí.

## <a name="video"></a>Video

### <a name="the-following-video-summarizes-azure-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Následující video shrnuje Azure Time Series Insights uchovávání dat a jejich plánování.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Každé z vašich Azure Time Series Insightsch prostředí má nastavení, které řídí **dobu uchovávání dat**. Hodnota zahrnuje 1 až 400 dní. Data se odstraňují na základě kapacity úložiště v prostředí nebo doby uchování, podle toho, co nastane dřív.

Kromě toho má Azure Time Series Insights prostředí nastavené **chování při překročení limitu úložiště** . Řídí chování vstupu a vyprázdnění při dosažení maximální kapacity prostředí. Při konfiguraci si můžete vybrat ze dvou příznaků:

- **Vyprázdnit stará data** (výchozí)  
- **Pozastavit příchozí přenos dat**

> [!NOTE]
> Ve výchozím nastavení platí, že při vytváření nového prostředí se uchování nakonfiguruje tak, aby **vymazala stará data**. Toto nastavení se dá po vytvoření pomocí Azure Portal přepínat podle potřeby, a to na stránce **konfigurace** Azure Time Series Insights prostředí.
>
> - Informace o tom, jak nakonfigurovat zásady uchovávání informací, najdete [v článku Konfigurace uchovávání v Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

Obě zásady uchovávání dat jsou popsané podrobněji.

## <a name="purge-old-data"></a>Vyprázdnit stará data

- **Vyprázdnit stará data** je výchozím nastavením pro Azure Time Series Insights prostředí.  
- **Vyprázdnit stará data** jsou upřednostňovaná, když uživatelé chtějí mít ve svém Azure Time Series Insights prostředí vždy nejnovější *data* .
- Nastavení **vyprázdnění starých dat** *vymaže* data po dosažení limitů prostředí (doba uchování, velikost nebo počet, podle toho, co nastane dřív). Doba uchování je ve výchozím nastavení nastavená na 30 dní.
- Nejstarší ingestovaná data se vyprázdní jako první (přístup "první v prvním)".

### <a name="example-one"></a>Příklad jedné

Vezměte v úvahu ukázkové prostředí s chováním uchovávání **a vyprázdnit stará data**:

**Doba uchovávání dat** je nastavená na 400 dní. **Kapacita** je nastavená na jednotku S1, která obsahuje 30 GB celkové kapacity. Vybereme, že příchozí data se za průměrně nashromáždí do 500 MB každého dne. Toto prostředí může uchovávat jenom 60 dní, které mají za sekundu množství příchozích dat, protože maximální kapacita dosáhne 60 dnů. Příchozí data se sčítají takto: 500 MB každý den × 60 dní = 30 GB.

V 61stém dni se v prostředí zobrazují data o aktuálnosti, ale vyprázdní nejstarší data starší než 60 dní. Vyprázdnit vytvoří místo pro nové streamování dat v, aby se nová data mohla dál prozkoumat. Pokud si uživatel přeje zachovat data déle, může zvětšit velikost prostředí přidáním dalších jednotek nebo může doručovat méně dat.  

### <a name="example-two"></a>Příklad 2

Vezměte v úvahu i prostředí, ve kterém se nakonfigurují i chování uchovávání **, a vyprázdnit stará data**. V tomto příkladu je **Doba uchovávání dat** nastavená na nižší hodnotu 180 dní. **Kapacita** je nastavená na jednotku S1, která obsahuje 30 GB celkové kapacity. Aby bylo možné ukládat data po celých 180 dní, denní příchozí přenos dat nemůže překročit 0,166 GB (166 MB) za den.  

Když se denní rychlost příchozího přenosu v tomto prostředí překročí 0,166 GB za den, data se nedají uložit na 180 dnů, protože se některá data vyprázdní. Během zaneprázdněného časového rámce zvažte stejné prostředí. Předpokládat, že se míra příchozího přenosu v prostředí může zvýšit na průměr 0,189 GB za den. V tomto časovém rámci je zachováno přibližně 158 dní dat (30 GB/0.189 = 158,73 dnů uchovávání). Tato doba je kratší než časový rámec požadované doby uchovávání dat.

## <a name="pause-ingress"></a>Pozastavit příchozí přenos dat

- Nastavení **pozastavit** příjem dat je navrženo tak, aby se zajistilo, že se data neodstraní, pokud jsou dosažena omezení velikosti a počtu před jejich dobou uchování.  
- **Pozastaví** příchozí přenos dat, aby uživatelé zvýšili kapacitu svého prostředí předtím, než se data vyprázdní kvůli porušení doby uchování.
- Pomáhá chránit před ztrátou dat, ale může vytvořit příležitost pro ztrátu vašich nejnovějších dat, pokud se příchozí přenos dat pozastaví mimo dobu uchování vašeho zdroje událostí.
- Jakmile však dosáhnete maximální kapacity prostředí, prostředí pozastaví příchozí přenos dat, dokud neproběhne následující další akce:

  - Maximální kapacitu prostředí můžete zvýšit tak, aby se přidaly další jednotky škálování, jak je popsáno v tématu [Jak škálovat Azure Time Series Insights prostředí](time-series-insights-how-to-scale-your-environment.md).
  - Doba uchovávání dat je dosažená a data se vyprázdní a přinášejí prostředí pod jeho maximální kapacitu.

### <a name="example-three"></a>Příklad tři

Zvažte prostředí s chováním uchovávání dat nakonfigurovaným pro **pozastavení**příchozího přenosu dat. V tomto příkladu je **Doba uchovávání dat** nakonfigurovaná na 60 dní. **Kapacita** je nastavená na tři (3) jednotky S1. Předpokládejme, že toto prostředí má každý den příchozí přenos dat na 2 GB. V tomto prostředí se příchozí přenos dat pozastaví, jakmile se dosáhne maximální kapacity.

V tuto chvíli prostředí zobrazuje stejnou datovou sadu, dokud se neobnoví nebo dokud nebude zapnuté příchozí **přenosy** (což by vymazalo starší data, aby uvolnila místo pro nová data).

Po obnovení příchozího přenosu dat:

- Toky dat v pořadí, ve kterém byly přijaty zdrojem událostí
- Události jsou indexovány na základě časového razítka, pokud jste nepřekročili zásady uchovávání informací ve zdroji událostí. Další informace o konfiguraci uchovávání informací o zdrojích událostí [Event Hubs Nejčastější dotazy](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Měli byste nastavit výstrahy, které vám poskytnou informace, které vám pomůžou zabránit pozastavenému přenosu. Může dojít ke ztrátě dat, protože výchozí doba uchování je 1 den pro zdroje událostí Azure. Proto když je příchozí příjem pozastaven, pravděpodobně ztratíte nejaktuálnější data, pokud se neprovede žádná další akce. Abyste se vyhnuli potenciálním ztrátám dat, je nutné zvýšit kapacitu nebo přepnout chování, aby se **vymazala stará data** .

V ovlivněných Event Hubs zvažte úpravu vlastnosti **uchovávání zpráv** , aby se minimalizovala ztráta dat, když dojde k pozastavení vstupu v Azure Time Series Insights.

[![Uchovávání zpráv centra událostí](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Pokud ve zdroji událostí () nejsou nakonfigurované žádné vlastnosti `timeStampPropertyName` , Azure Time Series Insights ve výchozím nastavení časové razítko doručení do centra událostí jako osu X. Pokud `timeStampPropertyName` je nakonfigurovaná tak, aby byla něco jiného, prostředí vyhledá `timeStampPropertyName` při analýze událostí nakonfigurovanou v datovém paketu.

Přečtěte si [, jak škálovat Azure Time Series Insights prostředí](time-series-insights-how-to-scale-your-environment.md) a škálovat prostředí tak, aby vyhovovalo další kapacitě nebo zvýšila délku uchovávání.

## <a name="next-steps"></a>Další kroky

- Informace o konfiguraci a změně nastavení uchovávání dat najdete [v Azure Time Series Insights konfigurace uchovávání v](time-series-insights-how-to-configure-retention.md)nástroji.

- Přečtěte si o [zmírnění latence v Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).
