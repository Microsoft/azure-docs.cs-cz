---
title: Co je Azure synapse Analytics (dřív SQL DW)?
description: Azure synapse Analytics (dřív SQL DW) je bezlimitní analytická služba, která přináší dohromady podnikové datové sklady a analýzu velkých objemů dat.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 27311270c1383a54192d072d0e152c7cf1a58225
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85200949"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Co je Azure synapse Analytics (dřív SQL DW)?

> [!NOTE] 
>Prozkoumejte [dokumentaci ke službě Azure synapse (pracovní prostory Preview)](../overview-what-is.md).
>

Azure synapse je analytická služba, která přináší dohromady podnikové datové sklady a analýzu velkých objemů dat. Dává vám možnost dotazovat se na data podle toho, jak vám to vyhovuje, s využitím bezserverové architektury na vyžádání, nebo zřízených prostředků, a to ve velkém měřítku. Azure Synapse spojuje tyto dva světy do sjednoceného prostředí, které nabízí příjem, přípravu, správu a obsluhu dat pro aktuální potřeby BI a strojového učení.

Azure synapse má čtyři součásti:

- Synapse SQL: kompletní analýzy založené na T-SQL – všeobecně dostupná
  - Fond SQL (placený za DWU zřízený)
  - SQL na vyžádání (platba za TB zpracovaná) (Preview)
- Spark: hluboce integrované Apache Spark (Preview)
- Kanály synapse: integrace hybridních dat (Preview)
- Studio: jednotné uživatelské prostředí. (Preview)

## <a name="synapse-sql-pool-in-azure-synapse"></a>Synapse fond SQL v Azure synapse

Synapse fond SQL odkazuje na funkce podnikových dat v datovém skladu, které jsou všeobecně dostupné v Azure synapse.

Fond SQL představuje kolekci analytických prostředků, které se zřídí při používání synapse SQL. Velikost fondu SQL je určena jednotkami datového skladu (DWU).

Importujte velké objemy dat pomocí jednoduchých [základních](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dotazů T-SQL a potom Využijte sílu MPP ke spouštění vysoce výkonných analýz. Při integraci a analýze se synapse fond SQL stane jedinou verzí pravdy, kterou vaše firma může počítat pro rychlejší a robustnější přehledy.  

## <a name="key-component-of-a-big-data-solution"></a>Klíčová součást řešení pro velké objemy dat

Datové sklady jsou klíčovou součástí cloudového a komplexního řešení pro velké objemy dat.

![Řešení datového skladu](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

V cloudovém datovém řešení se data ingestují do velkoobjemových úložišť dat z nejrůznějších zdrojů. Jakmile jsou data ve velkoobjemovém úložišti dat, můžou se připravit a vytrénovat pomocí Hadoopu, Sparku a algoritmů strojového učení. Když jsou data připravená na složitou analýzu, synapse fond SQL používá základnu k dotazování na úložiště s velkými objemy dat. Základ používá standardní dotazy T-SQL k převedení dat do tabulek synapse fondu SQL.

Synapse fond SQL ukládá data v relačních tabulkách s sloupcovým úložištěm. Tento formát výrazně snižuje náklady na úložiště dat a zvyšuje výkon dotazů. Po uložení dat můžete provádět analýzy v masivním měřítku. V porovnání s tradičními databázovými systémy se analytické dotazy dokončí v řádu sekund namísto minut nebo v řádu hodin namísto dnů.

Výsledky analýz můžou jít do aplikací nebo databází pro generování sestav po celém světě. Obchodní analytici pak můžou získávat přehledy, které jim umožní provádět informovaná obchodní rozhodnutí.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte [architekturu Azure synapse](massively-parallel-processing-mpp-architecture.md)
- Rychlé [Vytvoření fondu SQL](create-data-warehouse-portal.md)
- [Načíst ukázková data](load-data-from-azure-blob-storage-using-polybase.md).
- Prozkoumat [videa](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Nebo se podívejte na některé z těchto dalších prostředků Azure synapse.

- Hledat [Blogy](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- Odeslání [žádostí o funkce](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Vytvoření lístku podpory](sql-data-warehouse-get-started-create-support-ticket.md)
- Hledání [stránky s otázkou v Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- Hledat [Stack Overflow Fórum](https://stackoverflow.com/questions/tagged/azure-sqldw)
