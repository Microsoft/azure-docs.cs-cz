---
title: Pochopení monitorování úloh v Azure Stream Analytics
description: Tento článek popisuje, jak monitorovat úlohy Azure Stream Analytics v Azure Portal.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 3356e0bdd45b6a213ef5ef4a814e64585d8e8924
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726763"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Pochopení Stream Analytics monitorování úloh a postup monitorování dotazů

## <a name="introduction-the-monitor-page"></a>Úvod: stránka monitorování
Klíčové metriky výkonu Azure Portalch ploch, které se dají použít k monitorování a řešení problémů s výkonem a úlohami. Pokud se chcete podívat na tyto metriky, přejděte k Stream Analytics úlohy, které vás zajímá, zobrazit metriky pro a zobrazit část **monitorování** na stránce Přehled.  

![Stream Analytics odkaz monitorování úloh](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Okno se zobrazí, jak je znázorněno na následujícím obrázku:

![Řídicí panel monitorování úloh Stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metriky dostupné pro Stream Analytics
| Metric                 | Definice                               |
| ---------------------- | ---------------------------------------- |
| Nevyřízené události vstupu       | Počet nevyřízených vstupních událostí. Nenulová hodnota této metriky znamená, že vaše úloha nebude moci udržovat počet příchozích událostí. Pokud je tato hodnota pomalá nebo stále nenulová, měli byste škálovat svou úlohu. Další informace najdete v tématu [pochopení a úpravy jednotek streamování](stream-analytics-streaming-unit-consumption.md). |
| Chyby převodu dat | Počet výstupních událostí, které nebylo možné převést na očekávané výstupní schéma. Chcete-li odstranit události, které se vyskytnou v tomto scénáři, můžete změnit zásady chyb na možnost drop. |
| Využití procesoru v% (Preview)       | Procento využití procesoru vaší úlohou Pokud je tato metrika konzistentně vyšší než 80%, může to znamenat, že je vaše úloha v kritickém využití procesoru, a pravděpodobně způsobí, že se vstupní události vrátí do protokolu. Můžete zvýšit počet služby SUs přidělené vaší úloze a zmírnit tak takové problémy. |
| Události předčasného vstupu       | Události, jejichž časové razítko aplikace je dřívější než doba jejich příchodu, o více než 5 minut. |
| Neúspěšné žádosti o funkce | Počet neúspěšných volání funkce Azure Machine Learning (Pokud je k dispozici). |
| Události funkcí        | Počet událostí odeslaných do funkce Azure Machine Learning (Pokud je k dispozici). |
| Žádosti o funkce      | Počet volání funkce Azure Machine Learning (Pokud je k dispozici). |
| Chyby při deserializaci vstupu       | Počet vstupních událostí, které nebylo možné deserializovat.  |
| Bajty vstupních událostí      | Množství dat přijatých úlohou Stream Analytics v bajtech Tato možnost slouží k ověření, zda jsou události odesílány do vstupního zdroje. |
| Události vstupu           | Počet záznamů odkonstruovaných ze vstupních událostí. Tento počet neobsahuje příchozí události, jejichž výsledkem jsou chyby deserializace. Stejné události lze ingestovat Stream Analytics ve scénářích, jako jsou interní obnovy a autojoin. Proto se doporučuje neočekávat vstupní události a metriky výstupních událostí, aby se shodovaly, pokud má vaše úloha jednoduchý dotaz Pass through. |
| Přijaté vstupní zdroje       | Počet zpráv přijatých úlohou. V případě centra událostí je zpráva jedním EventDataem. V případě objektu BLOB je zpráva jedním objektem BLOB. Všimněte si, že vstupní zdroje jsou počítány před deserializací. Pokud dojde k chybám deserializace, mohou být vstupní zdroje větší než vstupní události. V opačném případě může být menší nebo rovno vstupním událostem, protože každá zpráva může obsahovat více událostí. |
| Zpožděné vstupní události      | Události, které dorazily později než nakonfigurované okno tolerance zpožděného doručení. Přečtěte si další informace o [Azure Stream Analyticsch důležitých informací o pořadí událostí](./stream-analytics-time-handling.md) . |
| Události mimo pořadí    | Počet událostí, které byly přijaty mimo pořadí, které byly buď vyřazeny nebo předány upravenému časovému razítku, na základě zásad řazení událostí. To může být ovlivněno konfigurací nastavení okna mimo pořadí tolerance. |
| Výstupní události          | Množství dat zaslaných úlohou Stream Analytics do cíle výstupu v rámci počtu událostí |
| Běhové chyby         | Celkový počet chyb souvisejících se zpracováním dotazů (kromě chyb nalezených při ingestování událostí nebo výstupních výsledků) |
| Využití SU%       | Procento paměti využívané vaší úlohou. Pokud je využití SU% konzistentně více než 80%, zpoždění vodoznaku roste a počet nevyřízených událostí roste, zvažte zvýšení počtu jednotek streamování. Vysoké využití indikuje, že úloha používá blízko maximálního počtu přidělených prostředků. |
| Zpoždění vodoznaku       | Maximální prodleva vodoznaku napříč všemi oddíly všech výstupů v rámci úlohy. |

Pomocí těchto metrik můžete [monitorovat výkon Stream Analytics úlohy](./stream-analytics-set-up-alerts.md#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Přizpůsobení monitorování v Azure Portal
V nastavení úprav grafu můžete upravit typ grafu, zobrazené metriky a časový rozsah. Podrobnosti najdete v tématu [Postup přizpůsobení monitorování](../azure-monitor/data-platform.md).

  ![Graf času monitorování Stream Analyticsového dotazu](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Poslední výstup
Další zajímavou datovou bodem pro monitorování vaší úlohy je čas posledního výstupu, který je zobrazený na stránce Přehled.
Tentokrát je čas aplikace (tj. čas použití časového razítka z dat události) posledního výstupu úlohy.

## <a name="get-help"></a>Získání pomoci
Pokud potřebujete další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)
