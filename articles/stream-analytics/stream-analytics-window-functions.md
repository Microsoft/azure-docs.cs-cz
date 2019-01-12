---
title: Úvod do služby Azure Stream Analytics okny
description: Tento článek popisuje čtyři okny (přeskakujícího, kdy se skok provádí, klouzavé, relace), které se používají v úlohách Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 04c19e7e51777db4c59bfab3d5a8a7598560556a
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231644"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Úvod do služby Stream Analytics okny
Ve scénářích streamování v čase provádění operací na data obsažená v dočasné systému windows je běžný vzor. Stream Analytics má nativní podporu pro okny, umožňuje vývojářům Autor zpracování komplexních datových proudů úloh s minimálním úsilím.

Existují čtyři druhy dočasné windows na výběr: [**Přeskakujícího**](https://msdn.microsoft.com/azure/stream-analytics/reference/tumbling-window-azure-stream-analytics), [ **dosáhneme**](https://msdn.microsoft.com/azure/stream-analytics/reference/hopping-window-azure-stream-analytics), [ **klouzavé**](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics), a [ **relace**  ](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) systému windows.  Použití funkcí okna v [ **Group** ](https://msdn.microsoft.com/azure/stream-analytics/reference/group-by-azure-stream-analytics) klauzule syntaxe dotazů ve svých úlohách Stream Analytics.

Všechny [oddílová](https://msdn.microsoft.com/azure/stream-analytics/reference/windowing-azure-stream-analytics) operací vypsání výsledků na **end** okna. Výstup v okně bude jedinou událost na základě agregační funkce používá. Výstupní událost může mít časové razítko konce okna a všech funkcí okna jsou definovány s pevnou délkou. 

![Koncepty funkcí okno Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Aktivační událost pro přeskakující okno
Přeskakujícího okna, které funkce se používají pro segment datový proud do určité časové segmentů a provádět funkce proti nim, jako je například následující příklad. Klíčových předností aktivační událost pro Přeskakující okno se, že zopakují, proveďte není překrytí a události nemůže patřit do více než jedna aktivační událost pro přeskakující okno.

![Stream Analytics aktivační událost pro přeskakující okno](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Skákající okno
Skákající okno funkce směrování vpřed v čase na pevné období. Může být snadno přirozeným způsobem jako aktivační událost pro Přeskakující okna, které může dojít k překrytí, takže události může patřit do více než jednu sadu výsledků Hopping okno. Chcete-li okno Hopping stejný jako aktivační událost pro Přeskakující okno, zadejte velikost skoku být stejná jako velikost okna. 

![Skákající okno Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Posuvné okno
Posuvné okno funkce a na rozdíl od Přeskakujícího nebo windows, kdy se skok provádí vytvořit výstup **pouze** při výskytu události. Každé okno bude mít alespoň jednu událost a v okně průběžně přesune vpřed € (epsilon). Například kdy se skok provádí windows, můžete události patří do více než jeden posuvné okno.

![Stream Analytics posuvné okno](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Okno relace
Funkce okna relace skupině událostí, které dorazí ve stejné době, odfiltrováním časová období tam, kde není žádná data. Má tři hlavní parametry: časový limit, maximální dobu trvání a dělení klíče (volitelné).

![Okno relace Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Okno Relace začíná, když dojde k první události. Pokud dojde k jiné události v rámci zadaného časového limitu od poslední přijaté události, v okně rozšiřuje zahrnout nové události. Jinak Pokud dojde k žádné události v časovém limitu, pak zavření okna na časový limit.

Pokud události zachovat ke kterým dochází v rámci zadaného časového limitu, ponechte okno relace rozšíření, dokud nebude dosaženo maximální doba trvání. Maximální doba trvání kontroly intervalech jsou nastaveny na stejné velikosti jako zadané maximální dobu trvání. Například pokud maximální dobu trvání je 10, kontroly, je-li okno být delší než maximální doba trvání se stane v t = 0, 10, 20, 30, atd.

Pokud se poskytne klíč oddílu, události, které jsou seskupeny podle klíče a okno relace platí pro každou skupinu nezávisle na sobě. Toto rozdělení do oddílů je užitečné pro případy, kdy potřebujete jinou relací windows pro různé uživatele nebo zařízení.


## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

