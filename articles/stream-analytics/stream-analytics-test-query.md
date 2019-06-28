---
title: Otestovat úlohu Azure Stream Analytics s ukázkovými daty
description: Tento článek popisuje, jak pomocí webu Azure portal k testování úloze Azure Stream Analytics, ukázkový vstup a odeslat ukázková data.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: c0a76ecd12143e0bbaa9997bfc6d7295df9c4ec7
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340868"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testování dotazu Stream Analytics s ukázkovými daty

Pomocí Azure Stream Analytics můžete ukázková data ze vstupů nebo nahrání ukázkových dat do testování dotazů na webu Azure Portal bez spuštění nebo zastavení úlohy.

## <a name="upload-sample-data-and-test-the-query"></a>Nahrání ukázkových dat a otestujte dotaz

1. Přihlaste se k portálu Azure. 

2. Vyhledejte existující úlohy Stream Analytics a vyberte ho.

3. Na Stream Analytics úlohy v části stránky, **topologie úlohy** záhlaví, vyberte **dotazu** otevřete okno editoru dotazů. 

4. Pokud chcete otestovat dotaz je lze potom buď ukázková data ze živých vstup nebo odeslání ze souboru. Data musí být serializované ve formátu JSON, CSV nebo AVRO. Ukázkový vstup musí být zakódován do kódování UTF-8 a není komprimovaná. Pro testování vstupu sdíleného svazku clusteru na portálu se podporuje jenom oddělovače čárky (,).

    1. Použití živého vstup: klikněte pravým tlačítkem na některý z vašich vstupů. Potom vyberte **ukázková data ze vstupu**. Na další obrazovce můžete nastavit dobu trvání vzorku.

    1. Pomocí souboru: klikněte pravým tlačítkem na některý z vašich vstupů. Potom vyberte **nahrání ukázkových dat ze souboru**. 

    ![Stream analytics dotaz v editoru testu dotazů](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Po dokončení vzorkování nebo uložení vyberte **testování** k otestování tohoto dotazu na ukázková data, které jste zadali.

    ![editor testu ukázková data dotazu Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Pokud potřebujete výstup testu pro pozdější použití, výstup tohoto dotazu se zobrazí v prohlížeči s odkazem na stažení výsledků. 

7. Využívejte iterativní upravte dotaz a otestujte ji znovu, abyste viděli, jak se změní výstup.

   ![Ukázkový výstup editoru dotazu Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Když použijete několik výstupů v dotazu, se výsledky zobrazí v samostatných kartách a můžete snadno přepínat mezi nimi.

8. Jakmile ověříte výsledky zobrazené v prohlížeči **Uložit** dotazu. Potom **Start** úlohy a nechat ji zpracovat příchozí události.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
