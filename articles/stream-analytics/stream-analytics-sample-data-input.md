---
title: Testování dotazu pomocí ukázkových dat v Azure Stream Analytics
description: Tento článek popisuje postup testování dotazu pomocí ukázková vstupní data v Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 305b767ee86de6c8b04fed17514a9092afc2d735
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/12/2018
ms.locfileid: "30961745"
---
# <a name="test-a-query-and-sample-input-in-azure-stream-analytics"></a>Testování dotazů a ukázkové vstup v Azure Stream Analytics 

Pomocí Azure Stream Analytics můžete zkusit vstupní události, které pocházejí ze souboru a testování dotazů na portálu bez nutnosti spuštění nebo zastavení úlohy.

## <a name="testing-your-query"></a>Testování dotazu

V podokně podrobností úlohy Stream Analytics, otevřete **editor dotazů** tak, že kliknete na název dotazu v části **dotazu**. (V našem ukázkový scénář, protože zatím nebyla vytvořena žádná dotazu, klikněte na tlačítko **< >** zástupný symbol.)

![Editor dotazů Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor.png)

Stejně jako tomu bylo v předchozí verzi, zobrazí se okno bohatě vybavený editor pro vytvoření dotazu. Teď byl aktualizován v okně s novou levém podokně, který ukazuje vstupy a výstupy, které se používají v dotazu a jsou definované pro tuto úlohu.

![Editor dotazů Stream Analytics vstup a výstupy seznamy](media/stream-analytics-sample-data-input/stream-analytics-query-editor-highlight.png)

Rovněž jsou uvedeny další vstupu a výstupu, které nejsou definovány. Pocházejí ze novou šablonu dotazu, kterou spustíte s. Změňte, nebo i zmizí, jak upravit dotaz. Můžete bez obav ignorovat je teď.

Chcete-li otestovat s ukázkovými daty vstupní, klikněte pravým tlačítkem na vstupy a pak vyberte **nahrát ukázková data ze souboru**.

![Stream Analytics query editor odeslání ukázkových dat z soubor – příkaz](media/stream-analytics-sample-data-input/stream-analytics-query-editor-upload.png)

Po dokončení nahrávání se klikněte na tlačítko **testování** k testování tento dotaz ukázková data, které jste právě zadali.

![Tlačítko Testovat editor dotazů Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-test.png)

Pokud chcete uložit test výstup pro pozdější použití, výstup tohoto dotazu se zobrazí v prohlížeči se zobrazí odkaz na výsledky stahování. Teď můžete snadno a interaktivně upravte dotaz a otestovat opakovaně a najdete v části Jak výstup změny.

![Stream Analytics query editor ukázkový výstup](media/stream-analytics-sample-data-input/stream-analytics-query-editor-samples-output.png)

Na předchozím obrázku byl přidán druhý výstup, nazývá **HighAvgTempOutput**.

Použijete-li několik výstupů v dotazu, můžete zobrazit výsledky pro každý výstup samostatně a snadno přepínat mezi nimi.

Až budete s výsledky spokojeni, můžete své uložit, spustit úlohu, sledujte a sledovat magické číslo Stream Analytics.

## <a name="get-help"></a>Podpora

O další pomoc, zkuste [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
