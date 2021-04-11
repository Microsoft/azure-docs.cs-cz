---
title: Konfigurace maximálního stupně paralelismu (MAXDOP)
titleSuffix: Azure SQL Database
description: Seznamte se s maximálním stupněm paralelismu (MAXDOP).
ms.date: 03/29/2021
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: tsql
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.openlocfilehash: 31ddf15975abdce70ea02b5de64ea5611e7e72b3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111570"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Konfigurace maximálního stupně paralelismu (MAXDOP) v Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  Tento článek popisuje **maximální úroveň paralelismu (MAXDOP)** v Azure SQL Database a způsob jejich konfigurace. 

> [!NOTE]
> **Tento obsah se zaměřuje na Azure SQL Database.** Azure SQL Database vychází z nejnovější stabilní verze databázového stroje Microsoft SQL Server, takže většina obsahu je podobná, i když řešení potíží a možnosti konfigurace se liší. Další informace o MAXDOP v SQL Server najdete v tématu [Konfigurace možnosti Maximální stupeň paralelního nastavení konfigurace serveru](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

## <a name="overview"></a>Přehled
  V Azure SQL Database výchozí nastavení MAXDOP pro každou novou databázi a databázi elastického fondu je 8. To znamená, že databázový stroj může provádět dotazy pomocí více vláken. Na rozdíl od SQL Server, kde výchozí MAXDOP na úrovni serveru je 0 (neomezeno), ve výchozím nastavení jsou nové databáze v Azure SQL Database nastavené na MAXDOP 8. Toto výchozí nastavení zabrání zbytečnému využití prostředků a zajistí konzistentní uživatelské prostředí. Obvykle není nutné dále konfigurovat MAXDOP v úlohách Azure SQL Database, ale může vám poskytnout výhody jako Pokročilé cvičení ladění výkonu.

> [!Note]
>   V září 2020 byla na základě roků telemetrie ve službě Azure SQL Database Service [MAXDOP 8 zvolena](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528) jako výchozí pro nové databáze jako optimální hodnota pro nejširší škálu úloh zákazníků. Tato výchozí hodnota pomáhá zabránit problémům s výkonem kvůli nadměrnému paralelismuům. Před tím bylo výchozí nastavení pro nové databáze MAXDOP 0. Možnost konfigurace s rozsahem databáze MAXDOP se nezměnila pro existující databáze vytvořené před 2020. září.

  Obecně platí, že pokud se databázový stroj rozhodne provést dotaz pomocí paralelismu, je čas spuštění rychlejší. Nadbytečné paralelismus ale může využívat nadbytečné prostředky procesoru bez zvýšení výkonu dotazů. Ve velkém měřítku může nadbytečná paralelismu negativně ovlivnit výkon dotazů všech dotazů spouštěných ve stejné instanci databázového stroje, takže nastavení horní hranice pro paralelismus je běžné cvičení výkonu v SQL Server úloh.

  V následující tabulce jsou popsány vlastnosti databázového stroje při provádění dotazů s různými MAXDOP hodnotami:

| MAXDOP | Chování | 
|--|--|
| = 1 | Databázový stroj nespouští dotazy pomocí několika souběžných vláken. | 
| > 1 | Databázový stroj nastaví horní hranici pro počet paralelních vláken. Databázový stroj zvolí počet dodatečných pracovních vláken, která se mají použít. Celkový počet pracovních vláken použitých k provedení dotazu může být vyšší než zadaná hodnota MAXDOP. |
| = 0 | Databázový stroj může použít několik paralelních vláken s horní hranicí závislou na celkovém počtu logických procesorů. Databázový stroj zvolí počet paralelních vláken, která se mají použít.| 
| | |
  
##  <a name="considerations"></a><a name="Considerations"></a> Odůvodněn  

-   V Azure SQL Database můžete změnit výchozí hodnotu MAXDOP:
    -   Na úrovni dotazu použijte [pomocný parametr dotazu](/sql/t-sql/queries/hints-transact-sql-query) **MAXDOP** .     
    -   Na úrovni databáze s použitím konfigurace s  [rozsahem databáze](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)MAXDOP.

-   Pro Azure SQL Database se vztahují i na dlouhotrvající SQL Server MAXDOP a [doporučení](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) pro použití. 

-   MAXDOP je vynutilo pro každou [úlohu](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql). Není vynutilo na [požadavek](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) nebo na dotaz. To znamená, že během paralelního provádění dotazu může jeden požadavek vytvořit více úkolů s horní hranicí určenou MAXDOP. Další informace najdete v části *plánování paralelních úkolů* v [Průvodci vláknem a architekturou úloh](/sql/relational-databases/thread-and-task-architecture-guide). 
  
-   Operace indexu, které vytvářejí nebo znovu sestaví index nebo které odkládají clusterovaný index, můžou být náročné na prostředky. Můžete přepsat maximální úroveň paralelismu databáze pro operace indexu zadáním možnosti indexu MAXDOP v `CREATE INDEX` `ALTER INDEX` příkazu or. Hodnota MAXDOP se použije na příkaz v době spuštění a není uložená v metadatech indexu. Další informace najdete v tématu [Konfigurace operací paralelního indexu](/sql/relational-databases/indexes/configure-parallel-index-operations).  
  
-   Kromě operací dotazů a indexů Určuje možnost konfigurace s rozsahem databáze pro MAXDOP také paralelismus pro příkazy DBCC CHECKTABLE, DBCC CHECKDB a DBCC CHECKFILEGROUP. 

##  <a name="recommendations"></a><a name="Security"></a> Doporučit  

  Změna MAXDOP pro databázi může mít významný dopad na výkon dotazů a využití prostředků, kladné i záporné. Neexistuje však žádná jediná hodnota MAXDOP, která je optimální pro všechny úlohy. Doporučení pro nastavení MAXDOP jsou odlišit a závisí na mnoha faktorech. 

  Některé špičkové úlohy můžou pracovat lépe s různými MAXDOP než jinými. Správně konfigurovaný MAXDOP by měl snížit riziko incidentů výkonu a dostupnosti a v některých případech snižuje náklady díky tomu, že by se zabránilo zbytečnému využití prostředků, a proto se navýšení sníží na nižší cíl služby.

### <a name="excessive-parallelism"></a>Nadměrné paralelismuy

  Vyšší MAXDOP často zkracuje dobu trvání dotazů náročných na procesor. Nadměrné paralelismuy ale můžou zhoršit další souběžný výkon úloh tím, že omezují další dotazy na prostředky procesoru a pracovního vlákna. V extrémních případech může nadměrný paralelismus spotřebovat všechny prostředky databáze nebo elastického fondu, což způsobuje vypršení časových limitů dotazů, chyb a výpadků aplikací. 

  Zákazníkům doporučujeme, aby se vyhnuli MAXDOP 0, i když se zdá, že v tuto chvíli nezpůsobují problémy. Nadměrné paralelismus se stává nejvíc problematickým, když procesor a pracovní vlákna přijímají více souběžných požadavků, než je možné v cíli služby podporovat. Vyhněte se MAXDOP 0, abyste snížili riziko potenciálních budoucích problémů kvůli nadměrnému paralelismuám, pokud je databáze škálovatelná, nebo pokud budoucí generace hardwaru v Azure SQL Database poskytují více jader pro stejný cíl databázové služby.

### <a name="modifying-maxdop"></a>Úprava MAXDOP 

  Pokud zjistíte, že pro Azure SQL Database úlohy je optimální jiné nastavení MAXDOP, můžete použít `ALTER DATABASE SCOPED CONFIGURATION` příkaz T-SQL. Příklady naleznete níže v části [Příklady jazyka Transact-SQL](#examples) . Přidejte tento krok do procesu nasazení, abyste změnili MAXDOP po vytvoření databáze.

  Pokud jiné než výchozí MAXDOP těží jenom podmnožinu dotazů v úloze, můžete přepsat MAXDOP na úrovni dotazu přidáním parametru OPTION (MAXDOP). Příklady naleznete níže v části [Příklady jazyka Transact-SQL](#examples) . 

  Důkladně otestujte změny konfigurace MAXDOP pomocí zátěžového testování zahrnujícího realistické souběžné načítání dotazů. 

  MAXDOP pro primární a sekundární repliky je možné nakonfigurovat nezávisle na využití různých optimálních nastavení MAXDOP pro úlohy pro čtení i zápis a jen pro čtení. To platí pro Azure SQL Database [čtení](read-scale-out.md)na více instancí, [geografickou replikaci](active-geo-replication-overview.md)a [Azure SQL Database sekundárních replik s škálovatelným škálováním](service-tier-hyperscale.md). Ve výchozím nastavení zdědí všechny sekundární repliky konfiguraci MAXDOP primární repliky.

## <a name="security"></a><a name="Security"></a> Bezpečnost  
  
###  <a name="permissions"></a><a name="Permissions"></a> Nastaven  
  `ALTER DATABASE SCOPED CONFIGURATION`Příkaz musí být spuštěn jako správce serveru, jako člen databázové role `db_owner` nebo uživatel, kterému bylo uděleno `ALTER ANY DATABASE SCOPED CONFIGURATION` oprávnění.
 
## <a name="examples"></a>Příklady   

  Tyto příklady používají nejnovější ukázkovou databázi **AdventureWorksLT** , pokud `SAMPLE` je zvolena možnost pro novou jedinou databázi Azure SQL Database.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>Konfigurace s rozsahem databáze MAXDOP   

  Tento příklad ukazuje použití příkazu [ALTER DATABASE scoped Configuration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) ke konfiguraci `max degree of parallelism` Možnosti na `2` . Nastavení se projeví okamžitě. Rutina PowerShellu [Invoke-Sqlcmd](/powershell/module/sqlserver/invoke-sqlcmd) spustí dotazy T-SQL, které nastaví a vrátí konfiguraci s rozsahem databáze MAXDOP. 

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP = 8

$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
     SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

Tento příklad je určený pro použití se službou Azure SQL Database s [povolenými replikami pro čtení](read-scale-out.md), [geografickou replikaci](active-geo-replication-overview.md)a [Azure SQL Database sekundárních replik](service-tier-hyperscale.md)v rámci škálování na více instancí. Například primární replika je nastavena na jiný výchozí MAXDOP jako sekundární replika a předpokládá, že může dojít k rozdílům mezi oprávněními ke čtení i zápisu a úlohou jen pro čtení.

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP_primary = 8
$desiredMAXDOP_secondary_readonly = 1
 
$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
    ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = ' + $desiredMAXDOP_secondary_readonly + ';
    SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

### <a name="transact-sql"></a>Transact-SQL
  
  K provedení dotazů T-SQL na Azure SQL Database můžete použít [Editor dotazů Azure Portal](connect-query-portal.md) [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)nebo [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) .

1.  Připojte se k Azure SQL Database. V hlavní databázi nemůžete změnit rozsah konfigurace databáze.
  
2.  Na standardním panelu vyberte možnost **Nový dotaz**.   
  
3.  Zkopírujte následující příklad a vložte ho do okna dotazu a vyberte **Spustit**. 


#### <a name="maxdop-database-scoped-configuration"></a>Konfigurace s rozsahem databáze MAXDOP

  Tento příklad ukazuje, jak určit aktuální konfiguraci oboru databáze MAXDOP databáze pomocí zobrazení katalogu systému [Sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) .

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  Tento příklad ukazuje použití příkazu [ALTER DATABASE scoped Configuration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) ke konfiguraci `max degree of parallelism` Možnosti na `8` . Nastavení se projeví okamžitě.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Tento příklad je určený pro použití se službou Azure SQL Database s [povolenými replikami pro čtení](read-scale-out.md), [geografickou replikaci](active-geo-replication-overview.md)a [Azure SQL Database sekundárních replik](service-tier-hyperscale.md)v rámci škálování na více instancí. Například primární replika je nastavena na jiný výchozí MAXDOP jako sekundární replika a předpokládá, že může dojít k rozdílům mezi oprávněními ke čtení i zápisu a úlohou jen pro čtení. `value_for_secondary`Sloupec `sys.database_scoped_configurations` obsahující nastavení pro sekundární repliku

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>Pomocný parametr dotazu MAXDOP

  Tento příklad ukazuje, jak spustit dotaz pomocí pomocný parametr dotazu pro vynucení `max degree of parallelism` `2` .  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>MAXDOP – možnost indexu

  Tento příklad ukazuje, jak znovu sestavit index pomocí možnosti index pro vynucení `max degree of parallelism` `12` .  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>Viz také  
* [ALTER DATABASE SCOPEd CONFIGURATION &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [Konfigurovat operace paralelního indexu](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [Nápověda k dotazům &#40;Transact-SQL&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [Nastavení možností indexu](/sql/relational-databases/indexes/set-index-options)     
* [Pochopení a řešení potíží s blokováním Azure SQL Database](understand-resolve-blocking.md)

## <a name="next-steps"></a>Další kroky

* [Monitorování a optimalizace výkonu](/sql/relational-databases/performance/monitor-and-tune-for-performance)
