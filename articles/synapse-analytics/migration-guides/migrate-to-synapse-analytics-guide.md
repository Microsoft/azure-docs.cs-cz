---
title: 'Azure synapse Analytics: Průvodce migrací'
description: Podle tohoto průvodce migrujte své databáze do vyhrazeného fondu SQL Azure synapse Analytics.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 8a76a637c4862032b100308d8b02bced76af38fe
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023397"
---
# <a name="migrating-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrace datového skladu do vyhrazeného fondu SQL ve službě Azure synapse Analytics 
Následující části poskytují přehled o tom, co je součástí migrace stávajícího řešení datového skladu do vyhrazeného fondu SQL Azure synapse Analytics.

## <a name="overview"></a>Přehled
Před migrací byste měli ověřit, že je Azure synapse Analytics nejlepší řešení pro vaše úlohy. Azure synapse Analytics je distribuovaný systém navržený tak, aby prováděl analýzy velkých objemů dat. Migrace na Azure synapse Analytics vyžaduje některé změny v návrhu, které se nedají obtížně pochopit, ale její implementace může nějakou dobu trvat. Pokud vaše firma vyžaduje datový sklad na podnikové úrovni, přináší to úsilí. Pokud ale výkon služby Azure synapse Analytics nepotřebujete, je cenově výhodnější používat [SQL Server](https://docs.microsoft.com/sql/sql-server/) nebo [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/).

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
- Nekompatibilní formáty (JSON, XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway
Jedním z nejdůležitějších zákazníků s blokováním, který čelí, je překlad databázových objektů při migraci z jednoho systému do jiného. [Azure synapse cest](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) vám pomůže upgradovat na moderní platformu datového skladu automatizací překladu objektů stávajícího datového skladu. Je to bezplatný, intuitivní a snadno použitelný nástroj, který automatizuje překlad kódu a umožňuje rychlejší migraci na Azure synapse Analytics.

## <a name="prerequisites"></a>Požadavky
# <a name="migrate-from-sql-server"></a>[Migrace z SQL Serveru](#tab/migratefromSQLServer)
Pokud chcete migrovat SQL Server datový sklad do služby Azure synapse Analytics, ujistěte se, že máte následující předpoklady: 

- Zatížení datového skladu nebo analýzy 
- Stáhněte si nejnovější nástroj [Azure synapse](https://www.microsoft.com/en-us/download/details.aspx?id=102787) pro migraci SQL Server objektů do objektů Azure synapse.
- [Vyhrazený fond SQL](../get-started-create-workspace.md) ve službě Azure synapse Workspace. 

# <a name="migrate-from-netezza"></a>[Migrace z Netezza](#tab/migratefromNetezza)
Pokud chcete migrovat datový sklad Netezza do služby Azure synapse Analytics, ujistěte se, že máte následující požadavky: 

- Stáhněte si nejnovější nástroj [Azure synapse](https://www.microsoft.com/en-us/download/details.aspx?id=102787) pro migraci SQL Server objektů do objektů Azure synapse.
- [Vyhrazený fond SQL](../get-started-create-workspace.md) ve službě Azure synapse Workspace.

Další informace najdete v [řešeních Azure synapse Analytics a v migraci pro Netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrace z Snowflake](#tab/migratefromSnowflake)
Pokud chcete migrovat datový sklad Snowflake do služby Azure synapse Analytics, ujistěte se, že máte následující požadavky: 

- Stáhněte si nejnovější nástroj [Azure synapse dischodník](https://www.microsoft.com/en-us/download/details.aspx?id=102787) pro migraci objektů Snowflake do objektů Azure synapse.
- [Vyhrazený fond SQL](../get-started-create-workspace.md) ve službě Azure synapse Workspace. 

# <a name="migrate-from-oracle"></a>[Migrace z Oracle](#tab/migratefromOracle)
Pokud chcete migrovat datový sklad Oracle do služby Azure synapse Analytics, ujistěte se, že máte následující požadavky: 

- Zatížení datového skladu nebo analýzy 
- Stáhněte si SSMA pro Oracle a převeďte objekty Oracle na SQL Server. Další informace najdete v tématu [migrace databází Oracle na SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql) . 
- Stáhněte si nejnovější verzi nástroje [Azure synapse](https://www.microsoft.com/download/details.aspx?id=102787) pro migraci SQL Server objektů do objektů Azure synapse.
- [Vyhrazený fond SQL](../get-started-create-workspace.md) ve službě Azure synapse Workspace. 

Další informace najdete v [řešeních Azure synapse Analytics a migrace pro datový sklad Oracle](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Před migrací
Až se rozhodnete migrovat existující řešení do Azure synapse Analytics, je důležité migraci naplánovat před zahájením práce. Hlavním cílem plánování je zajistit, aby vaše data, schémata tabulek a kód byly kompatibilní se službou Azure synapse Analytics. Existují některé rozdíly v kompatibilitě mezi aktuálním systémem a analýzou Azure synapse, které budete muset vyřešit. Navíc migrace velkých objemů dat do Azure trvá déle. Pečlivé plánování urychlí proces získávání vašich dat do Azure. Dalším důležitým cílem plánování je upravit návrh, abyste měli jistotu, že vaše řešení plně využívá vysoký výkon dotazů, který je navržený tak, aby Azure synapse Analytics poskytoval. Návrh datových skladů pro škálování přináší jedinečné vzory návrhu, takže tradiční přístupy nejsou vždycky nejlepší. I když je možné provést některé úpravy návrhu po migraci, změny v předchozí části procesu vám ušetří čas později.

## <a name="migrate"></a>Migrate
Úspěšná migrace vyžaduje, abyste provedli migraci schémat, kódu a dat tabulek. Podrobnější pokyny k těmto tématům najdete v následujících tématech:
- Článek [zvažte návrh tabulky](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview).
- Článek [zvažte změnu kódu](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques).
- Článek [migruje vaše data](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading).
- Článek [zvažte správu úloh](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management).

## <a name="additional-resources"></a>Další zdroje informací 
- CAT (poradní tým pro zákazníky) obsahuje některé skvělé doprovodné materiály k Azure synapse Analytics (dříve SQL DW) publikované jako blogové příspěvky. Nezapomeňte si probrat svůj článek, [který vám umožní migrovat data do Azure SQL Data Warehouse v praxi](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice), kde najdete další pokyny k migraci.

## <a name="migration-assets-from-real-world-engagements"></a>Migrace prostředků z reálných rezervací
Další pomoc s dokončením tohoto scénáře migrace najdete v následujících materiálech, které byly vyvinuty v rámci podpory zapojení projektu z reálného světa.

| Název/odkaz                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které výrazně pomáhají zrychlit hodnocení rozsáhlých nemovitostí tím, že zajišťují a automatizují a automatizují rozhodovací procesy platforem. |
| [Zpracování potíží s kódováním dat při načítání dat do služby Azure synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Tento blog má poskytnout přehled o některých problémech s kódováním dat, ke kterým může dojít při použití základny k načtení dat do SQL Data Warehouse. Tento článek také poskytuje některé možnosti, které můžete použít k překonání těchto problémů a k úspěšnému načtení dat. |
| [Získávání velikostí tabulek ve vyhrazeném fondu SQL Azure synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Jednou z klíčových úloh, které musí architekt provést, je získat metriky týkající se nového prostředí po migraci: shromažďování časů načítání z místního prostředí do cloudu, shromažďování časů základního zatížení atd. Z těchto úkolů je jedním z nejdůležitějších určení velikosti úložiště v SQL Data Warehouse v porovnání s aktuální platformou zákazníka. |
| [Nástroj pro přesun místních SQL Server přihlášení do Azure synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Skript PowerShellu, který vytvoří skript příkazu T-SQL pro opětovné vytvoření přihlašovacích údajů a výběr uživatelů databáze z místní SQL Server do služby Azure SQL PaaS. Tento nástroj umožňuje automatické mapování účtů Windows AD na účty Azure AD nebo pro každé přihlášení pomocí hlavního názvu uživatele (UPN) v místní službě Windows Active Directory. Tento nástroj se volitelně přesune taky SQL Server Nativní přihlášení. Vlastní role serveru a databáze jsou skripty a také členství v rolích a role databáze a uživatelská oprávnění. Obsažené databáze se zatím nepodporují a jsou spouštěny jenom podmnožina možných SQL Server oprávnění. To znamená, že oprávnění udělená u grantu nejsou podporovaná (komplexní stromy oprávnění). Další podrobnosti jsou k dispozici v dokumentu podpory a skript obsahuje komentáře pro usnadnění porozumění. |

Tyto prostředky byly vyvinuty jako součást programu data SQL expertem, který je financován technickým týmem Azure Data Group. Základní Chartou programu data SQL expertem je odblokování a urychlení komplexní modernizace a konkurenční možnosti migrace datových platforem na datovou platformu Azure od Microsoftu. Pokud si myslíte, že by vaše organizace mohla zajímat účast v programu data SQL expertem, obraťte se prosím na svůj tým a požádejte ho, aby podal jmenování.

## <a name="videos"></a>Videa
- Podívejte se, jak [Walgreens migrovali svůj systém maloobchodního inventáře](https://www.youtube.com/watch?v=86dhd8N1lH4) s informacemi o 100 TB dat od Netezza do Azure synapse Analytics (dřív SQL DW) v čase záznamu. 