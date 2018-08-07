---
title: Škálování izolované databáze prostředků – Azure SQL Database | Dokumentace Microsoftu
description: Tento článek popisuje, jak škálovat výpočetní a úložné prostředky dostupné pro izolované databáze ve službě Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: carlrab
ms.openlocfilehash: 8ddeedcde142a01cca9efa79ab08029aec798ceb
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525286"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Škálování izolované databáze prostředků ve službě Azure SQL Database

Tento článek popisuje, jak škálovat výpočetní a úložné prostředky dostupné pro izolované databáze ve službě Azure SQL Database. 

## <a name="vcore-based-purchasing-model-change-storage-size"></a>nákupní model založený na virtuálních jádrech: Změna velikosti úložiště

- Úložiště lze zřídit až po limit maximální velikosti 1 GB přírůstcích pomocí. Minimální konfigurovatelné datové úložiště je 5GB 
- Zvýšením nebo snížením jeho maximální velikost pomocí je možné zřídit úložiště pro izolovanou databázi [webu Azure portal](https://portal.azure.com), [příkazů jazyka Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql?r#examples), [Powershellu](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Rozhraní příkazového řádku azure](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update).
- SQL Database automaticky přiděluje 30 % další úložiště pro soubory protokolů a 32GB za – vCore pro databázi TempDB, ale která nepřekročí 384GB. Databáze TempDB je umístěná na připojené SSD ve všech úrovních služby.
- Ceny úložišť pro izolované databáze je součet množství dat úložiště a protokol úložiště vynásobí jednotkovou cenu úložiště na úrovni služby. Cena databáze tempdb je zahrnutá v ceně – vCore. Podrobnosti o cenách dodatečného úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="vcore-based-purchasing-model-change-compute-resources"></a>nákupní model založený na virtuálních jádrech: Změna výpočetních prostředků

Po počátečním výběru počet virtuálních jader, můžete vertikálně izolovanou databázi směrem nahoru nebo dolů dynamicky na základě aktuálních zkušeností pomocí [webu Azure portal](sql-database-single-database-scale.md#azure-portal-manage-logical-servers-and-databases), [příkazů jazyka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [Powershellu](/powershell/module/azurerm.sql/set-azurermsqldatabase), [rozhraní příkazového řádku Azure](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update). 

Se změnou úrovně služeb nebo úrovně výkonu databáze se vytvoří replika původní databáze na nové úrovni výkonu a následně se přepnou připojení na repliku. Během tohoto procesu se neztratí žádná data, ale během krátké chvíle, kdy se přepíná na repliku, jsou zakázána připojení k databázi, takže může dojít k vrácení některých probíhajících transakcí zpět. Doba pro přechod se liší, ale je obecně nepřekračuje 4 sekundy je menší než 30 sekund 99 % času. Pokud existují velké množství transakcí za pochodu okamžiku zákazu připojení probíhá jsou zakázané, může být delší dobu pro přechod. 

Délka trvání celého procesu vertikálního navyšování kapacity závisí na velikosti a úrovni služeb databáze před změnou a po ní. Například databázi 250 GB, která se mění na, z nebo v rámci úrovně služeb obecné účely, by se měla dokončit během šesti hodin. Pro databázi stejnou velikost, která se mění úrovně výkonu v rámci pro důležité obchodní informace úrovně služeb vertikálně navýšit by se měla dokončit během tří hodin.

> [!TIP]
> Monitorování operací v průběhu najdete v tématu: [správě operací pomocí rozhraní SQL API REST](/rest/api/sql/Operations/List), [správě operací pomocí rozhraní příkazového řádku](/cli/azure/sql/db/op), [sledování operací s použitím jazyka T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a tyto dvě Příkazy prostředí PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) a [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Pokud provádíte upgrade na vyšší úroveň výkonu a úroveň služby, maximální velikost databáze se nezvyšuje, pokud explicitně neurčíte větší velikost (maxsize).
* Přejít na nižší verzi databáze, databáze používá prostor musí být menší než maximální povolená velikost v cílové úrovně služby a výkonu úrovně. 
* Když upgradujete databázi s [geografickou replikaci](sql-database-geo-replication-portal.md) povolena, upgradovat její sekundární databáze na požadovanou úroveň výkonu před upgradem primární databáze (Obecné pokyny pro zajištění nejlepšího výkonu). Při upgradu na jiný, upgrade sekundární databáze nejprve je povinný.
* Při downgradu databáze s [geografickou replikaci](sql-database-geo-replication-portal.md) povolena, snížit jeho primární databáze na požadovanou úroveň výkonu před snížením sekundární databáze (Obecné pokyny pro zajištění nejlepšího výkonu). Při downgradu na jinou edici, downgradu primární databáze nejprve se vyžaduje.
* Nové vlastnosti databáze se nepoužijí, dokud nebudou změny dokončeny.

## <a name="dtu-based-purchasing-model-change-storage-size"></a>Nákupní model založený na DTU: Změna velikosti úložiště

- Cena za DTU pro izolovanou databázi zahrnuje objem úložiště bez dalších poplatků. Dodatečné úložiště nad rámec objemu zahrnutého v ceně je možné zřídit za poplatek až po limit maximální velikosti, v přírůstcích po 250 GB až 1 TB a potom dokupuje se násobek 256 GB nad rámec 1 TB. Částky zahrnutého úložiště a omezení maximální velikosti najdete v tématu [izolované databáze: velikosti úložiště a úrovně výkonu](#single-database-storage-sizes-and-performance-levels).
- Dodatečné úložiště pro izolovanou databázi je možné zřídit zvýšením jeho maximální velikost pomocí webu Azure portal, [příkazů jazyka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [rozhraní příkazového řádku Azure](/cli/azure/sql/db#az_sql_db_update), nebo [ Rozhraní REST API](/rest/api/sql/databases/update).
- Cena dodatečného úložiště pro izolovanou databázi se velikost dodatečného úložiště vynásobí jednotkovou cenu dodatečné úložiště na úrovni služby. Podrobnosti o cenách dodatečného úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-change-compute-resources-dtus"></a>Nákupní model založený na DTU: Změna výpočetních prostředků (Dtu)

Po počátečním výběru úrovně služeb, úroveň výkonu a velikost úložiště, můžete vertikálně izolovanou databázi směrem nahoru nebo dolů dynamicky na základě aktuálních zkušeností pomocí webu Azure portal, [příkazů jazyka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [Powershellu](/powershell/module/azurerm.sql/set-azurermsqldatabase), [rozhraní příkazového řádku Azure](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update). 

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

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Nákupní model založený na DTU: omezení P11 a P15 při maximální velikosti větší než 1 TB

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

Celkové omezení prostředků najdete v tématu [omezení prostředků založený na virtuálních jádrech SQL Database – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založený na DTU databáze SQL – elastických fondů](sql-database-dtu-resource-limits-single-databases.md).
