---
title: Azure SQL Data Warehouse možnosti správy a monitorování - dotaz aktivity, využití prostředků | Dokumentace Microsoftu
description: Zjistěte, jaké možnosti jsou k dispozici ke správě a monitorování Azure SQL Data Warehouse. Pomocí webu Azure portal a zobrazení dynamické správy (DMV) můžete pochopit dotaz aktivity a využití prostředků datového skladu.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 06/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 5038ae99a804b456c2cc388f07899278cc0f9a24
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312884"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Monitorování využití a dotaz aktivity prostředků ve službě Azure SQL Data Warehouse
Azure SQL Data Warehouse poskytuje bohaté možnosti monitorování na webu Azure portal na povrchu přehledy, které vaše úlohy datového skladu. Na webu Azure portal je doporučeným nástrojem při monitorování datového skladu, protože poskytuje možnost konfigurace uchovávání období, výstrah, doporučení a přizpůsobitelné grafy a řídicí panely pro metriky a protokoly. Na portálu můžete také integrovat s dalšími službami Azure monitorování jako je například Operations Management Suite (OMS) a Azure Monitor (protokoly) poskytuje holistické možnosti monitorování pouze váš datový sklad, ale také celou analýz Azure Platforma pro integrované možnosti monitorování. Tato dokumentace popisuje, jaké možnosti monitorování jsou k dispozici optimalizovat a spravovat vaši platformu analýzy s využitím SQL Data Warehouse. 

## <a name="resource-utilization"></a>Využití prostředků 
Tyto metriky jsou k dispozici na webu Azure Portal pro službu SQL Data Warehouse. Tyto metriky jsou prezentované prostřednictvím [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).

> [!NOTE]
> Aktuálně úrovni uzlu procesoru a vstupně-výstupních operací metriky neodrážejí správně použití datového skladu. Tyto metriky se odebere v blízké budoucnosti tým zlepšuje monitorování a odstraňování potíží pro službu SQL Data Warehouse. 

| Název metriky             | Popis                                                  | Typ agregace |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Procento CPU          | Využití procesoru napříč všemi uzly pro datový sklad      | Maximum          |
| Procento datových V/V      | Využití vstupně-výstupní operace napříč všemi uzly pro datový sklad       | Maximum          |
| Procento paměti       | Využití paměti (SQL Server) na všech uzlech pro datový sklad | Maximum          |
| Úspěšná připojení  | Počet úspěšných připojení k datům                 | Celkem            |
| Neúspěšná připojení      | Počet selhání připojení k datovému skladu           | Celkem            |
| Blokovaná bránou Firewall     | Počet přihlášení k datovému skladu, který se zablokoval     | Celkem            |
| Limit jednotky               | Cíl na úrovni služby datového skladu                | Maximum          |
| Procento DWU          | Maximální počet mezi procento využití procesoru a vstupů/výstupů dat        | Maximum          |
| Použít DWU                | Limit jednotky * procento DWU                                   | Maximum          |
| Procenta přístupů do mezipaměti    | (úspěšné přístupy do mezipaměti / Neúspěšné přístupy do mezipaměti) * 100 kde přístupů k mezipaměti je součet všech přístupů segmentů columnstore v místní mezipaměti SSD a Neúspěšné přístupy do mezipaměti je segmentů columnstore výpadky v místní mezipaměti SSD sčítat napříč všemi uzly | Maximum          |
| Procento použitá mezipaměť   | (mezipaměť použije / mezipaměti kapacity) * napříč všemi uzly mezipaměti 100, kde je mezipaměť používaná součtu všech bajtů v místní mezipaměti SSD napříč všemi uzly a kapacitu mezipaměti je součet úložnou kapacitu místní disk SSD | Maximum          |
| Procento místní databáze tempdb | Využití místní databázi tempdb na všech výpočetních uzlech - hodnoty jsou emitovány každých pět minut | Maximum          |

## <a name="query-activity"></a>Aktivita dotazů
Pro programový výkon při monitorování SQL Data Warehouse pomocí T-SQL služby poskytuje sadu zobrazení dynamické správy (DMV). Tato zobrazení jsou užitečné, pokud aktivně řešení potíží a nalezení problémových míst výkonu pomocí vašich úloh.

Chcete-li zobrazit seznam zobrazení dynamické správy, které poskytuje SQL Data Warehouse, najdete to [dokumentaci](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metriky a protokolování diagnostiky
Metriky a protokoly je možné exportovat do Azure monitoru, konkrétně [protokoly Azure monitoru](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) komponenty a programově přistupuje prostřednictvím [protokolu dotazy](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). Latence protokolu pro službu SQL Data Warehouse je asi 10 až 15 minut. Další podrobnosti o faktorů vliv na latenci najdete v následující dokumentaci.


## <a name="next-steps"></a>Další postup
Průvodce následující postupy popisují běžné scénáře a pomocí případech, kdy monitorování a správa datového skladu:

- [Monitorování úloh datového skladu pomocí zobrazení dynamické správy](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

