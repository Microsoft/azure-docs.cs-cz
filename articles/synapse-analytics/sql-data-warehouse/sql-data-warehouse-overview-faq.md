---
title: Vyhrazený fond SQL (dřív) nejčastější dotazy k SQL DW
description: Tento článek obsahuje seznam nejčastějších dotazů týkajících se vyhrazeného fondu SQL (dříve SQL DW) ve službě Azure synapse Analytics od zákazníků a vývojářů.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: c8f8ae81f6f030245899ec82dbe16b29846dab23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460510"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-frequently-asked-questions"></a>Vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics Nejčastější dotazy

## <a name="general"></a>Obecné

Otázka: Co je Azure Synapse?

A. Azure synapse je analytická služba, která přináší dohromady datové sklady a analýzu velkých objemů dat. Azure synapse přináší tyto dvě světů společně s jednotným prostředím pro ingestování, přípravu, správu a poskytování dat pro potřeby BI a strojového učení. Další informace najdete v tématu [co je Azure synapse Analytics](sql-data-warehouse-overview-what-is.md).

Otázka: Co se stalo s Azure SQL Data Warehouse?

A. Azure synapse se Azure SQL Data Warehouse vyvinul. Provedli jsme stejný oborový datový sklad na celou novou úroveň výkonu a schopností. V Azure synapse můžete dál spouštět stávající úlohy datového skladu v produkčním prostředí s vyhrazeným fondem SQL (dřív SQL DW). Další informace najdete v tématu [co je Azure synapse Analytics](sql-data-warehouse-overview-what-is.md).

Otázka: Co je vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics?

A. Vyhrazený fond SQL (dřív SQL DW) odkazuje na funkce podnikového datového skladu, které jsou všeobecně dostupné v Azure synapse. Další informace najdete v tématu [co je Azure synapse Analytics](sql-data-warehouse-overview-what-is.md).

Otázka: Návody začít s Azure synapse?

A. Můžete začít s [bezplatným účtem Azure](https://azure.microsoft.com/free/sql-data-warehouse/) nebo se [obrátit na prodej, kde najdete další informace](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html).

Otázka: Co Azure synapse nabízí pro zabezpečení dat?

A. Azure synapse nabízí několik řešení pro ochranu dat, jako je TDE a auditování. Další informace najdete v tématu [zabezpečení](sql-data-warehouse-overview-manage-security.md).

Otázka: Kde můžu zjistit, jaké právní nebo obchodní standardy Azure synapse dodržuje předpisy?

A. Navštivte stránku [dodržování předpisů Microsoftu](https://www.microsoft.com/trustcenter/compliance/complianceofferings) , kde najdete různé nabídky dodržování předpisů podle produktů, jako je SOC a ISO. Nejdřív vyberte podle názvu dodržování předpisů. Pak rozbalte Azure v části věnované cloudovým službám Microsoftu v oboru na pravé straně stránky a podívejte se, jaké služby jsou v Azure synapse kompatibilní.

Otázka: Můžu se Power BI připojit?

A. Ano. I když Power BI podporuje přímý dotaz s Azure synapse, není určený pro velký počet uživatelů nebo dat v reálném čase. Pokud chcete optimalizovat Power BI výkon, zvažte použití Power BI nad Azure Analysis Services nebo Analysis Service IaaS.

Otázka: Jaké jsou vyhrazené fondy SQL (dřív než SQL DW) omezení kapacity?

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

A. Podívejte se na  [datové typy](sql-data-warehouse-tables-data-types.md).

Otázka: Jaké funkce tabulky podporujete?

A. Je podporováno mnoho funkcí. Funkce, které nejsou podporované, najdete v [nepodporovaných funkcích tabulky](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Nástroje a Správa

Otázka: Podporuje vyhrazený fond SQL (dřív SQL DW) rozhraní REST API?

A. Ano. Většina funkcí REST, které je možné používat s SQL Database, je dostupná taky u vyhrazeného fondu SQL (dřív SQL DW). Informace o rozhraní API najdete v rámci stránek nebo [databází](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)v dokumentaci k REST.

## <a name="loading"></a>Načítá se

Otázka: Jaké ovladače klienta podporujete?

A. Podporu ovladačů vyhrazeného fondu SQL (dříve SQL DW) najdete na stránce [připojovací řetězce](sql-data-warehouse-connection-strings.md) .

Otázka: Jaké formáty souborů podporuje základová databáze?

O: Orc, RC, Parquet a plochý text oddělený textem

Otázka: Jaké zdroje dat se mohu připojit k používání základny?

O: [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) a [objekty blob Azure Storage](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

Otázka: když se připojujete k Azure Storage objektům blob nebo ADLS, je možné výpočet směrem dolů?

Odpověď: Ne, základová databáze komunikuje pouze s součástmi úložiště.

Otázka: je možné se připojit k HDI?

Odpověď: HDI může jako vrstvu HDFS použít buď ADLS, nebo WASB. Pokud máte buď vrstvu HDFS, můžete tato data načíst do vyhrazeného fondu SQL (dřív SQL DW). V instanci HDI ale nemůžete vygenerovat výpočet přenosů směrem dolů.

## <a name="next-steps"></a>Další kroky

Další informace o vyhrazeném fondu SQL (dřív SQL DW) ve službě Azure synapse najdete na naší stránce s [přehledem](sql-data-warehouse-overview-what-is.md) .
