---
title: Vytvoření upozornění s dynamickými prahovými hodnotami ve službě Azure Monitor
description: Vytvoření upozornění s dynamickými prahovými hodnotami využívajících machine learning
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 4024ecddde4b0d020e2c657214a4a258ea0b2ea5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449006"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor-public-preview"></a>Upozornění na metriku s dynamickými prahovými hodnotami ve službě Azure Monitor (Public Preview)

Upozornění na metriku s dynamickými prahovými hodnotami zjišťování využívá pokročilé machine learning (ML) historické chování metriky, identifikovat vzory a anomálie, které indikují problémy se službou je to možné. Poskytuje podporu jednoduchého uživatelského rozhraní a operací ve velkém měřítku tím, že uživatelé ke konfiguraci pravidla upozornění pomocí rozhraní API Azure Resource Manageru, plně automatizovanou způsobem.

Jakmile se vytvoří pravidlo upozornění, aktivuje se pouze pokud monitorované metriky nechová podle očekávání, na základě jeho míru prahových hodnot.

Rádi bychom znali váš názor, uchovávejte přicházející na azurealertsfeedback@microsoft.com.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Proč a kdy používá typ dynamická podmínka doporučeno?

1. **Škálovatelné upozorňování** – dynamickými prahovými hodnotami upozornění pravidla můžete vytvořit přizpůsobené prahové hodnoty pro stovky metriky řad najednou. Poskytuje ještě stejně jednoduché definování pravidla upozornění na jednu metriku. Pomocí uživatelského rozhraní nebo výsledků rozhraní API Azure Resource Manageru v méně pravidel upozornění pro správu. Škálovatelný přístup je zvlášť užitečný při práci s metriky dimenze nebo při použití několika prostředky, jako jsou všechny prostředky předplatného. Který se přeloží na značnou dobu ukládání na správu a vytváření pravidel upozornění. [Další informace o tom, jak konfigurovat metriku upozornění s dynamickými prahovými hodnotami pomocí šablon](alerts-metric-create-templates.md).

1. **Inteligentní rozpoznávání vzorců jako metriku** – pomocí naší jedinečné technologie ML, jsme byli schopni automaticky rozpoznat metrické vzory a reagovat na změny metriky v čase, které mohou často obsahovat sezónnosti (každou hodinu nebo každý den / týdně). Přizpůsobení chování metriky za čas a výstrahy založené na odchylky od jeho vzor přišla si museli dělat starosti znalost, "vpravo" prahová hodnota pro každou metriku. Algoritmus strojového učení, který je používán dynamické prahové hodnoty je určeno k ochraně hlučného (nízké přesnosti) nebo celého (nízké odvolání) prahové hodnoty, které nemají očekávanému vzoru.

1. **Intuitivní konfigurace** – dynamickými prahovými hodnotami povolit nastavení upozornění na metriku pomocí základními koncepty, boj měli po rozsáhlé znalosti o metriku.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Jak nakonfigurovat pravidla upozornění s dynamickými prahovými hodnotami?

Upozornění s dynamickými prahovými hodnotami, je možné nakonfigurovat pomocí upozornění metriky ve službě Azure Monitor. [Další informace o tom, jak nakonfigurovat výstrahy metrika](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Jak se počítají prahové hodnoty?

Dynamická prahová hodnota průběžně učí data řady metrik a pokusí model pomocí sady algoritmů a metody. a pokusí se model pomocí sady algoritmů a metody. Zjistí vzory v datech, jako je například sezónnost (každou hodinu nebo každý den / týdně) a je schopný zvládnout hlučného metriky (například počítač CPU, paměť) a také metrik s nízkou rozptylu (například dostupnost a chyba rychlost).

Prahové hodnoty jsou vybrány tak, že odchylky od těchto prahových hodnot označuje anomálie v chování metrik.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Co znamená "Citlivosti" nastavení dynamickými prahovými hodnotami střední?

Prahová hodnota pro výstrahu citlivost je základní koncept, který určuje dobu odchylky od chování metrik, které jsou potřebné k aktivaci výstrahy.
Tato možnost nevyžaduje znalosti o metrika jako statickou prahovou hodnotu. Dostupné jsou následující možnosti:

- Prahové hodnoty vysoké – bude těsně a blízko vzor řady metrik. Pravidlo upozornění se aktivuje na nejmenší odchylku, což vede k více výstrah.
- Střední – méně vysoké a více vyvážené prahové hodnoty, méně výstrah, než se Vysoká citlivost (výchozí).
- Nízká – prahové hodnoty bude přijít o provedené s další vzdálenost od řady metrik vzor. Pravidlo upozornění aktivuje pouze na velké odchylky, což vede k méně výstrah.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Jaké jsou možnosti nastavení 'Operator' v dynamické prahové hodnoty?

Dynamické prahové hodnoty, které můžete vytvořit pravidlo upozornění přizpůsobené prahové hodnoty na základě chování metrik pro velká i a dolní meze pomocí stejného pravidla výstrahy.
Můžete nastavit výstrahu, kterou chcete aktivovat na jednu z těchto tří podmínek:

- Větší než horní prahová hodnota nebo nižší než nižší prahová hodnota (výchozí)
- Větší než horní prahová hodnota
- Nižší než nižší prahová hodnota.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Co dělat v dynamickými prahovými hodnotami střední upřesňující nastavení?

**Období selhání** -dynamickými prahovými hodnotami také umožňuje nakonfigurovat, "Počet narušení pro aktivaci upozornění", s minimálním počtem odchylky požadované v určité časové okno systému vydání výstrahy (výchozí časový interval, jsou čtyři odchylky za pouhých 20 minut). Uživatel může konfigurovat selhání období a zvolte, co chcete být upozorněni na změnou selhání období a časový interval. Tato schopnost snižuje rušivé výstrahy generované přechodné špičky. Příklad:

Chcete-li aktivovat upozornění, když tento problém je souvislý 20 minut, 4 po sobě jdoucích v daném období seskupení 5 minut, použijte následující nastavení:

![Selhání období nastavení pro průběžné problém 20 minut, po sobě jdoucích 4krát v daném období seskupení 5 minut](media/alerts-dynamic-thresholds/0008.png)

Chcete-li aktivovat upozornění, když došlo k narušení z dynamickými prahovými hodnotami za pouhých 20 minut z posledních 30 minut s 5 minut, použijte následující nastavení:

![Selhání období nastavení pro vydání pro 20 minut mimo správný posledních 30 minut se období seskupení 5 minut](media/alerts-dynamic-thresholds/0009.png)

**Ignorovat data před** – uživatelé mohou také v případě potřeby definovat počáteční datum, které systém by měl zahájit výpočtu prahové hodnoty z. Typické použití případu může dojít při prostředek byl spuštěný v režimu, testování a je nyní povýšen na poskytování produkční úlohy, a proto by měl chování jakékoliv metriky během fáze testování ignorovány.

## <a name="will-slow-behavior-change-in-the-metric-trigger-an-alert"></a>Pomalé chování změní v aktivační událost metriky upozornění?

Pravděpodobně není. Jsou vhodné pro zjištění významné odchylky, spíše než pomalu se vyvíjejí problémy s dynamickými prahovými hodnotami.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Kolik dat se používá k zobrazení náhledu a pak vypočítat prahové hodnoty?

Prahové hodnoty uvedené v grafu, předtím, než se vytvoří pravidlo upozornění na metriku, se počítají na posledních 10 dnů historických dat, po vytvoření pravidla upozornění dynamickými prahovými hodnotami se získání dalších historických dat, která je k dispozici a bude Další informace neustále na základě nových dat do zpřesnit prahové hodnoty.
