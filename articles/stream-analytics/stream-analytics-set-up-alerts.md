---
title: Nastavení výstrah monitorování pro úlohy Azure Stream Analytics
description: Tento článek popisuje, jak použít Azure Portal k nastavení monitorování a výstrah pro úlohy Azure Stream Analytics.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.custom: contperfq1
ms.date: 06/21/2019
ms.openlocfilehash: 271cae3504601ffa42c077029541cef8c7726053
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461032"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Nastavení upozornění pro Azure Stream Analytics úlohy

Je důležité monitorovat Azure Stream Analytics úlohy, aby se zajistilo nepřetržité spouštění úlohy bez jakýchkoli problémů. Tento článek popisuje, jak nastavit výstrahy pro běžné scénáře, které by se měly monitorovat. 

Můžete definovat pravidla pro metriky z protokolů operací prostřednictvím portálu a také [programově](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a).

## <a name="set-up-alerts-in-the-azure-portal"></a>Nastavení výstrah v Azure Portal
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Získání výstrahy při neočekávaném zastavení úlohy

Následující příklad ukazuje, jak nastavit upozornění, když vaše úloha vstoupí do stavu selhání. Tato výstraha se doporučuje pro všechny úlohy.

1. V Azure Portal otevřete Stream Analytics úlohu, pro kterou chcete vytvořit výstrahu.

2. Na stránce **úloha** přejděte do části **monitorování** .  

3. Vyberte **metriky**a pak **nové pravidlo výstrahy**.

   ![Nastavení výstrah Azure Portal Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. Název vaší Stream Analytics úlohy by se měl automaticky zobrazit v části **prostředek**. Klikněte na **Přidat podmínku**a v části **Konfigurovat logiku signálu**vyberte **všechny operace správy** .

   ![Vyberte název signálu pro výstrahu Stream Analytics.](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. V části **Konfigurovat logiku signálu**změňte **úroveň události** na **vše** a změňte **stav** na **neúspěch**. Nechejte **událost iniciovaná** prázdným a vyberte **Hotovo**.

   ![Konfigurace výstrahy signálu pro Stream Analytics výstrahu](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Vyberte existující skupinu akcí nebo vytvořte novou skupinu. V tomto příkladu byla vytvořena nová skupina akcí s názvem **TIDashboardGroupActions** s akcí **emaily** , která uživatelům pošle e-mail s rolí **vlastníka** Azure Resource Manager.

   ![Nastavení výstrahy pro úlohu Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. Jednotlivé skupiny **prostředků**, **podmínek**a **akcí** by měly mít položku. Všimněte si, že aby se výstrahy mohly aktivovat, musí být splněné podmínky. Například můžete každých 5 minut měřit průměrnou hodnotu metriky za posledních 15 minut.

   ![Vytvořit pravidlo upozornění Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Přidejte k **podrobnostem výstrahy** **název pravidla výstrahy**, **Popis**a **skupinu prostředků** a kliknutím na **vytvořit pravidlo výstrahy** vytvořte pravidlo pro Stream Analytics úlohu.

   ![Vytvořit pravidlo upozornění Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Scénáře k monitorování

Následující výstrahy jsou doporučeny pro sledování výkonu Stream Analytics úlohy. Tyto metriky by se měly vyhodnocovat každou minutu za poslední období 5 minut.

|Metrika|Stav|Časová agregace|Prahová hodnota|Nápravné akce|
|-|-|-|-|-|
|Využití SU%|Je větší než|Maximum|80|Existuje několik faktorů, které zvyšují využití SU%. Můžete škálovat pomocí paralelního zpracování dotazů nebo zvýšit počet jednotek streamování. Další informace najdete v tématu [Využití paralelizace dotazů v Azure Stream Analytics](stream-analytics-parallelization.md).|
|Běhové chyby|Je větší než|Celkem|0|Projděte si protokoly aktivit nebo prostředků a proveďte příslušné změny ve vstupech, dotazech nebo výstupech.|
|Zpoždění vodoznaku|Je větší než|Maximum|V případě, že průměrná hodnota této metriky za posledních 15 minut je větší než pozdě tolerance doručení (v sekundách). Pokud jste nezměnili toleranci pozdního doručení, je výchozí hodnota nastavená na 5 sekund.|Zkuste zvýšit počet služby SUs nebo virtuálního dotazu. Další informace o službě SUs najdete v tématu [pochopení a úprava jednotek streamování](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Další informace o virtuálního dotazu naleznete v tématu [využití paralelismu dotazů v Azure Stream Analytics](stream-analytics-parallelization.md).|
|Chyby při deserializaci vstupu|Je větší než|Celkem|0|Zkontrolujte aktivity nebo protokoly prostředků a proveďte příslušné změny ve vstupu. Další informace o protokolech prostředků najdete v tématu [řešení potíží s Azure Stream Analytics pomocí protokolů prostředků](stream-analytics-job-diagnostic-logs.md) .|

## <a name="next-steps"></a>Další kroky

* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

