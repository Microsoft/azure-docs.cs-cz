---
title: Co je Azure Synapse Analytics (dříve SQL DW)?
description: Azure Synapse Analytics (dříve SQL DW) je neomezená analytická služba, která sdružuje podnikové datové sklady a analýzy velkých objemů dat.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a118d028cc85eb858dd0dc1fa6d5d2268f7db43b
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350404"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Co je Azure Synapse Analytics (dříve SQL DW)?

Azure Synapse je neomezená analytická služba, která spojuje podnikové skladování dat a analýzy velkých objemů dat. Dává vám možnost dotazovat se na data podle toho, jak vám to vyhovuje, s využitím bezserverové architektury na vyžádání, nebo zřízených prostředků, a to ve velkém měřítku. Azure Synapse přináší tyto dva světy spolu s jednotným prostředím pro ingestování, přípravu, správu a poskytování dat pro okamžité potřeby BI a strojového učení

Azure Synapse má čtyři součásti:
- SQL Analytics: Kompletní analýza založená na T-SQL – obecně dostupná
    - Fond SQL (platba za dwu zřízené) 
    - SQL na vyžádání (platba za zpracované TB) – (náhled)
- Jiskra: Hluboce integrovaná Apache Spark (náhled) 
- Integrace dat: Hybridní integrace dat (náhled)
- Studio: Jednotné uživatelské prostředí.  (Preview)

> [!NOTE]
> Chcete-li získat přístup k funkcím náhledu služby Azure Synapse, požádejte o přístup [zde](https://aka.ms/synapsepreview). Společnost Microsoft bude třídění všech požadavků a reagovat co nejdříve.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>SQL Analytics a fond SQL ve službě Azure Synapse

SQL Analytics odkazuje na funkce podnikového datového skladu, které jsou obecně dostupné v Azure Synapse. 

Fond SQL představuje kolekci analytických prostředků, které jsou zřizovány při použití SQL Analytics. Velikost fondu SQL je určena datovými skladovými jednotkami (DWU).

Importujte velká data pomocí jednoduchých dotazů [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL a pak použijte sílu MPP ke spuštění vysoce výkonné analýzy. Při integraci a analýze se SQL Analytics stane jedinou verzí pravdy, na kterou se vaše firma může spolehnout, pro rychlejší a robustnější přehledy.  

## <a name="key-component-of-a-big-data-solution"></a>Klíčová součást řešení pro velké objemy dat

Datové sklady jsou klíčovou součástí cloudového komplexního řešení pro velké objemy dat.

![Řešení datového skladu](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

V cloudovém datovém řešení se data ingestují do úložišť velkých objemů dat z celé řady zdrojů. Jakmile jsou data v úložišti velkých objemů dat, Hadoop, Spark a algoritmy machine learningu data připraví a natrénují. Když jsou data připravená pro komplexní analýzu, služba SQL Analytics využije PolyBase k dotazování na velkoobjemová úložiště dat. PolyBase pomocí standardních dotazů T-SQL načte data do tabulek služby SQL Analytics.
 
Služba SQL Analytics ukládá data v relačních tabulkách se sloupcovým úložištěm. Tento formát výrazně snižuje náklady na úložiště dat a zvyšuje výkon dotazů. Po uložení dat můžete provádět analýzy v masivním měřítku. V porovnání s tradičními databázovými systémy se dotazy analýz dokončí během několika sekund namísto minut nebo hodin namísto dnů. 

Výsledky analýz můžou jít do aplikací nebo databází pro generování sestav po celém světě. Obchodní analytici pak můžou získávat přehledy, které jim umožní provádět informovaná obchodní rozhodnutí.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte [architekturu Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- Rychlé [vytvoření fondu SQL](create-data-warehouse-portal.md)
- [Načtení ukázkových dat](load-data-from-azure-blob-storage-using-polybase.md).
- Prozkoumejte [videa](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Nebo se podívejte na některé z těchto dalších prostředků Azure Synapse.  
* Hledat [blogy](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* Odeslání [žádostí o funkci](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Vytvoření lístku podpory](sql-data-warehouse-get-started-create-support-ticket.md)
* Hledat [MSDN fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* Fórum [přetečení zásobníku vyhledávacího zásobníku](https://stackoverflow.com/questions/tagged/azure-sqldw)
