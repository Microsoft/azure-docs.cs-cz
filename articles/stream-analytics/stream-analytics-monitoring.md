---
title: Pochopení úloha monitorování v Azure Stream Analytics
description: Tento článek popisuje, jak monitorovat úlohy v Azure Stream Analytics
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 4b048705c80b7776ab0ab6823e27659a01eedeb5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Porozumění sledování úlohy Stream Analytics a monitorování dotazy

## <a name="introduction-the-monitor-page"></a>Úvod: Stránce monitorování
Azure portálu obě surface klíčové metriky, které lze použít ke sledování a řešení potíží s výkon dotazů a úlohy. Pokud chcete zobrazit tyto metriky, přejděte do úlohy Stream Analytics jsou nepotřebujete vidět metriky pro a zobrazit **monitorování** části na stránce Přehled.  

![Monitorování odkaz](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Jak je znázorněno, objeví se okno:

![Monitorování úlohy řídicí panel](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metriky, které jsou k dispozici pro Stream Analytics
| Metrika                 | Definice                               |
| ---------------------- | ---------------------------------------- |
| % využití SU       | Využití jednotek Streaming přiřazen do úlohy na kartě škálování úlohy. Tento ukazatel přístup 80 % nebo výše, je vysoká pravděpodobnost, že zpracování událostí může být zpoždění nebo zastavená, provedení průběh. |
| Vstupní události           | Množství dat přijatých úlohu služby Stream Analytics v počet událostí. To slouží k ověření, že události jsou odesílány do vstupního zdroje. |
| Výstupní události          | Množství dat odesílaných v úloze Stream Analytics cíl výstupu, počtu událostí. |
| Události mimo pořadí    | Počet událostí přijatých mimo pořadí, které byly vyřazeny nebo zadané upravenou časového razítka na základě zásad řazení událostí. To může mít vliv konfigurace nastavení interval Tolerance v pořádku. |
| Chyby převodu dat | Počet chyb při převodu dat způsobené úloha Stream Analytics. |
| Chyby za běhu         | Celkový počet chyb souvisejících s zpracování dotazu (s výjimkou chyby zjištěné při zpracování události nebo outputing výsledky) |
| Pozdní vstupní události      | Počet událostí přicházejících pozdní ze zdroje, které buď byla vyřazena nebo jejich časové razítko bylo změněno, na základě zásady řazení událostí Konfigurace nastavení pozdní interval Tolerance doručení. |
| Požadavky na funkce      | Počet volání funkce Azure Machine Learning (pokud existuje). |
| Nezdařené požadavky na funkce | Počet selhání volání funkce Azure Machine Learning (pokud existuje). |
| Události funkcí        | Počet událostí odeslaných do funkce Azure Machine Learning (pokud existuje). |
| Bajty vstupních událostí      | Množství dat přijatých úlohu služby Stream Analytics v bajtech. To slouží k ověření, že události jsou odesílány do vstupního zdroje. |


## <a name="customizing-monitoring-in-the-azure-portal"></a>Přizpůsobení sledování na portálu Azure
Můžete upravit typ grafu, metriky zobrazené a čas rozsah v nastavení upravit graf. Podrobnosti najdete v tématu [postup přizpůsobení monitorování](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Graf sledování času dotazu](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Nejnovější výstup
Jiné zajímavé datového bodu k monitorování vaší práce je čas poslední výstupu, zobrazí na stránce Přehled.
Tato doba je doba aplikace (tj. čas pomocí časové razítko z data události) nejnovější výstupu úlohy.

## <a name="get-help"></a>Podpora
Další podporu naleznete v našem [fóru služby Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

