---
title: Testovací vaše úloha Azure Stream Analytics s ukázkovými daty | Microsoft Docs
description: Postup testování vašich dotazů v úlohy Stream Analytics.
keywords: Testovací úloha, vstup vzorkování, nahrát ukázková data
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.workload: data-services
ms.date: 03/18/2018
ms.author: sngun
ms.openlocfilehash: c026a91fff5b8ef5774993b335f8d61877aa5d39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="test-your-stream-analytics-query-with-sample-data"></a>Testovat dotaz služby Stream Analytics s ukázkovými daty

Pomocí Azure Stream Analytics můžete nahrát ukázková data a testování dotazů na portálu bez spuštění nebo zastavení úlohy.

## <a name="upload-sample-data-and-test-the-query"></a>Nahrát ukázková data a otestujte dotaz

1. Přejděte do jednoho existující úlohy Stream Analytics > klikněte na **dotazu** otevřete okno editoru dotazů. 

2. Chcete-li otestovat dotazu s ukázkovými daty vstupní, klikněte pravým tlačítkem myši na žádném z vašich vstupů a pak vyberte **nahrát ukázková data ze souboru**. Nyní můžete uložit pouze data formátu JSON. Pokud je například sdíleného svazku clusteru do jiného formátu dat, musí ho převést na JSON před nahráním. Všechny opensource převodní nástroj můžete použít jako [CSV k JSON úpravy](http://www.convertcsv.com/csv-to-json.htm) vaše data převést do formátu JSON.

    ![Stream analytics dotaz editor testu dotazu](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

3. Po dokončení nahrávání se klikněte na tlačítko **testování** k testování tento dotaz ukázková data, které jste zadali.

    ![Dotaz služby Stream analytics editor testu ukázková data](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

4. Pokud chcete uložit test výstup pro pozdější použití, výstup tohoto dotazu se zobrazí v prohlížeči se zobrazí odkaz na výsledky stahování. Teď můžete snadno a interaktivně upravte dotaz a otestovat opakovaně a najdete v části Jak výstup změny.

   ![Stream Analytics query editor ukázkový výstup](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Použijete-li několik výstupů v dotazu, můžete zobrazit výsledky pro každý výstup samostatně a snadno přepínat mezi nimi. Po ověření na výsledky zobrazené v prohlížeči, můžete uložit dotazu, spustit úlohu a nechat zpracovat události bez chyby.
Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
