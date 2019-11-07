---
title: Co je Azure synapse Analytics (dřív SQL DW)?
description: Azure synapse Analytics (dřív SQL DW) je bezlimitní analytická služba, která přináší dohromady podnikové datové sklady a analýzu velkých objemů dat.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 07bf3070e42af8a03ce1fd7ea4445fc76557e8a3
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645515"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Co je Azure synapse Analytics (dřív SQL DW)?

Azure synapse je bezlimitní analytická služba, která přináší dohromady podnikové datové sklady a analýzu velkých objemů dat. Poskytuje vám volnost v dotazování na data podle vašich podmínek pomocí neserverových nebo zřízených prostředků na vyžádání – ve velkém měřítku. Azure synapse přináší tyto dvě světů společně s jednotným prostředím pro ingestování, přípravu, správu a poskytování dat pro potřeby okamžitého BI a strojového učení.

Azure synapse má čtyři součásti:
- SQL Analytics: kompletní analýza založená na T-SQL – všeobecně dostupná
    - Fond SQL (placený za DWU zřízený) 
    - SQL na vyžádání (platba za TB zpracovaná) – (Preview)
- Spark: hluboce integrované Apache Spark (Preview) 
- Integrace dat: integrace hybridních dat (Preview)
- Studio: jednotné uživatelské prostředí.  (Preview)

> [!NOTE]
> Pokud chcete získat přístup k funkcím verze Preview služby Azure synapse, požádejte o přístup [tady](https://aka.ms/synapsepreview). Microsoft bude podle potřeby roztříděna všechny žádosti a bude co nejdříve reagovat.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>SQL Analytics a fond SQL ve službě Azure synapse

SQL Analytics odkazuje na funkce podnikových dat v datovém skladu, které jsou všeobecně dostupné v Azure synapse. 

Fond SQL představuje kolekci analytických prostředků, které se zřídí při použití analýzy SQL. Velikost fondu SQL je určena jednotkami datového skladu (DWU).

Importujte velké objemy dat pomocí jednoduchých [základních](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) dotazů T-SQL a potom Využijte sílu MPP ke spouštění vysoce výkonných analýz. Při integraci a analýze se aplikace SQL Analytics stane jedinou verzí pravdy, kterou vaše firma může počítat pro rychlejší a robustnější přehledy.  

## <a name="key-component-of-a-big-data-solution"></a>Klíčová součást řešení pro velké objemy dat

Datové sklady jsou klíčovou součástí cloudového a komplexního řešení pro velké objemy dat.

![Řešení datového skladu](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

V cloudovém datovém řešení se data ingestují do úložišť velkých objemů dat z celé řady zdrojů. Jakmile jsou data v úložišti velkých objemů dat, Hadoop, Spark a algoritmy machine learningu data připraví a natrénují. Když jsou data připravená pro komplexní analýzu, používá SQL Analytics základ k dotazování na úložiště s velkými objemy dat. Základ používá standardní dotazy T-SQL k převedení dat do tabulek SQL Analytics.
 
SQL Analytics ukládá data v relačních tabulkách s sloupcovým úložištěm. Tento formát výrazně snižuje náklady na úložiště dat a zvyšuje výkon dotazů. Po uložení dat můžete analyzovat v obrovských škálováních. V porovnání s tradičními databázovými systémy se dotazy analýz dokončí během několika sekund namísto minut nebo hodin namísto dnů. 

Výsledky analýz můžou jít do aplikací nebo databází pro generování sestav po celém světě. Obchodní analytici pak můžou získávat přehledy, které jim umožní provádět informovaná obchodní rozhodnutí.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte [architekturu Azure synapse](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- Rychlé [Vytvoření fondu SQL](create-data-warehouse-portal.md)
- [Načíst ukázková data][load sample data].
- Prozkoumat [videa](/azure/sql-data-warehouse/sql-data-warehouse-videos)

Nebo se podívejte na některé z těchto dalších prostředků Azure synapse.  
* Hledat [Blogy]
* Odeslání [Žádosti o funkce]
* Hledání [Blogy zákaznického poradního týmu]
* [Vytvoření lístku podpory]
* Prohledat [Fórum MSDN]
* Hledat [Fórum Stack Overflow]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Vytvoření lístku podpory]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a data warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse Analytics solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogy]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogy zákaznického poradního týmu]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Žádosti o funkce]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Fórum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse Analytics]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
