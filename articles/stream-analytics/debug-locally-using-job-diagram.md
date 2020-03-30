---
title: Ladění dotazů Azure Stream Analytics místně pomocí diagramu úloh v Sadě Visual Studio
description: Tento článek popisuje, jak ladit dotazy místně pomocí diagramu úloh v Nástrojích Azure Stream Analytics pro Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 106b1f0b765700803d2cd55b5e049fae5be3dfad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76847196"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Ladění dotazů Azure Stream Analytics místně pomocí diagramu úloh v Sadě Visual Studio

Úlohy, které výstup žádný výsledek nebo neočekávané výsledky jsou běžné scénáře řešení potíží pro streamování dotazů. Diagram úloh můžete použít při testování dotazu místně v sadě Visual Studio prozkoumat zprostředkující sadu výsledků a metriky pro každý krok. Diagramy úloh vám mohou pomoci rychle izolovat zdroj problému při řešení problémů.

## <a name="debug-a-query-using-job-diagram"></a>Ladění dotazu pomocí diagramu úloh

Skript Azure Stream Analytics se používá k transformaci vstupních dat do výstupních dat. Diagram úloh ukazuje, jak data toky ze vstupních zdrojů (Event Hub, IoT Hub, atd.) prostřednictvím několika kroků dotazu a nakonec do výstupu propadů. Každý krok dotazu je mapován na dočasnou `WITH` sadu výsledků definovanou ve skriptu pomocí příkazu. Můžete zobrazit data, stejně jako metriky každého kroku dotazu v každé sadě zprostředkující výsledek najít zdroj problému.

> [!NOTE]
> Tento diagram úloh zobrazuje pouze data a metriky pro místní testování v jednom uzlu. Neměl by být používán pro ladění výkonu a řešení potíží.

### <a name="start-local-testing"></a>Spustit místní testování

Pomocí tohoto [úvodního panelu](stream-analytics-quick-create-vs.md) se dozvíte, jak vytvořit úlohu Stream Analytics pomocí sady Visual Studio nebo [exportovat existující úlohu do místního projektu](stream-analytics-vs-tools.md#export-jobs-to-a-project). Chcete-li dotaz otestovat pomocí místních vstupních dat, postupujte podle těchto [pokynů](stream-analytics-live-data-local-testing.md). Pokud chcete testovat s živým vstupem, přejděte k dalšímu kroku.

> [!NOTE]
> Pokud exportujete úlohu do místního projektu a chcete testovat proti živému vstupnímu datovému proudu, musíte znovu zadat pověření pro všechny vstupy.  

Zvolte vstupní a výstupní zdroj z editoru skriptů a vyberte **Spustit místně**. Diagram úloh se zobrazí na pravé straně.

### <a name="view-the-intermediate-result-set"></a>Zobrazit průběžnou sadu výsledků  

1. Vyberte krok dotazu pro přechod na skript. Budete automaticky přesměrováni na odpovídající skript v editoru vlevo.

   ![Skript navigace diagramu úloh](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Vyberte krok dotazu a v dialogovém okně Vyskočilo vyberte **Náhled.** Sada výsledků se zobrazí na kartě v dolním okně výsledků.

   ![Výsledek náhledu diagramu úlohy](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Zobrazit metriky kroku

V této části se podíváte na metriky, které jsou k dispozici pro každou část diagramu.

#### <a name="input-sources-live-stream"></a>Vstupní zdroje (živý přenos)

![Zdroje živého zadávání diagramu úloh](./media/debug-locally-using-job-diagram/live-input.png)

|Metrika|Popis|
|-|-|
|**TaxiRideRide**| Název vstupu.|
|**Centrum událostí** | Typ vstupního zdroje.|
|**Události**|Počet přečtených událostí.|
|**Backlogged zdroje událostí**|Kolik dalších zpráv je potřeba číst pro události huby a ioT hub vstupy.|
|**Události v byty**|Počet přečtených bajtů.|
| **Zhoršené události**|Počet událostí, které měly problém než s rekonstrukcí.|
|**Rané události**| Počet událostí, které mají časové razítko aplikace před vysokým vodoznakem.|
|**Pozdní události**| Počet událostí, které mají časové razítko aplikace za vysokým vodoznakem.|
|**Zdroje událostí**| Počet čtených datových jednotek. Například počet objektů BLOB.|

#### <a name="input-sources-local-input"></a>Vstupní zdroje (místní vstup)

![Místní vstupní zdroje diagramu úloh](./media/debug-locally-using-job-diagram/local-input.png)

|Metrika|Popis|
|-|-|
|**TaxiRideRide**| Název vstupu.|
|**Row Count**| Počet řádků generovaných z kroku.|
|**Velikost dat**| Velikost dat generovaných z tohoto kroku.|
|**Místní vstup**| Jako vstup použijte místní data.|

#### <a name="query-steps"></a>Kroky dotazu

![Krok dotazu diagramu úlohy](./media/debug-locally-using-job-diagram/query-step.png)

|Metrika|Popis|
|-|-|
|**TripData**|Název dočasné sady výsledků.|
|**Row Count**| Počet řádků generovaných z kroku.|
|**Velikost dat**| Velikost dat generovaných z tohoto kroku.|
  
#### <a name="output-sinks-live-output"></a>Výstupní propady (živý výstup)

![Místní propady výstupu diagramu úloh](./media/debug-locally-using-job-diagram/live-output.png)

|Metrika|Popis|
|-|-|
|**regionaggEH**|Název výstupu.|
|**Události**|Počet událostí výstupu jímky.|

#### <a name="output-sinks-local-output"></a>Výstupní propady (místní výstup)

![Místní propady výstupu diagramu úloh](./media/debug-locally-using-job-diagram/local-output.png)

|Metrika|Popis|
|-|-|
|**regionaggEH**|Název výstupu.|
|**Místní výstup**| Výstup výsledku do místního souboru.|
|**Row Count**| Počet řádků výstupu do místního souboru.|
|**Velikost dat**| Velikost výstupu dat do místního souboru.|

### <a name="close-job-diagram"></a>Zavřít diagram úlohy

Pokud už diagram úloh nepotřebujete, v pravém horním rohu vyberte **Zavřít.** Po zavření okna diagramu je třeba znovu spustit místní testování, abyste ho viděli.

### <a name="view-job-level-metrics-and-stop-running"></a>Zobrazit metriky úrovně úlohy a zastavit spuštění

Další metriky úrovně úloh se zobrazují v rozbalovací konzoli. Chcete-li úlohu zastavit, stiskněte v konzole **kombinaci kláves Ctrl+C.**

![Úloha zastavení úlohy](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Omezení

* Výstupní propady Power BI a Azure Data Lake Storage Gen1 nejsou podporované z důvodu omezení modelu ověřování.

* Pouze možnosti vstupu do cloudu mají podporu [zásad času,](stream-analytics-out-of-order-and-late-events.md) zatímco místní vstupní možnosti nemají.

## <a name="next-steps"></a>Další kroky

* [Úvodní příručka: Vytvoření úlohy Stream Analytics pomocí Visual Studia](stream-analytics-quick-create-vs.md)
* [Zobrazení úloh Azure Stream Analytics pomocí Visual Studia](stream-analytics-vs-tools.md)
* [Testování živých dat místně pomocí nástrojů Azure Stream Analytics pro Visual Studio (Preview)](stream-analytics-live-data-local-testing.md)
