---
title: Správa a monitorování – aktivita dotazů, využití prostředků
description: Zjistěte, jaké možnosti jsou dostupné pro správu a monitorování Azure synapse Analytics. Pomocí zobrazení Azure Portal a dynamické správy (zobrazení dynamické správy) můžete pochopit aktivity dotazů a využití prostředků datového skladu.
services: synapse-analytics
author: julieMSFT
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7b103991e22ffab8ed5bd2b3c10400330e1d09b3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568399"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitorování využití prostředků a aktivity dotazů v Azure synapse Analytics

Azure synapse Analytics poskytuje bohatě monitorovaný přehled v rámci Azure Portal k obí informací o úlohách datového skladu. Azure Portal je doporučeným nástrojem při monitorování datového skladu, protože poskytuje konfigurovatelné doby uchovávání, výstrahy, doporučení a přizpůsobitelné grafy a řídicí panely pro metriky a protokoly. Portál také umožňuje integraci s dalšími službami monitorování Azure, jako jsou Azure Monitor (protokoly), s Log Analytics a poskytuje tak prostředí pro monitorování holistický nejen pro datový sklad, ale i celou platformu Azure Analytics pro integrované monitorování. Tato dokumentace popisuje, jaké možnosti monitorování jsou k dispozici pro optimalizaci a správu analytických platforem pomocí synapse SQL.

## <a name="resource-utilization"></a>Využití prostředků

V Azure Portal pro synapse SQL jsou k dispozici následující metriky. Tyto metriky se procházejí [Azure monitor](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics).

| Název metriky             | Description                                                  | Typ agregace |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Procento CPU          | Využití CPU ve všech uzlech pro datový sklad      | AVG, min, Max    |
| Procento datových V/V      | Využití v/v na všech uzlech pro datový sklad       | AVG, min, Max    |
| Procento paměti       | Využití paměti (SQL Server) ve všech uzlech pro datový sklad | AVG, min, Max   |
| Aktivní dotazy          | Počet aktivních dotazů zpracovávaných v systému             | Sum              |
| Dotazy ve frontě          | Počet dotazů ve frontě čekajících na zahájení provádění          | Sum              |
| Úspěšná připojení  | Počet úspěšných připojení (přihlášení) k databázi | Sum, Count       |
| Neúspěšná připojení      | Počet neúspěšných připojení (přihlášení) k databázi | Sum, Count       |
| – Blokováno bránou firewall     | Počet přihlášení k datovému skladu, který byl zablokován     | Sum, Count       |
| DWU limit               | Cíl na úrovni služby datového skladu                | AVG, min, Max    |
| Procento DWU          | Maximální procento CPU a procento v/v dat        | AVG, min, Max    |
| DWU použito                | DWU limit * DWU procento                                   | AVG, min, Max    |
| Procento přístupů do mezipaměti    | (Neúspěšné přístupy do mezipaměti a Neúspěšné přístupy do mezipaměti) * 100 kde jsou přístupy do mezipaměti součtem všech segmentů columnstore v místní mezipaměti SSD a neúspěšných přístupů do mezipaměti jsou segmenty columnstore v místní mezipaměti SSD vyčtené ve všech uzlech. | AVG, min, Max    |
| Procento využité mezipaměti   | (využitá mezipaměť/kapacita mezipaměti) * 100, kde použitá mezipaměť je součet všech bajtů v místní mezipaměti SSD napříč všemi uzly a kapacita mezipaměti je součet kapacity úložiště místní mezipaměti SSD napříč všemi uzly. | AVG, min, Max    |
| Místní procento databáze tempdb | Místní využití databáze tempdb napříč všemi výpočetními uzly – hodnoty se generují každých pět minut. | AVG, min, Max    |

Co je potřeba vzít v úvahu při prohlížení metrik a nastavení výstrah:

- DWU používá **reprezentaci pouze vysoké úrovně využití** napříč fondem SQL a není určena k komplexnímu indikátoru využití. Pokud chcete určit, jestli se má horizontální navýšení nebo snížení kapacity škálovat, vezměte v úvahu všechny faktory, které můžou být ovlivněné DWU, jako je Concurrency, Memory, tempdb a adaptivní kapacita mezipaměti. Doporučujeme [spouštět vaše úlohy v různých nastaveních DWU](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) a určit, co nejlépe vyhovuje vašim obchodním cílům.
- Neúspěšná a úspěšná připojení se nahlásí pro konkrétní datový sklad – ne pro samotný server.
- Procento paměti odráží využití i v případě, že je datový sklad v nečinném stavu. neodráží spotřebu paměti aktivní úlohy. Tuto metriku můžete použít a sledovat společně s ostatními (tempdb, Gen2 cache) a vytvořit si holistický rozhodnutí o tom, jestli škálování pro další kapacitu mezipaměti zvýší výkon úlohy, aby splňovala vaše požadavky.

## <a name="query-activity"></a>Aktivita dotazu

Pro programové prostředí při monitorování synapse SQL prostřednictvím T-SQL poskytuje služba sadu zobrazení dynamické správy (zobrazení dynamické správy). Tato zobrazení jsou užitečná při aktivním řešení potíží a identifikaci slabých míst výkonu s vaším zatížením.

Pokud chcete zobrazit seznam zobrazení dynamické správy, které platí pro synapse SQL, přečtěte si tuto [dokumentaci](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metriky a diagnostické protokolování 

Obě metriky a protokoly je možné exportovat do Azure Monitor, konkrétně do [Azure monitor protokolů](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a můžou být prostřednictvím [dotazů protokolu](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)programově dostupné. Latence protokolu pro synapse SQL má přibližně 10-15 minut. Další informace o odezvách, které mají vliv na latenci, najdete v následující dokumentaci.

## <a name="next-steps"></a>Další kroky

Následující příručka popisuje běžné scénáře a případy použití při monitorování a správě datového skladu:

- [Monitorování úloh datového skladu pomocí zobrazení dynamické správy](sql-data-warehouse-manage-monitor.md)