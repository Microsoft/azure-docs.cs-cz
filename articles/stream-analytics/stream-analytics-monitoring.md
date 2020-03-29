---
title: Principy monitorování úloh ve Službě Azure Stream Analytics
description: Tento článek popisuje, jak sledovat úlohy Azure Stream Analytics na webu Azure Portal.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 4e9f90035816269d2d41781be34d0d8080628b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431656"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Principy monitorování úloh Služby Stream Analytics a monitorování dotazů

## <a name="introduction-the-monitor-page"></a>Úvod: Stránka monitoru
Portál Azure zobrazuje klíčové metriky výkonu, které se dá použít ke sledování a řešení potíží s dotazem a výkonem úlohy. Chcete-li tyto metriky zobrazit, přejděte k úloze Stream Analytics, pro kterou máte zájem, a podívejte se na část **Monitorování** na stránce Přehled.  

![Odkaz pro monitorování úloh služby Stream Analytics](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Okno se zobrazí takto:

![Řídicí panel monitorování úloh Služby Stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metriky dostupné pro Stream Analytics
| Metrika                 | Definice                               |
| ---------------------- | ---------------------------------------- |
| Zpětné vstupní události       | Počet vstupních událostí, které jsou backlogged. Nenulová hodnota pro tuto metriku znamená, že vaše úloha není schopna držet krok s počtem příchozích událostí. Pokud se tato hodnota pomalu zvyšuje nebo konzistentně nenulová, měli byste horizontální navýšení kapacity úlohy. Další informace naleznete na stránce [Principy a úprava jednotek streamování](stream-analytics-streaming-unit-consumption.md). |
| Chyby převodu dat | Počet výstupních událostí, které nelze převést na očekávané výstupní schéma. Zásady chyb lze změnit na 'Drop' k přetažení události, které se setkávají s tímto scénářem. |
| Události včasného vstupu       | Události, jejichž časové razítko aplikace je dřívější než čas jejich příjezdu o více než 5 minut. |
| Neúspěšné požadavky na funkci | Počet neúspěšných volání funkce Azure Machine Learning (pokud je k dispozici). |
| Události funkcí        | Počet událostí odeslaných do funkce Azure Machine Learning (pokud je k dispozici). |
| Požadavky na funkce      | Počet volání funkce Azure Machine Learning (pokud je k dispozici). |
| Vstupní chyby deserializace       | Počet vstupních událostí, které nelze rekonstruovat.  |
| Bajty vstupních událostí      | Množství dat přijatých úlohou Stream Analytics v bajtech. To lze použít k ověření, že události jsou odesílány do vstupního zdroje. |
| Vstupní události           | Počet záznamů rekonstruovaných ze vstupních událostí. Tento počet nezahrnuje příchozí události, které vedou k chybám deserializace. |
| Přijaté vstupní zdroje       | Počet zpráv přijatých úlohou. Pro Centrum událostí je zpráva jedna EventData. Pro objekt Blob je zpráva jeden objekt blob. Vezměte prosím na vědomí, že vstupní zdroje se počítají před rekonstrukcí. Pokud existují chyby deserializace, vstupní zdroje mohou být větší než vstupní události. V opačném případě může být menší než nebo rovna vstupní události, protože každá zpráva může obsahovat více událostí. |
| Události pozdního vstupu      | Události, které byly doručeny později než nakonfigurované okno tolerance pozdního doručení. Přečtěte si další informace o [aspektech objednávky událostí Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md) . |
| Události mimo pořadí    | Počet událostí přijatých mimo pořadí, které byly vynechány nebo upraveno časové razítko, na základě zásad řazení událostí. To může být ovlivněno konfigurací nastavení Okno tolerance mimo pořadí. |
| Výstupní události          | Množství dat odeslaných úlohou Stream Analytics do výstupního cíle v počtu událostí. |
| Chyby za běhu         | Celkový počet chyb souvisejících se zpracováním dotazů (s výjimkou chyb zjištěných při ingestování událostí nebo vykreslování výsledků) |
| Využití SU %       | Využití jednotek streamování přiřazených k úloze na kartě Měřítko úlohy. Pokud tento ukazatel dosáhne 80 % nebo vyšší, je vysoká pravděpodobnost, že zpracování událostí může být zpožděno nebo zastaveno. |
| Zpoždění vodoznaku       | Maximální zpoždění vodoznaku ve všech oddílech všech výstupů v úloze. |

Pomocí těchto metrik můžete [sledovat výkon úlohy Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Přizpůsobení monitorování na webu Azure Portal
Typ grafu, zobrazené metriky a časový rozsah můžete upravit v nastavení Upravit graf. Podrobnosti naleznete v tématu [Přizpůsobení monitorování](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Časový graf sledování dotazu Stream Analytics](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Nejnovější výstup
Dalším zajímavým datovým bodem pro sledování vaší úlohy je čas posledního výstupu, který je zobrazen na stránce Přehled.
Tento čas je čas aplikace (tj. čas pomocí časového razítka z dat události) nejnovějšího výstupu vaší úlohy.

## <a name="get-help"></a>Podpora
Další podporu naleznete v našem [fóru služby Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
