---
title: Nejčastější dotazy k Azure synapse Analytics (dříve SQL DW) | Microsoft Docs
description: V tomto článku najdete seznam nejčastějších dotazů ke službě Azure synapse Analytics (dříve SQL DW) od zákazníků a vývojářů.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 4db84ed69979713d613679e94d50cdb73475e2be
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520855"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Nejčastější dotazy k Azure synapse Analytics (dříve SQL DW)

## <a name="general"></a>Obecné

Otázka: Co je Azure synapse?

A. Azure synapse je bezlimitní analytická služba, která přináší dohromady datové sklady a analýzu velkých objemů dat. Poskytuje vám volnost v dotazování na data podle vašich podmínek, a to s využitím neserverových nebo zřízených prostředků se škálováním na vyžádání. Azure synapse přináší tyto dvě světů společně s jednotným prostředím pro ingestování, přípravu, správu a poskytování dat pro potřeby okamžitého BI a strojového učení. Další informace najdete v tématu [co je Azure synapse Analytics](sql-data-warehouse-overview-what-is.md).

Otázka: Co se stalo s Azure SQL Data Warehouse?

A. Azure synapse je vyvíjejí se Azure SQL Data Warehouse (SQL DW). Převzali jsme stejný špičkový datový sklad v oboru na celou novou úroveň výkonu a schopností. Můžete dál spouštět stávající úlohy datového skladu v provozu s Azure synapse a automaticky využívat výhod nových funkcí, které jsou ve verzi Preview. Další informace najdete v tématu [co je Azure synapse Analytics](sql-data-warehouse-overview-what-is.md).

Otázka: Co je SQL Analytics?

A. SQL Analytics odkazuje na funkce podnikových dat v datovém skladu, které jsou všeobecně dostupné v Azure synapse. Další informace najdete v tématu [co je Azure synapse Analytics](sql-data-warehouse-overview-what-is.md).

Otázka: Návody začít s Azure synapse?

A. Můžete začít s [bezplatným účtem Azure](https://azure.microsoft.com/free/sql-data-warehouse/) nebo se [obrátit na prodej, kde najdete další informace](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html). 

Otázka: Co Azure synapse nabízí pro zabezpečení dat?

A. Azure synapse nabízí několik řešení pro ochranu dat, jako je TDE a auditování. Další informace najdete v tématu [zabezpečení].

Otázka: Kde můžu zjistit, jaké právní nebo obchodní standardy Azure synapse dodržuje předpisy?

A. Navštivte stránku [dodržování předpisů Microsoftu] , kde najdete různé nabídky dodržování předpisů podle produktů, jako je SOC a ISO. Nejdřív zvolte podle nadpisu dodržování předpisů a potom v části Azure v oboru cloudové služby Microsoftu na pravé straně stránky rozbalte Azure, abyste viděli, které služby jsou v Azure synapse kompatibilní.

Otázka: Můžu se Power BI připojit?

A. Ano! I když Power BI podporuje přímý dotaz s Azure synapse, není určený pro velké množství uživatelů nebo dat v reálném čase. Pro využívání Power BI v produkčním prostředí doporučujeme používat Power BI nad Azure Analysis Services nebo Analysis Service IaaS. 

Otázka: Co jsou limity kapacity SQL Analytics?

A. Podívejte se na naše aktuální [omezení kapacity] . 

Otázka: Proč je můj rozsah/pozastavení/pokračování trvá tak dlouho?

A. Řada faktorů může ovlivnit čas operací správy služby Compute. Běžným případem dlouhotrvajících operací je vrácení transakční služby za provozu. Při zahájení operace škálování nebo pozastavení jsou všechny příchozí relace blokované a dotazy se vyprázdní. Aby bylo možné systém opustit v stabilním stavu, transakce musí být vráceny zpět před zahájením operace. Čím větší je počet a větší velikost protokolu transakcí, tím déle bude operace zablokované obnovení systému do stabilního stavu.

## <a name="user-support"></a>Podpora uživatelů

Otázka: Mám žádost o funkci, kde ji mohu odeslat?

A. Pokud máte žádost o funkci, odešlete ji na naši stránku [UserVoice] .

Otázka: Jak můžu provést x?

A. Pro pomoc s vývojem pomocí Azure synapse můžete klást otázky na stránce [Stack Overflow] . 

Otázka: Návody odeslat lístek podpory?

A. [Lístky podpory] mohou být archivovány prostřednictvím Azure Portal.

## <a name="sql-languagefeature-support"></a>Podpora jazyka a funkcí SQL 

Otázka: Jaké typy DataTypes jsou podporovány?

A. Podívejte se na [datové typy].

Otázka: Jaké funkce tabulky podporujete?

A. Řada funkcí je podporována, ty, které nejsou podporovány a jsou zdokumentovány v [Nepodporované funkce tabulky].

## <a name="tooling-and-administration"></a>Nástroje a Správa

Otázka: Podporujete databázové projekty v aplikaci Visual Studio.

A. V současné době nepodporujeme databázové projekty v aplikaci Visual Studio. Pokud chcete přetypování hlasovat, aby se tato funkce získala, navštivte náš požadavek na [Požadavek na funkce databázových projektů]uživatele.

Otázka: Podporuje SQL Analytics rozhraní REST API?

A. Ano. Většina funkcí REST, které je možné používat s SQL Database, je také k dispozici v rámci SQL Analytics. Informace o rozhraní API najdete na stránkách dokumentace k REST nebo na [MSDN].


## <a name="loading"></a>Načítá se

Otázka: Jaké ovladače klienta podporujete?

A. Podporu ovladačů pro DW najdete na stránce [připojovací řetězce] .

Otázka: Jaké formáty souborů podporuje základová databáze?

O: Orc, RC, Parquet a plochý text oddělený textem

Otázka: Jaké zdroje dat se mohu připojit k používání základny? 

O: [Azure Data Lake Store] a [objekty blob Azure Storage]

Otázka: když se připojujete k Azure Storage objektům blob nebo ADLS, je možné výpočet směrem dolů? 

Odpověď: Ne, základová databáze komunikuje pouze s součástmi úložiště. 

Otázka: je možné se připojit k HDI?

Odpověď: HDI může jako vrstvu HDFS použít buď ADLS, nebo WASB. Pokud máte buď vrstvu HDFS, můžete tato data načíst do SQL DW. V instanci HDI ale nemůžete vygenerovat výpočet přenosů směrem dolů. 

## <a name="next-steps"></a>Další kroky
Další informace o Azure synapse jako celku najdete na naší stránce s [Přehled] .


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Připojovací řetězce]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Lístky podpory]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Zabezpečení]: ./sql-data-warehouse-overview-manage-security.md
[Dodržování předpisů Microsoftu]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[omezení kapacity]: ./sql-data-warehouse-service-capacity-limits.md
[datové typy]: ./sql-data-warehouse-tables-data-types.md
[Nepodporované funkce tabulky]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Objekty blob Azure Storage]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Požadavek na funkce databázových projektů]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Přehled]: ./sql-data-warehouse-overview-faq.md
