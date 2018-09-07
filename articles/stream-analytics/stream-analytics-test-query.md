---
title: Otestovat úlohu Azure Stream Analytics s ukázkovými daty
description: Jak otestovat své dotazy v úlohách Stream Analytics.
keywords: Tento článek popisuje, jak pomocí webu Azure portal k testování úloze Azure Stream Analytics, ukázkový vstup a odeslat ukázková data.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 63f9b98bcd70378ae02769298f0ffcd796378612
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027261"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testování dotazu Stream Analytics s ukázkovými daty

Pomocí Azure Stream Analytics můžete nahrát ukázková data a testování dotazů na webu Azure Portal bez spuštění nebo zastavení úlohy.

## <a name="upload-sample-data-and-test-the-query"></a>Nahrání ukázkových dat a otestujte dotaz

1. Přihlaste se k portálu Azure. 

2. Vyhledejte existující úlohy Stream Analytics a vyberte ho.

3. Na Stream Analytics úlohy v části stránky, **topologie úlohy** záhlaví, vyberte **dotazu** otevřete okno editoru dotazů. 

4. Otestování dotazu s ukázková vstupní data, klikněte pravým tlačítkem na některý z vašich vstupů.  Potom vyberte **nahrání ukázkových dat ze souboru**. Data musí být serializované ve formátu JSON, CSV nebo AVRO. Ukázkový vstup musí být zakódován do kódování UTF-8 a není komprimovaná. Pro testování vstupu sdíleného svazku clusteru na portálu se podporuje jenom oddělovače čárky (,).

    ![Stream analytics dotaz v editoru testu dotazů](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Až se nahrávání dokončí, vyberte **testování** k otestování tohoto dotazu na ukázková data, které jste zadali.

    ![editor testu ukázková data dotazu Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Pokud potřebujete výstup testu pro pozdější použití, výstup tohoto dotazu se zobrazí v prohlížeči s odkazem na stažení výsledků. 

7. Využívejte iterativní upravte dotaz a otestujte ji znovu, abyste viděli, jak se změní výstup.

   ![Ukázkový výstup editoru dotazu Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Když použijete několik výstupů v dotazu, se výsledky zobrazí v samostatných kartách a můžete snadno přepínat mezi nimi.

8. Jakmile ověříte výsledky zobrazené v prohlížeči **Uložit** dotazu. Potom **Start** úlohy a nechat ji zpracovat příchozí události.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
