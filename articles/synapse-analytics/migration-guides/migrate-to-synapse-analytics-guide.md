---
title: 'Azure synapse Analytics: Průvodce migrací'
description: Podle tohoto průvodce migrujte vaše databáze do vyhrazeného fondu SQL Azure synapse Analytics.
ms.service: synapse-analytics
ms.subservice: sql
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 704c30516e9daf047bf5837aa6e2ed08306193db
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565640"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrace datového skladu do vyhrazeného fondu SQL ve službě Azure synapse Analytics

Následující části poskytují přehled o tom, co je součástí migrace stávajícího řešení datového skladu do vyhrazeného fondu SQL synapse Analytics.

## <a name="overview"></a>Přehled

Než začnete s migrací, měli byste ověřit, jestli je Azure synapse Analytics nejlepší řešení pro vaše úlohy. Azure synapse Analytics je distribuovaný systém navržený tak, aby prováděl analýzy velkých objemů dat. Migrace na Azure synapse Analytics vyžaduje některé změny návrhu, které není obtížné porozumět, ale to může nějakou dobu trvat. Pokud vaše firma vyžaduje datový sklad na podnikové úrovni, přináší to úsilí. Pokud ale výkon služby Azure synapse Analytics nepotřebujete, je cenově výhodnější použít [SQL Server](https://docs.microsoft.com/sql/sql-server/) nebo [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/).

Při používání Azure synapse Analytics zvažte následující:

- Mít jeden nebo více terabajtů dat.
- Naplánujte spouštění analýz na podstatných objemech dat.
- Je potřeba mít možnost škálovat výpočetní výkon a úložiště.
- Chcete ušetřit náklady tím, že se pozastaví výpočetní prostředky, když je nepotřebujete.

Místo Azure synapse Analytics zvažte jiné možnosti pro provozní úlohy (OLTP), které mají:

- Čtení a zápisy s vysokou frekvencí
- Je vybrána Velká čísla typu singleton.
- Horní svazky vložené do jednoho řádku.
- Požadavky na zpracování podle řádků
- Nekompatibilní formáty (například JSON a XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

Jedním z nejdůležitějších zákazníků s blokováním, který čelí, je překlad databázových objektů při migraci z jednoho systému do jiného. [Azure synapse cest](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) vám pomůže upgradovat na moderní platformu datového skladu automatizací překladu objektů stávajícího datového skladu. Je to bezplatný, intuitivní a snadno použitelný nástroj, který automatizuje překlad kódu a umožňuje rychlejší migraci na Azure synapse Analytics.

## <a name="prerequisites"></a>Požadavky

# <a name="migrate-from-sql-server"></a>[Migrace z SQL Serveru](#tab/migratefromSQLServer)

Pokud chcete migrovat SQL Server datový sklad do Azure synapse Analytics, ujistěte se, že splňujete následující předpoklady:

- Mít datový sklad nebo analytickou úlohu.
- Stáhněte si nejnovější verzi služby [Azure synapse](https://www.microsoft.com/en-us/download/details.aspx?id=102787) pro migraci SQL Server objektů do objektů Azure synapse.
- Mít [vyhrazený fond SQL](../get-started-create-workspace.md) v pracovním prostoru Azure synapse.

# <a name="migrate-from-netezza"></a>[Migrace z Netezza](#tab/migratefromNetezza)

Pokud chcete migrovat datový sklad Netezza do Azure synapse Analytics, ujistěte se, že splňujete následující předpoklady:

- Stáhněte si nejnovější verzi služby [Azure synapse](https://www.microsoft.com/en-us/download/details.aspx?id=102787) pro migraci SQL Server objektů do objektů Azure synapse.
- Mít [vyhrazený fond SQL](../get-started-create-workspace.md) v pracovním prostoru Azure synapse.

Další informace najdete v tématu [řešení a migrace Azure synapse Analytics pro Netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrace z Snowflake](#tab/migratefromSnowflake)

Pokud chcete migrovat datový sklad Snowflake do Azure synapse Analytics, ujistěte se, že splňujete následující předpoklady:

- Stáhněte si nejnovější verzi [Azure synapse cest](https://www.microsoft.com/en-us/download/details.aspx?id=102787) pro migraci objektů Snowflake do objektů Azure synapse.
- Mít [vyhrazený fond SQL](../get-started-create-workspace.md) v pracovním prostoru Azure synapse.

# <a name="migrate-from-oracle"></a>[Migrace z Oracle](#tab/migratefromOracle)

Pokud chcete migrovat datový sklad Oracle do Azure synapse Analytics, ujistěte se, že splňujete následující předpoklady:

- Mít datový sklad nebo analytickou úlohu.
- Stáhněte Pomocník s migrací SQL Serveru pro Oracle, aby se objekty Oracle převedly na SQL Server. Další informace najdete v tématu [migrace databází Oracle do SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql).
- Stáhněte si nejnovější verzi služby [Azure synapse](https://www.microsoft.com/download/details.aspx?id=102787) pro migraci SQL Server objektů do objektů Azure synapse.
- Mít [vyhrazený fond SQL](../get-started-create-workspace.md) v pracovním prostoru Azure synapse.

Další informace najdete v tématu [Řešení Azure synapse Analytics a migrace pro datový sklad Oracle](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Před migrací

Až se rozhodnete migrovat existující řešení do služby Azure synapse Analytics, budete muset před zahájením plánování migrace naplánovat. Hlavním cílem plánování je zajistit, aby vaše data, schémata tabulek a kód byly kompatibilní se službou Azure synapse Analytics. Existují některé rozdíly v kompatibilitě mezi aktuálním systémem a analýzou Azure synapse, které budete muset vyřešit. Navíc migrace velkých objemů dat do Azure trvá déle. Pečlivé plánování urychlí proces získávání vašich dat do Azure.

Dalším důležitým cílem plánování je upravit návrh, abyste měli jistotu, že vaše řešení plně využívá vysoký výkon dotazů, který je navržený tak, aby Azure synapse Analytics poskytoval. Návrh datových skladů pro škálování přináší jedinečné vzory návrhu, takže tradiční přístupy nejsou vždycky nejlepší. I když je možné provést některé úpravy návrhu po migraci, změny v předchozí části procesu vám ušetří čas později.

## <a name="migrate"></a>Migrate

Úspěšná migrace vyžaduje, abyste provedli migraci schémat, kódu a dat tabulek. Podrobnější pokyny k těmto tématům najdete v následujících článcích:

- [Zvážit návrh tabulky](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Vzít v úvahu změnu kódu](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [Migrace dat](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [Zvažte správu úloh.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>Další zdroje informací

Poradenský tým pro zákazníky obsahuje některé skvělé doprovodné materiály k Azure synapse Analytics (dříve Azure SQL Data Warehouse) publikované jako blogové příspěvky. Další informace o migraci najdete v tématu [migrace dat do Azure SQL Data Warehouse v praxi](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Migrace prostředků z reálných rezervací

Další pomoc s dokončením tohoto scénáře migrace najdete v následujících zdrojích informací. Byly vyvinuty v rámci podpory realizace projektu migrace do reálného světa.

| Název/odkaz                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které pomáhají zrychlit vyhodnocení velkých majetku poskytnutím automatizovaného a sjednoceného rozhodovacího procesu platformy. |
| [Zpracování potíží s kódováním dat při načítání dat do služby Azure synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Tento Blogový příspěvek nabízí přehled o některých problémech s kódováním dat, ke kterým může dojít při použití základny k načítání dat do SQL Data Warehouse. Tento článek také poskytuje některé možnosti, které můžete použít k překonání těchto problémů a k úspěšnému načtení dat. |
| [Získávání velikostí tabulek ve vyhrazeném fondu SQL Azure synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Jednou ze základních úloh, které musí architekt provést, je získat metriky o novém prostředí po migraci. Příklady zahrnují shromažďování časů načítání z místního prostředí do cloudu a shromažďování časů základní zátěže. Jednou z nejdůležitějších úkolů je určení velikosti úložiště v SQL Data Warehouse v porovnání s aktuální platformou zákazníka. |
| [Nástroj pro přesun místních SQL Server přihlášení do Azure synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Skript PowerShellu vytvoří skript příkazového řádku T-SQL, který znovu vytvoří přihlašovací údaje a vybere uživatele databáze z místní instance SQL Server do služby Azure SQL Platform as a Service (PaaS). Tento nástroj umožňuje automatické mapování účtů služby Windows Server Active Directory na Azure Active Directory účtů nebo pro každé přihlášení k místní službě Windows Server Active Directory provádět vyhledávání UPN. Tento nástroj může volitelně přesunout SQL Server Nativní přihlášení. Vlastní role serveru a databáze jsou spouštěny společně s členstvím v rolích, databázových rolí a uživatelských oprávnění. Obsažené databáze nejsou podporovány a jsou spouštěny pouze podmnožina možných SQL Server oprávnění. Další informace jsou k dispozici v dokumentu podpory a skript obsahuje komentáře pro usnadnění porozumění. |

Data tým SQL Engineering vyvinuli tyto prostředky. Základní Chart týmu je odblokování a urychlení komplexní modernizace pro projekty migrace datové platformy na datovou platformu Azure od Microsoftu.

## <a name="videos"></a>Videa

Podívejte se, jak [Walgreens migrovali svůj systém maloobchodního inventáře](https://www.youtube.com/watch?v=86dhd8N1lH4) s přibližně 100 TB dat z Netezza do Azure synapse Analytics v čase záznamu.
