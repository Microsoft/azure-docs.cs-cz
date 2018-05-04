---
title: Testovací úlohu služby Azure Stream Analytics s ukázkovými daty
description: Postup testování vašich dotazů v úlohy Stream Analytics.
keywords: Tento článek popisuje, jak používat portál Azure k testování úlohy Azure Stream Analytics, ukázka vstup a nahrát ukázková data.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 3dc9091934f3db8ededc13f74d2f302eccace4d6
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testování dotazu Stream Analytics s ukázkovými daty

Pomocí Azure Stream Analytics můžete nahrát ukázková data a testování dotazů na portálu Azure bez spuštění nebo zastavení úlohy.

## <a name="upload-sample-data-and-test-the-query"></a>Nahrát ukázková data a otestujte dotaz

1. Přihlaste se k portálu Azure. 

2. Vyhledejte existující úlohu služby Stream Analytics a vyberte ho.

3. Na Stream Analytics úlohy v části stránky, **úlohy topologie** záhlaví, vyberte **dotazu** otevřete okno editoru dotazů. 

4. Chcete-li testovat dotaz s ukázkovými daty vstupní, klikněte pravým tlačítkem na žádném z vašich vstupů.  Potom vyberte **nahrát ukázková data ze souboru**.

   Data musí být pouze data formátu JSON. Pokud je například sdíleného svazku clusteru do jiného formátu dat, musí ho převést na JSON před nahráním. Všechny opensource převodní nástroj můžete použít jako [CSV k JSON úpravy](http://www.convertcsv.com/csv-to-json.htm) vaše data převést do formátu JSON.

    ![Stream analytics dotaz editor testu dotazu](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Po dokončení nahrávání vyberte **testování** k testování tento dotaz ukázková data, které jste zadali.

    ![Dotaz služby Stream analytics editor testu ukázková data](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Pokud potřebujete výstup testu pro pozdější použití, výstup tohoto dotazu se zobrazí v prohlížeči se zobrazí odkaz na výsledky stahování. 

7. Opakované upravte dotaz a otestovat zjistíte, jak se změní výstup.

   ![Stream Analytics query editor ukázkový výstup](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Použijete-li několik výstupů v dotazu, výsledky se zobrazí na samostatných záložkách a můžete snadno přepínat mezi nimi.

8. Po ověření na výsledky zobrazené v prohlížeči **Uložit** dotazu. Potom **spustit** úlohu a nechat ji zpracovat příchozí události.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
