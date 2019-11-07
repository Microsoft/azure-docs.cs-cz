---
title: Správa a monitorování – aktivita dotazů, využití prostředků
description: Zjistěte, jaké možnosti jsou dostupné pro správu a monitorování Azure SQL Data Warehouse. Pomocí zobrazení Azure Portal a dynamické správy (zobrazení dynamické správy) můžete pochopit aktivity dotazů a využití prostředků datového skladu.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/09/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1a210e2622212ed59dfa12f9f9a108c6ffe08714
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692892"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Monitorování využití prostředků a aktivity dotazů v Azure SQL Data Warehouse
Azure SQL Data Warehouse poskytuje bohatě monitorovanou možnost monitorování v rámci Azure Portal, která umožňuje obcházet vaše úlohy datového skladu na Surface. Azure Portal je doporučeným nástrojem při monitorování datového skladu, protože poskytuje konfigurovatelné doby uchovávání, výstrahy, doporučení a přizpůsobitelné grafy a řídicí panely pro metriky a protokoly. Portál také umožňuje integraci s dalšími službami monitorování Azure, jako je Operations Management Suite (OMS) a Azure Monitor (protokoly), a poskytuje tak prostředí pro monitorování holistický jenom pro datový sklad, ale také pro celou službu Azure Analytics. platforma pro integrovanou monitorovací prostředí. Tato dokumentace popisuje, jaké možnosti monitorování jsou k dispozici pro optimalizaci a správu vaší analytické platformy pomocí SQL Data Warehouse. 

## <a name="resource-utilization"></a>Využití prostředků 
V Azure Portal pro SQL Data Warehouse jsou k dispozici následující metriky. Tyto metriky se procházejí [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).


| Název metriky             | Popis                                                  | Typ agregace |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Procento CPU          | Využití CPU ve všech uzlech pro datový sklad      | Maximum          |
| Procento datových V/V      | Využití v/v na všech uzlech pro datový sklad       | Maximum          |
| Procento paměti       | Využití paměti (SQL Server) ve všech uzlech pro datový sklad | Maximum          |
| Úspěšná připojení  | Počet úspěšných připojení k datům                 | Celkem            |
| Neúspěšná připojení      | Počet neúspěšných připojení k datovému skladu           | Celkem            |
| Blokováno bránou firewall     | Počet přihlášení k datovému skladu, který byl zablokován     | Celkem            |
| DWU limit               | Cíl na úrovni služby datového skladu                | Maximum          |
| Procento DWU          | Maximální procento CPU a procento v/v dat        | Maximum          |
| DWU použito                | DWU limit * DWU procento                                   | Maximum          |
| Procento přístupů do mezipaměti    | (Neúspěšné přístupy do mezipaměti a Neúspěšné přístupy do mezipaměti) * 100 kde jsou přístupy do mezipaměti součtem všech segmentů columnstore v místní mezipaměti SSD a neúspěšných přístupů do mezipaměti jsou segmenty columnstore v místní mezipaměti SSD vyčtené ve všech uzlech. | Maximum          |
| Procento využité mezipaměti   | (využitá mezipaměť/kapacita mezipaměti) * 100, kde použitá mezipaměť je součet všech bajtů v místní mezipaměti SSD napříč všemi uzly a kapacita mezipaměti je součet kapacity úložiště místní mezipaměti SSD napříč všemi uzly. | Maximum          |
| Místní procento databáze tempdb | Místní využití databáze tempdb napříč všemi výpočetními uzly – hodnoty se generují každých pět minut. | Maximum          |

> Co je potřeba vzít v úvahu při prohlížení metrik a nastavení výstrah:
>
> - Neúspěšná a úspěšná připojení se nahlásí pro konkrétní datový sklad – ne pro logický Server.
> - Procento paměti odráží využití i v případě, že je datový sklad v nečinném stavu. neodráží spotřebu paměti aktivní úlohy. Tuto metriku můžete použít a sledovat společně s ostatními (tempdb, Gen2 cache) a vytvořit si holistický rozhodnutí o tom, jestli škálování pro další kapacitu mezipaměti zvýší výkon úlohy, aby splňovala vaše požadavky.


## <a name="query-activity"></a>Aktivita dotazu
Pro programové prostředí při monitorování SQL Data Warehouse přes T-SQL poskytuje služba sadu zobrazení dynamické správy (zobrazení dynamické správy). Tato zobrazení jsou užitečná při aktivním řešení potíží a identifikaci slabých míst výkonu s vaším zatížením.

Chcete-li zobrazit seznam zobrazení dynamické správy, které poskytuje SQL Data Warehouse, přečtěte si tuto [dokumentaci](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metriky a protokolování diagnostiky
Obě metriky a protokoly je možné exportovat do Azure Monitor, konkrétně do [Azure monitor protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) a můžou být prostřednictvím [dotazů protokolu](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)programově dostupné. Latence protokolu pro SQL Data Warehouse je přibližně 10-15 minut. Další informace o odezvách, které mají vliv na latenci, najdete v následující dokumentaci.


## <a name="next-steps"></a>Další kroky
Následující příručky návody popisují běžné scénáře a případy použití při monitorování a správě datového skladu:

- [Monitorování úloh datového skladu pomocí zobrazení dynamické správy](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
