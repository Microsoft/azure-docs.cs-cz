---
title: Řešení potíží s Azure Stream Analytics dotazy
description: Tento článek popisuje techniky řešení potíží s dotazy v úlohách Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 5534a46ba99d1536d331b5852ef47588f03d73a4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980278"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Řešení potíží s Azure Stream Analytics dotazy

Tento článek popisuje běžné problémy při vývoji Stream Analytics dotazů a jejich řešení.

## <a name="query-is-not-producing-expected-output"></a>Dotaz nevyrábí očekávaný výstup.
1.  Kontrola chyb pomocí místního testování:
    - Na kartě **dotaz** vyberte **test**. K [otestování dotazu](stream-analytics-test-query.md)použijte stažená ukázková data. Prověřte případné chyby a pokuste se je opravit.   
    - Dotaz můžete také [testovat přímo na živém vstupu](stream-analytics-live-data-local-testing.md) pomocí Stream Analyticsch nástrojů pro Visual Studio.

2.  Pokud používáte [**časové razítko**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), ověřte, zda mají události časové razítko větší, než je [čas spuštění úlohy](stream-analytics-out-of-order-and-late-events.md).

3.  Odstraňte běžné nástrah, například:
    - Klauzule [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) v dotazu vyfiltroval všechny události, což znemožňuje vygenerování všech výstupů.
    - Funkce [**cast**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) selže, což způsobí selhání úlohy. Chcete-li se vyhnout chybám přetypování typů, použijte místo toho [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics) .
    - Při použití funkcí okna počkejte na celou dobu trvání okna, aby se zobrazil výstup dotazu.
    - Časové razítko pro události předchází času zahájení úlohy a proto se události vynechává.

4.  Zajistěte, aby byly zásady řazení událostí nakonfigurované podle očekávání. Přejít do **Nastavení** a vybrat [**řazení událostí**](stream-analytics-out-of-order-and-late-events.md). Zásada *se nepoužije,* když použijete tlačítko **test** k otestování dotazu. Výsledkem je jeden rozdíl mezi testováním v prohlížeči a spuštění úlohy v produkčním prostředí.

5. Ladit pomocí protokolů auditu a diagnostiky:
    - K identifikaci a ladění chyb použijte [protokoly auditu](../azure-resource-manager/management/view-activity-logs.md)a filtr.
    - K identifikaci a ladění chyb použijte [protokoly diagnostiky úlohy](stream-analytics-job-diagnostic-logs.md) .

## <a name="job-is-consuming-too-many-streaming-units"></a>Úloha spotřebovává příliš mnoho jednotek streamování.
Zajistěte, abyste využili výhod paralelního využívání Azure Stream Analytics. Můžete se dozvědět, jak [škálovat pomocí paralelního zpracování dotazů](stream-analytics-parallelization.md) Stream Analytics úloh, a to konfigurací vstupních oddílů a optimalizací definice analytického dotazu.

## <a name="debug-queries-progressively"></a>Postupně ladit dotazy

Při zpracování dat v reálném čase může být užitečné, aby data vypadala uprostřed dotazu. Vzhledem k tomu, že vstupy nebo kroky Azure Stream Analytics úlohy je možné přečíst vícekrát, můžete napsat nadbytečné příkazy SELECT INTO. Tím dojde k výstupování mezilehlých dat do úložiště a umožní vám zkontrolovat správnost dat, stejně jako *sledované proměnné* , při ladění programu.

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
![vybrat do tabulky temp1 Stream Analytics dotaz](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**tabulka temp2**
![vybrat do tabulky Temp2 Stream Analytics dotaz](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Jak vidíte, temp1 a Temp2 obojí mají data a sloupec Name se v Temp2 vyplní správně. Protože však ve výstupu stále nejsou žádná data, je něco špatného:

![VYBRAT do tabulky output1 bez dotazu na data Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Vzorkováním dat můžete být skoro jisti, že se jedná o problém s druhým připojením. Můžete si stáhnout referenční data z objektu BLOB a prohlédnout si je:

![VYBRAT do referenční tabulky Stream Analytics dotaz](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Jak vidíte, formát identifikátoru GUID v těchto referenčních datech se liší od formátu sloupce [from] v Temp2. To je důvod, proč se data nedostala do output1 podle očekávání.

Můžete opravit formát dat, odeslat ho do referenčního objektu BLOB a zkusit to znovu:

![VYBRAT do dočasné tabulky Stream Analytics dotaz](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Tentokrát se data ve výstupu naformátují a vyplní podle očekávání.

![VYBRAT do konečné tabulky Stream Analytics dotaz](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Získání nápovědy

Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
