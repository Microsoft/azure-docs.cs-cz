---
title: Principy sledování úloh ve službě Azure Stream Analytics
description: Tento článek popisuje, jak monitorovat úlohy ve službě Azure Stream Analytics
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 81c5eabcfcca6818293ff9dbbfb1515d5729dd79
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963280"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Porozumění sledování úlohu Stream Analytics a monitorování dotazů

## <a name="introduction-the-monitor-page"></a>Představení: Stránka sledování
Azure portal i zařízení surface klíčové metriky výkonu, který slouží k monitorování a ladění výkonu dotazů a úlohy. Pokud chcete zobrazit tyto metriky, přejděte na úlohu Stream Analytics mají zájem o metriky pro zobrazení a zobrazení **monitorování** části na stránce Přehled.  

![Monitorování odkaz](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Jak je znázorněno, objeví se okno:

![Časový limit úlohy monitorování řídicího panelu](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metriky, které jsou k dispozici pro Stream Analytics
| Metrika                 | Definice                               |
| ---------------------- | ---------------------------------------- |
| Vstupní události v backlogu       | Počet vstupních událostí, které jsou v backlogu. |
| Chyby převodu dat | Počet výstupních událostech, které nebylo možné převést na očekávaný výstup schématu. |
| Události předčasného vstupu       | Počet událostí přijatých již v rané fázi. |
| Nezdařené požadavky na funkce | Počet neúspěšných volání funkce Azure Machine Learning (pokud existuje). |
| Události funkcí        | Počet událostí odeslaných do funkce Azure Machine Learning (pokud existuje). |
| Požadavky na funkce      | Počet volání funkce Azure Machine Learning (pokud existuje). |
| Chyby deserializace vstupu       | Počet událostí, které se nepodařilo deserializovat.  |
| Bajty vstupních událostí      | Objem dat přijatých úlohy Stream Analytics, v bajtech. To lze použít k ověření, že vstupní zdroj odeslání události. |
| Vstupní události           | Objem dat přijatých úloze Stream Analytics v počtu událostí. To lze použít k ověření, že vstupní zdroj odeslání události. |
| Přijaté vstupní zdroje       | Počet událostí přicházejících z vstupního zdroje. |
| Pozdní vstupní události      | Počet událostí přicházejících pozdní ze zdroje, které buď byla vyřazena nebo jejich časového razítka byl upraven na základě zásady řazení událostí Konfigurace nastavení zpoždění okno Tolerance pozdního přijetí. |
| Události mimo pořadí    | Počet událostí přijatých mimo pořadí, ve kterém byly vyřazeny nebo zadaný upravené časové razítko na základě zásad řazení událostí. To může mít vliv konfigurace okna pro toleranci mimo pořadí z nastavení. |
| Výstupní události          | Objem dat odesílaných úlohy Stream Analytics na cíl výstupu v počtu událostí. |
| Chyby za běhu         | Celkový počet chyb souvisejících s zpracování dotazů (s výjimkou chyby při ingestování události nebo výsledky outputing) |
| % využití SU       | Využití jednotek streamování přiřazené úlohy, z na kartě Měřítko úlohy. By měl tento ukazatel dosáhnou 80 % nebo vyšší, je vysoká pravděpodobnost, že zpracování událostí se může zpozdit nebo zastavená vidět pokrok. |
| Zpoždění vodoznaku       | Meze maximální zpoždění mezi všechny oddíly všechny výstupy v rámci úlohy. |

## <a name="customizing-monitoring-in-the-azure-portal"></a>Přizpůsobení sledování na webu Azure Portal
Můžete upravit typ grafu je vidět, metriky a časový rozsah v nastavení upravit graf. Podrobnosti najdete v tématu [jak přizpůsobit monitorování](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Graf doby monitorování dotazů](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


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

