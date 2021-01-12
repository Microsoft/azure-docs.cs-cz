---
title: Co je vyhrazený fond SQL (dřív SQL DW)?
description: Vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics je podniková funkce datového skladu v Azure synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b8738b46359287ce1d4e410292cdce213ab80999
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115816"
---
# <a name="what-is-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Co je vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics?

Azure Synapse Analytics je analytická služba, která spojuje podnikové datové sklady s analýzou velkých objemů dat. Vyhrazený fond SQL (dřív SQL DW) odkazuje na funkce podnikových dat v datovém skladu, které jsou k dispozici ve službě Azure synapse Analytics.



![Vyhrazený fond SQL (dřív SQL DW) ve vztahu k Azure synapse](./media/sql-data-warehouse-overview-what-is/dedicated-sql-pool.png)



Vyhrazený fond SQL (dříve SQL DW) představuje kolekci analytických prostředků, které jsou zřízené při používání synapse SQL. Velikost vyhrazeného fondu SQL (dříve SQL DW) je určená jednotkami datového skladu (DWU).

Po vytvoření vyhrazeného fondu SQL můžete importovat velké objemy dat pomocí jednoduchých [základních](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dotazů T-SQL a pak využít sílu modulu distribuovaných dotazů ke spouštění vysoce výkonných analýz. Při integraci a analýze dat se vyhrazený fond SQL (dřív SQL DW) stane jedinou verzí pravdy, kterou vaše firma může počítat pro rychlejší a robustnější přehledy.

> [!NOTE]
>Prozkoumejte [dokumentaci ke službě Azure synapse Analytics](../overview-what-is.md).
> 

## <a name="key-component-of-a-big-data-solution"></a>Klíčová součást řešení pro velké objemy dat

Datové sklady jsou klíčovou součástí cloudového a komplexního řešení pro velké objemy dat.

![Řešení datového skladu](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

V cloudovém datovém řešení se data ingestují do velkoobjemových úložišť dat z nejrůznějších zdrojů. Jakmile jsou data ve velkoobjemovém úložišti dat, můžou se připravit a vytrénovat pomocí Hadoopu, Sparku a algoritmů strojového učení. Když jsou data připravená na složitou analýzu, vyhrazený fond SQL používá základnu k dotazování na úložiště s velkými objemy dat. Základ používá standardní dotazy T-SQL k přenesení dat do vyhrazených tabulek fondu SQL (dříve SQL DW).

Vyhrazený fond SQL (dříve SQL DW) ukládá data v relačních tabulkách s sloupcovým úložištěm. Tento formát výrazně snižuje náklady na úložiště dat a zvyšuje výkon dotazů. Po uložení dat můžete provádět analýzy v masivním měřítku. V porovnání s tradičními databázovými systémy se analytické dotazy dokončí v řádu sekund namísto minut nebo v řádu hodin namísto dnů.

Výsledky analýz můžou jít do aplikací nebo databází pro generování sestav po celém světě. Obchodní analytici pak můžou získávat přehledy, které jim umožní provádět informovaná obchodní rozhodnutí.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte [architekturu Azure synapse](massively-parallel-processing-mpp-architecture.md)
- Rychlé [vytvoření vyhrazeného fondu SQL](create-data-warehouse-portal.md)
- [Načíst ukázková data](./load-data-from-azure-blob-storage-using-copy.md).
- Prozkoumat [videa](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Nebo se podívejte na některé z těchto dalších prostředků Azure synapse.

- Hledat [Blogy](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- Odeslání [žádostí o funkce](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Vytvoření lístku podpory](sql-data-warehouse-get-started-create-support-ticket.md)
- Hledání [stránky s otázkou v Microsoft Q&](/answers/topics/azure-synapse-analytics.html)
- Hledat [Stack Overflow Fórum](https://stackoverflow.com/questions/tagged/azure-sqldw)