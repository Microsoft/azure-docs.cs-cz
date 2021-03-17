---
title: Optimalizace mezipaměti Gen2
description: Naučte se monitorovat mezipaměť Gen2 pomocí Azure Portal.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 041751b5b23dbb3153f1ae638303579a860c0e5b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020159"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>Jak monitorovat adaptivní mezipaměť

Tento článek popisuje, jak monitorovat a řešit potíže s pomalým výkonem dotazů tím, že určíte, jestli vaše zatížení optimálně využívá adaptivní mezipaměť pro vyhrazené fondy SQL.

Vyhrazená architektura úložiště fondu SQL automaticky vychází z nejčastěji dotazovaných segmentů columnstore v mezipaměti, která je umístěná v SSD založených na NVMe. Když dotazy načítají segmenty, které jsou umístěné v mezipaměti, budete mít větší výkon.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Řešení potíží pomocí Azure Portal

Pomocí Azure Monitor můžete zobrazit metriky mezipaměti a řešit potíže s výkonem dotazů. Nejprve přejděte na Azure Portal a klikněte na **monitorování**, **metriky** a **Vyberte rozsah**:

![Snímek obrazovky ukazuje vybrat obor vybraný z metrik v Azure Portal.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

K vyhledání vyhrazeného fondu SQL použijte panely hledání a rozevírací seznam. Pak vyberte použít.

![Snímek obrazovky se zobrazí v podokně vybrat obor, kde můžete vybrat svůj datový sklad.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

Klíčovou metrikou pro řešení potíží s mezipamětí jsou **Procento přístupů do mezipaměti** a **procento využité v mezipaměti**. Vyberte **Procento přístupů do mezipaměti** a potom pomocí tlačítka **Přidat metriku** přidejte **procento využité mezipaměti**. 

![Metriky mezipaměti](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Počet přístupů do mezipaměti a použité procento

Následující matice popisuje scénáře v závislosti na hodnotách metrik mezipaměti:

|                                | **Procento přístupů do vysoké mezipaměti** | **Procento přístupů do mezipaměti v nízkém rozsahu** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Procento využité vysoké mezipaměti** |          Scénář 1           |          Scénář 2          |
| **Procento využité mezipaměti nízké úrovně**  |          Scénář 3           |          Scénář 4          |

**Scénář 1:** Používáte svou mezipaměť optimálně. [Řešení potíží s](sql-data-warehouse-manage-monitor.md) ostatními oblastmi, které mohou zpomalovat vaše dotazy.

**Scénář 2:** Vaše aktuální pracovní sada dat se nemůže vejít do mezipaměti, která způsobuje nedostatečné procento přístupů do mezipaměti z důvodu fyzických čtení. Zvažte možnost škálovat úroveň výkonu a znovu spustit úlohu, abyste mohli naplnit mezipaměť.

**Scénář 3:** Je možné, že dotaz běží pomalu z důvodů, které nesouvisí s mezipamětí. [Řešení potíží s](sql-data-warehouse-manage-monitor.md) ostatními oblastmi, které mohou zpomalovat vaše dotazy. Můžete také zvážit horizontální snížení kapacity [instance](sql-data-warehouse-manage-monitor.md) , aby se snížila velikost mezipaměti, aby se ušetřily náklady. 

**Scénář 4:** Měli byste mít studenou mezipaměť, což by mohlo být důvod, proč byl dotaz pomalý. Zkuste dotaz znovu spustit, protože pracovní sada dat by měla být teď v mezipaměti. 

> [!IMPORTANT]
> Pokud procento přístupů do mezipaměti nebo procento využití mezipaměti není po reběhu úlohy aktualizováno, vaše pracovní sada už může být umístěná v paměti. Pouze clusterované tabulky columnstore jsou uloženy v mezipaměti.

## <a name="next-steps"></a>Další kroky
Další informace o obecném ladění výkonu dotazů najdete v tématu [monitorování provádění dotazů](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
