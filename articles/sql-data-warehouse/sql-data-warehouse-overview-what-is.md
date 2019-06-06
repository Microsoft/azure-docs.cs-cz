---
title: Co je Azure SQL Data Warehouse? | Dokumenty Microsoft
description: Distribuovaná databáze podnikové třídy, která dokáže zpracovávat petabajtové objemy relačních a nerelačních dat. Je v oboru první cloudový datový sklad v možnost zvětšit, zmenšit nebo pozastavit během pár sekund.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: a9126e9023091dd8c3df71f2aa2558a01227a8be
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428030"
---
# <a name="what-is-azure-sql-data-warehouse"></a>Co je Azure SQL Data Warehouse?

SQL Data Warehouse je cloudové Enterprise Data skladu třídy, který se používá k rychlému spouštění složitých dotazů nad petabajty dat. masivně paralelní zpracování (MPP). Využijte službu SQL Data Warehouse jako klíčovou součást řešení pro velké objemy dat. Importovat velké objemy dat do SQL Data Warehouse pomocí jednoduchého [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) dotazy T-SQL a pak použijte sílu architektury MPP ke spouštění vysoce výkonných analýz. Postupným integrováním a analyzováním se datový sklad stane pro vaši firmu jediným spolehlivým zdrojem pravdivých informací.  

## <a name="key-component-of-big-data-solution"></a>Klíčová součást řešení pro velké objemy dat

SQL Data Warehouse je klíčovou součástí uceleného řešení pro velké objemy dat v cloudu.

![Řešení datového skladu](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

V cloudovém datovém řešení se data ingestují do úložišť velkých objemů dat z celé řady zdrojů. Jakmile jsou data v úložišti velkých objemů dat, Hadoop, Spark a algoritmy machine learningu data připraví a natrénují. Když jsou data připravena pro komplexní analýzu, SQL Data Warehouse použije PolyBase k dotazování úložišť velkých objemů dat. PolyBase využívá standardní dotazy T-SQL k načítání dat do služby SQL Data Warehouse.
 
SQL Data Warehouse ukládá data v relačních tabulkách se sloupcovým úložištěm. Tento formát výrazně snižuje náklady na úložiště dat a zvyšuje výkon dotazů. Jakmile jsou data uložená ve službě SQL Data Warehouse, můžete spouštět analýzy ve velkém měřítku. V porovnání s tradičními databázovými systémy se dotazy analýz dokončí během několika sekund namísto minut nebo hodin namísto dnů. 

Výsledky analýz můžou jít do aplikací nebo databází pro generování sestav po celém světě. Obchodní analytici pak můžou získávat přehledy, které jim umožní provádět informovaná obchodní rozhodnutí.

## <a name="next-steps"></a>Další postup

- Prozkoumejte [architektury Azure SQL Data Warehouse](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- Rychle [vytvořit SQL Data Warehouse][create a SQL Data Warehouse]
- [Načtení ukázkových dat][load sample data].
- Prozkoumejte [videa](/azure/sql-data-warehouse/sql-data-warehouse-videos)

Můžete se také podívat na některé z těchto dalších zdrojů ke službě SQL Data Warehouse.  
* Hledání [blogy]
* Odeslat [Žádosti o funkce]
* Hledání [blogy zákaznického poradního týmu]
* [Vytvoření lístku podpory]
* Hledání [Fórum MSDN]
* Hledání [fórum Stack Overflow]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Vytvoření lístku podpory]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
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
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
