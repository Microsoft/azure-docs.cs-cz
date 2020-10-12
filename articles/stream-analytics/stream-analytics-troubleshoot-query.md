---
title: Řešení potíží s Azure Stream Analytics dotazy
description: Tento článek popisuje techniky řešení potíží s dotazy v úlohách Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: ead175cbcaa9467cb5263ad95100facdda096991
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87337802"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Řešení potíží s Azure Stream Analytics dotazy

Tento článek popisuje běžné problémy při vývoji Stream Analytics dotazů a jejich řešení.

Tento článek popisuje běžné problémy s vývojem Azure Stream Analyticsch dotazů, odstraňování potíží s dotazy a postup řešení problémů. Mnoho kroků pro řešení potíží vyžaduje, aby byly pro vaši úlohu Stream Analytics Povolené protokoly prostředků. Pokud nemáte Povolené protokoly prostředků, přečtěte si téma [řešení potíží s Azure Stream Analytics pomocí protokolů prostředků](stream-analytics-job-diagnostic-logs.md).

## <a name="query-is-not-producing-expected-output"></a>Dotaz nevyrábí očekávaný výstup.

1.  Kontrola chyb pomocí místního testování:

    - Na Azure Portal na kartě **dotaz** vyberte **test**. K [otestování dotazu](stream-analytics-test-query.md)použijte stažená ukázková data. Prověřte případné chyby a pokuste se je opravit.   
    - Dotaz můžete také [místně otestovat](stream-analytics-live-data-local-testing.md) pomocí Azure Stream Analyticsch nástrojů pro aplikaci Visual Studio nebo [Visual Studio Code](visual-studio-code-local-run-live-input.md). 

2.  [Ladit dotazy krok za krokem místně pomocí diagramu úloh](debug-locally-using-job-diagram-vs-code.md) v Azure Stream Analytics nástrojů pro Visual Studio Code. Diagram úlohy ukazuje, jak toky dat ze vstupních zdrojů (centra událostí, IoT Hub atd.) prostřednictvím více kroků dotazů a nakonec až po výstupní jímky. Každý krok dotazu je namapován na dočasnou sadu výsledků definovanou ve skriptu pomocí příkazu WITH. Můžete zobrazit data a také metriky v každé mezilehlé sadě výsledků, abyste našli zdroj problému.

    ![Výsledek náhledu diagramu úlohy](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

3.  Pokud používáte [**časové razítko**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), ověřte, zda mají události časové razítko větší, než je [čas spuštění úlohy](stream-analytics-out-of-order-and-late-events.md).

4.  Odstraňte běžné nástrah, například:
    - Klauzule [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) v dotazu vyfiltroval všechny události, což znemožňuje vygenerování všech výstupů.
    - Funkce [**cast**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) selže, což způsobí selhání úlohy. Chcete-li se vyhnout chybám přetypování typů, použijte místo toho [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics) .
    - Při použití funkcí okna počkejte na celou dobu trvání okna, aby se zobrazil výstup dotazu.
    - Časové razítko pro události předchází času zahájení úlohy a události jsou vyřazeny.
    - Podmínky [**spojení**](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) se neshodují. Pokud se žádné shody neshodují, bude nulový výstup.

5.  Zajistěte, aby byly zásady řazení událostí nakonfigurované podle očekávání. Přejít na **Nastavení** a vybrat [**řazení událostí**](stream-analytics-out-of-order-and-late-events.md). Zásada *se nepoužije,* když použijete tlačítko **test** k otestování dotazu. Výsledkem je jeden rozdíl mezi testováním v prohlížeči a spuštění úlohy v produkčním prostředí. 

6. Ladění pomocí protokolů aktivit a prostředků:
    - Použijte [protokoly aktivit](../azure-resource-manager/resource-group-audit.md)a filtrujte k identifikaci a ladění chyb.
    - K identifikaci a ladění chyb použijte [protokoly prostředků úloh](stream-analytics-job-diagnostic-logs.md) .

## <a name="resource-utilization-is-high"></a>Využití prostředků je vysoké.

Zajistěte, abyste využili výhod paralelního využívání Azure Stream Analytics. Můžete se dozvědět, jak [škálovat pomocí paralelního zpracování dotazů](stream-analytics-parallelization.md) Stream Analytics úloh, a to konfigurací vstupních oddílů a optimalizací definice analytického dotazu.

## <a name="debug-queries-progressively"></a>Postupné ladění dotazů

Při zpracování dat v reálném čase může být užitečné, aby data vypadala uprostřed dotazu. To můžete zobrazit pomocí diagramu úloh v aplikaci Visual Studio. Pokud nemáte aplikaci Visual Studio, můžete provést další kroky pro výstup mezilehlých dat.

Vzhledem k tomu, že vstupy nebo kroky Azure Stream Analytics úlohy je možné přečíst vícekrát, můžete napsat nadbytečné příkazy SELECT INTO. Tím dojde k výstupování mezilehlých dat do úložiště a umožní vám zkontrolovat správnost dat, stejně jako *sledované proměnné* , při ladění programu.

Následující příklad dotazu v úloze Azure Stream Analytics má jeden vstup streamu, dva vstupy referenčních dat a výstup do Azure Table Storage. Dotaz spojuje data z centra událostí a dva objekty blob odkazů, aby získal informace o názvu a kategorii:

![Příklad Stream Analytics výběru do dotazu](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Všimněte si, že úloha je spuštěná, ale ve výstupu nejsou vytvářeny žádné události. Na dlaždici **monitorování** můžete vidět, že vstup vyrábí data, ale nevíte, který krok **spojení** způsobil, že všechny události mají být vyřazeny.

![Dlaždice monitorování Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

V této situaci můžete přidat několik příkazů SELECT INTO do log (protokol). výsledky mezilehlého spojení a data, která jsou čtena ze vstupu.

V tomto příkladu jsme přidali dva nové "dočasné výstupy". Může to být jakákoli jímka, kterou si přejete. Tady používáme Azure Storage jako příklad:

![Přidání dalších příkazů SELECT INTO do Stream Analytics dotazů](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Pak můžete tento dotaz přepsat tímto způsobem:

![Přepsaný výběr do Stream Analyticsho dotazu](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Nyní spusťte úlohu znovu a nechte ji běžet po dobu několika minut. Pak proveďte dotazování temp1 a Temp2 pomocí Průzkumníka cloudu sady Visual Studio a vytvořte následující tabulky:

**tabulka temp1** 
 ![ VYBRAT do tabulky temp1 Stream Analytics dotaz](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**tabulka Temp2** 
 ![ VYBRAT do tabulky Temp2 Stream Analytics dotaz](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Jak vidíte, temp1 a Temp2 obojí mají data a sloupec Name se v Temp2 vyplní správně. Protože však ve výstupu stále nejsou žádná data, je něco špatného:

![VYBRAT do tabulky output1 bez dotazu na data Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Vzorkováním dat můžete být skoro jisti, že se jedná o problém s druhým připojením. Můžete si stáhnout referenční data z objektu BLOB a prohlédnout si je:

![VYBRAT do referenční tabulky Stream Analytics dotaz](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Jak vidíte, formát identifikátoru GUID v těchto referenčních datech se liší od formátu sloupce [from] v Temp2. To je důvod, proč se data nedostala do output1 podle očekávání.

Můžete opravit formát dat, odeslat ho do referenčního objektu BLOB a zkusit to znovu:

![VYBRAT do dočasné tabulky Stream Analytics dotaz](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Tentokrát se data ve výstupu naformátují a vyplní podle očekávání.

![VYBRAT do konečné tabulky Stream Analytics dotaz](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Získání pomoci

Pokud chcete získat další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
