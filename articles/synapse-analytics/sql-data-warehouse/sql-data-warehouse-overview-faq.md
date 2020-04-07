---
title: Azure Synapse Analytics (dříve SQL DW) Nejčastější dotazy
description: V tomto článku jsou uvedeny nejčastější dotazy týkající se Azure Synapse Analytics (dříve SQL DW) od zákazníků a vývojářů.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: dcefe6a6144c9a8f9c6ab7daf5b34b2d81a73c3f
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742998"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics (dříve SQL DW) Nejčastější dotazy

## <a name="general"></a>Obecné

Otázka: Co je Azure Synapse?

A. Azure Synapse je neomezená analytická služba, která sdružuje datové sklady a analýzy velkých objemů dat. Poskytuje vám svobodu dotazování dat na vaše podmínky, pomocí bezserveru na vyžádání nebo zřízených prostředků - ve velkém měřítku. Azure Synapse spojuje tyto dva světy s jednotným prostředím pro ingestování, přípravu, správu a poskytování dat pro okamžité potřeby BI a strojového učení. Další informace naleznete v tématu [Co je Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Otázka: Co se stalo s Azure SQL Data Warehouse?

A. Azure Synapse je Azure SQL Data Warehouse (SQL DW) vyvinul. Posrali jsme stejný datový sklad, který je v oboru, na zcela novou úroveň výkonu a schopností. Můžete pokračovat ve spuštění stávajících úloh datového skladu v produkčním prostředí s Azure Synapse a automaticky využívat nové funkce, které jsou ve verzi preview. Další informace naleznete v tématu [Co je Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Otázka: Co je synapse SQL fond?

A. Synapse SQL fond odkazuje na podnikové datové sklady funkce, které jsou obecně dostupné s Azure Synapse. Další informace naleznete v tématu [Co je Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Otázka: Jak začít s Azure Synapse?

A. Můžete začít s [bezplatným účtem Azure](https://azure.microsoft.com/free/sql-data-warehouse/) nebo [se obrátit na prodej, kde najdete další informace](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html).

Otázka: Co Azure Synapse nabízí pro zabezpečení dat?

A. Azure Synapse nabízí několik řešení pro ochranu dat, jako je TDE a auditování. Další informace naleznete v tématu [Zabezpečení](sql-data-warehouse-overview-manage-security.md).

Otázka: Kde zjistím, jaké právní nebo obchodní standardy Azure Synapse splňuje?

A. Navštivte stránku [Microsoft Compliance,](https://www.microsoft.com/trustcenter/compliance/complianceofferings) kde najdete různé nabídky dodržování předpisů podle produktů, jako jsou SOC a ISO.
Nejprve zvolte podle názvu dodržování předpisů. Pak rozbalte Azure v části cloudových služeb Microsoft in-scope na pravé straně stránky a zjistěte, jaké služby jsou kompatibilní s Azure Synapse.

Otázka: Můžu připojit Power BI?

A. Ano! I když Power BI podporuje přímý dotaz s Azure Synapse, není určen pro velký počet uživatelů nebo data v reálném čase. Pokud chcete výkon Power BI dále optimalizovat, zvažte použití Power BI nad Azure Analysis Services nebo Analysis Service IaaS.

Otázka: Co jsou limity kapacity fondu SQL Synapse?

A. Podívejte se na naši aktuální [stránku omezení kapacity.](sql-data-warehouse-service-capacity-limits.md)

Otázka: Proč moje scale/pause/resume trvá tak dlouho?

A. Několik faktorů může ovlivnit čas pro operace správy výpočetních prostředků. Běžným případem pro dlouhotrvající operace je transakční vrácení zpět. Při zahájení operace škálování nebo pozastavení jsou blokovány všechny příchozí relace a dotazy jsou vybité. Chcete-li ponechat systém ve stabilním stavu, musí být transakce před zahájením operace vráceny zpět. Čím větší je počet a větší velikost protokolu transakcí, tím déle bude operace pozastavena obnovení systému do stabilního stavu.

## <a name="user-support"></a>Podpora uživatelů

Otázka: Mám žádost o funkci, kde ji mám odeslat?

A. Pokud máte žádost o funkci, odešlete ji na naši stránku [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse)

Otázka: Jak mohu udělat x?

A. Pokud potřebujete pomoc s vývojem s Azure Synapse, můžete klást otázky na naší stránce [Přetečení zásobníku.](https://stackoverflow.com/questions/tagged/azure-sqldw)

Otázka: Jak odešlu lístek podpory?

A. [Lístky podpory](sql-data-warehouse-get-started-create-support-ticket.md) lze podat prostřednictvím portálu Azure.

## <a name="sql-languagefeature-support"></a>Podpora jazyka/funkcí SQL

Otázka: Jaké datové typy jsou podporovány?

A. Viz [datové typy](sql-data-warehouse-tables-data-types.md).

Otázka: Jaké funkce tabulky podporujete?

A. Mnoho funkcí je podporováno. Funkce, které nejsou podporovány, lze nalézt v [nepodporovaných funkcích tabulky](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Nástroje a správa

Otázka: Podporuje fond SQL Synapse REST API?

A. Ano. Většina funkcí REST, které lze použít s databází SQL, je také k dispozici s fondem SYNApse SQL. Informace o rozhraní API najdete na stránkách dokumentace REST nebo [v databázích](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="loading"></a>Načítání

Otázka: Jaké ovladače klientů podporujete?

A. Podpora ovladačů pro fond Synapse SQL naleznete na stránce [Připojovací řetězce](sql-data-warehouse-connection-strings.md)

Otázka: Jaké formáty souborů jsou podporovány PolyBase?

A: Skorta, RC, parkety a plochý oddělený text

Otázka: K jakým zdrojům dat se mohu připojit pomocí technologie PolyBase?

A: [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) a Azure Storage [objekty BLOB](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

Otázka: Je možné nabízení výpočtů při připojování k objektům BLOB úložiště Azure nebo ADLS?

A: Ne, PolyBase pouze spolupracuje s komponenty úložiště.

Otázka: Mohu se připojit k HDI?

A: HDI můžete použít buď ADLS nebo WASB jako vrstvu HDFS. Pokud máte buď jako vrstva HDFS, můžete načíst data do fondu SYNApse SQL. Nelze však generovat výpočty pushdown do instance HDI.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Synapse jako celku najdete na naší stránce [Přehled.](sql-data-warehouse-overview-faq.md)
