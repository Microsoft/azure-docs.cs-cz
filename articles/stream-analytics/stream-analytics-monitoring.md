---
title: Principy sledování úloh ve službě Azure Stream Analytics
description: Tento článek popisuje, jak monitorovat úlohy Azure Stream Analytics na portálu Azure portal.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 9432b43498cd48352427bf1ec2709a2d0f13a797
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172761"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Porozumění sledování úlohu Stream Analytics a monitorování dotazů

## <a name="introduction-the-monitor-page"></a>Ukázek Stránka monitorování
Klíčové metriky výkonu Azure Portalch ploch, které se dají použít k monitorování a řešení problémů s výkonem a úlohami. Pokud chcete zobrazit tyto metriky, přejděte na úlohu Stream Analytics mají zájem o metriky pro zobrazení a zobrazení **monitorování** části na stránce Přehled.  

![Úlohy Stream Analytics monitorování odkaz](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Jak je znázorněno, objeví se okno:

![Řídicí panel monitorování úlohy Stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metriky, které jsou k dispozici pro Stream Analytics
| Metrika                 | Definice                               |
| ---------------------- | ---------------------------------------- |
| Vstupní události v backlogu       | Počet vstupních událostí, které jsou v backlogu. Nenulová hodnota této metriky znamená, že vaše úloha nebude moci udržovat počet příchozích událostí. Pokud je tato hodnota pomalá nebo stále nenulová, měli byste škálovat svou úlohu. Další informace najdete v tématu [pochopení a úpravy jednotek streamování](stream-analytics-streaming-unit-consumption.md). |
| Chyby převodu dat | Počet výstupních událostech, které nebylo možné převést na očekávaný výstup schématu. Chcete-li odstranit události, které se vyskytnou v tomto scénáři, můžete změnit zásady chyb na možnost drop. |
| Události předčasného vstupu       | Události, jejichž časové razítko aplikace je dřívější než doba jejich příchodu, o více než 5 minut. |
| Nezdařené požadavky na funkce | Počet neúspěšných volání funkce Azure Machine Learning (pokud existuje). |
| Události funkcí        | Počet událostí odeslaných do funkce Azure Machine Learning (pokud existuje). |
| Požadavky na funkce      | Počet volání funkce Azure Machine Learning (pokud existuje). |
| Chyby deserializace vstupu       | Počet vstupních událostí, které nebylo možné deserializovat.  |
| Bajty vstupních událostí      | Objem dat přijatých úlohy Stream Analytics, v bajtech. To lze použít k ověření, že vstupní zdroj odeslání události. |
| Vstupní události           | Počet záznamů odkonstruovaných ze vstupních událostí. Tento počet neobsahuje příchozí události, jejichž výsledkem jsou chyby deserializace. |
| Přijaté vstupní zdroje       | Počet zpráv přijatých úlohou. V případě centra událostí je zpráva jedním EventDataem. V případě objektu BLOB je zpráva jedním objektem BLOB. Všimněte si, že vstupní zdroje jsou počítány před deserializací. Pokud dojde k chybám deserializace, mohou být vstupní zdroje větší než vstupní události. V opačném případě může být menší nebo rovno vstupním událostem, protože každá zpráva může obsahovat více událostí. |
| Pozdní vstupní události      | Události, které dorazily později než nakonfigurované okno tolerance zpožděného doručení. Přečtěte si další informace o [Azure Stream Analyticsch důležitých informací o pořadí událostí](stream-analytics-out-of-order-and-late-events.md) . |
| Události mimo pořadí    | Počet událostí přijatých mimo pořadí, ve kterém byly vyřazeny nebo zadaný upravené časové razítko na základě zásad řazení událostí. To může mít vliv konfigurace okna pro toleranci mimo pořadí z nastavení. |
| Výstupní události          | Objem dat odesílaných úlohy Stream Analytics na cíl výstupu v počtu událostí. |
| Chyby za běhu         | Celkový počet chyb souvisejících se zpracováním dotazů (kromě chyb nalezených při ingestování událostí nebo výstupních výsledků) |
| % využití SU       | Využití jednotek streamování přiřazené úlohy, z na kartě Měřítko úlohy. By měl tento ukazatel dosáhnou 80 % nebo vyšší, je vysoká pravděpodobnost, že zpracování událostí se může zpozdit nebo zastavená vidět pokrok. |
| Zpoždění vodoznaku       | Meze maximální zpoždění mezi všechny oddíly všechny výstupy v rámci úlohy. |

Pomocí těchto metrik můžete [monitorovat výkon Stream Analytics úlohy](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). 

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
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
