---
title: Nejčastější dotazy k Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Tento článek uvádí si nejčastější dotazy ohledně Azure SQL Data Warehouse od zákazníků a vývojářů
services: sql-data-warehouse
author: acomet
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: acomet
ms.reviewer: igorstan
ms.openlocfilehash: 35252b4d262833daef330a89f1874cc0835c480a
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306736"
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>SQL Data Warehouse nejčastější dotazy

## <a name="general"></a>Obecné

Otázka: Co SQL data Warehouse nabízí zabezpečení dat?

A. SQL data Warehouse nabízí několik řešení pro ochranu dat, jako je transparentní šifrování dat a auditování. Další informace najdete v tématu [zabezpečení].

Otázka: Kde najdu jaké standardy právní nebo business se SQL data Warehouse kompatibilní s?

A. Přejděte [Dodržování předpisů Microsoft] stránky pro různé nabídky dodržování předpisů podle produktu, jako je například SOC a ISO. Nejprve zvolte podle názvu dodržování předpisů a potom rozbalte Azure v části služby Microsoft cloud v oboru na pravé straně stránky a zjistit, jaké služby jsou Azure services jsou kompatibilní.

Otázka: Můžete připojit k Power BI?

A. Ano! Když Power BI podporuje přímých dotazů s SQL data Warehouse, není určena pro velký počet uživatelů nebo dat v reálném čase. Pro produkční použití Power BI doporučujeme používat Power BI nad Azure Analysis Services nebo analýzy služby IaaS. 

Otázka: Jaké jsou limity kapacity datového skladu SQL?

A. V tématu naše aktuální [limity kapacity] stránky. 

Otázka: Proč se Moje Škálovací/pozastavit/obnovit trvá tak dlouho?

A. Různé faktory mohou mít vliv na dobu výpočetní operace správy. Společný malá a velká pro dlouho běžící operací je transakční vrácení zpět. Při zahájení operace škálování nebo pozastavit, jsou blokovány všechny příchozí relace a jsou Vyprázdněné dotazy. Pokud chcete nechat systém stabilní, musí být transakce vráceny zpět před započetím operace. Větší číslo a větší velikost protokolu transakcí, tím déle operace bude být zastaven a proces obnovení systému do stabilního stavu.

## <a name="user-support"></a>Podpora uživatelů

Otázka: Mám žádost o funkci, kde ji odeslat?

A. Pokud máte žádost o funkci, odeslat ho na naše [UserVoice] stránky

Otázka: Jak to mohu provést x?

A. Pro pomoc při vývoji s využitím SQL Data Warehouse, můžete klást dotazy na naše [Stack Overflow] stránky. 

Otázka: Jak odeslat lístek podpory?

A. [Lístky žádostí o podporu] podává prostřednictvím webu Azure portal.

## <a name="sql-languagefeature-support"></a>Podpora jazyka nebo funkci SQL 

Otázka: Jaké datové typy SQL Data Warehouse podporuje?

A. SQL Data Warehouse viz [datové typy].

Otázka: Jaké funkce tabulky podporujete?

A. I když SQL Data Warehouse podporuje mnoho funkcí, některé nejsou podporovány a jsou popsány v [Nepodporovaná tabulka funkce].

## <a name="tooling-and-administration"></a>Nástroje a Správa

Otázka: Podporujete databázové projekty v sadě Visual Studio.

A. Aktuálně nepodporujeme databázové projekty v sadě Visual Studio pro službu SQL Data Warehouse. Pokud chcete přetypovat hlas získat tuto funkci, navštivte naši User Voice [Žádost o funkci databázové projekty].

Otázka: SQL Data Warehouse podporuje rozhraní REST API?

A. Ano. Co nejvíce funkcí REST, který lze použít s SQL Database je také dostupná v SQL Data Warehouse. Můžete najít informace o rozhraní API v rámci stránky dokumentace k REST nebo [MSDN].


## <a name="loading"></a>Načítá se

Otázka: Jaké ovladače klienta podporujete?

A. Podpora ovladačů pro datový Sklad můžete najít na [připojovací řetězce] stránky

Otázka: které formáty souborů jsou podporovány službou SQL Data Warehouse polybase?

Odpověď: Orc, RC, Parquet nebo bez stromové struktury text s oddělovači

Otázka: Co můžu připojit k z SQL data Warehouse pomocí PolyBase? 

Odpověď: [Azure Data Lake Store] a [objekty BLOB služby Azure Storage]

Otázka: je výpočet přenosu směrem dolů možné při připojování ke službě Azure Storage Blobs nebo ADLS? 

Odpověď: Ne, PolyBase služby SQL data Warehouse komunikuje pouze komponenty úložiště. 

Otázka: je možné připojit k službě Hdinsight?

Odpověď: HDI můžete použít WASB nebo ADLS jako vrstva HDFS. Pokud máte jako vrstva HDFS, můžete načíst data do SQL data Warehouse. Však nelze generovat výpočtu přenosu směrem dolů k instanci HDI. 

## <a name="next-steps"></a>Další postup
Další informace o SQL Data Warehouse jako celek, najdete v našich [přehled] stránky.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Připojovací řetězce]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Lístky žádostí o podporu]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Zabezpečení]: ./sql-data-warehouse-overview-manage-security.md
[Dodržování předpisů Microsoft]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[limity kapacity]: ./sql-data-warehouse-service-capacity-limits.md
[datové typy]: ./sql-data-warehouse-tables-data-types.md
[Nepodporovaná tabulka funkce]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Objekty BLOB služby Azure Storage]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Žádost o funkci databázové projekty]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Přehled]: ./sql-data-warehouse-overview-faq.md
