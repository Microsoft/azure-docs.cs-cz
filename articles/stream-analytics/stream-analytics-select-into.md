---
title: Ladění Azure Stream Analytics dotazy pomocí SELECT INTO | Microsoft Docs
description: Ukázkový dotaz střední data pomocí příkazů SELECT INTO v Stream Analytics
keywords: ''
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: f232ba9804886f416ac0941ddc7e602782b6ebe2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="debug-queries-by-using-select-into-statements"></a>Ladění dotazy pomocí příkazů SELECT INTO

Při zpracování dat v reálném čase zároveň budete vědět, co data vypadá uprostřed dotazu může být užitečné. Protože vstupů nebo kroky úlohu služby Azure Stream Analytics lze číst vícekrát, můžete napsat doplňující příkazy SELECT INTO. Díky tomu výstupy mezilehlá data do úložiště a umožňuje vám zkontrolujte správnost dat, stejně jako *sledovat proměnné* provést při ladění programu.

## <a name="use-select-into-to-check-the-data-stream"></a>Pomocí SELECT INTO zkontrolujte datový proud

Následující příklad dotazu v úloze služby Azure Stream Analytics má jeden datový proud vstup, dvěma vstupy referenční data a výstup do úložiště tabulek Azure. Dotaz propojuje dat z centra událostí a odkaz na dva objekty BLOB se získat informace o názvu a kategorie:

![Příklad SELECT INTO dotazu](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Všimněte si, že úloha je spuštěná, ale žádné události se vytváří ve výstupu. Na **monitorování** dlaždici zobrazeny zde, uvidíte, že vstup je generovala data, ale nevíte, který krok **připojení** způsobila všechny události, které chcete vyřadit.

![Na dlaždici monitorování](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
V takovém případě můžete přidat několik navíc příkazech SELECT INTO se "přihlásit" mezilehlých výsledků spojení a data, která je načtený ze vstupu.

V tomto příkladu jsme přidali dvě nové "dočasné výstupy." Může se jednat žádné podřízený, které se vám líbí. Tady používáme Azure Storage jako příklad:

![Přidání další příkazy SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Pak je možné znovu vytvořit dotaz takto:

![Přepsaná dotaz SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Nyní znovu spustit úlohu a nechat ji spustit několik minut. Potom budete dotazovat temp1 a temp2 s cloudu Průzkumníka Visual Studio k vytvoření následujících tabulek:

**Tabulka temp1**
![SELECT INTO temp1 tabulky](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**Tabulka temp2**
![SELECT INTO temp2 tabulky](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Jak vidíte, temp1 a temp2 mají data, a název sloupce je v temp2 správně zadána. Ale protože ve výstupu stále neexistuje žádná data, je něco špatně:

![Vyberte do tabulky output1 bez dat](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Vzorkování dat, můžete si být téměř jisti, že problém s druhou spojení. Můžete stáhnout z objektu blob referenčních dat a podívejte se:

![SELECT INTO ref tabulky](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Jak vidíte, se liší od formát formát identifikátoru GUID v těchto datech odkaz [sloupec v temp2 z]. To je důvod, proč data nebyla doručení output1 podle očekávání.

Můžete opravit formát dat, nahrajte ho odkazovat na objekt blob a potom akci opakujte:

![Vyberte do dočasné tabulky](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Tento čas data ve výstupu je naformátován a vyplní podle očekávání.

![SELECT INTO poslední tabulky](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>Podpora

Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

