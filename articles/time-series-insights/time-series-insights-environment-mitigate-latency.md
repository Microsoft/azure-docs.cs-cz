---
title: Jak sledovat a omezit omezení – Azure Time Series Insights | Dokumenty společnosti Microsoft
description: Zjistěte, jak monitorovat, diagnostikovat a zmírňovat problémy s výkonem, které způsobují latenci a omezení v Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 245a0b18187ff1c1b226e94b03374f2c071e51c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314823"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Sledování a zmírnění omezení ke snížení latence v Azure Time Series Insights

Pokud množství příchozích dat překročí konfiguraci vašeho prostředí, může dojít k latenci nebo omezení v Azure Time Series Insights.

Latenci a omezení se můžete vyhnout správnou konfigurací prostředí pro množství dat, která chcete analyzovat.

S největší pravděpodobností zažijete latenci a omezení, když:

- Přidejte zdroj událostí, který obsahuje stará data, která mohou překročit přidělenou rychlost příchozího přenosu dat (Time Series Insights bude muset dohnat).
- Přidejte do prostředí další zdroje událostí, což vede k nárůstu z dalších událostí (které mohou překročit kapacitu vašeho prostředí).
- Zatlačte velké množství historických událostí do zdroje událostí, což má za následek zpoždění (Time Series Insights bude muset dohnat).
- Připojte referenční data pomocí telemetrie, což má za následek větší velikost událostí. Z hlediska omezení příchozí datový paket s velikostí paketu 32 KB je považován za 32 událostí, z nichž každá velikost 1 KB. Maximální povolená velikost události je 32 kB; datové pakety větší než 32 KB jsou zkráceny.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Přečtěte si o chování příchozích dat time series insights a o tom, jak ho naplánovat.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Sledování latence a omezení pomocí výstrah

Výstrahy vám mohou pomoci diagnostikovat a zmírnit problémy s latencí, ke kterým dochází ve vašem prostředí.

1. Na webu Azure Portal vyberte prostředí Time Series Insights. Pak vyberte **Výstrahy**.

   [![Přidání upozornění do prostředí Time Series Insights](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Vyberte **+ Nové pravidlo upozornění**. Poté se zobrazí panel **Vytvořit pravidla.** Vyberte **Přidat** pod **podmínkou**.

   [![Podokno upozornění přidání](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Dále nakonfigurujte přesné podmínky pro logiku signálu.

   [![Konfigurace logiky signálů](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   Odtud můžete nakonfigurovat výstrahy pomocí některých z následujících podmínek:

   |Metrika  |Popis  |
   |---------|---------|
   |**Příchozí přijaté bajty**     | Počet nezpracovaných bajtů přečtených ze zdrojů událostí. Nezpracovaný počet obvykle zahrnuje název vlastnosti a hodnotu.  |  
   |**Příchozí přenos dat přijal neplatné zprávy**     | Počet neplatných zpráv přečtených ze všech center událostí Azure nebo zdrojů událostí Azure IoT Hub.      |
   |**Příchozí odchozí přenos zpráv**   | Počet zpráv přečtených ze všech center událostí nebo zdrojů událostí IoT Hubs.        |
   |**Příchozí uložené bajty**     | Celková velikost událostí uložených a dostupných pro dotaz. Velikost se vypočítá pouze na hodnotu vlastnosti.        |
   |**Příchozí uložené události**    |   Počet vyrovnaných událostí uložených a dostupných pro dotaz.      |
   |**Prodleva času přijaté zprávy příchozího přenosu dat**   |  Rozdíl v sekundách mezi časem, kdy je zpráva zařazena do fronty ve zdroji událostí, a časem, kdy je zpracována v příchozím přenosu dat.      |
   |**Prodleva počtu přijatých zpráv příchozího přenosu dat**   |  Rozdíl mezi pořadovým číslem poslední zprávy zařazené do fronty ve zdrojovém oddílu události a pořadovým číslem zprávy zpracovávané v příchozím přenosu dat.      |

   Vyberte **Done** (Hotovo).

1. Po konfiguraci požadované logiky signálu si vizuálně zkontrolujte zvolené pravidlo výstrahy.

   [![Zobrazení latence a vytváření grafů](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Řízení omezení a příchozího přenosu dat

* Pokud jste škrtili, bude registrována hodnota *pro prodleva příchozího přenosu dat přijaté zprávy,* která vás informuje o tom, kolik sekund za prostředím TIme Series Insights pochází od skutečného okamžiku, kdy zpráva narazí na zdroj události (s výjimkou doby indexování appx. 30-60 sekund).  

  *Prodlevy počtu přijatých zpráv příchozího přenosu dat* by měl mít také hodnotu, což umožňuje určit, kolik zpráv za vámi.  Nejjednodušší způsob, jak se chytit, je zvýšit kapacitu vašeho prostředí na velikost, která vám umožní překonat rozdíl.  

  Například pokud vaše prostředí S1 ukazuje zpoždění 5 000 000 zpráv, můžete zvětšit velikost vašeho prostředí na šest jednotek pro přibližně jeden den uvíznou.  Dalo by se zvýšit ještě dále dohnat rychleji. Doba dohánění je běžný výskyt při počátečním zřizování prostředí, zejména když jej připojíte ke zdroji událostí, který již v něm má události, nebo když hromadně odesíláte velké množství historických dat.

* Další technikou je nastavení **výstrahy ingress stored events** >= prahová hodnota mírně pod celkovou kapacitou prostředí po dobu 2 hodin.  Tato výstraha vám může pomoci pochopit, pokud jste neustále na kapacitě, což znamená vysokou pravděpodobnost latence. 

  Například pokud máte zřízené tři jednotky S1 (nebo 2100 událostí za minutu příchozího přenosu dat kapacity), můžete nastavit **ingress uložené události** upozornění pro >= 1900 událostí po dobu 2 hodin. Pokud neustále překračujete tuto prahovou hodnotu a tím aktivujete výstrahu, pravděpodobně se vám nedaří.  

* Pokud máte podezření, že jste omezeni, můžete porovnat **příchozí přenos dat přijaté zprávy** s odchozí zprávy zdroje událostí.  Pokud příchozí přenos dat do centra událostí je větší než **příchozí přenos dat přijaté zprávy**, časové řady Přehledy jsou pravděpodobně omezeny.

## <a name="improving-performance"></a>Zlepšení výkonu

Chcete-li snížit omezení nebo dochází k latence, nejlepší způsob, jak opravit je zvýšit kapacitu prostředí.

Latenci a omezení se můžete vyhnout správnou konfigurací prostředí pro množství dat, která chcete analyzovat. Další informace o tom, jak přidat kapacitu do prostředí, [našlápnete měřítko prostředí](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [diagnostice a řešení problémů v prostředí Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).

- Přečtěte [si, jak škálovat prostředí Time Series Insights](time-series-insights-how-to-scale-your-environment.md).