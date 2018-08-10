---
title: Vysvětlení uchovávání dat ve vašem prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje dvě nastavení, která řídí uchovávání dat ve vašem prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 204a9d64d14fe190cb0de73c964bb95b4b9b475f
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628762"
---
# <a name="understand-data-retention-in-time-series-insights"></a>Vysvětlení uchovávání dat v Time Series Insights
Tento článek popisuje dvě nastavení, které ovlivňují uchovávání dat ve vašem prostředí Time Series Insights (TSI).

Každé prostředí TSI má nastavení, které řídí **doby uchování dat**. Hodnota sahá od 1 až 400 dnů. Data se odstraní podle prostředí kapacity nebo uchovávání doba trvání úložiště (1 až 400), podle toho, co nastane dřív.

Každé prostředí TSI má další nastavení **překročil limit úložiště chování**. Toto nastavení řídí chování příchozího přenosu dat a mazání po dosažení maximální kapacity prostředí. Existují dva chování lze vybírat:
- **Vymazat stará data** (výchozí)  
- **Pozastavit příchozího přenosu dat**

> [!NOTE]
> Ve výchozím nastavení se při vytváření nového prostředí uchovávání umožňují **vymazat stará data**. Toto nastavení můžou být v případě potřeby po pomocí webu Azure portal, v okamžiku vytvoření **konfigurovat** stránky prostředí TSI.

Informace o přepínání chování uchovávání dat najdete v tématu [konfigurace uchovávání naleznete v Time Series Insights](time-series-insights-how-to-configure-retention.md).

Porovnejte chování uchovávání dat:

## <a name="purge-old-data"></a>Vymazat stará data
- Toto chování je výchozí chování pro prostředí TSI a dodatků stejné prostředí TSI chování vystavoval od spuštěna ve verzi public preview.  
- Toto chování se upřednostňuje v případě uživatelů chcete vždy zobrazit jejich *nejnovější data* ve svém prostředí TSI. 
- Toto chování *vymaže* dat jednou prostředí je dosaženo omezení (doba uchovávání, velikost nebo count, podle toho, co nastane dřív). Ve výchozím nastavení do 30 dnů uchovávání informací. 
- Nejstarší přijatých dat se vyprazdňují první (FIFO přístup).

### <a name="example-1"></a>Příklad 1:
Vezměte v úvahu příklad prostředí s uchování chování **pokračovat příchozího přenosu dat a vymazat stará data**: V tomto příkladu **doby uchování dat** je nastavena na 400 dnů. **Kapacita** nastavená na jednotka S1, která obsahuje 30 GB celkové kapacity.   Předpokládejme, že příchozí data hromadí na 500 MB denně v průměru. Toto prostředí pouze uchovávat 60 dní, po který dat zadaný počet příchozích dat, protože na 60 dní je dosaženo maximální kapacity. Příchozí data hromadí jako: 500 MB každý den x 60 dnů = 30 GB. 

V tomto příkladu 61st dne prostředí ukazuje nejčerstvější data, ale odstraní nejstarší data starší než 60 dnů. K vyprázdnění díky prostor pro nová data streamování, tak, aby nová data může dál prozkoumat. 

Pokud uživatel chce uchovávat data déle, můžete zvýšit velikost prostředí přidáním dalších jednotek nebo méně data můžete nabízet.  

### <a name="example-2"></a>Příklad 2:
Vezměte v úvahu prostředí také nakonfigurována uchování chování **pokračovat příchozího přenosu dat a vymazat stará data**. V tomto příkladu **doby uchování dat** je nastavena na hodnotu nižší než 180 dnů. **Kapacita** nastavená na jednotka S1, která obsahuje 30 GB celkové kapacity. Jen tak půjde ukládat data pro úplné 180 dnů, denní příchozí přenos dat nemůže být delší než 0.166 GB (166 MB) za den.  

Pokaždé, když se toto prostředí denní rychlost příchozího přenosu dat překročí 0.166 GB za den, data nelze ukládat na 180 dnů, protože některá data získá odstraněna. Vezměte v úvahu tato stejné prostředí během zaneprázdněný časového rámce. Předpokládejme, že průměrná 0.189 GB za den může zvýšit rychlost příchozího přenosu dat prostředí. V tomto zaneprázdněný časovém rámci, se zachovají data o 158 dnů (30GB/0.189 = 158,73 dnů uchovávání). Tentokrát je menší než požadovaná data uchování časový rámec.

## <a name="pause-ingress"></a>Pozastavit příchozího přenosu dat
- Toto chování je navržený tak, aby, že data se vymazat, pokud je dosaženo omezení velikosti a počtu před jejich doba uchování.  
- Toto chování poskytuje další čas pro uživatele, které chcete zvýšit kapacitu prostředí před data se vyprázdní se kvůli porušení doby uchování
- Toto chování pomáhá chránit před únikem informací, ale vytvoří příležitost ztráty nejnovější data, pokud je pozastaven příchozího přenosu dat nad rámec doby uchování váš zdroj událostí.
- Ale po dosažení maximální kapacitu prostředí, pozastaví prostředí příchozího přenosu dat, dokud provedou další akce: 
   - Můžete zvýšit maximální kapacitu prostředí. Další informace najdete v tématu [jak škálovat vaše prostředí Time Series Insights](time-series-insights-how-to-scale-your-environment.md) přidáte další jednotky škálování.
   - Je dosaženo období uchovávání dat a vymazat data tedy přináší prostředí pod maximální kapacitě.

### <a name="example-3"></a>Příklad 3:
Vezměte v úvahu prostředí díky uchování chování nakonfigurovat tak, aby **pozastavit příchozího přenosu dat**. V tomto příkladu **období uchovávání dat** nastaven na 60 dnů. **Kapacita** nastavená na 3 jednotky S1. Předpokládejme, že toto prostředí má 2GB dat denně. V tomto prostředí se pozastaví příchozího přenosu dat po dosažení maximální kapacity. V tu chvíli prostředí zobrazuje stejné datové sady až do obnoví příchozího přenosu dat nebo dokud 'příkaz continue příchozího přenosu dat"je povoleno (což by vymazat starších dat, aby uvolnil prostor pro nová data). 

Když se obnoví příchozího přenosu dat:
- Datové toky v pořadí, ve kterém byla přijata podle zdroje události
- Události jsou indexovány podle časové značky, pokud jste nepřekročili zásady uchovávání informací na váš zdroj událostí. Další informace o konfiguraci uchování zdroje událostí [nejčastější dotazy k Event Hubs](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Měli byste nastavit výstrahy a oznámit, že se chcete vyhnout příchozího přenosu dat byla pozastavena. Ztráta dat je možné, protože doba uchování výchozí je 1 den pro zdroje událostí Azure. Proto po příchozího přenosu dat je pozastavený, pravděpodobně ztratíte nejnovějších dat. není-li další akci. Musíte zvýšit kapacitu nebo přepnout chování **vymazat stará data** aby se zabránilo potenciální ztrátě dat.

Ovlivněné služby Event Hubs, zvažte možnost Upravit **uchovávání zpráv** vlastnost účelem minimalizace ztráty dat, když dojde k pozastavení příchozího přenosu dat v Time Series Insights.

![Uchovávání zpráv centra událostí.](media/time-series-insights-contepts-retention/event-hub-retention.png)

Pokud žádné vlastnosti, které jsou nakonfigurované u zdroje události (timeStampPropertyName), výchozí hodnota TSI časové razítko přijetí v Centru událostí jako osu x. Pokud timeStampPropertyName byl nakonfigurován jako něco jiného, prostředí nakonfigurované timeStampPropertyName v datový paket hledá, když jsou analyzovány události. 

Pokud je potřeba škálovat vaše prostředí a přizpůsobit další kapacitu nebo chcete zvýšit dobu uchování, přečtěte si téma [jak škálovat vaše prostředí Time Series Insights](time-series-insights-how-to-scale-your-environment.md) Další informace.  

## <a name="next-steps"></a>Další postup
Informace o přepínání chování uchovávání dat najdete v tématu [konfigurace uchovávání naleznete v Time Series Insights](time-series-insights-how-to-configure-retention.md).
