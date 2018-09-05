---
title: Ladění dotazů Azure Stream Analytics s využitím SELECT INTO
description: Tento článek popisuje, jak ukázková uprostřed dotaz na data v úloze Azure Stream Analytics pomocí příkazů SELECT INTO v syntaxi dotazu.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: b056d4c29464451d3dc0ef62437f934535820489
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697988"
---
# <a name="debug-queries-by-using-select-into-statements"></a>Ladění dotazů pomocí příkazů SELECT INTO

Při zpracování dat v reálném čase vědět, co vypadá uprostřed dotazu může být užitečné. Protože vstupy nebo kroky úlohy Azure Stream Analytics může číst více než jednou, můžete napsat další příkazy SELECT INTO. Uděláte výstupů dočasných dat do služby storage a umožňuje zkontrolovat správnost dat, stejně jako *sledovat proměnné* udělat při ladění programu.

## <a name="use-select-into-to-check-the-data-stream"></a>Použijte příkaz SELECT INTO ke kontrole datový proud

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

