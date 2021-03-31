---
title: Jak monitorovat a snižovat omezení – Azure Time Series Insights | Microsoft Docs
description: Naučte se monitorovat, diagnostikovat a zmírnit problémy s výkonem, které způsobují latenci a omezování v Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: e89189b22b144d9e92ee8315bc6fd9aabe699eec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91531645"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights-gen1"></a>Monitorování a zmírnění omezení pro snížení latence v Azure Time Series Insights Gen1

> [!CAUTION]
> Toto je Gen1 článek.

Když velikost příchozích dat překročí konfiguraci vašeho prostředí, může docházet k latenci nebo omezování v Azure Time Series Insights.

Latenci a omezování můžete zabránit tím, že správně nakonfigurujete prostředí pro množství dat, která chcete analyzovat.

Máte pravděpodobně na latenci a omezení, když máte tyto možnosti:

- Přidejte zdroj události, který obsahuje stará data, která by mohla překročit vaši přidělenou míru příchozího přenosu dat (Azure Time Series Insights bude nutné zachytit).
- Přidání dalších zdrojů událostí do prostředí a výsledkem je špička dalších událostí (což by mohlo překročit kapacitu vašeho prostředí).
- Nahrajte velké objemy historických událostí do zdroje událostí, čímž dojde k prodlevě (Azure Time Series Insights bude nutné zachytit).
- Spojování referenčních dat s telemetrie a výsledkem je větší velikost události. Maximální povolená velikost paketu je 32 KB; datové pakety větší než 32 KB se zkrátí.

## <a name="video"></a>Video

### <a name="learn-about-azure-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Přečtěte si o chování Azure Time Series Insights dat a o tom, jak je naplánovat.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitorování latence a omezování pomocí výstrah

Výstrahy umožňují diagnostikovat a zmírnit problémy s latencí, ke kterým dochází ve vašem prostředí.

1. V Azure Portal vyberte své prostředí Azure Time Series Insights. Pak vyberte **výstrahy**.

   [![Přidání výstrahy do prostředí Azure Time Series Insights](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Vyberte **+ Nové pravidlo upozornění**. Zobrazí se panel **vytvořit pravidlo** . Vyberte **Přidat** pod **podmínkou**.

   [![Přidat podokno výstrah](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Dále nakonfigurujte přesné podmínky pro logiku signálu.

   [![Konfigurace logiky signálů](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   Odtud můžete nakonfigurovat výstrahy pomocí některých z těchto podmínek:

   |Metric  |Popis  |
   |---------|---------|
   |**Přijaté bajty příchozího přenosu dat**     | Počet nezpracovaných bajtů načtených ze zdrojů událostí. Nezpracovaný počet obvykle zahrnuje název vlastnosti a hodnotu.  |  
   |**Příchozí přenos dat přijal neplatné zprávy.**     | Počet neplatných zpráv přečtených ze všech zdrojů událostí Azure Event Hubs nebo Azure IoT Hub.      |
   |**Příchozí zprávy příchozího přenosu dat**   | Počet zpráv načtených ze všech Event Hubs nebo zdrojů událostí centra IoT.        |
   |**Uložené bajty příchozího přenosu dat**     | Celková velikost uložených událostí a dostupných pro dotaz Velikost je vypočítána pouze v hodnotě vlastnosti.        |
   |**Uložené události příchozího přenosu dat**     |   Počet sloučených událostí uložených a dostupných pro dotaz      |
   |**Prodleva při příjmu příchozího přenosu zpráv**    |  Rozdíl v sekundách mezi časem, kdy je zpráva zařazená do fronty ve zdroji událostí a čas zpracování v příchozím přenosu.      |
   |**Prodleva počtu přijatých zpráv příchozího přenosu dat**    |  Rozdíl mezi pořadovým číslem poslední zprávy ve frontě ve zdrojovém oddílu události a pořadovým číslem zprávy zpracovávaných v příchozím přenosu.      |

   Vyberte **Hotovo**.

1. Po nakonfigurování požadované logiky signálu si prohlédněte zvolené pravidlo výstrahy vizuálně.

   [![Zobrazení latence a vytváření grafů](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Omezování a správa pro příchozí přenosy

- Pokud jste omezili omezení, bude zaregistrována hodnota *časové prodlevy přijatých zpráv příchozího* přenosu informací o tom, kolik sekund za vaším Azure Time Series Insights prostředím probíhá od skutečného času, kdy zpráva narazí na zdroj události (s výjimkou času indexování appx). 30-60 sekund).  

  *Prodleva počtu přijatých zpráv příchozího* přenosu dat by měla mít také hodnotu, která vám umožní určit, kolik zpráv je za vás.  Nejjednodušší způsob, jak se získat, je zvýšit kapacitu vašeho prostředí na velikost, která vám umožní překonat rozdíl.  

  Pokud například vaše prostředí S1 předchází prodlevě 5 000 000 zpráv, můžete zvýšit velikost svého prostředí na šest jednotek po dobu zhruba dne, abyste se mohli zachytit.  Můžete ještě víc zvýšit, abyste rychleji zachytili. Při počátečním zřizování prostředí, zejména v případě, že ho připojíte ke zdroji událostí, který už obsahuje události nebo když hromadně nahráváte spoustu historických dat, je období zachycení běžným výskytem.

- Další možností je nastavit upozornění na **uložené události** příchozího přenosu >= mezní hodnota mírně pod celkovou kapacitou prostředí po dobu 2 hodin.  Tato výstraha vám pomůže pochopit, jestli máte neustále na kapacitě, což znamená vysokou pravděpodobnost latence.

  Například pokud máte tři jednotky S1 zřízené (nebo 2100 událostí za minutu příchozího přenosu dat), můžete nastavit upozornění na **uložené události** příchozího přenosu dat pro >= 1900 události na 2 hodiny. Pokud tuto prahovou hodnotu trvale obdržíte, a proto aktivujete upozornění, pravděpodobně jste v souladu se zřízením.  

- Pokud se domníváte, že jste omezili, můžete porovnat **přijaté zprávy s příchozími** zprávami ve zdroji událostí.  Pokud příchozí přenos dat do centra událostí je větší než vaše **příchozí zprávy**, vaše Azure Time Series Insights jsou nejspíš omezené.

## <a name="improving-performance"></a>Zlepšení výkonu

Aby se snížila latence nebo dochází k latenci, nejlepším způsobem, jak ho opravit, je zvýšit kapacitu vašeho prostředí.

Latenci a omezování můžete zabránit tím, že správně nakonfigurujete prostředí pro množství dat, která chcete analyzovat. Další informace o tom, jak přidat kapacitu do svého prostředí, najdete v tématu [škálování prostředí](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si informace o [diagnostice a řešení problémů v prostředí Azure Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).

- Naučte [se škálovat Azure Time Series Insights prostředí](time-series-insights-how-to-scale-your-environment.md).
