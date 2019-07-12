---
title: Řešení potíží s dotazy Azure Stream Analytics
description: Tento článek popisuje postupy řešení potíží s dotazy v úlohách Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 586ddb237144daddf0cbfd19785fcba7658469a0
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621478"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Řešení potíží s dotazy Azure Stream Analytics

Tento článek popisuje běžné problémy s vývojem dotazů Stream Analytics a postupy jejich řešení.

## <a name="query-is-not-producing-expected-output"></a>Dotaz není vytváření očekávaný výstup 
1.  Zkontrolujte chyby při testování místně:
    - Na **dotazu** kartu, vyberte možnost **Test**. Použijte stažené ukázková data do [otestujte dotaz](stream-analytics-test-query.md). Zkontrolujte případné chyby a pokuste se je opravit.   
    - Můžete také [otestovat dotaz přímo na živé vstupní](stream-analytics-live-data-local-testing.md) pomocí Stream Analytics tools pro Visual Studio.

2.  Pokud používáte [ **Timestamp By**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), ověřte, že všechny události mají časové razítko větší, než [úlohy počáteční čas](stream-analytics-out-of-order-and-late-events.md).

3.  Vylučte běžné nástrahy, jako například:
    - A [ **kde** ](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) klauzule v dotazu vyfiltrovala všechny události, brání generován žádný výstup.
    - A [ **PŘETYPOVÁNÍ** ](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) selže, což způsobí selhání úlohy funkce. Aby se zabránilo selhání přetypování typu, použijte [ **TRY_CAST** ](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics) místo.
    - Při použití funkcí okna, počkejte po dobu celé okno výstup z dotazu.
    - Časové razítko pro události předchází časem spuštění úlohy, a proto se události vypustí.

4.  Zajistěte, aby zásady pořadí událostí jsou nakonfigurované podle očekávání. Přejděte **nastavení** a vyberte [ **řazení událostí**](stream-analytics-out-of-order-and-late-events.md). Tato zásada je *není* použitý při použití **testování** tlačítko a otestujte dotaz. Tento výsledek je jedním z rozdílů mezi testováním v prohlížeči a spuštěním úlohy v produkčním prostředí. 

5. Ladění pomocí auditování a diagnostické protokoly:
    - Použití [protokoly auditu](../azure-resource-manager/resource-group-audit.md)a filtr k identifikaci a ladit chyby.
    - Použití [úlohy diagnostické protokoly](stream-analytics-job-diagnostic-logs.md) k identifikaci a ladit chyby.

## <a name="job-is-consuming-too-many-streaming-units"></a>Úloha spotřebovává příliš mnoho jednotek streamování
Zajistěte, aby že mohli využívat paralelního zpracování v Azure Stream Analytics. Můžete naučit [škálování s využitím paralelizace dotazů](stream-analytics-parallelization.md) úlohy Stream Analytics pomocí konfigurace vstupního oddíly a ladění definice dotazu analýzy.

## <a name="debug-queries-progressively"></a>Postupně ladění dotazů

Při zpracování dat v reálném čase vědět, co vypadá uprostřed dotazu může být užitečné. Protože vstupy nebo kroky úlohy Azure Stream Analytics může číst více než jednou, můžete napsat další příkazy SELECT INTO. Uděláte výstupů dočasných dat do služby storage a umožňuje zkontrolovat správnost dat, stejně jako *sledovat proměnné* udělat při ladění programu.

Následující příklad dotazu v úloze Azure Stream Analytics je zadání jednoho datového proudu, dvě referenční datové výstupy a výstup do služby Azure Table Storage. Dotaz spojuje data z centra událostí a dvě referenční přes bloby až po získání informací o názvů a kategorie:

![Příklad Stream Analytics dotaz SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Všimněte si, že je úloha spuštěná, ale žádné události jsou vytvořených ve výstupu. Na **monitorování** dlaždice zobrazené, uvidíte, že vstup je vytváření dat, ale si nejste jisti, který krok **připojte se k** způsobil všechny události zavěšení.

![Dlaždice monitorování Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
V takovém případě můžete přidat několik dalších příkazech SELECT INTO "přihlásit" mezilehlé výsledky spojení a data, která je načtený ze vstupu.

V tomto příkladu jsme přidali dvě nové "dočasné výstupy." Mohou být libovolné jímky, kolikrát chcete. Tady používáme jako příklad služby Azure Storage:

![Přidání dalších příkazech SELECT INTO do dotazu Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Potom můžete přepište dotaz následujícím způsobem:

![Přepsaný dotazu vyberte do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Nyní spusťte znovu úlohu a nechat běžet několik minut. Potom zadejte dotaz temp1 a temp2 pomocí Průzkumníka cloudu Visual Studio k vytvoření následujících tabulek:

**Tabulka temp1**
![dotaz SELECT INTO temp1 tabulky Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**Tabulka temp2**
![dotaz SELECT INTO temp2 tabulky Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Jak je vidět, temp1 a temp2 mají data a název sloupce je v temp2 správně zadána. Ale protože ve výstupu ještě neexistuje žádná data, něco se nepovedlo:

![Příkaz SELECT INTO tabulku output1 se žádná data dotazu Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Vzorkování dat, můžete si být téměř jisti, že problém s druhou spojení. Můžete stáhnout z objektu blob referenčních dat a podívejte se:

![Dotaz SELECT INTO ref tabulky Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Jak je vidět, se liší od formátu formát čísla GUID v této referenční data [sloupec v temp2 z]. To je důvod, proč data nebyla doručení output1 podle očekávání.

Můžete opravit formát dat, nahrajte ho odkazovat na objekt blob, a zkuste to znovu:

![Příkaz SELECT INTO dotazu Stream Analytics dočasné tabulky](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Tentokrát, data ve výstupu jsou ve formátu a vyplní podle očekávání.

![Dotaz SELECT INTO konečná tabulka takto Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Podpora

Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)