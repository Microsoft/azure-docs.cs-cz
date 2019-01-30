---
title: Principy sledování úloh ve službě Azure Stream Analytics
description: Tento článek popisuje, jak monitorovat úlohy Azure Stream Analytics na portálu Azure portal.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: f4dbfae4bada77cf882f7249353a17defebf312e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209345"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Porozumění sledování úlohu Stream Analytics a monitorování dotazů

## <a name="introduction-the-monitor-page"></a>Představení: Na stránce monitorování
Azure portal i zařízení surface klíčové metriky výkonu, který slouží k monitorování a ladění výkonu dotazů a úlohy. Pokud chcete zobrazit tyto metriky, přejděte na úlohu Stream Analytics mají zájem o metriky pro zobrazení a zobrazení **monitorování** části na stránce Přehled.  

![Úlohy Stream Analytics monitorování odkaz](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Jak je znázorněno, objeví se okno:

![Řídicí panel monitorování úlohy Stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metriky, které jsou k dispozici pro Stream Analytics
| Metrika                 | Definice                               |
| ---------------------- | ---------------------------------------- |
| Vstupní události v backlogu       | Počet vstupních událostí, které jsou v backlogu. Nenulové hodnoty pro tuto metriku znamená, že vaše úloha není schopné udržovat tempo s počtem příchozí události. Pokud je tato hodnota pomalu rostoucí nebo konzistentně nula, byste měli horizontálně vaši úlohu. Další informace najdete [principy a úpravy jednotek streamování](stream-analytics-streaming-unit-consumption.md). |
| Chyby převodu dat | Počet výstupních událostech, které nebylo možné převést na očekávaný výstup schématu. Chybové zásady můžete změnit na "Rozevírací" odpojit události, ke které dojde k této situaci. |
| Události předčasného vstupu       | Události, jejichž aplikace časové razítko je dřívější než čas jejich doručení o víc než 5 minut. |
| Nezdařené požadavky na funkce | Počet neúspěšných volání funkce Azure Machine Learning (pokud existuje). |
| Události funkcí        | Počet událostí odeslaných do funkce Azure Machine Learning (pokud existuje). |
| Požadavky na funkce      | Počet volání funkce Azure Machine Learning (pokud existuje). |
| Chyby deserializace vstupu       | Počet vstupních událostí, které se nepodařilo deserializovat.  |
| Bajty vstupních událostí      | Objem dat přijatých úlohy Stream Analytics, v bajtech. To lze použít k ověření, že vstupní zdroj odeslání události. |
| Vstupní události           | Počet záznamů rekonstruovat z události vstupu. |
| Přijaté vstupní zdroje       | Počet přijatých úloze událostí. To lze použít k ověření, že vstupní zdroj odeslání události. |
| Pozdní vstupní události      | Události, které byly přijaty později než nakonfigurované pozdní okno tolerance pozdního přijetí. Další informace o [aspekty pořadí událostí Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md) . |
| Události mimo pořadí    | Počet událostí přijatých mimo pořadí, ve kterém byly vyřazeny nebo zadaný upravené časové razítko na základě zásad řazení událostí. To může mít vliv konfigurace okna pro toleranci mimo pořadí z nastavení. |
| Výstupní události          | Objem dat odesílaných úlohy Stream Analytics na cíl výstupu v počtu událostí. |
| Chyby za běhu         | Celkový počet chyb souvisejících s zpracování dotazů (s výjimkou chyby při ingestování události nebo výsledky outputing) |
| % využití SU       | Využití jednotek streamování přiřazené úlohy, z na kartě Měřítko úlohy. By měl tento ukazatel dosáhnou 80 % nebo vyšší, je vysoká pravděpodobnost, že zpracování událostí se může zpozdit nebo zastavená vidět pokrok. |
| Zpoždění vodoznaku       | Meze maximální zpoždění mezi všechny oddíly všechny výstupy v rámci úlohy. |

Můžete použít tyto metriky a [sledovat výkon vaší úlohy Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Přizpůsobení sledování na webu Azure Portal
Můžete upravit typ grafu je vidět, metriky a časový rozsah v nastavení upravit graf. Podrobnosti najdete v tématu [jak přizpůsobit monitorování](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Graf doby monitorování dotazu Stream Analytics](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Nejnovější výstup
Další zajímavé datový bod a monitorování vaší úlohy je čas posledního výstupu, na stránce Přehled.
Čas je čas aplikace (tj. čas pomocí časového razítka od data události) nejnovější výstupu úlohy.

## <a name="get-help"></a>Podpora
Další podporu naleznete v našem [fóru služby Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

