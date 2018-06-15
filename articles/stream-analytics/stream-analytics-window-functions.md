---
title: Úvod do Azure Stream Analytics oddílová funkce
description: Tento článek popisuje čtyři funkce oddílová (přeskakujícího, vše, klouzavé, relace), které se používají v úlohy Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 2650058e277bc0338c779655ce381be046fb120a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33893640"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Úvod do služby Stream Analytics oddílová funkce
Ve scénářích streamování čas provádění operací na data obsažená v dočasné windows je běžný vzor. Stream Analytics má nativní podporu pro oddílová funkce umožňují vývojářům úlohy zpracování Autor komplexní datového proudu s minimálním úsilím.

Existují čtyři typy dočasné Windows vybírat: [ **Přeskakujícího**](https://msdn.microsoft.com/azure/stream-analytics/reference/tumbling-window-azure-stream-analytics), [ **Hopping**](https://msdn.microsoft.com/azure/stream-analytics/reference/hopping-window-azure-stream-analytics), [  **Klouzavé**](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics), a [ **relace** ](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) systému windows.  Použijte okno funkcí v [ **GROUP BY** ](https://msdn.microsoft.com/azure/stream-analytics/reference/group-by-azure-stream-analytics) klauzule syntaxe dotazů v úlohách Stream Analytics.

Všechny [oddílová](https://msdn.microsoft.com/azure/stream-analytics/reference/windowing-azure-stream-analytics) operations výstup výsledků v **end** okna. Výstup okna bude jedinou událost podle používá agregační funkci. Výstupní událost může mít časové razítko konce okna a všechny funkce okna jsou definovány s pevnou délkou. 

![Stream Analytics okno funkce koncepty](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Přeskakující okno
Přeskakujícího okna, které funkce se používají pro segment datový proud do různých čas segmentů a provádět funkce proti, jako je například následující příklad. Klíče differentiators z Přeskakující okno se, že zopakují, proveďte není překrývají a událost nemůže patřit do více než jeden přeskakující okno.

![Stream Analytics přeskakující okno](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Skákající okno
Skákající okno funkce směrování dál v čase prostřednictvím uplynutí určité doby. To může být snadno si je představit jako Přeskakující windows, které může dojít k překrytí, takže události můžou patřit do více než jednu sadu výsledků Hopping okno. Vytvoření okna Hopping stejný jako Přeskakující okno, zadejte velikost skoku tak, aby byla stejná jako velikost okna. 

![Stream Analytics skákající okno](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Posuvné okno
Posuvné okno funkce, na rozdíl od Přeskakujícího nebo posílání windows, vytvořit výstup **pouze** při výskytu události. Každý okno bude mít aspoň jednu událost a okna nepřetržitě přesune dál € (Epsilon –). Jako posílání windows, můžete události patří do více než jeden posuvné okno.

![Stream Analytics posuvné okno](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window-preview"></a>Okno relace (Preview)
Funkce okno relace skupiny události, které přicházejí na podobné dobu, filtrování časová období, kdy se žádná data. Má tři hlavní parametry: Vypršel časový limit, maximální délka trvání a klíč rozdělení (volitelné).

![Stream Analytics relace okna](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Okno Relace začíná, když dojde k první události. Pokud jiné události dojde v rámci zadaného časového limitu od poslední události ingestovaný, okno rozšiřuje zahrnout novou událost. V opačném případě dojde-li žádné události v časovém limitu, pak zavření okna na časový limit.

Pokud v rámci zadaného časového limitu zachovat výskytu události, zachovat okno relace rozšíření, dokud nebude dosaženo maximální délka trvání. Maximální délka trvání intervalů kontrola, zda jsou nastaveny na stejnou velikost jako zadaná maximální doba trvání. Například pokud maximální doba trvání je 10, kontroly, je-li okno překročit maximální doba trvání bude dojít při t = 0, 10, 20, 30, atd.

Pokud je zadaný klíč oddílu, události, které jsou seskupeny dohromady pomocí klíče a okno relace se použije ke každé skupině nezávisle. Toto rozdělení do oddílů je užitečné v případě, kdy potřebujete různé relace windows pro různé uživatele nebo zařízení.


## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

