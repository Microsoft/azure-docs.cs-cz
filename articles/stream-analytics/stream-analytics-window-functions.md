---
title: Úvod do Azure Stream Analyticsch funkcí okna
description: Tento článek popisuje čtyři funkce okna (bubny, skákající, klouzavé, relace), které se používají v úlohách Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: c4ddffa5f9e9ff4b313f05c9cedb3cb207695225
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129699"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Úvod do Stream Analyticsch funkcí okna

V rámci scénářů streamování je provádění operací s daty obsaženými v dočasných oknech běžným vzorem. Stream Analytics má nativní podporu pro funkce oken a umožňuje vývojářům vytvářet složité úlohy zpracování streamů s minimálním úsilím.

Existuje pět druhů dočasných oken, ze kterých můžete vybírat: [**bubny**](/stream-analytics-query/tumbling-window-azure-stream-analytics), [**skákající**](/stream-analytics-query/hopping-window-azure-stream-analytics), [**klouzavé**](/stream-analytics-query/sliding-window-azure-stream-analytics), [**relační**](/stream-analytics-query/session-window-azure-stream-analytics)a [**snímková**](/stream-analytics-query/snapshot-window-azure-stream-analytics) okna.  Použijte funkce okna v klauzuli [**Group by**](/stream-analytics-query/group-by-azure-stream-analytics) syntaxe dotazu v úlohách Stream Analytics. Můžete také agregovat události do více oken pomocí [funkce **Windows ()**](/stream-analytics-query/windows-azure-stream-analytics).

Na **konci** okna se vrátí výstup všech operací s operacemi [Window](/stream-analytics-query/windowing-azure-stream-analytics) . Všimněte si, že při spuštění úlohy Stream Analytics můžete zadat *čas spuštění výstupu úlohy* a systém automaticky načte předchozí události v příchozích datových proudech, aby se v zadaném čase načetl výstup do prvního okna. Například pokud začnete s možností *nyní* , začne ihned generovat data. Výstupem okna bude jediná událost založená na použité agregační funkci. Výstupní událost bude mít časové razítko konce okna a všechny funkce okna budou definovány s pevnou délkou. 

![Stream Analytics – koncepty funkcí okna](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Okno bubnu
Funkce bubnového okna se používají k segmentaci datového proudu do různých časových segmentů a k provedení funkce proti nim, jako je například příklad níže. Hlavním rozlišovacím znakem přeskakujícího okna je, že se opakuje, ale nepřekrývá, tzn. že událost nemůže patřit do více než jednoho přeskakujícího okna.

![Okno Stream Analyticsho bubnu](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Okno skákající
Funkce skákajících oken skáčou v čase dopředu o pevně danou dobu. Je možné je snadno představit jako Bubnová okna, která se můžou překrývat a vysílat častěji než velikost okna. Události mohou patřit do více než jedné sady výsledků okna skákající. Chcete-li, aby okno skákající bylo stejné jako okno s bubnem, určete velikost segmentu směrování, který bude stejný jako velikost okna. 

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

Snapshot Windows seskupuje události, které mají stejné časové razítko. Na rozdíl od jiných typů oken, které vyžadují konkrétní funkci okna (například [SessionWindow ()](/stream-analytics-query/session-window-azure-stream-analytics), můžete použít okno snímku přidáním System. timestamp () do klauzule GROUP by.

![Stream Analytics okno snímku](media/stream-analytics-window-functions/snapshot.png)

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)