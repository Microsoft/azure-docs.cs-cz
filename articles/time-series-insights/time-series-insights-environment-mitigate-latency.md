---
title: Jak monitorovat a snížení šířky pásma v Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak monitorování, diagnostikovat a zmírnit problémy s výkonem, které způsobují latenci a omezování v Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/27/2017
ms.openlocfilehash: a404eb1393f9e99c2e2932c2d23724051f1b72a0
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628483"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Monitorování a zmírnění omezování snížit latenci v Azure Time Series Insights
Pokud objem příchozích dat překročí konfiguraci vašeho prostředí, můžete setkat s latencí nebo omezení využití sítě v Azure Time Series Insights.

Můžete vyhnout latenci a omezování tím, že správně nakonfigurujete prostředí objem dat, který chcete analyzovat.

Které bývají nejčastějším docházet k latenci a omezování, když jste:

- Přidání zdroje událostí, který obsahuje původní data, která může být delší než vaše frekvence přiděleného příchozího přenosu dat (dohnat se musí Time Series Insights).
- Přidáte další zdroje událostí do prostředí, což vede k zásobníku z další události (které by mohla překročit kapacitu vašeho prostředí).
- Nahrání velkého množství historických událostí do zdroje událostí, což vede k prodlevě (Time Series Insights potřebovat dohnat).
- Připojte se k referenčních dat pomocí telemetrie, což vede k větší velikost události.  Z hlediska omezení paket ingressed data s velikost paketu 32 kB považuje za 32 události, každý velikosti 1 KB. Události maximální povolená velikost je 32 KB; datových paketů větší než 32 KB se oříznou.


## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitorování latenci a omezování s výstrahami

Výstrahy můžete pomoci diagnostikovat a zmírnit problémy s latencí způsobené vašeho prostředí. 

1. Na webu Azure Portal, klikněte na tlačítko **metriky**. 

   ![Metriky](media/environment-mitigate-latency/add-metrics.png)

2. Klikněte na tlačítko **přidat upozornění metriky**.  

    ![Přidat upozornění metriky](media/environment-mitigate-latency/add-metric-alert.png)

Odtud můžete nakonfigurovat výstrahy pomocí následující metriky:

|Metrika  |Popis  |
|---------|---------|
|**Příchozí přenos dat přijatých bajtů**     | Počet nezpracovaných Bajty čtení z zdroje událostí. Počet nezpracovaných obvykle zahrnuje název vlastnosti a hodnotu.  |  
|**Příchozí přenos dat přijatých neplatné zprávy**     | Počet zpráv neplatná číst ze všech zdrojů událostí služby Azure Event Hubs nebo Azure IoT Hub.      |
|**Příchozí přenos dat přijatých zpráv**   | Počet zpráv, které číst ze zdroje událostí pro všechny služby Event Hubs a centra IoT hub.        |
|**Příchozí přenos dat uložené bajtů**     | Celková velikost událostí uložených a k dispozici pro dotaz. Velikost je vypočítán pouze na hodnotu vlastnosti.        |
|**Příchozí přenos dat uložených událostí**     |   Počet plochá událostí uložených a k dispozici pro dotaz.      |
|**Příchozí přenos dat přijaté zprávy časový interval**    |  Zdroj a čas, kdy se zpracovávají v příchozího přenosu dat, rozdíl v sekundách mezi časem, že je zpráva událostí zařazených do fronty.      |
|**Prodleva počet zpráv přijatých příchozího přenosu dat**    |  Rozdíl mezi pořadové číslo poslední zprávy ve frontě událostí zdroje oddílu a pořadovým číslem zprávy jsou zpracovávány v příchozí.      |


![Latence](media/environment-mitigate-latency/latency.png)

Pokud jste se omezují, zobrazí se hodnota pro *příchozího přenosu dat přijatých zpráv časová prodleva*, vás informuje o tom, kolik sekund za TSI pochází z skutečný čas zprávy narazí na zdroj události (s výjimkou indexování čas appx. 30 – 60 sekund).  *Prodleva počet zpráv přijatých příchozího přenosu dat* by měl také obsahovat hodnotu, umožňuje určit, kolik zpráv za vás.  Nejjednodušší způsob, jak získat zachycena je zvýšení kapacity pro vaše prostředí na hodnotu, která vám umožní vám pomohou překonat rozdíl.  

Například pokud máte jednu jednotku S1 prostředí a podívejte se, že je prodleva pět milionů zpráv, může zvýšit velikost prostředí šest jednotky pro kolem denně k získání zachycena.  Vám může zlepšit i dál catch nahoru rychleji.  Můžete projít období je společného výskytu při počátečním zřizování prostředí, zejména při připojení ke zdroji událostí, který již v sobě obsahuje události nebo když hromadné nahrání spoustu historická data.

Další technikou je nastavit **uložených událostí příchozího přenosu dat** výstrahu > = prahovou hodnotu mírně pod vaší kapacity celkové prostředí po dobu 2 hodin.  Toto upozornění můžete zjistit, jestli je neustále plně využívá kapacitu, který označuje vysokou pravděpodobnost latence.  

Například pokud máte tři jednotky S1 zřízené (nebo 2100 událostí za kapacitu minuta příchozího přenosu dat), můžete nastavit **uložených událostí příchozího přenosu dat** výstrah pro > = 1900 události po dobu 2 hodin. Pokud jsou neustále překročení této mezní hodnoty a proto se aktivuje upozornění, je pro vás pravděpodobně v rámci zřízený.  

Také, pokud máte podezření, se omezují, můžete porovnat vaše **příchozího přenosu dat přijatých zpráv** pomocí události uživatele egressed zdroj zprávy.  Pokud příchozí přenos dat do vašeho centra událostí je větší než vaše **příchozího přenosu dat přijatých zpráv**, služby Time Series Insights jsou pravděpodobně omezené.

## <a name="improving-performance"></a>Zvýšení výkonu 
Ke snížení omezení šířky pásma a latence, je nejlepší způsob, jak ho opravit zvýšit kapacitu vašeho prostředí. 

Můžete vyhnout latenci a omezování tím, že správně nakonfigurujete prostředí objem dat, který chcete analyzovat. Další informace o tom, jak přidat kapacitu do svého prostředí najdete v tématu [škálování prostředí](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Další postup
- Další postup řešení potíží [diagnostikovat a řešit problémy ve vašem prostředí Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).
- Další pomoc, zahájit konverzaci na [fórum na webu MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) nebo [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). Taky se můžete obrátit [podpory Azure](https://azure.microsoft.com/support/options/) pro možnosti podpory s asistencí.
