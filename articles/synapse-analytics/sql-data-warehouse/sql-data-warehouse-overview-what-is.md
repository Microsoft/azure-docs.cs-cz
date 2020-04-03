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
ms.openlocfilehash: 3363c4f5828f412bd11ca57e3c5f9013910a8055
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586451"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Co je Azure Synapse Analytics (dříve SQL DW)?

Azure Synapse je neomezená analytická služba, která spojuje podnikové skladování dat a analýzy velkých objemů dat. Dává vám možnost dotazovat se na data podle toho, jak vám to vyhovuje, s využitím bezserverové architektury na vyžádání, nebo zřízených prostředků, a to ve velkém měřítku. Azure Synapse přináší tyto dva světy spolu s jednotným prostředím pro ingestování, přípravu, správu a poskytování dat pro okamžité potřeby BI a strojového učení

Azure Synapse má čtyři součásti:
- Synapse SQL: Kompletní analýza založená na T-SQL – obecně dostupná
    - Fond SQL (platba za dwu zřízené) 
    - SQL na vyžádání (platba za zpracované TB) – (náhled)
- Jiskra: Hluboce integrovaná Apache Spark (náhled) 
- Integrace dat: Hybridní integrace dat (náhled)
- Studio: Jednotné uživatelské prostředí.  (Preview)

> [!NOTE]
> Chcete-li získat přístup k funkcím náhledu služby Azure Synapse, požádejte o přístup [zde](https://aka.ms/synapsepreview). Společnost Microsoft bude třídění všech požadavků a reagovat co nejdříve.

## <a name="synapse-sql-pool-in-azure-synapse"></a>Fond synapse SQL v Azure Synapse

Fond SQL Synapse odkazuje na funkce podnikového datového skladu, které jsou obecně dostupné v Azure Synapse. 

Fond SQL představuje kolekci analytických prostředků, které jsou zřizovány při použití Synapse SQL. Velikost fondu SQL je určena datovými skladovými jednotkami (DWU).

Importujte velká data pomocí jednoduchých dotazů [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL a pak použijte sílu MPP ke spuštění vysoce výkonné analýzy. Při integraci a analýze se fond Synapse SQL stane jedinou verzí pravdy, na kterou se vaše firma může spolehnout pro rychlejší a robustnější přehledy.  

## <a name="key-component-of-a-big-data-solution"></a>Klíčová součást řešení pro velké objemy dat

Datové sklady jsou klíčovou součástí cloudového komplexního řešení pro velké objemy dat.

![Řešení datového skladu](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

V cloudovém datovém řešení se data ingestují do úložišť velkých objemů dat z celé řady zdrojů. Jakmile jsou data v úložišti velkých objemů dat, Hadoop, Spark a algoritmy machine learningu data připraví a natrénují. Když jsou data připravena pro komplexní analýzu, fond Synapse SQL používá PolyBase k dotazování na úložiště velkých objemů dat. PolyBase používá standardní T-SQL dotazy přenést data do tabulky fondu Synapse SQL.
 
Fond synapse SQL ukládá data v relačních tabulkách se sloupcovým úložištěm. Tento formát výrazně snižuje náklady na úložiště dat a zvyšuje výkon dotazů. Po uložení dat můžete provádět analýzy v masivním měřítku. V porovnání s tradičními databázovými systémy se dotazy analýz dokončí během několika sekund namísto minut nebo hodin namísto dnů. 

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
