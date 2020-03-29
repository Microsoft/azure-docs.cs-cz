---
title: Nastavení upozornění na monitorování pro úlohy Azure Stream Analytics
description: Tento článek popisuje, jak pomocí portálu Azure nastavit monitorování a výstrahy pro úlohy Azure Stream Analytics.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 836b7a489e3c73d745b128cbbc0c3566220ac409
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458734"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Nastavení výstrah pro úlohy Azure Stream Analytics

Je důležité sledovat úlohu Azure Stream Analytics, abyste zajistili, že úloha běží nepřetržitě bez problémů. Tento článek popisuje, jak nastavit výstrahy pro běžné scénáře, které by měly být sledovány. 

Můžete definovat pravidla pro metriky z dat protokolů operací prostřednictvím portálu, stejně jako [programově](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a).

## <a name="set-up-alerts-in-the-azure-portal"></a>Nastavení výstrah na webu Azure Portal
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Upozornění, když se úloha neočekávaně zastaví

Následující příklad ukazuje, jak nastavit výstrahy, když vaše úloha vstoupí do stavu selhání. Tato výstraha je doporučena pro všechny úlohy.

1. Na webu Azure Portal otevřete úlohu Stream Analytics, pro kterou chcete vytvořit výstrahu.

2. Na stránce **Úloha** přejděte do části **Monitorování.**  

3. Vyberte **metriky**a potom **nové pravidlo výstrahy**.

   ![Nastavení upozornění Azure portálu Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. Název úlohy Stream Analytics by se měl automaticky zobrazit v části **ZDROJ**. Klepněte na tlačítko **Přidat podmínku**a v části **Konfigurovat logiku signálu**vyberte **Všechny operace správy** .

   ![Výběr názvu signálu pro výstrahu Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. V části **Konfigurovat logiku signálu**změňte **úroveň události** na **vše** a **změňte stav** na **Neúspěšný**. Ponechat **událost iniciovat prázdnou** a vyberte **Hotovo**.

   ![Konfigurace logiky signálu pro výstrahu Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Vyberte existující skupinu akcí nebo vytvořte novou skupinu. V tomto příkladu byla vytvořena nová skupina akcí s názvem **TIDashboardGroupActions** s akcí **E-maily,** která odešle e-mail uživatelům s rolí Správce prostředků **Azure** vlastníka.

   ![Nastavení upozornění pro úlohu Azure Streaming Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. **Skupiny zdrojů,** **podmínek**a akcí by **měly** mít položku. Všimněte si, že aby se výstrahy oheň, definované podmínky musí být splněny. Například můžete každých 5 minut měřit průměrnou hodnotu metriky za posledních 15 minut.

   ![Vytvořit pravidlo upozornění Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Přidejte **název pravidla výstrahy**, **popis**a **skupinu zdrojů** do **podrobností výstrahy** a kliknutím na **Vytvořit pravidlo výstrahy** vytvořte pravidlo pro úlohu Analýzy streamu.

   ![Vytvořit pravidlo upozornění Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Scénáře ke sledování

Pro sledování výkonu úlohy Stream Analytics se doporučují následující upozornění. Tyto metriky by měly být vyhodnocovány každou minutu za posledních 5 minut.

|Metrika|Podmínka|Agregace času|Prahová hodnota|Nápravná opatření|
|-|-|-|-|-|
|SU% využití|Větší než|Maximum|80|Existuje několik faktorů, které zvyšují SU% Využití. Můžete škálovat pomocí paralelizace dotazu nebo zvýšit počet jednotek streamování. Další informace najdete v tématu [Využití paralelizace dotazů v Azure Stream Analytics](stream-analytics-parallelization.md).|
|Chyby za běhu|Větší než|Celkem|0|Zkontrolujte aktivity nebo diagnostické protokoly a proveďte příslušné změny vstupů, dotazů nebo výstupů.|
|Zpoždění vodoznaku|Větší než|Maximum|Pokud je průměrná hodnota této metriky za posledních 15 minut větší než tolerance pozdního doručení (v sekundách). Pokud jste neupravili toleranci pozdního doručení, je výchozí hodnota nastavena na 5 sekund.|Zkuste zvýšit počet su nebo paralelizovat dotaz. Další informace o jednotkách su naleznete v [tématu Understand and adjust Streaming Units](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Další informace o paralelizaci dotazu najdete [v tématu Využití paralelizace dotazů ve službě Azure Stream Analytics](stream-analytics-parallelization.md).|
|Vstupní chyby deserializace|Větší než|Celkem|0|Zkontrolujte aktivitu nebo diagnostické protokoly a proveďte příslušné změny vstupu. Další informace o diagnostických protokolech najdete [v tématu Poradce při potížích s Azure Stream Analytics pomocí diagnostických protokolů.](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>Podpora

Další podrobnosti o konfiguraci výstrah na webu Azure Portal najdete v tématu [Příjem oznámení o výstražných upozorněních](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

Další pomoc našlápneme na fórum [Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-get-started.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

