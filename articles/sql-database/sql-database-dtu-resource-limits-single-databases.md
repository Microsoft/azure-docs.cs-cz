---
title: Prostředek založený na DTU databáze SQL Azure omezuje izolované databáze | Dokumentace Microsoftu
description: Tato stránka popisuje některé běžné limity prostředků založený na DTU pro izolované databáze ve službě Azure SQL Database.
services: sql-database
author: sachinpMSFT
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: effb09cfc68961065ad0b4e4be52255bcd1fe4e0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414163"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Omezení prostředků pro izolované databáze pomocí nákupní model založený na DTU 

Tento článek obsahuje podrobné prostředků limity pro elastické fondy Azure SQL Database pomocí nákupní model založený na DTU.

Založený na DTU nákupní model prostředků omezení pro elastické fondy najdete v tématu [omezení prostředků založený na DTU - elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md). Omezení prostředků založený na virtuálních jádrech najdete v tématu [omezení prostředků na základě virtuálních jader – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založený na virtuálních jádrech - elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Izolované databáze: velikosti úložiště a úrovně výkonu

Pro izolované databáze následující tabulky popisují prostředky dostupné pro izolované databáze na každé úrovni úrovni a výkonu služby. Můžete nastavit úroveň služby, úroveň výkonu a velikost úložiště pro izolovanou databázi pomocí [webu Azure portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-logical-servers-and-databases), [rozhraní příkazového řádku Azure](sql-database-single-databases-manage.md#azure-cli-manage-logical-servers-and-databases), nebo [Rozhraní REST API](sql-database-single-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="basic-service-tier"></a>Úroveň služeb Basic
| **Úroveň výkonu** | **Basic** |
| :--- | --: |
| Maximální počet DTU | 5 |
| Zahrnuté úložiště (GB) | 2 |
| Maximální počet možností úložiště (GB) | 2 |
| Maximální úložiště OLTP v paměti (GB) |neuvedeno |
| Maximální počet souběžných pracovních procesů (požadavků) | 30 |
| Maximální počet souběžných relací | 300 |
|||

### <a name="standard-service-tier"></a>Úroveň služeb Standard
| **Úroveň výkonu** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| Maximální počet DTU | 10 | 20 | 50 | 100 |
| Zahrnuté úložiště (GB) | 250 | 250 | 250 | 250 |
| Maximální počet možností úložiště (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno |
| Maximální počet souběžných pracovních procesů (požadavků)| 60 | 90 | 120 | 200 |
| Maximální počet souběžných relací |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Úrovně služeb Standard (pokračování)
| **Úroveň výkonu** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| Maximální počet DTU | 200 | 400 | 800 | 1600 | 3000 |
| Zahrnuté úložiště (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximální počet možností úložiště (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno |neuvedeno |
| Maximální počet souběžných pracovních procesů (požadavků)| 400 | 800 | 1600 | 3200 |6000 |
| Maximální počet souběžných relací |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Úroveň služeb Premium 
| **Úroveň výkonu** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| Maximální počet DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Zahrnuté úložiště (GB) | 500 | 500 | 500 | 500 | 4 096 | 4 096 |
| Maximální počet možností úložiště (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4 096 | 4 096 |
| Maximální úložiště OLTP v paměti (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximální počet souběžných pracovních procesů (požadavků)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximální počet souběžných relací | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> Více než 1 TB úložiště na úrovni Premium je aktuálně k dispozici ve všech oblastech kromě následujících: západní USA – střed, Čína – východ, USDoDCentral, Německo – střed, USDoDEast, USA (gov) – jihozápad, Německo – severovýchod, USGovIowa, Čína – sever. V ostatních oblastech je úložiště na úrovni Premium omezeno na 1 TB. Viz [Aktuální omezení pro P11–P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="single-database-change-storage-size"></a>Izolované databáze: Změna velikosti úložiště

- Cena za DTU pro izolovanou databázi zahrnuje objem úložiště bez dalších poplatků. Dodatečné úložiště nad rámec objemu zahrnutého v ceně je možné zřídit za poplatek až po limit maximální velikosti, v přírůstcích po 250 GB až 1 TB a potom dokupuje se násobek 256 GB nad rámec 1 TB. Částky zahrnutého úložiště a omezení maximální velikosti najdete v tématu [izolované databáze: velikosti úložiště a úrovně výkonu](#single-database-storage-sizes-and-performance-levels).
- Dodatečné úložiště pro izolovanou databázi je možné zřídit zvýšením jeho pomocí maximální velikosti [webu Azure portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [příkazů jazyka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure Rozhraní příkazového řádku](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update).
- Cena dodatečného úložiště pro izolovanou databázi se velikost dodatečného úložiště vynásobí jednotkovou cenu dodatečné úložiště na úrovni služby. Podrobnosti o cenách dodatečného úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Izolované databáze: změnit počet jednotek Dtu

Po počátečním výběru úrovně služeb, úroveň výkonu a velikost úložiště, můžete vertikálně izolovanou databázi směrem nahoru nebo dolů dynamicky na základě aktuálních zkušeností pomocí [webu Azure portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [příkazů jazyka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [rozhraní příkazového řádku Azure](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update). 

Následující video ukazuje, dynamicky měnit úroveň výkonu pro zvýšení dostupné Dtu pro izolovanou databázi.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Se změnou úrovně služeb nebo úrovně výkonu databáze se vytvoří replika původní databáze na nové úrovni výkonu a následně se přepnou připojení na repliku. Během tohoto procesu se neztratí žádná data, ale během krátké chvíle, kdy se přepíná na repliku, jsou zakázána připojení k databázi, takže může dojít k vrácení některých probíhajících transakcí zpět. Doba pro přechod se liší, ale je méně než 30 sekund 99 % času. Pokud existují velké množství transakcí za pochodu okamžiku zákazu připojení probíhá jsou zakázané, může být delší dobu pro přechod. 

Délka trvání celého procesu vertikálního navyšování kapacity závisí na velikosti a úrovni služeb databáze před změnou a po ní. Například databázi 250 GB, která se mění na, z nebo v rámci úrovně služeb Standard, by se měla dokončit během šesti hodin. Pro databázi stejnou velikost, která se mění úrovně výkonu v rámci úrovně služeb Premium vertikálně navýšit by se měla dokončit během tří hodin.

> [!TIP]
> Monitorování operací v průběhu najdete v tématu: [správě operací pomocí rozhraní SQL API REST](/rest/api/sql/Operations/List), [správě operací pomocí rozhraní příkazového řádku](/cli/azure/sql/db/op), [sledování operací s použitím jazyka T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a tyto dvě Příkazy prostředí PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) a [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Pokud provádíte upgrade na vyšší úroveň výkonu a úroveň služby, maximální velikost databáze se nezvyšuje, pokud explicitně neurčíte větší velikost (maxsize).
* Přejít na nižší verzi databáze, databáze používá prostor musí být menší než maximální povolená velikost v cílové úrovně služby a výkonu úrovně. 
* Při downgradu z verze **Premium** k **standardní** vrstvy, náklady na úložiště platí v případě, že (1) na maximální velikost databáze je podporováno v cílovou úrovní výkonu i (2) na maximální velikost je větší než velikost zahrnutého úložiště cílovou úrovní výkonu. Například pokud je databáze P1 a maximální velikosti 500 GB downsized na S3, pak náklady na úložiště platí od S3 podporuje maximální velikosti 500 GB a jeho velikost zahrnutého úložiště je pouze 250 GB. Ano velikost dodatečného úložiště je 500 GB – 250 GB = 250 GB. Ceny za dodatečné úložiště, najdete v části [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/). Pokud se skutečné množství využité je menší než velikost zahrnutého úložiště, pak toho dalších poplatků se lze vyvarovat snížením maximální velikost databáze na objemu zahrnutého v ceně. 
* Když upgradujete databázi s [geografickou replikaci](sql-database-geo-replication-portal.md) povolena, upgradovat její sekundární databáze na požadovanou úroveň výkonu před upgradem primární databáze (Obecné pokyny pro zajištění nejlepšího výkonu). Při upgradu na jiný, upgrade sekundární databáze nejprve je povinný.
* Při downgradu databáze s [geografickou replikaci](sql-database-geo-replication-portal.md) povolena, snížit jeho primární databáze na požadovanou úroveň výkonu před snížením sekundární databáze (Obecné pokyny pro zajištění nejlepšího výkonu). Při downgradu na jinou edici, downgradu primární databáze nejprve se vyžaduje.
* Nabídky služeb pro obnovení se u různých úrovní služeb liší. Pokud přecházíte na **základní** vrstvy, je zkrátit období uchovávání záloh – viz [zálohy Azure SQL Database](sql-database-automated-backups.md).
* Nové vlastnosti databáze se nepoužijí, dokud nebudou změny dokončeny.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Izolované databáze: omezení pro P11 a P15 při maximální velikosti větší než 1 TB

Maximální velikost větší než 1 TB pro databáze P11 a P15 je podporovaná v následujících oblastech: Austrálie – východ, Austrálie – jihovýchod, Brazílie – Jih, Kanada – střed, Kanada – východ, střed USA, Francie – střed, Německo – střed, Japonsko – východ, Japonsko – Západ, Korea centrální USA (střed) – sever, Severní Evropa, střed USA – Jih, jihovýchodní Asie, Velká Británie – Jih, Velká Británie – Západ, USA – východ 2, USA – Západ, USA (gov) Virginia a západní Evropa. Následující požadavky a omezení platí pro databáze P11 a P15 s maximální velikostí větší než 1 TB:

- Pokud vyberete možnost maximální velikosti větší než 1 TB, při vytváření databáze (s použitím hodnotu 4 TB nebo 4096 GB), příkazu pro vytvoření selže s chybou, pokud je databáze zřízena v nepodporované oblasti.
- U existující databáze P11 a P15 žijí v podporované oblasti, můžete zvýšit maximální úložiště přesáhne 1 TB dokupuje se násobek 256 GB až 4 TB. Chcete-li zjistit, jestli podporuje větší velikost ve vaší oblasti, použijte [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) funkce nebo zkontrolujte velikost databáze na webu Azure Portal. Upgrade existující P11 nebo P15 databáze lze provést pouze pomocí hlavního přihlášení na úrovni serveru nebo členové databázové role dbmanager. 
- Pokud se operace upgradu provádí v podporované oblasti je ihned aktualizovat konfiguraci. Během procesu upgradu zůstává online databáze. Však nemůže využít v plné výši úložiště přesáhne 1 TB úložiště, dokud skutečné databázové soubory se upgradovaly na nový maximální velikost. Délka čas potřebný závisí na velikosti databáze se upgraduje. 
- Při vytváření nebo aktualizaci databáze P11 nebo P15, můžete zvolit pouze maximální velikost 1 TB nebo 4 TB dokupuje se násobek 256 GB. Při vytváření P11 nebo P15 je předem vybraná výchozí možnost úložiště o velikosti 1 TB. U databáze nachází v jednom z podporovaných oblastí můžete zvýšit maximální úložiště až do maximálního počtu 4 TB pro nové nebo existující izolované databáze. Pro všechny ostatní oblasti nelze zvýšit maximální velikost 1 TB. Cena se nemění, když vyberete 4 TB zahrnutého úložiště.
- Pokud nastavena maximální velikost databáze je větší než 1 TB, pak jej nelze změnit na 1 TB i v případě, že skutečné využití úložiště je nižší než 1 TB. Proto nemůžete přejít P11 nebo P15 s maximální velikostí větší než 1 TB na 1 TB P11 nebo TB P15 1 nebo nižší výkon vrstvy, jako je například P1 – P6). Toto omezení platí také pro obnovení a kopírování scénářů, jako jsou třeba bodu v čase, geografické obnovení, dlouhé-dlouhodobé zálohování uchovávání a kopie databáze. Jakmile je databáze nakonfigurovaná s maximální velikostí větší než 1 TB, musí být spuštěn všechny operace obnovení databáze do P11 nebo P15 s maximální velikostí větší než 1 TB.
- Pro scénáře aktivní geografickou replikaci:
   - Nastavení relaci geografické replikace: Pokud je primární databáze P11 nebo P15, secondary(ies) musí být také P11 nebo P15; nižší úrovně výkonu jsou byl odmítnut jako sekundární databáze, protože nejsou schopný zajistit podporu více než 1 TB.
   - Upgrade z primární databáze v relaci geografické replikace: Změna maximální velikosti větší než 1 TB na primární databázi aktivuje stejnou změnu v sekundární databázi. I upgrady musí být úspěšná, aby se změny na primárním se projeví. Platí omezení oblasti pro možnost více než 1 TB. Je-li sekundární v oblasti, která nepodporuje více než 1 TB, není aktualizován primární.
- Použití služby Import/Export pro načítání databáze P11 a P15 s více než 1 TB není podporováno. Použít SqlPackage.exe k [importovat](sql-database-import.md) a [exportovat](sql-database-export.md) data.

## <a name="next-steps"></a>Další postup

- Zobrazit [nejčastější dotazy k SQL Database](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).
- Informace o jednotkách Dtu a Edtu najdete v tématu [jednotkách Dtu a Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- Informace o omezení velikosti databáze tempdb, naleznete v tématu https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
