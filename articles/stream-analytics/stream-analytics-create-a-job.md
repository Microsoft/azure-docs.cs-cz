---
title: Postup vytvoření úlohy zpracování dat analytics pro Stream Analytics | Microsoft Docs
description: Vytvoření úlohy zpracování dat analytics pro Stream Analytics | učení segmentu cesty.
keywords: zpracování analýzy dat
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: 03f21c076624ee758d313c4f58e29c616226de57
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Postup vytvoření úlohy zpracování dat analytics pro Stream Analytics
Nejvyšší úrovně prostředků v Azure Stream Analytics je úlohy Stream Analytics.  Obsahuje jeden nebo více zdrojů vstupních dat, dotaz vyjadřující transformaci dat a jeden nebo více cílů výstup, které výsledky se zapisují do. Společně tyto umožní uživatelům provádět analýzy dat, zpracování pro streamování dat scénáře.

Chcete-li začít používat Stream Analytics, začněte tím, že vytváření nové úlohy Stream Analytics.  Všimněte si, že tato akce nemá žádné fakturace dopady až po zahájení úlohy.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **vytvořit prostředek** > **Data + analýzy** > **úlohy služby Stream Analytics**.
3. Vyberte **Vytvořit**.
   
3. Zadejte požadovanou konfiguraci pro úlohu služby Stream Analytics.
   
   * V **název úlohy** pole, zadejte název pro identifikaci úlohu služby Stream Analytics. Když **název úlohy** byl ověřen, do pole Název úlohy se zobrazí zelená značka zaškrtnutí. **Název úlohy** může obsahovat pouze alfanumerické znaky a '-' znak a musí být v rozmezí 3 až 63 znaků.
   * Použití **umístění** k určení zeměpisného umístění, ve které chcete spustit úlohu.
   * Zadejte novou nebo stávající **skupiny prostředků** pro uložení související prostředky pro vaši aplikaci.
4. Vyberte **Vytvořit**.
Může trvat několik minut pro úlohu služby Stream Analytics, který se má vytvořit. Chcete-li zkontrolovat stav, můžete sledovat průběh v centru oznámení.
    
   ![Vytvoření úlohy úlohy Azure portálu Data analytics zpracování](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. Nová úloha se zobrazí stav **vytvořená**. Všimněte si, že **spustit** tlačítko k dispozici. Před spuštěním úlohy, nakonfigurujte vstupu úlohy, dotaz a výstup.

   
   ![Azure portálu analýzy dat zpracování stavu úlohy](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Podpora
Další podporu naleznete v našem [fóru služby Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

