---
title: Vysvětlení uchovávání dat ve vašem prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje dvě nastavení, která řídí uchovávání dat ve vašem prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: dc192b1e8f5492cdfbfb434b5efb573182c51bb1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164568"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Vysvětlení uchovávání dat v Azure Time Series Insights

Tento článek popisuje dvě nastavení, které ovlivňují uchovávání dat ve vašem prostředí Azure Time Series Insights.

## <a name="video"></a>Video

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>V následujícím videu shrnuje uchovávání dat Time Series Insights a jak ji plánovat.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Prostředí Azure Time Series obsahují nastavení, které řídí **doby uchování dat**. Hodnota sahá od 1 do 400 dnů. Data se odstraní podle kapacity úložiště prostředí nebo doba uchovávání, podle toho, co nastane dřív.

Kromě toho má prostředí Azure Time Series **překročil limit úložiště chování** nastavení. Řídí příchozí přenos dat a vymazat chování, když je dosaženo maximální kapacity prostředí. Existují dva chování lze vybírat při jeho konfiguraci:

- **Vymazat stará data** (výchozí)  
- **Pozastavit příchozího přenosu dat**

> [!NOTE]
> Ve výchozím nastavení se při vytváření nového prostředí uchovávání umožňují **vymazat stará data**. Toto nastavení můžou být v případě potřeby po pomocí webu Azure portal, v okamžiku vytvoření **konfigurovat** stránky prostředí Time Series Insights.

Informace o přepínání chování uchovávání dat najdete v tématu [konfigurace uchovávání naleznete v Time Series Insights](time-series-insights-how-to-configure-retention.md).

Porovnejte chování uchovávání dat:

## <a name="purge-old-data"></a>Vymazat stará data

- Toto chování je výchozí chování pro prostředí Time Series Insights.  
- Toto chování se upřednostňuje v případě uživatelů chcete vždy zobrazit jejich *nejnovější data* ve svém prostředí Time Series Insights.
- Toto chování *vymaže* dat jednou prostředí je dosaženo omezení (doba uchovávání, velikost nebo count, podle toho, co nastane dřív). Ve výchozím nastavení do 30 dnů uchovávání informací.
- Nejstarší přijatých dat se vyprazdňují první (FIFO přístup).

### <a name="example-one"></a>Příklad jednoho

Vezměte v úvahu příklad prostředí s uchování chování **pokračovat příchozího přenosu dat a vymazat stará data**:

**Doba uchovávání dat** je nastavena na 400 dnů. **Kapacita** nastavená na jednotka S1, která obsahuje 30 GB celkové kapacity.   Předpokládejme, že příchozí data hromadí na 500 MB denně v průměru. Toto prostředí pouze uchovávat 60 dní, po který dat zadaný počet příchozích dat, protože na 60 dní je dosaženo maximální kapacity. Příchozí data hromadí jako: 500 MB každý den x 60 dnů = 30 GB.

61st dne prostředí ukazuje nejčerstvější data, ale odstraní nejstarší data starší než 60 dnů. K vyprázdnění díky prostor pro nová data streamování, tak, aby nová data může dál prozkoumat. Pokud uživatel chce uchovávat data déle, můžete zvýšit velikost prostředí přidáním dalších jednotek nebo méně data můžete nabízet.  

### <a name="example-two"></a>Příklad 2

Vezměte v úvahu prostředí také nakonfigurována uchování chování **pokračovat příchozího přenosu dat a vymazat stará data**. V tomto příkladu **doby uchování dat** je nastavena na hodnotu nižší než 180 dnů. **Kapacita** nastavená na jednotka S1, která obsahuje 30 GB celkové kapacity. Jen tak půjde ukládat data pro úplné 180 dnů, denní příchozí přenos dat nemůže být delší než 0.166 GB (166 MB) za den.  

Pokaždé, když se toto prostředí denní rychlost příchozího přenosu dat překročí 0.166 GB za den, data nelze ukládat na 180 dnů, protože některá data získá odstraněna. Vezměte v úvahu tato stejné prostředí během zaneprázdněný časového rámce. Předpokládejme, že průměrná 0.189 GB za den může zvýšit rychlost příchozího přenosu dat prostředí. V tomto zaneprázdněný časovém rámci, se zachovají data o 158 dnů (30GB/0.189 = 158,73 dnů uchovávání). Tentokrát je menší než požadovaná data uchování časový rámec.

## <a name="pause-ingress"></a>Pozastavit příchozího přenosu dat

- **Pozastavit příchozího přenosu dat** nastavení je navržený tak, aby data není vymazat, pokud je dosaženo omezení velikosti a počtu před jejich doba uchování.  
- **Pozastavit příchozího přenosu dat** poskytuje další čas pro uživatele, které chcete zvýšit kapacitu prostředí před data se vyprázdní se kvůli porušení doby uchování
- Přispívá k ochraně před ztrátou dat ale můžete vytvořit příležitosti pro ztrátu nejnovější data, pokud příchozí přenos dat je pozastavený nad rámec doby uchování váš zdroj událostí.
- Však po dosažení maximální kapacitu prostředí pozastaví prostředí příchozího přenosu dat, dokud se provedou následující další akce:

   - Zvyšte maximální kapacitu prostředí přidat více jednotek škálování, jak je popsáno v [jak škálovat vaše prostředí Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
   - Je dosaženo období uchovávání dat a vymazat data přináší prostředí pod maximální kapacitě.

### <a name="example-three"></a>Příklad tří

Vezměte v úvahu prostředí díky uchování chování nakonfigurovat tak, aby **pozastavit příchozího přenosu dat**. V tomto příkladu **období uchovávání dat** nastaven na 60 dnů. **Kapacita** je nastavena na tři (3) jednotky S1. Předpokládejme, že toto prostředí má 2GB dat denně. V tomto prostředí se pozastaví příchozího přenosu dat po dosažení maximální kapacity.

V tu chvíli prostředí zobrazuje stejné datové sady až do obnoví příchozího přenosu dat nebo do **pokračovat příchozího přenosu dat** je povolená (což by vymazat starších dat, aby uvolnil prostor pro nová data).

Když se obnoví příchozího přenosu dat:

- Datové toky v pořadí, ve kterém byla přijata podle zdroje události
- Události jsou indexovány podle časové značky, pokud jste nepřekročili zásady uchovávání informací na váš zdroj událostí. Další informace o konfiguraci uchování zdroje událostí [nejčastější dotazy k Event Hubs](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Měli byste nastavit výstrahy a oznámit, že se chcete vyhnout příchozího přenosu dat byla pozastavena. Ztráta dat je možné, protože doba uchování výchozí je 1 den pro zdroje událostí Azure. Proto po příchozího přenosu dat je pozastavený, pravděpodobně ztratíte nejnovějších dat. není-li další akci. Musíte zvýšit kapacitu nebo přepnout chování **vymazat stará data** aby se zabránilo potenciální ztrátě dat.

Ovlivněné služby Event Hubs, zvažte možnost Upravit **uchovávání zpráv** vlastnost účelem minimalizace ztráty dat, když dojde k pozastavení příchozího přenosu dat v Time Series Insights.

[![Uchovávání zpráv centra událostí.](media/time-series-insights-contepts-retention/event-hub-retention.png)](media/time-series-insights-contepts-retention/event-hub-retention.png#lightbox)

Pokud žádné vlastnosti, které jsou nakonfigurované u zdroje události (`timeStampPropertyName`), výchozí hodnota Time Series Insights je časové razítko přijetí v Centru událostí jako osu x. Pokud `timeStampPropertyName` je nakonfigurovaný, aby byl něco jiného, vyhledá prostředí pro nakonfigurované `timeStampPropertyName` v datový paket, když jsou analyzovány události.

Pokud je potřeba škálovat vaše prostředí a přizpůsobit další kapacitu nebo chcete zvýšit dobu uchování, přečtěte si téma [jak škálovat vaše prostředí Time Series Insights](time-series-insights-how-to-scale-your-environment.md) Další informace.  

## <a name="next-steps"></a>Další postup

- Informace o konfiguraci nebo změna nastavení uchovávání dat najdete v tématu [konfigurace uchovávání naleznete v Time Series Insights](time-series-insights-how-to-configure-retention.md).
