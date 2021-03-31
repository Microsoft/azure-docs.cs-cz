---
title: Ladění Azure Stream Analytics dotazů místně pomocí diagramu úloh v Visual Studio Code
description: Tento článek popisuje, jak místně ladit dotazy pomocí diagramu úloh v rozšíření Azure Stream Analytics pro Visual Studio Code.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 25ad696ad345fbf672f6bf26eb3f35a13fb03ea5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019495"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>Ladění Azure Stream Analytics dotazů místně pomocí diagramu úloh v Visual Studio Code

Úlohy streamování bez výsledku nebo neočekávaných výsledků často potřebují řešit problémy. Rozšíření Visual Studio Code pro Azure Stream Analytics integruje diagramy úloh, metriky, diagnostické protokoly a mezilehlé výsledky, které vám pomůžou rychle izolovat zdroj problému. Diagram úloh můžete použít k místnímu testování dotazu pro kontrolu mezilehlé sady výsledků dotazu a metriky pro každý krok.

## <a name="debug-a-query-using-job-diagram"></a>Ladění dotazu pomocí diagramu úloh

Skript Azure Stream Analytics slouží k transformaci vstupních dat na výstupní data. Diagram úlohy ukazuje, jak toky dat ze vstupních zdrojů, jako je centrum událostí nebo IoT Hub, prostřednictvím více kroků dotazování na výstupní jímky. Každý krok dotazu je namapován na dočasnou sadu výsledků definovanou ve skriptu pomocí `WITH` příkazu. Můžete zobrazit data a také metriky jednotlivých kroků dotazu v každé mezilehlé sadě výsledků, abyste našli zdroj problému.

> [!NOTE]
> Tento diagram úlohy zobrazuje pouze data a metriky pro místní testování v jednom uzlu. Neměl by se používat k ladění výkonu a odstraňování potíží.

### <a name="start-local-testing"></a>Spustit místní testování

V tomto [rychlém](quick-create-visual-studio-code.md) startu se dozvíte, jak vytvořit úlohu Stream Analytics pomocí Visual Studio Code nebo [Jak exportovat existující úlohu do místního projektu](visual-studio-code-explore-jobs.md). Pro exportované úlohy se automaticky naplní přihlašovací údaje pro vstupy a výstupy.

Pokud chcete otestovat dotaz pomocí místních vstupních dat, postupujte podle těchto [pokynů](visual-studio-code-local-run.md). Pokud chcete provést test pomocí živého vstupu, [nakonfigurujte vstupní](stream-analytics-add-inputs.md) přesun na další krok. 

Otevřete soubor skriptu *\. asaql* a vyberte **spustit místně**. Pak vyberte **použít místní vstup** nebo **použít živý vstup**. Diagram úlohy se zobrazí na pravé straně okna.

### <a name="view-the-output-and-intermediate-result-set"></a>Zobrazit výstupní a průběžnou sadu výsledků  

1. Všechny výstupy úlohy se zobrazí v okně výsledků v pravém dolním rohu okna Visual Studio Code.

   > [!div class="mx-imgBorder"]
   > ![Výsledky výstupu úlohy](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. Vyberte krok dotazu pro přechod ke skriptu. Automaticky jste přesměrováni na odpovídající skript v editoru na levé straně. Mezilehlé výsledky se zobrazí v okně výsledků v pravém dolním rohu okna Visual Studio Code.

   > [!div class="mx-imgBorder"]
   > ![Výsledek náhledu diagramu úlohy](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>Zobrazit metriky

V této části prozkoumáte metriky, které jsou k dispozici pro každou část diagramu.

1. Vyberte kartu **metriky** vedle karty **výsledek** v pravém dolním rohu okna Visual Studio Code.

2. V rozevíracím seznamu vyberte **úlohu** . Můžete vybrat libovolné prázdné místo v uzlu grafu a přejít tak na metriky na úrovni úlohy. Toto zobrazení obsahuje všechny metriky, které se aktualizují každých 10 sekund, když je úloha spuštěná. Můžete vybrat nebo zrušit výběr metrik na pravé straně, abyste je mohli zobrazit v grafech.

   > [!div class="mx-imgBorder"]
   > ![Metriky diagramu úloh](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. Pokud chcete zobrazit vstupní metriky, vyberte v rozevíracím seznamu název vstupního zdroje dat. Zdroj vstupu na snímku obrazovky níže se nazývá *uvozovky*. Další informace o vstupních metrikách najdete v tématu [pochopení Stream Analytics monitorování úloh a postup monitorování dotazů](stream-analytics-monitoring.md).

   > [!div class="mx-imgBorder"]
   > ![Vstupní metrika diagramu úloh](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. Vyberte krok dotazu z diagramu úlohy nebo v rozevíracím seznamu vyberte název kroku, abyste viděli metriky na úrovni kroku. Zpoždění vodoznaku je jediná dostupná metrika kroku.

   > [!div class="mx-imgBorder"]
   > ![Krokovat metriky](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. Vyberte výstup v diagramu nebo v rozevírací nabídce pro zobrazení metrik souvisejících s výstupem. Další informace o výstupních metrikách najdete v tématu [pochopení Stream Analytics monitorování úloh a postup monitorování dotazů](stream-analytics-monitoring.md). Živé výstupní jímky nejsou podporované.

   > [!div class="mx-imgBorder"]
   > ![Výstupní metriky](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>Zobrazit diagnostické protokoly

Diagnostické protokoly na úrovni úlohy obsahují diagnostické informace pro vstupní zdroje dat a výstupní jímky. Když vyberete vstupní uzel nebo výstupní uzel, zobrazí se pouze odpovídající protokoly. Pokud vyberete krok dotazu, nezobrazí se žádné protokoly. Všechny protokoly můžete najít na úrovni úlohy a protokoly můžete filtrovat podle závažnosti a času.

   > [!div class="mx-imgBorder"]
   > ![Diagnostické protokoly](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   Vyberte položku protokolu pro zobrazení celé zprávy.

   > [!div class="mx-imgBorder"]
   > ![Zpráva diagnostické protokoly](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>Další funkce diagramu úloh

Podle potřeby můžete na panelu nástrojů vybrat **zastavit** nebo **pozastavit** . Jakmile je úloha pozastavena, můžete ji obnovit z posledního výstupu.

> [!div class="mx-imgBorder"]
> ![Zastavit nebo pozastavit úlohu](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

Vyberte **Souhrn úlohy** v pravém horním rohu diagramu úlohy, abyste viděli vlastnosti a konfigurace pro místní úlohu.

> [!div class="mx-imgBorder"]
> ![Souhrn místní úlohy](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>Omezení

* Živé výstupní jímky nejsou v místním spuštění podporovány.

* Úlohu spustit místně pomocí JavaScriptu Function je podporována pouze v operačním systému Windows.

* Vlastní kód a Azure Machine Learning funkce jazyka C# nejsou podporovány. 

* Pouze možnosti cloudového vstupu mají podporu [časových zásad](./stream-analytics-time-handling.md) , zatímco možnosti místního vstupu ne.

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Visual Studio Code](quick-create-visual-studio-code.md)
* [Prozkoumejte Azure Stream Analytics s Visual Studio Code](visual-studio-code-explore-jobs.md)
* [Test Stream Analytics dotazy místně s použitím ukázkových dat pomocí Visual Studio Code](visual-studio-code-local-run.md)
* [Testování Azure Stream Analytics úloh místně pomocí živého vstupu pomocí Visual Studio Code](visual-studio-code-local-run-live-input.md)