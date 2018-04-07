---
title: Nastavení monitorování výstrah pro úlohy Azure Stream Analytics
description: Tento článek popisuje, jak nastavit monitorování a výstrahy pro úlohy Azure Stream Analytics pomocí portálu Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/26/2017
ms.openlocfilehash: 2498c0960ef8fd50064e40428f87d106abf10ecd
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Nastavit výstrahy pro úlohy Azure Stream Analytics
## <a name="introduction-monitor-page"></a>Úvod: Stránka sledování
Výstrahy můžete nastavit, které spustí výstrahu, když metriky dosáhne podmínku, která zadáte. Může například nastavit výstrahy pro podmínku takto:

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

Pravidla můžete nastavit na metriky prostřednictvím portálu nebo se dají konfigurovat [prostřednictvím kódu programu](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) přes protokoly operací data.

## <a name="set-up-alerts-in-the-azure-portal"></a>Nastavit výstrahy na portálu Azure
1. Na portálu Azure otevřete úlohu služby Stream Analytics, kterou chcete vytvořit výstrahu pro. 

2. V **úlohy** okně klikněte na tlačítko **monitorování** části.  

3. V **metrika** okně klikněte **přidat upozornění** příkaz.

      ![Nastavení portálu Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Zadejte název a popis.

5. Použijte na selektory můžete definovat podmínku, pod kterým bude zasílat výstrahu.

6. Zadejte informace o tom, kde by měl navštěvují výstrahy.

      ![Nastavení oznámení pro úlohu Azure streamování Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Další podrobnosti týkající se konfigurace výstrahy na portálu Azure najdete [dostávat oznámení o výstrahách](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  


## <a name="get-help"></a>Podpora
Další podporu naleznete v našem [fóru služby Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-get-started.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

