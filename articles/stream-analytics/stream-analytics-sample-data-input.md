---
title: Dotaz testování v Azure Stream Analytics | Microsoft Docs
description: Přesně stanovit problémy při řešení potíží s úlohy Stream Analytics.
keywords: řešení potíží s vstupní, vstupní vzorkování
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 99c9f7f2dc84f53d09b7aee1916c5532427de692
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="azure-stream-analytics-query-testing-and-input-stream-sampling"></a>Testování Azure Stream Analytics dotazů a vzorkování vstupu stream

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

Až budete s výsledky spokojeni, můžete uložit dotazu, spustit úlohu, sledujte a sledovat magické číslo Stream Analytics.

## <a name="get-help"></a>Podpora

Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
