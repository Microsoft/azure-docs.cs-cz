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
ms.openlocfilehash: ad0e0ca75bf3d3a8d9d1029d42f8609b3c4c627b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620833"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testování dotazu Stream Analytics s ukázkovými daty

Pomocí Azure Stream Analytics můžete ukázková data ze vstupů nebo nahrání ukázkových dat do testování dotazů na webu Azure Portal bez spuštění nebo zastavení úlohy.

## <a name="upload-or-sample-data-from-a-live-source-to-test-the-query"></a>Odeslání nebo ukázková data z živého zdroje otestujte dotaz

1. Přihlaste se k portálu Azure. 

2. Vyhledejte existující úlohy Stream Analytics a vyberte ho.

3. Na Stream Analytics úlohy v části stránky, **topologie úlohy** záhlaví, vyberte **dotazu** otevřete okno editoru dotazů. 

4. Pokud chcete otestovat dotaz je lze potom buď ukázková data ze živých vstup nebo odeslání ze souboru. Data musí být serializované ve formátu JSON, CSV nebo AVRO. Ukázkový vstup musí být zakódován do kódování UTF-8 a není komprimovaná. Pro testování vstupu sdíleného svazku clusteru na portálu se podporuje jenom oddělovače čárky (,).

    1. Použití živého vstup: klikněte pravým tlačítkem na některý z vašich vstupů. Potom vyberte **ukázková data ze vstupu**. Na další obrazovce můžete nastavit dobu trvání vzorku. Odběr událostí z živého zdroje načte až 1 000 událostí nebo 1 MB (podle toho, co nastane dřív), takže data vzorkovány nemusí reprezentovat celou časový interval určený.

    1. Pomocí souboru: klikněte pravým tlačítkem na některý z vašich vstupů. Potom vyberte **nahrání ukázkových dat ze souboru**. 

    ![Stream analytics dotaz v editoru testu dotazů](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Po dokončení vzorkování nebo uložení vyberte **testování** k otestování tohoto dotazu na ukázková data, které jste zadali.

    ![editor testu ukázková data dotazu Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Pokud potřebujete výstup testu pro pozdější použití, výstup tohoto dotazu se zobrazí v prohlížeči s odkazem na stažení výsledků. 

7. Využívejte iterativní upravte dotaz a otestujte ji znovu, abyste viděli, jak se změní výstup.

   ![Ukázkový výstup editoru dotazu Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Když použijete několik výstupů v dotazu, se výsledky zobrazí v samostatných kartách a můžete snadno přepínat mezi nimi.

8. Jakmile ověříte výsledky zobrazené v prohlížeči **Uložit** dotazu. Potom **Start** úlohy a nechat ji zpracovat příchozí události.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
