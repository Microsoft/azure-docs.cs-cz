---
title: Nastavení monitorování výstrah pro úlohy Azure Stream Analytics
description: Tento článek popisuje, jak nastavit monitorování a výstrah pro úlohy Azure Stream Analytics pomocí webu Azure portal.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2019
ms.custom: seodec18
ms.openlocfilehash: 52db8217cc1e1f84d25ab896be9b42db3bf6bd81
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769477"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Nastavení upozornění pro úlohy Azure Stream Analytics

Je důležité monitorovat úlohu Azure Stream Analytics zajistit, že úloha běží nepřetržitě bez problémů. Tento článek popisuje, jak nastavit výstrahy pro běžné scénáře, které by se měly monitorovat. 

Pravidla lze nastavit na metrik na portálu a dá se [programově](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) nad daty protokoly operací.

## <a name="set-up-alerts-in-the-azure-portal"></a>Nastavte si upozornění na webu Azure Portal

Následující příklad ukazuje, jak nastavit upozornění, pokud vaše úloha přejde do stavu selhání. Tato výstraha se doporučuje pro všechny úlohy.

1. Na webu Azure Portal otevřete chcete vytvořit upozornění pro úlohu Stream Analytics.

2. Na **úlohy** stránce, přejděte **monitorování** oddílu.  

3. Vyberte **metriky**a potom klikněte na tlačítko **nové pravidlo upozornění**.

   ![Azure portal nastavení výstrah Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. Název úlohy Stream Analytics by měl automaticky zobrazí v části **prostředků**. Klikněte na tlačítko **přidat podmínku**a vyberte **operace správy všech** pod **konfigurovat logiku signálů**.

   ![Vyberte název signálu pro upozornění Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. V části **konfigurovat logiku signálů**, změnit **úroveň události** k **všechny** a změňte **stav** k **neúspěšné** . Ponechte **události iniciovaných** prázdné a klikněte na tlačítko **provádí**.

   ![Konfigurovat logiku signálů pro upozornění Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Vyberte existující skupinu akcí nebo vytvořte novou skupinu. V tomto příkladu volá se nová skupina akcí **TIDashboardGroupActions** byl vytvořen pomocí **e-mailů** akci, která odešle e-mail uživatelům s **vlastníka** prostředků Azure Role správce.

   ![Nastavení oznámení pro úlohu Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. **Prostředků**, **PODMÍNKU**, a **skupiny akcí** mít položku. Všimněte si, že pro výstrahy, která se aktivuje, definované podmínky nutné splnit. Například lze změřit průměrnou hodnotu metriky za posledních 15 minut každých 5 minut.

   ![Vytvořit pravidlo upozornění Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Přidat **název pravidla upozornění**, **popis**a vaše **skupiny prostředků** k **podrobnosti VÝSTRAHY** a klikněte na tlačítko **vytvořit upozornění pravidlo** a vytvořte pravidlo pro svou úlohu Stream Analytics.

   ![Vytvořit pravidlo upozornění Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Scénáře monitorování

Tyto výstrahy se doporučují pro monitorování výkonu vaší úlohy Stream Analytics. Tyto metriky vyhodnocení každou minutu po dobu posledních 5 minut.

|Metrika|Podmínka|Časová agregace|Prahová hodnota|Nápravná opatření|
|-|-|-|-|-|
|% Využití SU|Větší než|Maximum|80|Existují několika faktory, které zvyšují % využití SU. Můžete škálovat s využitím paralelizace dotazů nebo zvýšit počet jednotek streamování. Další informace najdete v tématu [Využití paralelizace dotazů v Azure Stream Analytics](stream-analytics-parallelization.md).|
|Chyby za běhu|Větší než|Celkem|0|Zkontrolujte aktivity nebo diagnostické protokoly a provedli odpovídající změny vstupů, dotaz nebo výstupy.|
|Zpoždění vodoznak|Větší než|Maximum|Průměrná hodnota této metriky za posledních 15 minut po větší tolerance pozdního přijetí (v sekundách). Pokud jste ještě tolerance pozdního přijetí, výchozí hodnota je nastavena na 5 sekund.|Zkuste zvýšit počet su nebo paralelní provádění dotazu. Další informace o su, naleznete v tématu [principy a úpravy jednotek streamování](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Další informace o paralelní provádění vašeho dotazu, naleznete v tématu [využití paralelizace dotazů ve službě Azure Stream Analytics](stream-analytics-parallelization.md).|
|Chyby deserializace vstupu|Větší než|Celkem|0|Zkontrolujte aktivity nebo diagnostické protokoly a provedli odpovídající změny na vstup. Další informace o diagnostických protokolech najdete v tématu [Poradce při potížích s Azure Stream Analytics a využívat protokoly diagnostiky](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>Podpora

Další podrobnosti týkající se konfigurace výstrahy na webu Azure Portal najdete [doručování oznámení o upozorněních](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-get-started.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

