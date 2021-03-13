---
title: 'SQL Server ke službě Azure synapse Analytics: Průvodce migrací'
description: Podle tohoto průvodce migrujte své databáze SQL do fondu SQL Azure synapse Analytics.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 09914b409c7d8412f6ba30d4412e28e264bd50f6
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225738"
---
# <a name="migration-guide-sql-server-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Průvodce migrací: SQL Server do vyhrazeného fondu SQL ve službě Azure synapse Analytics 
Následující části poskytují přehled o tom, co je součástí migrace stávajícího řešení SQL Server datového skladu do fondu SQL Azure synapse Analytics.

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

## <a name="prerequisites"></a>Požadavky
Pokud chcete migrovat SQL Server do služby Azure synapse Analytics, ujistěte se, že máte následující požadavky: 

- Zatížení datového skladu nebo analýzy 
- Nejnovější verze nástroje [Azure synapse discestách](https://www.microsoft.com/en-us/download/details.aspx?id=102787) pro migraci SQL Server objektů do objektů Azure synapse.
- [Vyhrazený fond SQL](../get-started-create-workspace.md) ve službě Azure synapse Workspace. 

## <a name="pre-migration"></a>Před migrací
Až se rozhodnete migrovat existující řešení do Azure synapse Analytics, je důležité migraci naplánovat před zahájením práce. Hlavním cílem plánování je zajistit, aby vaše data, schémata tabulek a kód byly kompatibilní se službou Azure synapse Analytics. Existují některé rozdíly v kompatibilitě mezi vaším aktuálním systémem a SQL Data Warehouse, které budete potřebovat k obejít. Navíc migrace velkých objemů dat do Azure trvá déle. Pečlivé plánování urychlí proces získávání vašich dat do Azure. Dalším důležitým cílem plánování je upravit návrh, abyste měli jistotu, že vaše řešení plně využívá vysoký výkon dotazů, který je navržený tak, aby Azure synapse Analytics poskytoval. Návrh datových skladů pro škálování přináší jedinečné vzory návrhu, takže tradiční přístupy nejsou vždycky nejlepší. I když je možné provést některé úpravy návrhu po migraci, změny v předchozí části procesu vám ušetří čas později.

## <a name="azure-synapse-pathway"></a>Synapse cest k Azure
Jedním z uživatelů s kritickými blokujícími, kteří čelí, je překlad kódu SQL při migraci z jednoho systému do jiného. [Azure synapse cest](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) vám pomůže upgradovat na moderní platformu datového skladu automatizací překladu kódu stávajícího datového skladu. Je to bezplatný, intuitivní a snadno použitelný nástroj, který automatizuje překlad kódu a umožňuje rychlejší migraci na Azure synapse Analytics.

## <a name="migrate"></a>Migrate
Úspěšná migrace vyžaduje, abyste provedli migraci schémat, kódu a dat tabulek. Podrobnější pokyny k těmto tématům najdete v následujících tématech:
- Článek [migruje vaše schémata](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- Článek [migruje váš kód](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- Článek [migruje vaše data](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).

## <a name="additional-resources"></a>Další zdroje informací 
- CAT (poradní tým pro zákazníky) obsahuje některé skvělé doprovodné materiály k Azure synapse Analytics (dříve SQL Data Warehouse) publikované jako blogové příspěvky. Nezapomeňte si probrat svůj článek, [který vám umožní migrovat data do Azure SQL Data Warehouse v praxi](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice), kde najdete další pokyny k migraci.
- Další informace a doporučení najdete v dokumentu White Paper [výběr cesty migrace databáze do Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/choosing-your-database-migration-path-to-azure/Choosing_your_database_migration_path_to_Azure.pdf) .
- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat a speciálních úlohách, najdete v článku [služba a nástroje pro migraci dat](https://docs.microsoft.com/azure/dms/dms-tools-matrix). 

## <a name="migration-assets-from-real-world-engagements"></a>Migrace prostředků z reálných rezervací
Další pomoc s dokončením tohoto scénáře migrace najdete v následujících materiálech, které byly vyvinuty v rámci podpory zapojení projektu z reálného světa.

| Název/odkaz                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Model a nástroj pro vyhodnocení datových úloh](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Tento nástroj poskytuje navrženou cílovou platformu "nejlépe vyhovující", připravenost na Cloud a úroveň nápravy aplikace nebo databáze pro danou úlohu. Nabízí jednoduché výpočetní operace s jedním kliknutím a generování sestav, které výrazně pomáhají zrychlit hodnocení rozsáhlých nemovitostí tím, že zajišťují a automatizují a automatizují rozhodovací procesy platforem. |
| [Zpracování potíží s kódováním dat při načítání dat do služby Azure synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Tento blog má poskytnout přehled o některých problémech s kódováním dat, ke kterým může dojít při použití základny k načtení dat do SQL Data Warehouse. Tento článek také poskytuje některé možnosti, které můžete použít k překonání těchto problémů a k úspěšnému načtení dat. |
| [Získávání velikostí tabulek ve fondu SQL Azure synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Jednou z klíčových úloh, které musí architekt provést, je získat metriky týkající se nového prostředí po migraci: shromažďování časů načítání z místního prostředí do cloudu, shromažďování časů základního zatížení atd. Z těchto úkolů je jedním z nejdůležitějších určení velikosti úložiště v SQL Data Warehouse v porovnání s aktuální platformou zákazníka. |
| [Nástroj pro přesun místních SQL Server přihlášení do Azure synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Skript PowerShellu, který vytvoří skript příkazu T-SQL pro opětovné vytvoření přihlašovacích údajů a výběr uživatelů databáze z místní SQL Server do služby Azure SQL PaaS. Tento nástroj umožňuje automatické mapování účtů Windows AD na účty Azure AD nebo pro každé přihlášení pomocí hlavního názvu uživatele (UPN) v místní službě Windows Active Directory. Tento nástroj se volitelně přesune taky SQL Server Nativní přihlášení. Vlastní role serveru a databáze jsou skripty a také členství v rolích a role databáze a uživatelská oprávnění. Obsažené databáze se zatím nepodporují a jsou spouštěny jenom podmnožina možných SQL Server oprávnění. To znamená, že oprávnění udělená u grantu nejsou podporovaná (komplexní stromy oprávnění). Další podrobnosti jsou k dispozici v dokumentu podpory a skript obsahuje komentáře pro usnadnění porozumění. |

> [!NOTE]
> Tyto prostředky byly vyvinuty v rámci programu Migrace dat rychlé zprovoznění (DM rychlé zprovoznění), který je sponzorný technickým týmem Azure Data Group. Základní Chartou DM rychlé zprovoznění je odblokování a urychlení komplexní modernizace a konkurenční možnosti migrace datových platforem na datovou platformu Azure od Microsoftu. Pokud si myslíte, že by vaše organizace mohla zajímat účast v programu DM rychlé zprovoznění, obraťte se na tým svého účtu a požádejte ho, aby předložil jmenování.

## <a name="videos"></a>Videa
- Přehled Průvodce migrací databáze Azure a informací, které obsahuje, najdete v tématu video [o použití Průvodce migrací databáze](https://azure.microsoft.com/resources/videos/how-to-use-the-azure-database-migration-guide/).
- Podrobné informace o fázích procesu migrace a podrobnostech o konkrétních nástrojích a službách, které se doporučují k provedení posouzení a migrace, najdete v [přehledu videa o cestě migrace a o nástrojích/službách doporučených pro provedení posouzení a migrace](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).