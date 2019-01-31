---
title: Azure SQL Data Warehouse možnosti správy a monitorování - dotaz aktivity, využití prostředků | Dokumentace Microsoftu
description: Zjistěte, jaké možnosti jsou k dispozici ke správě a monitorování Azure SQL Data Warehouse. Pomocí webu Azure portal a zobrazení dynamické správy (DMV) můžete pochopit dotaz aktivity a využití prostředků datového skladu.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/27/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 4613a16ee27168dd5c00435ee04fa5a7f95f4d97
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460416"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Monitorování využití a dotaz aktivity prostředků ve službě Azure SQL Data Warehouse
Azure SQL Data Warehouse poskytuje bohaté možnosti monitorování na webu Azure portal na povrchu přehledy, které vaše úlohy datového skladu. Na webu Azure portal je doporučeným nástrojem při monitorování datového skladu, protože poskytuje možnost konfigurace uchovávání období, výstrah, doporučení a přizpůsobitelné grafy a řídicí panely pro metriky a protokoly. Na portálu můžete také integrovat další služby, jako je například Operations Management Suite (OMS) pro monitorování Azure / Log Analytics a Azure Monitor k poskytování komplexní monitorování prostředí pro pouze váš datový sklad, ale také celou Azure analytická platforma pro integrované možnosti monitorování. Tato dokumentace popisuje, jaké možnosti monitorování jsou k dispozici optimalizovat a spravovat vaši platformu analýzy s využitím SQL Data Warehouse. 

## <a name="resource-utilization"></a>Využití prostředků 
Tyto metriky jsou k dispozici na webu Azure Portal pro službu SQL Data Warehouse. Tyto metriky jsou prezentované prostřednictvím [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).

> [!NOTE]
> Od listopadu 2018 technický tým řeší problém způsobující procento využití procesoru a vstupů/výstupů dat do underreport. To způsobí, že používá DWU a procento underreport také. 

| Název metriky                           | Popis     | Typ agregace |
| --------------------------------------- | ---------------- | --------------------------------------- |
| Procento CPU                          | Využití procesoru napříč všemi uzly pro datový sklad | Maximum      |
| Procento datových V/V                      | Využití vstupně-výstupní operace napříč všemi uzly pro datový sklad | Maximum   |
| Úspěšná připojení                  | Počet úspěšných připojení k datům | Celkem            |
| Chyba připojení                      | Počet selhání připojení k datovému skladu | Celkem            |
| Blokovaná bránou Firewall                     | Počet přihlášení k datovému skladu, který se zablokoval | Celkem            |
| Limit jednotky                              | Cíl na úrovni služby datového skladu | Maximum   |
| Procento DWU                          | Maximální počet mezi procento využití procesoru a vstupů/výstupů dat | Maximum   |
| Použít DWU                                | Limit jednotky * procento DWU | Maximum   |
| Procenta přístupů do mezipaměti | (úspěšné přístupy do mezipaměti / Neúspěšné přístupy do mezipaměti) * 100 kde přístupů k mezipaměti je součet všech přístupů segmentů columnstore v místní mezipaměti SSD a Neúspěšné přístupy do mezipaměti je segmentů columnstore výpadky v místní mezipaměti SSD sčítat napříč všemi uzly | Maximum |
| Procento použitá mezipaměť | (mezipaměť použije / mezipaměti kapacity) * napříč všemi uzly mezipaměti 100, kde je mezipaměť používaná součtu všech bajtů v místní mezipaměti SSD napříč všemi uzly a kapacitu mezipaměti je součet úložnou kapacitu místní disk SSD | Maximum |
| Procento místní databáze tempdb | Využití místní databázi tempdb na všech výpočetních uzlech - hodnoty jsou emitovány každých pět minut | Maximum |

## <a name="query-activity"></a>Aktivita dotazů
Pro programový výkon při monitorování SQL Data Warehouse pomocí T-SQL služby poskytuje sadu zobrazení dynamické správy (DMV). Tato zobrazení jsou užitečné, pokud aktivně řešení potíží a nalezení problémových míst výkonu pomocí vašich úloh.

Chcete-li zobrazit seznam zobrazení dynamické správy, které poskytuje SQL Data Warehouse, najdete to [dokumentaci](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metriky a protokolování diagnostiky
Metriky a protokoly je možné exportovat do Azure monitoru, konkrétně [Log analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) komponenty a programově přistupuje prostřednictvím [prohledávání protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata).


> [!NOTE]
> Od listopadu 2018 protokoly jsou momentálně se nasazuje pro SQL Data Warehouse

## <a name="next-steps"></a>Další postup
Průvodce následující postupy popisují běžné scénáře a pomocí případech, kdy monitorování a správa datového skladu:

- [Monitorování úloh datového skladu pomocí zobrazení dynamické správy](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

