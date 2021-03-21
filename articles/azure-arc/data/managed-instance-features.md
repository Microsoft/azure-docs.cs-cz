---
title: Funkce a možnosti spravované instance SQL ARC Azure
description: Funkce a možnosti spravované instance SQL ARC Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c42acb69e13cc1eb0fbba3fcafaec1451bc4d77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97589215"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>Funkce a možnosti spravované instance SQL ARC Azure

Spravovaná instance SQL ARC s podporou Azure sdílí společný základ kódu s nejnovější stabilní verzí SQL Server. Většina standardního jazyka SQL, zpracování dotazů a funkcí správy databáze je stejná. Mezi funkce, které jsou společné pro SQL Server a SQL Database nebo spravované instance SQL, patří:

- Jazykové funkce – [Určuje klíčová slova jazyka toku](/sql/t-sql/language-elements/control-of-flow), [kurzory](/sql/t-sql/language-elements/cursors-transact-sql), [datové typy](/sql/t-sql/data-types/data-types-transact-sql), [Příkazy DML](/sql/t-sql/queries/queries), [predikáty](/sql/t-sql/queries/predicates), [čísla sekvence](/sql/relational-databases/sequence-numbers/sequence-numbers), [uložené procedury](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)a [proměnné](/sql/t-sql/language-elements/variables-transact-sql).
- Funkce databáze – [Automatické ladění (vynucené plánování)](/sql/relational-databases/automatic-tuning/automatic-tuning), [sledování změn](/sql/relational-databases/track-changes/about-change-tracking-sql-server), [kolace databáze](/sql/relational-databases/collations/set-or-change-the-database-collation) [, databáze s](/sql/relational-databases/databases/contained-databases)omezením, [zahrnuté uživatele](/sql/relational-databases/security/contained-database-users-making-your-database-portable), [komprese dat](/sql/relational-databases/data-compression/data-compression), [nastavení konfigurace databáze](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), [Online operace indexu](/sql/relational-databases/indexes/perform-index-operations-online), [vytváření oddílů](/sql/relational-databases/partitions/partitioned-tables-and-indexes)a [dočasné tabulky](/sql/relational-databases/tables/temporal-tables) ([viz příručka Začínáme](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables)).
- Funkce zabezpečení – [aplikační role](/sql/relational-databases/security/authentication-access/application-roles), [Maskování dynamických dat](/sql/relational-databases/security/dynamic-data-masking) ([Začínáme s SQL Database maskování dynamických dat pomocí Azure Portal](../../azure-sql/database/dynamic-data-masking-configure-portal.md)), zabezpečení na [úrovni řádků](/sql/relational-databases/security/row-level-security)
- Možnosti více modelů – [zpracování grafů](/sql/relational-databases/graphs/sql-graph-overview), [data JSON](/sql/relational-databases/json/json-data-sql-server), [dokumentu OPENXML](/sql/t-sql/functions/openxml-transact-sql), [prostorové](/sql/relational-databases/spatial/spatial-data-sql-server), [OPENJSON](/sql/t-sql/functions/openjson-transact-sql)a [XML indexy](/sql/t-sql/statements/create-xml-index-transact-sql).



[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="features-of-azure-arc-enabled-sql-managed-instance"></a>Funkce spravované instance SQL ARC s podporou Azure

###  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> RDBMS vysoká dostupnost  
  
|Funkce|SQL Managed Instance s podporou služby Azure Arc|
|-------------|----------------|
|Přesouvání protokolu|Yes| 
|Komprese záloh|Yes|
|Snímek databáze|Yes|
|Vždy v instanci clusteru s podporou převzetí služeb při selhání<sup>1</sup>| Nelze použít. Podobné možnosti k dispozici |
|Skupiny dostupnosti Always On<sup>2</sup>|Možnosti HA jsou plánovány.|
|Základní skupiny dostupnosti <sup>2</sup>|Možnosti HA jsou plánovány.|
|Minimální Skupina dostupnosti potvrzení repliky <sup>2</sup>|Možnosti HA jsou plánovány.|
|Skupina dostupnosti bez clusteru|Yes|
|Online stránka a obnovení souborů|Yes|
|Online indexování|Yes|
|Obnovitelné online opětovné sestavení indexu|Yes|
|Změna schématu online|Yes|
|Rychlé obnovení|Yes|
|Zrcadlené zálohy|Yes|
|Horké přidání paměti a procesoru|Yes|
|Šifrované zálohování|Yes|
|Hybridní zálohování do Azure (zálohování na adresu URL)|Yes|

<sup>1</sup> v situaci, kdy dojde k selhání, se spustí nová spravovaná instance SQL a znovu se připojí k trvalému svazku obsahujícímu vaše data. [Další informace o trvalých svazcích Kubernetes najdete tady](https://kubernetes.io/docs/concepts/storage/persistent-volumes).

<sup>2</sup> budoucí verze budou poskytovat možnosti AG. 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> RDBMS škálovatelnost a výkon  

| Funkce | SQL Managed Instance s podporou služby Azure Arc |
|--|--|
| Clusterovaného | Yes |
| Binární soubory velkých objektů v clusterovaných indexech columnstore | Yes |
| Opětovné sestavení neclusterovaných indexů columnstore online | Yes |
| OLTP v paměti | Yes |
| Trvalá hlavní paměť | Yes |
| Dělení tabulek a indexů | Yes |
| Komprese dat | Yes |
| Správce prostředků | Yes |
| Paralelismus dělené tabulky | Yes |
| Technologie NUMA a přidělování paměťových polí a vyrovnávací paměti pro velké stránky | Yes |
| Zásady správného řízení prostředků v/v | Yes |
| Opožděná trvanlivost | Yes |
| Automatické ladění | Yes |
| Adaptivní spojení v dávkovém režimu | Yes |
| Váš názor na přidělení paměti v dávkovém režimu | Yes |
| Prokládaný běh pro funkce vracející tabulku s více příkazy | Yes |
| Vylepšení hromadného vkládání | Yes |

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> Zabezpečení RDBMS

| Funkce | SQL Managed Instance s podporou služby Azure Arc |
|--|--|
| Zabezpečení na úrovni řádků | Yes |
| Always Encrypted | Yes |
| Always Encrypted se zabezpečenými enklávami | No |
| Dynamické maskování dat | Yes |
| Základní auditování | Yes |
| Podrobné auditování | Yes |
| Transparentní šifrování databáze | Yes |
| Uživatelsky definované role | Yes |
| Databáze s omezením | Yes |
| Šifrování pro zálohy | Yes |

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> Správa RDBMS  

| Funkce | SQL Managed Instance s podporou služby Azure Arc |
|--|--|
| Vyhrazené připojení správce | Yes |
| Podpora skriptování prostředí PowerShell | Yes |
| Podpora operací komponenty aplikace na datové vrstvě – extrakce, nasazení, upgrade, odstranění | Yes |
| Automatizace zásad (kontrolu plánu a změny) | Yes |
| Kolektor dat výkonu | Yes |
| Standardní sestavy výkonu | Yes |
| Průvodce plánem a zamrznutí pro plány plánů | Yes |
| Přímý dotaz na indexovaná zobrazení (pomocí příkazu Rozbalit nápovědu) | Yes |
| Údržba automatických indexovaných zobrazení | Yes |
| Distribuované zobrazení oddílů | Yes |
| Paralelní indexované operace | Yes |
| Automatické použití indexovaného zobrazení pomocí optimalizátoru dotazů | Yes |
| Paralelní kontrola konzistence | Yes |

### <a name="programmability"></a><a name="Programmability"></a> Programovatelnosti  

| Funkce | SQL Managed Instance s podporou služby Azure Arc |
|--|--|
| JSON | Yes |
| Úložiště dotazů | Yes |  |
| Dočasné | Yes |  |
| Nativní podpora jazyka XML | Yes |  |
| Indexování XML | Yes |  |
| UPSERT možnosti sloučení & | Yes |  |
| Datové typy data a času | Yes |  |
| Podpora pro mezinárodní využití | Yes |  |
| Fulltextové a sémantické vyhledávání | No |
| Specifikace jazyka v dotazu | Yes |  |
| Service Broker (zasílání zpráv) | Yes |  |
| Koncové body Transact-SQL | Yes |  |
| Graph | Yes |  |
| Machine Learning Services | No |  |
| PolyBase | No |


### <a name="tools"></a>Nástroje

Spravovaná instance SQL s podporou ARC Azure podporuje různé datové nástroje, které vám můžou pomoci při správě vašich dat.

| **Nástroj** | SQL Managed Instance s podporou služby Azure Arc|
| --- | --- | --- |
| Azure Portal <sup>1</sup> | No |
| Azure CLI | No |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | Yes |
| Azure PowerShell | Yes |
| [Soubor BACPAC (export)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Yes |
| [Soubor BACPAC (import)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Yes |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | Yes |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | Yes |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | Yes |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | Yes |

<sup>1</sup> Azure Portal se v režimu jen pro čtení ve verzi Preview používá jenom k zobrazení spravovaných instancí SQL ARC s podporou Azure ARC.


### <a name="unsupported-features--services"></a><a name="Unsupported"></a> Nepodporované funkce & Services

Pro spravovanou instanci SQL ARC s podporou Azure ARC nejsou k dispozici následující funkce a služby. Podpora těchto funkcí bude v průběhu času stále zapnutá.

| Plošný | Nepodporovaná funkce nebo služba |
|-----|-----|
| **Databázový stroj** | Sloučit replikaci |
| &nbsp; | Stretch DB |
| &nbsp; | Distribuovaný dotaz s připojeními třetích stran |
| &nbsp; | Propojené servery s jinými zdroji dat než SQL Server a produkty Azure SQL |
| &nbsp; | Systémové rozšířené uložené procedury (XP_CMDSHELL atd.) |
| &nbsp; | FileTable, FILESTREAM |
| &nbsp; | Sestavení CLR s EXTERNAL_ACCESS nebo nezabezpečenou sadou oprávnění |
| &nbsp; | Rozšíření fondu vyrovnávací paměti |
| **Agent SQL Server** |  Subsystémy: CmdExec, PowerShell, čtečka front, SSIS, SSAS, SSRS |
| &nbsp; | Výstrahy |
| &nbsp; | Spravované zálohování |
| **Vysoká dostupnost** | Zrcadlení databáze  |
| **Zabezpečení** | Rozšiřitelná Správa klíčů |
| &nbsp; | Ověřování AD pro propojené servery | 
| &nbsp; | Ověřování AD pro skupiny dostupnosti (AGs) | 