---
title: Řešení potíží s dotazy Azure Stream Analytics
description: Tento článek popisuje postupy řešení potíží s dotazy v úlohách Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/11/2018
ms.openlocfilehash: c437f350e394dc8c264903508a2a5a66fa8225a7
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49346685"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Řešení potíží s dotazy Azure Stream Analytics

Tento článek popisuje běžné problémy s vývojem dotazů Stream Analytics a postupy jejich řešení.

## <a name="query-is-not-producing-expected-output"></a>Dotaz není vytváření očekávaný výstup 
1.  Zkontrolujte chyby při testování místně:
    - Na **dotazu** kartu, vyberte možnost **Test**. Použijte stažené ukázková data do [otestujte dotaz](stream-analytics-test-query.md). Zkontrolujte případné chyby a pokuste se je opravit.   
    - Můžete také [otestovat dotaz přímo na živé vstupní](stream-analytics-live-data-local-testing.md) pomocí Stream Analytics tools pro Visual Studio.

2.  Pokud používáte [ **Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), ověřte, že všechny události mají časové razítko větší, než [úlohy počáteční čas](stream-analytics-out-of-order-and-late-events.md).

3.  Vylučte běžné nástrahy, jako například:
    - A [ **kde** ](https://msdn.microsoft.com/library/azure/dn835048.aspx) klauzule v dotazu vyfiltrovala všechny události, brání generován žádný výstup.
    - A [ **PŘETYPOVÁNÍ** ](https://msdn.microsoft.com/azure/stream-analytics/reference/cast-azure-stream-analytics) selže, což způsobí selhání úlohy funkce. Aby se zabránilo selhání přetypování typu, použijte [ **TRY_CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/try-cast-azure-stream-analytics) místo.
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

![Dotaz SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Všimněte si, že je úloha spuštěná, ale žádné události jsou vytvořených ve výstupu. Na **monitorování** dlaždice zobrazené, uvidíte, že vstup je vytváření dat, ale si nejste jisti, který krok **připojte se k** způsobil všechny události zavěšení.

![Dlaždice monitorování](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
V takovém případě můžete přidat několik dalších příkazech SELECT INTO "přihlásit" mezilehlé výsledky spojení a data, která je načtený ze vstupu.

V tomto příkladu jsme přidali dvě nové "dočasné výstupy." Mohou být libovolné jímky, kolikrát chcete. Tady používáme jako příklad služby Azure Storage:

![Přidání dalších příkazech SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Potom můžete přepište dotaz následujícím způsobem:

![Přepsaný dotaz SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Nyní spusťte znovu úlohu a nechat běžet několik minut. Potom zadejte dotaz temp1 a temp2 pomocí Průzkumníka cloudu Visual Studio k vytvoření následujících tabulek:

**Tabulka temp1**
![SELECT INTO temp1 tabulky](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**Tabulka temp2**
![SELECT INTO temp2 tabulky](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Jak je vidět, temp1 a temp2 mají data a název sloupce je v temp2 správně zadána. Ale protože ve výstupu ještě neexistuje žádná data, něco se nepovedlo:

![Příkaz SELECT INTO tabulky output1 bez dat](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Vzorkování dat, můžete si být téměř jisti, že problém s druhou spojení. Můžete stáhnout z objektu blob referenčních dat a podívejte se:

![Příkaz SELECT INTO Referenční tabulka](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Jak je vidět, se liší od formátu formát čísla GUID v této referenční data [sloupec v temp2 z]. To je důvod, proč data nebyla doručení output1 podle očekávání.

Můžete opravit formát dat, nahrajte ho odkazovat na objekt blob, a zkuste to znovu:

![Příkaz SELECT INTO dočasné tabulky](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Tentokrát, data ve výstupu jsou ve formátu a vyplní podle očekávání.

![Příkaz SELECT INTO konečné tabulky](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Podpora

Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)