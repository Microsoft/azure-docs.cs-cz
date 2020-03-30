---
title: Optimalizace mezipaměti Gen2
description: Přečtěte si, jak sledovat mezipaměť Gen2 pomocí portálu Azure.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bb5560164af2b573e6aaffd4e4c62bbe0dc24a51
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350424"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Jak sledovat mezipaměť Gen2

Tento článek popisuje, jak sledovat a řešit pomalý výkon dotazu určením, zda vaše úloha optimálně využívá mezipaměť Gen2.

Architektura úložiště Gen2 automaticky vrství vaše nejčastěji dotazované segmenty columnstore v mezipaměti, která se nachází na ssd discích založených na NVMe určených pro datové sklady Gen2. Vyšší výkon je realizován, když vaše dotazy načíst segmenty, které jsou s bydlištěm v mezipaměti.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Poradce při potížích s používáním portálu Azure

Azure Monitor můžete použít k zobrazení metriky mezipaměti Gen2 k řešení výkonu dotazů. Nejprve přejděte na portál Azure a klikněte na **Monitor**, **Metriky** a **+ Vyberte obor**:

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Pomocí vyhledávacích a rozevíracích pruhů vyhledejte datový sklad. Pak vyberte použít.

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

Klíčové metriky pro řešení potíží s mezipaměť Gen2 jsou **procento přístupů do mezipaměti** a **procento použité mezipaměti**. Vyberte **procento zásahu do mezipaměti** a potom pomocí tlačítka **Přidat metriku** přidejte **procento použité mezipaměti**. 

![Metriky mezipaměti](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Procento přístupů a použitých v mezipaměti

Níže uvedená matice popisuje scénáře založené na hodnotách metrik mezipaměti:

|                                | **Procento přístupů s vysokou mezipamětí** | **Procento zásahu při nízké mezipaměti** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Procento použití vysoké mezipaměti** |          Scénář 1           |          Scénář 2          |
| **Procento použití nízké mezipaměti**  |          Scénář 3           |          Scénář 4          |

**Scénář 1:** Jste optimálně pomocí mezipaměti. [Poradce při potížích s](sql-data-warehouse-manage-monitor.md) jinými oblastmi, které mohou zpomalovat vaše dotazy.

**Scénář 2:** Aktuální sada pracovních dat se nevejde do mezipaměti, což způsobuje nízké procento přístupů do mezipaměti z důvodu fyzického čtení. Zvažte navýšení úrovně výkonu a opětovné spuštění úlohy k naplnění mezipaměti.

**Scénář 3:** Je pravděpodobné, že dotaz běží pomalu z důvodů, které nesouvisejí s mezipamětí. [Poradce při potížích s](sql-data-warehouse-manage-monitor.md) jinými oblastmi, které mohou zpomalovat vaše dotazy. Můžete také zvážit [zmenšení instance,](sql-data-warehouse-manage-monitor.md) abyste snížili velikost mezipaměti a ušetřili náklady. 

**Scénář 4:** Měli jste studenou cache, která by mohla být důvodem, proč byl váš dotaz pomalý. Zvažte opětovné spuštění dotazu, protože pracovní datová sada by nyní měla být v mezipaměti. 

> [!IMPORTANT]
> Pokud se procento přístupů do mezipaměti nebo procento použité mezipaměti neaktualizuje po opětovném spuštění úlohy, může být pracovní sada již uložena v paměti. Pouze clusterované tabulky columnstore jsou ukládány do mezipaměti.

## <a name="next-steps"></a>Další kroky
Další informace o obecném ladění výkonu dotazu naleznete v [tématu Sledování provádění dotazů](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
