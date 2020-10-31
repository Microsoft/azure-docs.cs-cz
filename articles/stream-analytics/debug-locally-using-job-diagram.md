---
title: Ladění Azure Stream Analytics dotazů místně pomocí diagramu úloh v aplikaci Visual Studio
description: Tento článek popisuje, jak místně ladit dotazy pomocí diagramu úloh v Azure Stream Analyticsch nástrojích pro Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: c32ba468979e4566d8e349d481eddca7d407b659
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130982"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Ladění Azure Stream Analytics dotazů místně pomocí diagramu úloh v aplikaci Visual Studio

Úlohy, které mají výstup bez výsledku nebo neočekávané výsledky, jsou běžnými scénáři řešení potíží pro streamování dotazů. Diagram úlohy můžete použít při testování dotazu místně v sadě Visual Studio, abyste prozkoumali mezilehlé sady výsledků a metriky pro jednotlivé kroky. Diagramy úloh vám můžou pomoct rychle izolovat zdroj problému při řešení problémů.

## <a name="debug-a-query-using-job-diagram"></a>Ladění dotazu pomocí diagramu úloh

Skript Azure Stream Analytics slouží k transformaci vstupních dat na výstupní data. Diagram úlohy ukazuje, jak toky dat ze vstupních zdrojů (centra událostí, IoT Hub atd.) prostřednictvím více kroků dotazů a nakonec až po výstupní jímky. Každý krok dotazu je namapován na dočasnou sadu výsledků definovanou ve skriptu pomocí `WITH` příkazu. Můžete zobrazit data a také metriky jednotlivých kroků dotazu v každé mezilehlé sadě výsledků, abyste našli zdroj problému.

> [!NOTE]
> Tento diagram úlohy zobrazuje pouze data a metriky pro místní testování v jednom uzlu. Neměl by se používat k ladění výkonu a odstraňování potíží.

### <a name="start-local-testing"></a>Spustit místní testování

V tomto [rychlém](stream-analytics-quick-create-vs.md) startu se dozvíte, jak vytvořit úlohu Stream Analytics pomocí sady Visual Studio nebo [Jak exportovat existující úlohu do místního projektu](stream-analytics-vs-tools.md#export-jobs-to-a-project). Pokud chcete otestovat dotaz pomocí místních vstupních dat, postupujte podle těchto [pokynů](stream-analytics-live-data-local-testing.md). Pokud chcete provést test pomocí živého vstupu, přejděte k dalšímu kroku.

> [!NOTE]
> Pokud exportujete úlohu do místního projektu a chcete provést test proti živému vstupnímu streamu, musíte zadat přihlašovací údaje pro všechny vstupy znovu.  

V editoru skriptů zvolte vstupní a výstupní zdroj a vyberte **spustit místně** . Diagram úlohy se zobrazí na pravé straně.

### <a name="view-the-intermediate-result-set"></a>Zobrazit mezilehlé sady výsledků  

1. Vyberte krok dotazu pro přechod ke skriptu. Automaticky se přesměruje na odpovídající skript v editoru na levé straně.

   ![Skript pro procházení diagramu úloh](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Vyberte krok dotazu a v dialogovém okně vyberte **Náhled** . Sada výsledků se zobrazí na kartě v dolním okně výsledků.

   ![Výsledek náhledu diagramu úlohy](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Zobrazit metriky kroků

V této části prozkoumáte metriky, které jsou k dispozici pro každou část diagramu.

#### <a name="input-sources-live-stream"></a>Vstupní zdroje (živý datový proud)

![Zdroje dynamických vstupních zdrojů diagramu úloh](./media/debug-locally-using-job-diagram/live-input.png)

|Metrika|Popis|
|-|-|
|**TaxiRide**| Název vstupu.|
|**Centrum událostí** | Vstupní typ zdroje.|
|**Události**|Počet přečtených událostí.|
|**Nevyřízené zdroje událostí**|Kolik dalších zpráv je potřeba číst pro Event Hubs a IoT Hub vstupy.|
|**Události v bajtech**|Počet přečtených bajtů.|
| **Omezené události**|Počet událostí, které mají jiný problém než s deserializací.|
|**Předčasné události**| Počet událostí, které mají časové razítko aplikace před horním limitem.|
|**Zpožděné události**| Počet událostí, které mají časové razítko aplikace za horním limitem.|
|**Zdroje událostí**| Počet přečtených datových jednotek. Například počet objektů BLOB.|

#### <a name="input-sources-local-input"></a>Vstupní zdroje (místní vstup)

![Zdroje místních vstupů diagramu úloh](./media/debug-locally-using-job-diagram/local-input.png)

|Metrika|Popis|
|-|-|
|**TaxiRide**| Název vstupu.|
|**Row Count**| Počet řádků vygenerovaných z kroku.|
|**Velikost dat**| Velikost dat vygenerovaných z tohoto kroku.|
|**Místní vstup**| Použijte místní data jako vstup.|

#### <a name="query-steps"></a>Kroky dotazu

![Krok dotazu na diagram úlohy](./media/debug-locally-using-job-diagram/query-step.png)

|Metrika|Popis|
|-|-|
|**TripData**|Název dočasné sady výsledků.|
|**Row Count**| Počet řádků vygenerovaných z kroku.|
|**Velikost dat**| Velikost dat vygenerovaných z tohoto kroku.|
  
#### <a name="output-sinks-live-output"></a>Výstupní jímky (živý výstup)

![Diagram úlohy, který zobrazuje místní výstupní jímky.](./media/debug-locally-using-job-diagram/live-output.png)

|Metrika|Popis|
|-|-|
|**regionaggEH**|Název výstupu.|
|**Události**|Počet událostí výstupu do jímky.|

#### <a name="output-sinks-local-output"></a>Výstupní jímky (místní výstup)

![Místní výstupní jímky diagramu úloh](./media/debug-locally-using-job-diagram/local-output.png)

|Metrika|Popis|
|-|-|
|**regionaggEH**|Název výstupu.|
|**Místní výstup**| Výstup výsledku do místního souboru.|
|**Row Count**| Počet výstupních řádků do místního souboru.|
|**Velikost dat**| Velikost výstupu dat do místního souboru.|

### <a name="close-job-diagram"></a>Zavřít diagram úlohy

Pokud diagram úlohy již nepotřebujete, v pravém horním rohu vyberte **Zavřít** . Po zavření okna diagramu je třeba znovu spustit místní testování, aby se zobrazilo.

### <a name="view-job-level-metrics-and-stop-running"></a>Zobrazení metrik na úrovni úlohy a zastavení běhu

Další metriky na úrovni úlohy se zobrazí v místní konzole. Pokud chcete úlohu zastavit, stiskněte **CTRL + C** v konzole.

![Úloha zastavení diagramu úlohy](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Omezení

* Výstupní jímky Power BI a Azure Data Lake Storage Gen1 nejsou v důsledku omezení modelu ověřování podporované.

* Pouze možnosti cloudového vstupu mají podporu [časových zásad](./stream-analytics-time-handling.md) , zatímco možnosti místního vstupu ne.

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Použití sady Visual Studio k zobrazení Azure Stream Analyticsch úloh](stream-analytics-vs-tools.md)
* [Místní testování živých dat pomocí Azure Stream Analyticsch nástrojů pro Visual Studio (Preview)](stream-analytics-live-data-local-testing.md)