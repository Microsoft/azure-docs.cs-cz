---
title: Principy uchovávání dat ve vašem prostředí – Azure Time Series Insight | Dokumenty společnosti Microsoft
description: Tento článek popisuje dvě nastavení, která řídí uchovávání dat v prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: fd34595d5ea942602efc920904ff326fc203c088
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380693"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Principy uchovávání dat v Azure Time Series Insights

Tento článek popisuje dvě primární nastavení, která mají vliv na uchovávání dat v prostředí Azure Time Series Insights.

## <a name="video"></a>Video

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Následující video shrnuje uchovávání dat Time Series Insights a jak je naplánovat.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Každé z vašich prostředí Azure Time Series Insights má nastavení, které řídí **čas uchovávání dat**. Hodnota se rozprostírá od 1 do 400 dnů. Data se odstraní na základě kapacity úložiště prostředí nebo doby uchovávání, podle toho, co nastane dříve.

Vaše prostředí Azure Time Series Insights má navíc **překročení limitu úložiště.** Řídí příchozí přenos dat a vyprázdnit chování při dosažení maximální kapacity prostředí. Při konfiguraci je třeba vybrat ze dvou chování:

- **Vyčistit stará data** (výchozí)  
- **Pozastavení příchozího přenosu dat**

> [!NOTE]
> Ve výchozím nastavení je při vytváření nového prostředí uchovávání nakonfigurováno na **vymazání starých dat**. Toto nastavení lze podle potřeby přepnout po vytvoření pomocí portálu Azure na stránce **Konfigurace** prostředí Time Series Insights.
> * Informace o konfiguraci zásad uchovávání informací naleznete [v zásadách konfigurace uchovávání informací v přehledech časové řady .](time-series-insights-how-to-configure-retention.md)

Obě zásady uchovávání dat jsou popsány podrobněji níže.

## <a name="purge-old-data"></a>Vyčistit stará data

- **Vymazání starých dat** je výchozí nastavení pro prostředí Azure Time Series Insights.  
- **Vymazat stará data** je upřednostňováno, když uživatelé chtějí mít vždy nejnovější *data* ve svém prostředí Time Series Insights.
- **Nastavení vyčistit stará data** vyčistí data, jakmile jsou *dosaženy* limity prostředí (doba uchovávání, velikost nebo počet, podle toho, co nastane dříve). Uchovávání je ve výchozím nastavení nastaveno na 30 dní.
- Nejstarší ingestovaná data jsou nejprve vymazána (přístup "First In First Out").

### <a name="example-one"></a>Příklad jednoho

Vezměme si příklad prostředí s chováním uchovávání **informací Pokračovat v přenosu dat a vymazat stará data**:

**Doba uchovávání dat** je nastavena na 400 dní. **Kapacita** je nastavena na jednotku S1, která obsahuje 30 GB celkové kapacity. Předpokládejme, že příchozí data se hromadí na 500 MB každý den v průměru. Toto prostředí může zachovat pouze 60 dní v hodnotě dat vzhledem k rychlosti příchozích dat, protože maximální kapacita je dosaženo na 60 dnů. Příchozí data se hromadí jako: 500 MB každý den x 60 dní = 30 GB.

61. den prostředí zobrazuje nejčerstvější data, ale vyčistí nejstarší data starší než 60 dní. Vymazání vytváří prostor pro nové datové proudy, takže nová data mohou být i nadále prozkoumány. Pokud si uživatel přeje uchovávat data déle, může zvětšit velikost prostředí přidáním dalších jednotek nebo může tlačit méně dat.  

### <a name="example-two"></a>Příklad dva

Zvažte prostředí také nakonfigurované chování uchovávání **informací Pokračovat v přenosu dat a vymazat stará data**. V tomto příkladu je **doba uchovávání dat** nastavena na nižší hodnotu 180 dní. **Kapacita** je nastavena na jednotku S1, která obsahuje 30 GB celkové kapacity. Chcete-li ukládat data za celých 180 dní, denní příchozí přenos dat nesmí překročit 0,166 GB (166 MB) za den.  

Vždy, když toto prostředí denní rychlost příchozího přenosu dat překročí 0,166 GB za den, data nelze uložit po dobu 180 dnů, protože některá data získá vymazány. Zvažte stejné prostředí během zaneprázdněného časového rámce. Předpokládejme, že rychlost příchozího přenosu dat prostředí může zvýšit na průměr 0.189 GB za den. V tomto rušném časovém rámci jsou zachovány přibližně 158 dny dat (30 GB/0,189 = 158,73 dnů uchovávání). Tato doba je kratší než požadovaný časový rámec uchovávání dat.

## <a name="pause-ingress"></a>Pozastavení příchozího přenosu dat

- Nastavení **Pozastavení příchozího přenosu dat** je navržentak, aby zajistilo, že data nebudou vymazána, pokud je dosaženo limitů velikosti a počtu před jejich dobou uchovávání.  
- **Pozastavení příchozího přenosu dat** poskytuje uživatelům další čas na zvýšení kapacity jejich prostředí před vymazáním dat z důvodu porušení doby uchovávání.
- Pomáhá chránit před ztrátou dat, ale může vytvořit příležitost pro ztrátu nejnovějších dat, pokud je příchozí přenos dat pozastaven po dobu uchování zdroje událostí.
- Jakmile je však dosaženo maximální kapacity prostředí, prostředí pozastaví příchozí přenos dat, dokud nedojde k následujícím dalším akcím:

   - Maximální kapacitu prostředí pro přidání dalších jednotek škálování, jak je popsáno v části [Jak škálovat prostředí Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
   - Je dosaženo doby uchovávání dat a data jsou vymazána, čímž se prostředí pod jeho maximální kapacitu.

### <a name="example-three"></a>Příklad tři

Zvažte prostředí s chováním uchovávání informací nakonfigurovaným tak, aby **pozastavilo příchozí přenos dat**. V tomto příkladu je **doba uchovávání dat** nakonfigurována na 60 dní. **Kapacita** je nastavena na tři (3) jednotky S1. Předpokládejme, že toto prostředí má příchozí přenos dat 2 GB každý den. V tomto prostředí příchozí přenos dat je pozastavena po dosažení maximální kapacity.

V té době prostředí zobrazuje stejnou datovou sadu, dokud příchozí přenos dat pokračuje nebo dokud není povolen **a pokračovat v příchozím přenosu dat** (což by očistit starší data, aby se uvolnilo místo pro nová data).

Při obnovení příchozího přenosu dat:

- Toky dat v pořadí, v jakém byly přijaty podle zdroje událostí
- Události jsou indexovány na základě jejich časové razítko, pokud jste překročili zásady uchovávání informací na zdroj událostí. Další informace o konfiguraci uchovávání zdrojů událostí [nazbudou nejčastější dotazy k centru událostí.](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Měli byste nastavit výstrahy poskytnout oznámení, aby se zabránilo příchozí přenos dat pozastavena. Ztráta dat je možná, protože výchozí uchovávání je 1 den pro zdroje událostí Azure. Proto po pozastavení příchozího přenosu dat pravděpodobně ztratíte nejnovější data, pokud není provedena další akce. Je nutné zvýšit kapacitu nebo přepnout chování na **vymazat stará data,** aby se zabránilo ztrátě potenciálu dat.

V ovlivněných rozbočovačích událostí zvažte úpravu vlastnosti **Uchovávání zpráv,** abyste minimalizovali ztrátu dat, když dojde k pozastavení příchozího přenosu dat v přehledech časové řady.

[![Uchovávání zpráv centra událostí.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Pokud nejsou ve zdroji událostí`timeStampPropertyName`( ) nakonfigurovány žádné vlastnosti, výchozí časové řady Přehledy na časové razítko doručení v centru událostí jako osa X. Pokud `timeStampPropertyName` je nakonfigurován jako něco jiného, `timeStampPropertyName` prostředí hledá nakonfigurované v datovém paketu při analýzě událostí.

Přečtěte [si, jak škálovat prostředí Time Series Insights](time-series-insights-how-to-scale-your-environment.md) tak, aby škálování prostředí tak, aby vyhovovalo další kapacity nebo zvýšit délku uchovávání.

## <a name="next-steps"></a>Další kroky

- Informace o konfiguraci nebo změně nastavení uchovávání dat naleznete [v části Konfigurace uchovávání informací v přehledech časové řady .](time-series-insights-how-to-configure-retention.md)

- Přečtěte si o [zmírnění latence v Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).
