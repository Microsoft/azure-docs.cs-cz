---
title: Konfigurace maximálního stupně paralelismu (MAXDOP)
titleSuffix: Azure SQL Database
description: Seznamte se s maximálním stupněm paralelismu (MAXDOP).
ms.date: 04/12/2021
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
ms.openlocfilehash: c9b8e916c82a42df7addb3c49b4452c0eb403023
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536903"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Konfigurace maximálního stupně paralelismu (MAXDOP) ve službě Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  Tento článek popisuje nastavení konfigurace **maximálního stupně paralelismu (MAXDOP)** v Azure SQL Database. 

> [!NOTE]
> **Tento obsah se zaměřuje na Azure SQL Database.** Azure SQL Database vychází z nejnovější stabilní verze databázového stroje Microsoft SQL Server, takže většina obsahu je podobná, i když řešení potíží a možnosti konfigurace se liší. Další informace o MAXDOP v SQL Server najdete v tématu [Konfigurace možnosti Maximální stupeň paralelního nastavení konfigurace serveru](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

## <a name="overview"></a>Přehled
  MAXDOP ovládá paralelismus uvnitř dotazování v databázovém stroji. Vyšší hodnoty MAXDOP obvykle mají za následek více paralelních vláken na dotaz a rychlejší provádění dotazů. 

  V Azure SQL Database výchozí nastavení MAXDOP pro každou novou databázi a databázi elastického fondu je 8. Toto výchozí nastavení zabrání zbytečnému využití prostředků a zároveň umožňuje databázovému stroji provádět dotazy rychleji pomocí paralelních vláken. Obvykle není nutné dále konfigurovat MAXDOP v úlohách Azure SQL Database, i když vám může poskytnout výhody jako Pokročilé cvičení ladění výkonu.

> [!Note]
>   V září 2020 byla na základě roků telemetrie ve službě Azure SQL Database Service MAXDOP 8 pro [nové databáze nastavena](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528)jako optimální hodnota pro nejširší škálu úloh zákazníků. Toto výchozí nastavení pomáhá zabránit problémům s výkonem kvůli nadměrnému paralelismu. Před tím bylo výchozí nastavení pro nové databáze MAXDOP 0. MAXDOP se automaticky nezměnil pro existující databáze vytvořené před 2020. září.

  Obecně platí, že pokud se databázový stroj rozhodne provést dotaz pomocí paralelismu, je čas spuštění rychlejší. Nadbytečné paralelismus však mohou spotřebovat další prostředky procesoru bez zvýšení výkonu dotazů. Ve velkém měřítku může nadbytečná paralelismu negativně ovlivnit výkon dotazů všech dotazů spouštěných ve stejné instanci databázového stroje. Tradičně je nastavení horní meze pro paralelismus běžným cvičením ladění výkonu v SQL Server úloh.

  V následující tabulce jsou popsány vlastnosti databázového stroje při provádění dotazů s různými MAXDOP hodnotami:

| MAXDOP | Chování | 
|--|--|
| = 1 | Databázový stroj používá pro spouštění dotazů jeden sériový podproces. Paralelní vlákna se nepoužívají. | 
| > 1 | Databázový stroj nastaví počet dalších [plánovačů](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) , které mají být použity paralelními vlákny na hodnotu MAXDOP, nebo celkový počet logických procesorů, podle toho, co je menší. |
| = 0 | Databázový stroj nastaví počet dalších [plánovačů](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) , které mají být použity paralelními vlákny, na celkový počet logických procesorů nebo 64, podle toho, co je menší. | 
| | |

> [!Note]
> Každý dotaz se spustí alespoň s jedním plánovačem a jedním pracovním vláknem v tomto plánovači.
>
> Dotaz prováděný s paralelismus používá další plánovače a další paralelní vlákna. Vzhledem k tomu, že více paralelních vláken může být spuštěno ve stejném plánovači, celkový počet vláken použitých k provedení dotazu může být vyšší než zadaná hodnota MAXDOP nebo celkový počet logických procesorů. Další informace najdete v tématu [plánování paralelních úkolů](/sql/relational-databases/thread-and-task-architecture-guide#scheduling-parallel-tasks).

##  <a name="considerations"></a><a name="Considerations"></a> Odůvodněn  

-   V Azure SQL Database můžete změnit výchozí hodnotu MAXDOP:
    -   Na úrovni dotazu použijte [pomocný parametr dotazu](/sql/t-sql/queries/hints-transact-sql-query) **MAXDOP** .     
    -   Na úrovni databáze s použitím konfigurace s  [rozsahem databáze](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)MAXDOP.

-   Pro Azure SQL Database se vztahují i na dlouhotrvající SQL Server MAXDOP a [doporučení](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) pro použití. 

-   Operace indexu, které vytvářejí nebo znovu sestaví index nebo které odkládají clusterovaný index, můžou být náročné na prostředky. Hodnotu MAXDOP databáze pro operace indexu můžete přepsat zadáním možnosti index MAXDOP v `CREATE INDEX` `ALTER INDEX` příkazu or. Hodnota MAXDOP se použije na příkaz v době spuštění a není uložená v metadatech indexu. Další informace najdete v tématu [Konfigurace operací paralelního indexu](/sql/relational-databases/indexes/configure-parallel-index-operations).  
  
-   Kromě operací dotazů a indexů Určuje možnost konfigurace s rozsahem databáze pro MAXDOP také paralelně jiné příkazy, které mohou používat paralelní spouštění, například DBCC CHECKTABLE, DBCC CHECKDB a DBCC CHECKFILEGROUP. 

##  <a name="recommendations"></a><a name="Recommendations"></a> Doporučit  

  Změna MAXDOP pro databázi může mít významný dopad na výkon dotazů a využití prostředků, kladné i záporné. Neexistuje však žádná jediná hodnota MAXDOP, která je optimální pro všechny úlohy. [Doporučení](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) pro nastavení MAXDOP jsou odlišit a závisí na mnoha faktorech. 

  Některé špičkové úlohy můžou pracovat lépe s různými MAXDOP než jinými. Správně nakonfigurovaná MAXDOP by měla snížit riziko incidentů výkonu a dostupnosti a v některých případech může snížit náklady tím, že se bude moci vyhnout zbytečnému využití prostředků, a proto škálovat dolů na nižší cíl služby.

### <a name="excessive-parallelism"></a>Nadměrné paralelismuy

  Vyšší MAXDOP často zkracuje dobu trvání dotazů náročných na procesor. Nadměrné paralelismuy ale můžou zhoršit další souběžný výkon úloh tím, že omezují další dotazy na prostředky procesoru a pracovního vlákna. V extrémních případech může nadměrný paralelismus spotřebovat všechny prostředky databáze nebo elastického fondu, což způsobuje vypršení časových limitů dotazů, chyb a výpadků aplikací. 

> [!Tip]
> Zákazníkům doporučujeme, aby nedocházelo k nastavování MAXDOP 0, i když se zdá, že v tuto chvíli nezpůsobují problémy.

  Nadměrné paralelismus se dá nejvícně problematické, pokud jsou víc souběžných požadavků, než je podporuje prostředky procesoru a pracovního vlákna, které poskytuje cíl služby. Vyhněte se MAXDOP 0, abyste snížili riziko potenciálních budoucích problémů kvůli nadměrnému paralelismuám, pokud je databáze škálovatelná, nebo pokud budoucí generace hardwaru v Azure SQL Database poskytují více jader pro stejný cíl databázové služby.

### <a name="modifying-maxdop"></a>Úprava MAXDOP 

  Pokud určíte, že nastavení MAXDOP, které se liší od výchozí hodnoty, je optimální pro vaši úlohu Azure SQL Database, můžete použít `ALTER DATABASE SCOPED CONFIGURATION` příkaz T-SQL. Příklady naleznete níže v části [Příklady jazyka Transact-SQL](#examples) . Pokud chcete změnit MAXDOP na jinou než výchozí hodnotu pro každou novou databázi, kterou vytvoříte, přidejte tento krok do procesu nasazení databáze.

  Pokud jiné než výchozí MAXDOP těží jenom malou podmnožinu dotazů v úloze, můžete MAXDOP na úrovni dotazu přepsat přidáním parametru OPTION (MAXDOP). Příklady naleznete níže v části [Příklady jazyka Transact-SQL](#examples) . 

  Důkladně otestujte změny konfigurace MAXDOP pomocí zátěžového testování zahrnujícího realistické souběžné načítání dotazů. 

  MAXDOP pro primární a sekundární repliky je možné nakonfigurovat nezávisle, pokud jsou pro úlohy pro čtení i zápis optimální pro čtení i zápis. To platí pro Azure SQL Database [čtení](read-scale-out.md)na více instancí, [geografickou replikaci](active-geo-replication-overview.md)a sekundární repliky s škálovatelným [škálováním](service-tier-hyperscale.md) . Ve výchozím nastavení zdědí všechny sekundární repliky konfiguraci MAXDOP primární repliky.

## <a name="security"></a><a name="Security"></a> Bezpečnost  
  
###  <a name="permissions"></a><a name="Permissions"></a> Nastaven  
  `ALTER DATABASE SCOPED CONFIGURATION`Příkaz musí být spuštěn jako správce serveru, jako člen databázové role `db_owner` nebo uživatel, kterému bylo uděleno `ALTER ANY DATABASE SCOPED CONFIGURATION` oprávnění.
 
## <a name="examples"></a>Příklady   

  Tyto příklady používají nejnovější ukázkovou databázi **AdventureWorksLT** , pokud `SAMPLE` je zvolena možnost pro novou jedinou databázi Azure SQL Database.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>Konfigurace s rozsahem databáze MAXDOP   

  Tento příklad ukazuje použití příkazu [ALTER DATABASE scoped Configuration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) k nastavení `MAXDOP` Konfigurace na `2` . Nastavení se projeví okamžitě pro nové dotazy. Rutina PowerShellu [Invoke-Sqlcmd](/powershell/module/sqlserver/invoke-sqlcmd) spustí dotazy T-SQL, které nastaví a vrátí konfiguraci s rozsahem databáze MAXDOP. 

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

1.  Otevřete nové okno dotazu.

2.  Připojte se k databázi, ve které chcete změnit MAXDOP. V hlavní databázi nemůžete změnit konfigurace s rozsahem databáze.
  
3.  Zkopírujte následující příklad a vložte ho do okna dotazu a vyberte **Spustit**. 

#### <a name="maxdop-database-scoped-configuration"></a>Konfigurace s rozsahem databáze MAXDOP

  Tento příklad ukazuje, jak určit aktuální konfiguraci oboru databáze MAXDOP databáze pomocí zobrazení katalogu systému [Sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) .

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  Tento příklad ukazuje použití příkazu [ALTER DATABASE scoped Configuration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) k nastavení `MAXDOP` Konfigurace na `8` . Nastavení se projeví okamžitě.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Tento příklad je určený pro použití s databázemi Azure SQL s [povolenými replikami čtení](read-scale-out.md), [geografickou replikací](active-geo-replication-overview.md)a sekundárními replikami s škálovatelným [škálováním](service-tier-hyperscale.md) . Například primární replika je nastavena na jiný MAXDOP než sekundární replika a předpokládá, že existují rozdíly mezi úlohami pro čtení i zápis a jen pro čtení. Všechny příkazy jsou spouštěny v primární replice. `value_for_secondary`Sloupec `sys.database_scoped_configurations` obsahující nastavení pro sekundární repliku

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
