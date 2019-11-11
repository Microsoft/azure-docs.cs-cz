---
title: Optimalizace mezipaměti Gen2
description: Naučte se monitorovat mezipaměť Gen2 pomocí Azure Portal.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b33f7cedca4ef130eefa28c1dbaaedd82d11a9e4
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645768"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Jak monitorovat mezipaměť Gen2
Architektura úložiště Gen2 automaticky vychází z nejčastěji dotazovaných segmentů columnstore v mezipaměti, která je umístěná v NVMe na SSD, která je určená pro Gen2 datové sklady. Vyšší výkon je realizován, když dotazy načítají segmenty, které jsou umístěny v mezipaměti. Tento článek popisuje, jak monitorovat a řešit potíže s pomalým výkonem dotazů tím, že určíte, jestli vaše zatížení optimálně využívá Gen2 cache.  
## <a name="troubleshoot-using-the-azure-portal"></a>Řešení potíží pomocí Azure Portal
Pomocí Azure Monitor můžete zobrazit metriky mezipaměti Gen2 a řešit potíže s výkonem dotazů. Nejprve přejděte na Azure Portal a klikněte na monitorování:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Vyberte tlačítko metriky a vyplňte **předplatné**, **skupinu** **prostředků** , **typ prostředku**a **název prostředku** datového skladu.

Klíčovou metrikou pro řešení potíží s mezipamětí Gen2 je **Procento přístupů do mezipaměti** a **procento využití mezipaměti**. Nakonfigurujte graf metrik Azure tak, aby zobrazoval tyto dvě metriky.

![Metriky mezipaměti](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Počet přístupů do mezipaměti a použité procento
Následující matice popisuje scénáře v závislosti na hodnotách metrik mezipaměti:

|                                | **Procento přístupů do vysoké mezipaměti** | **Procento přístupů do mezipaměti v nízkém rozsahu** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Procento využité vysoké mezipaměti** |          Scénář 1           |          Scénář 2          |
| **Procento využité mezipaměti nízké úrovně**  |          Scénář 3           |          Scénář 4          |

**Scénář 1:** Používáte svou mezipaměť optimálně. [Řešení potíží s](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) ostatními oblastmi, které mohou zpomalovat vaše dotazy.

**Scénář 2:** Vaše aktuální pracovní sada dat se nemůže vejít do mezipaměti, která způsobuje nedostatečné procento přístupů do mezipaměti z důvodu fyzických čtení. Zvažte možnost škálovat úroveň výkonu a znovu spustit úlohu, abyste mohli naplnit mezipaměť.

**Scénář 3:** Je možné, že dotaz běží pomalu z důvodů, které nesouvisí s mezipamětí. [Řešení potíží s](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) ostatními oblastmi, které mohou zpomalovat vaše dotazy. Můžete také zvážit horizontální snížení kapacity [instance](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) , aby se snížila velikost mezipaměti, aby se ušetřily náklady. 

**Scénář 4:** Měli byste mít studenou mezipaměť, což by mohlo být důvod, proč byl dotaz pomalý. Zkuste dotaz znovu spustit, protože pracovní sada dat by měla být teď v mezipaměti. 

**Důležité: Pokud procento přístupů do mezipaměti nebo procento využité mezipaměti neaktualizujete po spuštění úlohy, může být vaše pracovní sada už umístěná v paměti. Poznámka: pouze clusterované tabulky columnstore jsou uloženy v mezipaměti.**

## <a name="next-steps"></a>Další kroky
Další informace o obecném ladění výkonu dotazů najdete v tématu [monitorování provádění dotazů](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
