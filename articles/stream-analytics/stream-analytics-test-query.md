---
title: Testování dotazů v Azure Stream Analytics | Microsoft Docs
description: Postup testování vašich dotazů v úlohy Stream Analytics.
keywords: Test dotazu, řešení potíží s dotazu
documentation center: ''
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
ms.openlocfilehash: 50bfce426bf48ba986887f8a2e2873fe04ea2507
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Testování dotazů Azure Stream Analytics na portálu Azure

Pomocí služby Azure Stream Analytics můžete otestovat dotazy na portálu Azure bez nutnosti spuštění nebo zastavení úlohy.

## <a name="test-the-input"></a>Testování vstupu

1. Chcete-li otestovat s ukázkovými daty vstupní, klikněte pravým tlačítkem na vstupy a pak vyberte **nahrát ukázková data ze souboru**. Nyní můžete uložit pouze data formátu JSON. Pokud je například sdíleného svazku clusteru do jiného formátu dat, musí ho převést na JSON před nahráním. Všechny opensource převodní nástroj můžete použít jako [CSV k JSON úpravy](http://www.convertcsv.com/csv-to-json.htm) vaše data převést do formátu JSON.

    ![Stream analytics dotaz editor testu dotazu](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. Po dokončení nahrávání se klikněte na tlačítko **testování** k testování tento dotaz ukázková data, které jste zadali.

    ![Dotaz služby Stream analytics editor testu ukázková data](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

Výstup tohoto dotazu se zobrazí v prohlížeči s výsledky odkaz ke stažení, budete chtít uložit výstup testu pro pozdější použití. Teď můžete snadno a interaktivně upravte dotaz a otestovat opakovaně a najdete v části Jak výstup změny.

![Stream Analytics query editor ukázkový výstup](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

S více výstupů použitých v dotazu můžete zobrazit výsledky pro obě výstupy samostatně a snadno přepínat mezi nimi.

Jakmile budete spokojeni se na výsledky zobrazené v prohlížeči, můžete uložit dotazu, spusťte úlohu a nechat zpracovat události bez chyby.

## <a name="get-help"></a>Podpora

Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
