---
title: Nejčastější dotazy k Azure SQL Data Warehouse | Microsoft Docs
description: Tento článek obsahuje seznam si nejčastější dotazy k Azure SQL Data Warehouse od zákazníků a vývojářů
services: sql-data-warehouse
author: acomet
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: acomet
ms.reviewer: igorstan
ms.openlocfilehash: 7a570dfa6e0a2812a8dd3a25b8903c70fe07befc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187068"
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>SQL Data Warehouse nejčastější dotazy

## <a name="general"></a>Obecné

OTÁZKY. Co nabízí SQL DW zabezpečení dat?

A. SQL DW nabízí několik řešení pro ochranu dat, jako je například šifrování TDE a auditování. Další informace najdete v tématu [zabezpečení].

OTÁZKY. Kde lze zjistit SQL datového skladu je jaké právní či obchodní normy kompatibilní s?

A. Přejděte [Microsoft Compliance] stránky pro různé nabídky dodržování předpisů produktu, jako je například SOC a ISO. Nejdřív vyberte podle názvu dodržování předpisů, pak rozbalte Azure v části služby Microsoft cloud v oboru na pravé straně stránky uvidíte, jaké služby jsou Azure jsou služby kompatibilní.

OTÁZKY. Můžete připojit PowerBI?

A. Ano! Když PowerBI podporuje přímý dotaz s datovým Skladem SQL, není určena pro velké množství uživatelů nebo dat v reálném čase. Pro produkční použití PowerBI doporučujeme používat PowerBI na Azure Analysis Services nebo na Analysis Service IaaS. 

OTÁZKY. Jaké jsou limity SQL Data Warehouse kapacity?

A. V tématu naše aktuální [limity kapacity] stránky. 

OTÁZKY. Proč můj škálování nebo pozastavení nebo obnovení trvá tak dlouho?

A. Různé faktory mohou mít vliv na dobu výpočetní operace správy. Společné případ dlouho běžící operace je vrácení transakcí zpět. Při zahájení operace škálování nebo pozastavení blokovány všechny příchozí relace a jsou nečekaně dotazy. Aby bylo možné ponechat systém stabilní, musí transakce vrácena zpět před započetím operace. Větší počet a větší velikost protokolu transakcí, tím déle operaci bude být zastaven a proces obnovení systému stabilního stavu.

## <a name="user-support"></a>Podpora uživatelů

OTÁZKY. Jsou žádosti o funkci, kde ji odeslat?

A. Pokud máte žádosti o funkci, odešlete ji na našem [UserVoice] stránky

OTÁZKY. Jak můžete provést x?

A. Pomoc při vývoji s SQL Data Warehouse, můžete klást otázky na našem [Stack Overflow] stránky. 

OTÁZKY. Jak mohu odeslat lístek podpory?

A. [Lístky podpory] podává prostřednictvím portálu Azure.

## <a name="sql-languagefeature-support"></a>Podpora jazyka nebo funkce serveru SQL 

OTÁZKY. Jaké datové typy SQL Data Warehouse podporuje?

A. Najdete v části SQL Data Warehouse [datové typy].

OTÁZKY. Jaké funkce tabulky podporujete?

A. I když SQL Data Warehouse podporuje mnoho funkcí, některé nejsou podporovány a jsou dokumentovány v článku [nepodporované funkce tabulky].

## <a name="tooling-and-administration"></a>Nástroje a Správa

OTÁZKY. Podporujete databázové projekty v sadě Visual Studio.

A. Aktuálně nepodporujeme databázové projekty v sadě Visual Studio pro SQL Data Warehouse. Pokud chcete převést hlas získat tuto funkci, navštivte naše User Voice [databázové projekty funkci požadavku].

OTÁZKY. Podporuje SQL Data Warehouse rozhraní REST API?

A. Ano. Většina funkcí REST, které lze použít s databází SQL je také dostupná v SQL Data Warehouse. Můžete najít informace o rozhraní API v rámci stránky dokumentace k REST nebo [MSDN].


## <a name="loading"></a>Načítá se

OTÁZKY. Jaké ovladače klienta podporujete?

A. Podporu ovladačů pro datového skladu naleznete na [připojovací řetězce] stránky

Otázka: jaký formáty souborů jsou podporovány službou SQL Data Warehouse polybase?

Odpověď: Orc, RC, Parquet a ploché odděleného textu

Otázka: co je možné připojit k z datového skladu SQL pomocí PolyBase? 

Odpověď: [Azure Data Lake Store] a [objektů BLOB služby Azure Storage]

Otázka: je přenos směrem dolů výpočetní možné při připojení k Azure Storage Blobs nebo ADLS? 

A: PolyBase datového skladu SQL Ne, komunikuje pouze komponenty úložiště. 

Otázka: je možné připojit k HDI?

Odpověď: HDI můžete použít buď ADLS nebo WASB jako vrstva HDFS. Pokud máte buď jako HDFS vrstvě, můžete načíst data do datového skladu SQL. Však nelze vygenerovat výpočetní přenos směrem dolů k instanci HDI. 

## <a name="next-steps"></a>Další postup
Další informace o SQL Data Warehouse jako celku najdete v tématu naše [přehled] stránky.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[připojovací řetězce]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Lístky podpory]: ./sql-data-warehouse-get-started-create-support-ticket.md
[zabezpečení]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft Compliance]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[limity kapacity]: ./sql-data-warehouse-service-capacity-limits.md
[datové typy]: ./sql-data-warehouse-tables-data-types.md
[nepodporované funkce tabulky]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[objektů BLOB služby Azure Storage]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[databázové projekty funkci požadavku]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[přehled]: ./sql-data-warehouse-overview-faq.md
