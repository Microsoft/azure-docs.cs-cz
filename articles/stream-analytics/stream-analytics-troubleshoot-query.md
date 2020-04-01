---
title: Poradce při potížích s dotazy Azure Stream Analytics
description: Tento článek popisuje techniky řešení vašich dotazů v azure stream analytics úlohy.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: f049dc6d1261a8201cf79d1779e522b30d13c4b0
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409441"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Poradce při potížích s dotazy Azure Stream Analytics

Tento článek popisuje běžné problémy s vývojem dotazů Stream Analytics a jak je řešit.

Tento článek popisuje běžné problémy s vývojem dotazů Azure Stream Analytics, jak řešit problémy s dotazy a jak opravit problémy. Mnoho kroků řešení potíží vyžaduje, aby byly pro úlohu Stream Analytics povoleny diagnostické protokoly. Pokud nemáte povolené diagnostické protokoly, [přečtěte si článek Poradce při potížích s Azure Stream Analytics pomocí diagnostických protokolů](stream-analytics-job-diagnostic-logs.md).

## <a name="query-is-not-producing-expected-output"></a>Dotaz nevytváří očekávaný výstup

1.  Zkontrolujte chyby testováním místně:

    - Na webu Azure Portal na kartě **Dotaz** vyberte **Testovat**. Pomocí stažených ukázkových dat [otestujte dotaz](stream-analytics-test-query.md). Zkontrolujte všechny chyby a pokuste se je opravit.   
    - Dotaz můžete také [otestovat místně](stream-analytics-live-data-local-testing.md) pomocí nástrojů Azure Stream Analytics pro Visual Studio nebo [Visual Studio Code](visual-studio-code-local-run-live-input.md). 

2.  [Ladění dotazů krok za krokem místně pomocí diagramu úloh](debug-locally-using-job-diagram.md) v nástrojích Azure Stream Analytics pro Visual Studio. Diagram úloh ukazuje, jak data toky ze vstupních zdrojů (Event Hub, IoT Hub, atd.) prostřednictvím několika kroků dotazu a nakonec do výstupu propadů. Každý krok dotazu je mapován na dočasnou sadu výsledků definovanou ve skriptu pomocí příkazu WITH. Můžete zobrazit data, stejně jako metriky, v každé sadě zprostředkující výsledek najít zdroj problému.

    ![Výsledek náhledu diagramu úlohy](./media/debug-locally-using-job-diagram/preview-result.png)

3.  [**Používáte-li časové razítko podle**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), ověřte, zda mají události časová razítka větší než [čas zahájení úlohy](stream-analytics-out-of-order-and-late-events.md).

4.  Eliminujte běžné nástrahy, například:
    - Klauzule [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) v dotazu odfiltrovala všechny události a zabránila generování výstupu.
    - Funkce [**CAST**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) se nezdaří, což způsobuje selhání úlohy. Chcete-li se vyhnout selhání přetypovaného typu, použijte místo toho [**TRY_CAST.**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics)
    - Při použití funkce okna, počkejte na celé okno trvání zobrazí výstup z dotazu.
    - Časové razítko pro události předchází čas zahájení úlohy a události jsou vynechány.
    - [**Podmínky JOIN**](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) se neshodují. Pokud neexistují žádné shody, bude nulový výstup.

5.  Ujistěte se, že zásady řazení událostí jsou nakonfigurovány podle očekávání. Přejděte do **nastavení** a vyberte [**pořadí událostí**](stream-analytics-out-of-order-and-late-events.md). Zásada *není* použita při použití tlačítka **Test** k testování dotazu. Tento výsledek je jeden rozdíl mezi testování v prohlížeči versus spuštění úlohy v produkčním prostředí. 

6. Ladění pomocí protokolů auditování a diagnostiky:
    - Pomocí [protokolů auditování](../azure-resource-manager/resource-group-audit.md)a filtru k identifikaci a ladění chyb.
    - Pomocí [protokolů diagnostiky úloh](stream-analytics-job-diagnostic-logs.md) identifikujte a ladte chyby.

## <a name="resource-utilization-is-high"></a>Využití zdrojů je vysoké

Ujistěte se, že využíváte paralelizace v Azure Stream Analytics. Můžete se naučit [škálovat pomocí paralelizace dotazů](stream-analytics-parallelization.md) úloh Stream Analytics konfigurací vstupních oddílů a optimalizací definice analytického dotazu.

## <a name="debug-queries-progressively"></a>Postupné ladění dotazů

Při zpracování dat v reálném čase může být užitečné vědět, jak data vypadají uprostřed dotazu. Můžete to vidět pomocí diagramu úloh v sadě Visual Studio. Pokud nemáte Visual Studio, můžete provést další kroky k výstupu zprostředkující data.

Vzhledem k tomu, že vstupy nebo kroky úlohy Azure Stream Analytics lze číst vícekrát, můžete napsat další příkazy SELECT INTO. Tím výstupy zprostředkující data do úložiště a umožňuje kontrolovat správnost dat, stejně jako *sledovat proměnné* dělat při ladění programu.

Následující ukázkový dotaz v úloze Azure Stream Analytics má jeden vstup datového proudu, dva vstupy referenčních dat a výstup do Azure Table Storage. Dotaz spojuje data z centra událostí a dva referenční objekty BLOB, aby získal informace o názvu a kategorii:

![Příklad streamové analýzy VÝBĚR DO dotazu](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Všimněte si, že úloha je spuštěna, ale ve výstupu nejsou vytvářeny žádné události. Na dlaždici **Monitorování,** která je zde zobrazena, můžete vidět, že vstup vytváří data, ale nevíte, který krok **spojení** způsobil, že všechny události byly vynechány.

![Dlaždice sledování analýzy datového proudu](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

V takovém případě můžete přidat několik dalších SELECT INTO příkazy "protokolu" průběžné join výsledky a data, která je čtení ze vstupu.

V tomto příkladu jsme přidali dva nové "dočasné výstupy.". Mohou být jakékoliv umyvadlo se vám líbí. Zde používáme Azure Storage jako příklad:

![Přidání dalších příkazů SELECT INTO do dotazu Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Dotaz pak můžete přepsat takto:

![Přepsaný dotaz SELECT INTO Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Nyní spusťte úlohu znovu a nechte ji několik minut běžet. Pak dotaz temp1 a temp2 s Visual Studio Cloud Explorer k vytvoření následující tabulky:

**temp1 tabulka**
![SELECT INTO temp1 tabulka Stream Analytics dotaz](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 tabulka**
![SELECT INTO temp2 tabulka Stream Analytics dotaz](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Jak můžete vidět, temp1 a temp2 oba mají data a název sloupec je vyplněn správně temp2. Nicméně, protože ve výstupu stále nejsou žádná data, je něco špatně:

![VYBRAT tabulku výstupu1 bez dotazu Data Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Vzorkováním dat si můžete být téměř jisti, že problém je s druhým join. Referenční data si můžete stáhnout z objektu blob a podívat se:

![Vybrat do ref tabulka Stream Analytics dotaz](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Jak můžete vidět, formát IDENTIFIKÁTORU GUID v těchto referenčních datech se liší od formátu sloupce [from] v temp2. To je důvod, proč data nedorazila ve výstupu1 podle očekávání.

Formát dat můžete opravit, nahrát do referenčního objektu blob a zkusit to znovu:

![VYBRAT DO dotazu dočasné tabulky Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Tentokrát jsou data ve výstupu formátována a naplněna podle očekávání.

![Výběr do dotazu Stream Analytics v konečné tabulce](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Podpora

Další pomoc našlápneme na fórum [Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
