---
title: Nastavení monitorování výstrah pro úlohy Azure Stream Analytics
description: Tento článek popisuje, jak nastavit monitorování a výstrah pro úlohy Azure Stream Analytics pomocí webu Azure portal.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/26/2017
ms.openlocfilehash: 4c676ab3039a02a4fda27ab00312133e5de8077a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35904036"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Nastavení upozornění pro úlohy Azure Stream Analytics
Můžete nastavit výstrahy aktivovat upozornění, když metrika dosáhne podmínku, která zadáte. Může například nastavit upozornění pro podmínku vypadat asi takto:

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

Pravidla lze nastavit na metrik na portálu, nebo se dají konfigurovat [programově](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) nad daty protokoly operací.

## <a name="set-up-alerts-in-the-azure-portal"></a>Nastavte si upozornění na webu Azure Portal
1. Na webu Azure Portal otevřete chcete vytvořit upozornění pro úlohu Stream Analytics. 

2. V **úlohy** okna, klikněte na tlačítko **monitorování** oddílu.  

3. V **metrika** okna, klikněte na tlačítko **přidat upozornění** příkazu.

      ![Nastavení Azure portal](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Zadejte název a popis.

5. Pomocí voliče můžete definovat podmínky, pod kterým se pošle oznámení.

6. Zadání informací o kde se mají směrovat výstrahy.

      ![Nastavení oznámení pro úlohu Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Další podrobnosti týkající se konfigurace výstrahy na webu Azure Portal najdete [doručování oznámení o upozorněních](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  


## <a name="get-help"></a>Podpora
Další podporu naleznete v našem [fóru služby Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-get-started.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

