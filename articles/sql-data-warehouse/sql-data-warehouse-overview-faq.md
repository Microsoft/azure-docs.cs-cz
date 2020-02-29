---
title: Nejčastější dotazy k Azure synapse Analytics (dříve SQL DW)
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
ms.openlocfilehash: f7cac849caf478850707eb0a71b2369dcfa49520
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197177"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Nejčastější dotazy k Azure synapse Analytics (dříve SQL DW)

## <a name="general"></a>Obecné

Otázka: Co je Azure Synapse?

A. Azure synapse je bezlimitní analytická služba, která přináší dohromady datové sklady a analýzu velkých objemů dat. Poskytuje vám volnost v dotazování na data podle vašich podmínek, a to s využitím neserverových nebo zřízených prostředků se škálováním na vyžádání. Azure synapse přináší tyto dvě světů společně s jednotným prostředím pro ingestování, přípravu, správu a poskytování dat pro potřeby okamžitého BI a strojového učení. Další informace najdete v tématu [co je Azure synapse Analytics](sql-data-warehouse-overview-what-is.md).

Otázka: Co se stalo s Azure SQL Data Warehouse?

A. Azure synapse je vyvíjejí se Azure SQL Data Warehouse (SQL DW). Provedli jsme stejný oborový datový sklad na celou novou úroveň výkonu a schopností. Můžete dál spouštět stávající úlohy datového skladu v produkčním prostředí s využitím Azure synapse a automaticky využívat výhod nových funkcí, které jsou ve verzi Preview. Další informace najdete v tématu [co je Azure synapse Analytics](sql-data-warehouse-overview-what-is.md).

Otázka: Co je SQL Analytics?

A. SQL Analytics odkazuje na funkce podnikových dat v datovém skladu, které jsou všeobecně dostupné v Azure synapse. Další informace najdete v tématu [co je Azure synapse Analytics](sql-data-warehouse-overview-what-is.md).

Otázka: Návody začít s Azure synapse?

A. Můžete začít s [bezplatným účtem Azure](https://azure.microsoft.com/free/sql-data-warehouse/) nebo se [obrátit na prodej, kde najdete další informace](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html). 

Otázka: Co Azure synapse nabízí pro zabezpečení dat?

A. Azure synapse nabízí několik řešení pro ochranu dat, jako je TDE a auditování. Další informace najdete v tématu [zabezpečení](sql-data-warehouse-overview-manage-security.md).

Otázka: Kde můžu zjistit, jaké právní nebo obchodní standardy Azure synapse dodržuje předpisy?

A. Navštivte stránku [dodržování předpisů Microsoftu](https://www.microsoft.com/trustcenter/compliance/complianceofferings) , kde najdete různé nabídky dodržování předpisů podle produktů, jako je SOC a ISO. Nejdřív vyberte podle názvu dodržování předpisů. Pak rozbalte Azure v části věnované cloudovým službám Microsoftu v oboru na pravé straně stránky a podívejte se, jaké služby jsou v Azure synapse kompatibilní.

Otázka: Můžu se Power BI připojit?

A. Ano! I když Power BI podporuje přímý dotaz s Azure synapse, není určený pro velký počet uživatelů nebo dat v reálném čase. Pokud chcete optimalizovat Power BI výkon, zvažte použití Power BI nad Azure Analysis Services nebo Analysis Service IaaS.

Otázka: Co jsou limity kapacity SQL Analytics?

A. Podívejte se na naše aktuální [omezení kapacity](sql-data-warehouse-service-capacity-limits.md) . 

Otázka: Proč je můj rozsah/pozastavení/pokračování trvá tak dlouho?

A. Několik faktorů může ovlivnit čas operací správy výpočetních prostředků. Běžným případem dlouhotrvajících operací je vrácení transakční služby za provozu. Při zahájení operace škálování nebo pozastavení jsou všechny příchozí relace blokované a dotazy se vyprázdní. Aby bylo možné systém opustit v stabilním stavu, transakce musí být vráceny zpět před zahájením operace. Čím větší je počet a větší velikost protokolu transakcí, tím déle bude operace zablokované obnovení systému do stabilního stavu.

## <a name="user-support"></a>Podpora uživatelů

Otázka: Mám žádost o funkci, kde ji mohu odeslat?

A. Pokud máte žádost o funkci, odešlete ji na naši stránku [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) .

Otázka: Jak můžu provést x?

A. Pokud potřebujete pomoc s vývojem pomocí Azure synapse, můžete klást otázky na stránce [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) . 

Otázka: Návody odeslat lístek podpory?

A. [Lístky podpory](sql-data-warehouse-get-started-create-support-ticket.md) mohou být archivovány prostřednictvím Azure Portal.

## <a name="sql-languagefeature-support"></a>Podpora jazyka a funkcí SQL 

Otázka: Jaké datové typy jsou podporovány?

A. Podívejte se na [datové typy](sql-data-warehouse-tables-data-types.md).

Otázka: Jaké funkce tabulky podporujete?

A. Je podporováno mnoho funkcí. Funkce, které nejsou podporované, najdete v [nepodporovaných funkcích tabulky](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Nástroje a Správa

Otázka: Podporuje SQL Analytics rozhraní REST API?

A. Ano. Většina funkcí REST, které je možné používat s SQL Database, je také k dispozici v rámci SQL Analytics. Informace o rozhraní API najdete na stránkách dokumentace k REST nebo na [MSDN](https://msdn.microsoft.com/library/azure/mt163685.aspx).


## <a name="loading"></a>Načítá

Otázka: Jaké ovladače klienta podporujete?

A. Podporu ovladačů pro SQL Analytics najdete na stránce [připojovací řetězce](sql-data-warehouse-connection-strings.md) .

Otázka: Jaké formáty souborů podporuje základová databáze?

O: Orc, RC, Parquet a plochý text oddělený textem

Otázka: Jaké zdroje dat se mohu připojit k používání základny? 

O: [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) a [objekty blob Azure Storage](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

Otázka: když se připojujete k Azure Storage objektům blob nebo ADLS, je možné výpočet směrem dolů? 

Odpověď: Ne, základová databáze komunikuje pouze s součástmi úložiště. 

Otázka: je možné se připojit k HDI?

Odpověď: HDI může jako vrstvu HDFS použít buď ADLS, nebo WASB. Pokud máte buď vrstvu HDFS, můžete tato data načíst do datového skladu SQL Analytics. V instanci HDI ale nemůžete vygenerovat výpočet přenosů směrem dolů. 

## <a name="next-steps"></a>Další kroky
Další informace o Azure synapse jako celku najdete na naší stránce s [přehledem](sql-data-warehouse-overview-faq.md) .
