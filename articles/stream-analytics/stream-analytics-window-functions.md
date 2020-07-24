---
title: Úvod do Azure Stream Analyticsch funkcí okna
description: Tento článek popisuje čtyři funkce okna (bubny, skákající, klouzavé, relace), které se používají v úlohách Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: fd741a9401a3936ec02939562e8e85046e829d31
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075910"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Úvod do Stream Analyticsch funkcí okna

V rámci scénářů streamování je provádění operací s daty obsaženými v dočasných oknech běžným vzorem. Stream Analytics má nativní podporu pro funkce oken a umožňuje vývojářům vytvářet složité úlohy zpracování streamů s minimálním úsilím.

Existují čtyři druhy dočasná okna, ze kterých můžete vybírat: [**bubny**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), [**skákající**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), [**posuvné**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)a okna [**relace**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) .  Použijte funkce okna v klauzuli [**Group by**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) syntaxe dotazu v úlohách Stream Analytics. Můžete také agregovat události do více oken pomocí [funkce **Windows ()** ](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics).

Na **konci** okna se vrátí výstup všech operací s operacemi [Window](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) . Výstupem okna bude jediná událost založená na použité agregační funkci. Výstupní událost bude mít časové razítko konce okna a všechny funkce okna budou definovány s pevnou délkou. 

![Stream Analytics – koncepty funkcí okna](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Okno bubnu
Funkce bubnového okna se používají k segmentaci datového proudu do různých časových segmentů a k provedení funkce proti nim, jako je například příklad níže. Hlavním rozlišovacím znakem přeskakujícího okna je, že se opakuje, ale nepřekrývá, tzn. že událost nemůže patřit do více než jednoho přeskakujícího okna.

![Okno Stream Analyticsho bubnu](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Okno skákající
Funkce skákajících oken skáčou v čase dopředu o pevně danou dobu. Můžete si je jednoduše představit jako přeskakující okna, která se překrývají, takže události můžou v sadě výsledků dotazu patřit do několika skákajících oken. Chcete-li, aby okno skákající bylo stejné jako okno s bubnem, určete velikost segmentu směrování, který bude stejný jako velikost okna. 

![Stream Analytics okno skákající](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Posuvné okno

Posuvná okna, na rozdíl od bubnu nebo skákající Windows, jsou výstupní události jenom pro body v čase, kdy se obsah okna skutečně změní. Jinými slovy, když událost vstoupí do okna nebo je ukončí. Každé okno má alespoň jednu událost, například v případě skákající Windows, můžou události patřit do více než jednoho posuvního okna.

![Posuvné okno Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Okno relace
Okno relace funkcí seskupuje události, které dorazí v podobných časech, a vyfiltruje tak časová období, ve kterých nejsou žádná data. Tato funkce má tři hlavní parametry: časový limit, maximální dobu trvání a dělicí klíč (volitelný).

![Okno relace Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Okno relace začíná, když dojde k první události. Pokud v rámci zadaného časového limitu z poslední přijaté události dojde k jiné události, pak se okno rozšíří na novou událost. V opačném případě dojde v případě, že v rámci časového limitu neexistují žádné události, okno je v časovém limitu uzavřen

Pokud se události v rámci zadaného časového limitu chovají, zůstane okno relace delší, než se dosáhne maximální doby trvání. Maximální intervaly kontroly doby trvání jsou nastavené tak, aby měly stejnou velikost jako zadaná maximální doba trvání. Například pokud je maximální doba trvání 10, pak kontroly, pokud by okno překročilo maximální dobu trvání, nastane při t = 0, 10, 20, 30 atd.

Při zadání klíče oddílu se události seskupí podle klíče a okno relace se aplikuje na každou skupinu nezávisle na sobě. Tento oddíl je vhodný pro případy, kdy pro různé uživatele nebo zařízení potřebujete jiná okna relace.

## <a name="snapshot-window"></a>Okno snímku

Snapshot Windows seskupuje události, které mají stejné časové razítko. Na rozdíl od jiných typů oken, které vyžadují konkrétní funkci okna (například [SessionWindow ()](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics), můžete použít okno snímku přidáním System. timestamp () do klauzule GROUP by.

![Stream Analytics okno snímku](media/stream-analytics-window-functions/snapshot.png)

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

