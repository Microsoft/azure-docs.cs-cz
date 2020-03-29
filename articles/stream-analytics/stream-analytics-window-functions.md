---
title: Úvod k funkcím windowsaslužby Azure Stream Analytics
description: Tento článek popisuje čtyři funkce oken (omílání, přeskakování, posuvné, relace), které se používají v azure stream analytics úlohy.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: a0547243ddf114d5c9f7034f182a5e76d8c3e016
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75369418"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Úvod k funkcím okna Stream Analytics

Ve scénářích časového vysílání je provádění operací s daty obsaženými v časových oknech běžným vzorem. Stream Analytics má nativní podporu pro funkce oken, což vývojářům umožňuje vytvářet úlohy zpracování složitých datových proudů s minimálním úsilím.

Na výběr jsou čtyři druhy časových oken: [**Okna Omílání**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), [**Hopping**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), [**Posuvné**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)a [**Session.**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics)  Funkce okna se používají v klauzuli [**GROUP BY**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) syntaxe dotazu v úlohách Analýzy datového proudu. Události můžete také agregovat přes více oken pomocí funkce [ **Windows()** ](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics).

Všechny [okna](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) operace výstupní výsledky **na** konci okna. Výstupem okna bude jedna událost založená na použité agregační funkci. Výstupní událost bude mít časové razítko konce okna a všechny funkce okna jsou definovány s pevnou délkou. 

![Koncepty funkcí okna Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Omílání okno
Funkce omílání okna se používají k segmentu datového proudu do odlišných časových segmentů a k provedení funkce proti nim, například v příkladu níže. Hlavním rozlišovacím znakem přeskakujícího okna je, že se opakuje, ale nepřekrývá, tzn. že událost nemůže patřit do více než jednoho přeskakujícího okna.

![Okno omílání služby Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Hopping okno
Funkce skákajících oken skáčou v čase dopředu o pevně danou dobu. Můžete si je jednoduše představit jako přeskakující okna, která se překrývají, takže události můžou v sadě výsledků dotazu patřit do několika skákajících oken. Chcete-li, aby okno Hopping bylo stejné jako okno Omílaní, zadejte velikost směrování, která má být stejná jako velikost okna. 

![Stream Analytics hopping okno](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Posuvné okno
Posuvné okno funkce, na rozdíl od Omílání nebo Hopping okna, vytvořit výstup **pouze** v případě, že dojde k události. Každé okno bude mít alespoň jednu událost a toto okno se nepřetržitě posouvá dopředu o hodnotu € (epsilon). Stejně jako u skákajících oken můžou události patřit do několika posuvných oken.

![Posuvné okno Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Okno relace
Okno relace funguje události skupiny, které dorazí v podobné době, filtrování období, kdy nejsou k dispozici žádná data. Tato funkce má tři hlavní parametry: časový limit, maximální dobu trvání a dělicí klíč (volitelný).

![Okno relace Analýzy streamu](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Okno relace začíná, když dojde k první události. Pokud dojde k jiné události v rámci zadaného časového času z poslední události požití, okno se rozšíří zahrnout novou událost. V opačném případě, pokud dojde k žádné události v rámci časového odnož, okno je uzavřen v časovém rámci.

Pokud události stále dochází v rámci zadaného časového limitu, okno relace bude pokračovat v prodloužení, dokud není dosaženo maximální doby trvání. Maximální intervaly kontroly doby trvání jsou nastaveny na stejnou velikost jako zadaná maximální doba trvání. Pokud je například maximální doba trvání 10, pak kontroly, pokud okno překročí maximální dobu trvání, dojde v t = 0, 10, 20, 30 atd.

Pokud je k dispozici klíč oddílu, události jsou seskupeny podle klíče a okno relace je použita pro každou skupinu nezávisle. Toto dělení je užitečné v případech, kdy potřebujete různá okna relace pro různé uživatele nebo zařízení.


## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

