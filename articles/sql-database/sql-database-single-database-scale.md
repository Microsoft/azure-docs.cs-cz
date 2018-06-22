---
title: Škálování izolovaných databází prostředků – Azure SQL Database | Microsoft Docs
description: Tento článek popisuje, jak se škálovat výpočetní a úložnou kapacitu pro jednu databázi k dispozici ve službě Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 525416506a22f386de574ca02b2e919ac47b8737
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309880"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Škálování jedné databáze prostředků ve službě Azure SQL Database

Tento článek popisuje, jak se škálovat výpočetní a úložnou kapacitu pro jednu databázi k dispozici ve službě Azure SQL Database. 

## <a name="vcore-based-purchasing-model-change-storage-size"></a>na základě vCore nákupní model: Změna velikosti úložiště

- Úložiště se dá zřídit až do limitu maximální velikost pomocí 1 GB přírůstcích. Minimální konfigurovat úložiště je 5GB 
- Úložiště pro izolované databáze může být zřízen zvýšením nebo snížením jeho maximální velikost pomocí [portál Azure](https://portal.azure.com), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Rozhraní příkazového řádku azure](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update).
- SQL Database automaticky přiděluje 30 % dodatečné úložiště pro soubory protokolů a 32GB za vCore pro databázi TempDB, ale nechcete překročit 384GB. Databáze TempDB se nachází na připojené SSD na všech úrovních služby.
- Cena úložiště pro jednu databázi je součet množství dat úložiště a protokol úložiště násobí hodnotou jednotkové ceny úložiště vrstvy služby. Náklady na databázi TempDB je součástí vCore cenu. Podrobnosti na cenu dodatečné úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="vcore-based-purchasing-model-change-compute-resources"></a>na základě vCore nákupní model: Změna výpočetní prostředky

Po výběru původně počet vCores, je možné škálovat jedné databáze nahoru nebo dolů dynamicky podle skutečné prostředí pomocí [portál Azure](sql-database-single-database-scale.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [rozhraní příkazového řádku Azure](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update). 

Se změnou úrovně služeb nebo úrovně výkonu databáze se vytvoří replika původní databáze na nové úrovni výkonu a následně se přepnou připojení na repliku. Během tohoto procesu se neztratí žádná data, ale během krátké chvíle, kdy se přepíná na repliku, jsou zakázána připojení k databázi, takže může dojít k vrácení některých probíhajících transakcí zpět. Doba pro přechod se liší, ale je obecně méně než 30 sekundách 99 % času je v části 4 sekundy. Pokud máte velké množství transakcí pohybující se v okamžiku připojení jsou zakázané, může být delší dobu pro přechod. 

Délka trvání celého procesu vertikálního navyšování kapacity závisí na velikosti a úrovni služeb databáze před změnou a po ní. Například 250 GB databázi, která mění k, z a do vrstvy služeb obecné účely, by se měla dokončit v rámci šest hodin. Pro databázi stejnou velikost, která je změna úrovně výkonu v rámci kritické obchodní vrstvy služeb škálování by se měla dokončit do tří hodin.

> [!TIP]
> Monitorování operací v mailech naleznete v tématu: [spravovat operace pomocí SQL REST API](/rest/api/sql/Operations/List), [spravovat operace pomocí rozhraní příkazového řádku](/cli/azure/sql/db/op), [sledovat operace pomocí T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a tyto dvě Příkazy prostředí PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) a [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Pokud provádíte upgrade na vyšší úroveň výkonu a vrstvu služby, maximální velikost databáze se nezvyšuje, pokud je explicitně zadat větší velikost (maxsize).
* Přejít na starší verzi databáze, databáze používá prostor musí být menší než maximální povolená velikost úroveň cílové služby a výkonu. 
* Při upgradování databáze s [geografická replikace](sql-database-geo-replication-portal.md) povoleno, upgradujte její sekundární databáze úrovně požadovaný výkon před upgradem primární databáze (Obecné pokyny pro nejlepší výkon). Při upgradu na jiný, upgrade sekundární databázi nejprve je vyžadováno.
* Když přechod na starší verzi databáze s [geografická replikace](sql-database-geo-replication-portal.md) povoleno, starší verzi její primární databáze k vrstvě požadovaný výkon před snížením sekundární databáze (Obecné pokyny pro nejlepší výkon). Při přechod na starší verzi na jinou edici, přechod na starší verzi primární databáze nejprve je vyžadováno.
* Nové vlastnosti databáze se nepoužijí, dokud nebudou změny dokončeny.

## <a name="dtu-based-purchasing-model-change-storage-size"></a>Na základě DTU nákupní model: Změna velikosti úložiště

- Cena jednotky DTU pro jednu databázi zahrnuje množství úložiště bez dalších poplatků. Nad rámec zahrnuté množství dodatečné úložiště se dá zřídit další náklady až do limitu maximální velikost v přírůstcích po 250 GB až 1 TB a pak v přírůstcích po 256 GB nad rámec 1 TB. Objemy zahrnuté úložiště a omezení maximální velikosti najdete v tématu [jedné databáze: velikosti úložiště a úrovně výkonu](#single-database-storage-sizes-and-performance-levels).
- Dodatečné úložiště pro izolované databáze může být zřízen zvýšením jeho maximální velikost pomocí [portál Azure](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure Rozhraní příkazového řádku](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update).
- Cena dodatečné úložiště pro jednu databázi je velikost úložiště navíc násobí hodnotou jednotkové ceny dodatečné úložiště vrstvy služby. Podrobnosti na cenu dodatečné úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="dtu-based-purchasing-model-change-compute-resources-dtus"></a>Na základě DTU nákupní model: Změna výpočetní prostředky (Dtu)

Po výběru původně vrstvy služeb, úroveň výkonu a velikost úložiště, můžete škálovat jedné databáze nahoru nebo dolů dynamicky podle skutečné prostředí pomocí [portál Azure](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [Prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [rozhraní příkazového řádku Azure](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update). 

Následující video ukazuje, dynamická změna úroveň výkonu zvýšit dostupné Dtu pro jednu databázi.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Se změnou úrovně služeb nebo úrovně výkonu databáze se vytvoří replika původní databáze na nové úrovni výkonu a následně se přepnou připojení na repliku. Během tohoto procesu se neztratí žádná data, ale během krátké chvíle, kdy se přepíná na repliku, jsou zakázána připojení k databázi, takže může dojít k vrácení některých probíhajících transakcí zpět. Doba pro přechod se liší, ale je méně než 30 sekundách 99 % času. Pokud máte velké množství transakcí pohybující se v okamžiku připojení jsou zakázané, může být delší dobu pro přechod. 

Délka trvání celého procesu vertikálního navyšování kapacity závisí na velikosti a úrovni služeb databáze před změnou a po ní. Například 250 GB databázi, která mění k, z a do vrstvy standardní služby, by se měla dokončit v rámci šest hodin. Pro databázi stejnou velikost, která je změna úrovně výkonu v rámci úroveň služeb Premium by se měla dokončit škálování do tří hodin.

> [!TIP]
> Monitorování operací v mailech naleznete v tématu: [spravovat operace pomocí SQL REST API](/rest/api/sql/Operations/List), [spravovat operace pomocí rozhraní příkazového řádku](/cli/azure/sql/db/op), [sledovat operace pomocí T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a tyto dvě Příkazy prostředí PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) a [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Pokud provádíte upgrade na vyšší úroveň výkonu a vrstvu služby, maximální velikost databáze se nezvyšuje, pokud je explicitně zadat větší velikost (maxsize).
* Přejít na starší verzi databáze, databáze používá prostor musí být menší než maximální povolená velikost úroveň cílové služby a výkonu. 
* Když přechod na starší verzi z **Premium** k **standardní** vrstvy, náklady na úložiště navíc platí v případě, že (1) je maximální velikost databáze je podporována v úroveň výkonu cílové i (2) je maximální velikost překračuje úložiště zahrnuté množství na úroveň výkonu cílové. Například pokud databázi P1 a maximální velikost 500 GB je downsized k S3, pak náklady na úložiště navíc platí od S3 podporuje maximální velikost 500 GB a jeho součástí úložiště je pouze 250 GB. Ano velikost úložiště je 500 GB – 250 GB = 250 GB. Ceny navíc úložiště, najdete v části [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/). Pokud skutečná velikost využitého místa je menší než množství součástí úložiště, pak tato zpoplatněné se vyhnout snížením maximální velikost databáze na zahrnuté množství. 
* Při upgradování databáze s [geografická replikace](sql-database-geo-replication-portal.md) povoleno, upgradujte její sekundární databáze úrovně požadovaný výkon před upgradem primární databáze (Obecné pokyny pro nejlepší výkon). Při upgradu na jiný, upgrade sekundární databázi nejprve je vyžadováno.
* Když přechod na starší verzi databáze s [geografická replikace](sql-database-geo-replication-portal.md) povoleno, starší verzi její primární databáze k vrstvě požadovaný výkon před snížením sekundární databáze (Obecné pokyny pro nejlepší výkon). Při přechod na starší verzi na jinou edici, přechod na starší verzi primární databáze nejprve je vyžadováno.
* Nabídky služeb pro obnovení se u různých úrovní služeb liší. Pokud jsou Downgrade k **základní** vrstvy, není obdobím uchovávání záloh – viz [zálohy databáze SQL Azure](sql-database-automated-backups.md).
* Nové vlastnosti databáze se nepoužijí, dokud nebudou změny dokončeny.

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Na základě DTU nákupní model: omezení P11 a P15 při maximální velikosti větší než 1 TB

Maximální velikost větší než 1 TB P11 a P15 databáze je podporováno v následujících oblastech: Austrálie – východ, Austrálie – jihovýchod, Brazílie – Jih, Střední Kanada, Východní Kanada, střed USA, Francie centrální, Německo centrální, Japonsko – východ, Japonsko – Západ, Korejská centrální Střed USA – sever, Severní Evropa, střed USA – Jih, jihovýchodní Asie, Spojené království – Jih, Spojené království – Západ, East2 USA, západ USA, Virginia verze pro státní správu USA a západní Evropa. Následující požadavky a omezení platí pro databáze P11 a P15 s maximální velikostí větší než 1 TB:

- Pokud si zvolíte maximální velikost větší než 1 TB, když se vytváří databáze (pomocí hodnotu 4 TB nebo 4096 GB), provedení příkazu pro vytvoření selže s chybou, pokud databáze je zajištěna v nepodporované oblast.
- Pro existující P11 a P15 databáze nachází v jedné z podporovaných oblastí, můžete zvýšit maximální velikost úložiště na nad rámec 1 TB v přírůstcích po 256 GB až 4 TB. Pokud chcete zobrazit, pokud větší velikost je podporován ve vašem regionu, použijte [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) funkce nebo zkontrolujte velikost databáze na portálu Azure. Upgrade na existující P11 nebo P15 databáze lze provést pouze pomocí hlavního přihlášení na úrovni serveru nebo pomocí členové databázové role dbmanager. 
- Pokud se operace upgradu se spustí v podporované oblasti konfigurace aktualizoval okamžitě. Během procesu upgradu zůstane online databáze. Nelze však využívat celkovou velikost úložiště na více než 1 TB úložiště, dokud se neupgradují skutečné databázové soubory do nové maximální velikost. Délka čas potřebný závisí na velikosti databáze během upgradu. 
- Při vytváření nebo aktualizaci P11 nebo P15 databáze, můžete zvolit pouze 1 TB a 4 TB maximální velikost v přírůstcích po 256 GB. Při vytváření P11/P15, je předem vybranou výchozí možnost úložiště o velikosti 1 TB. Pro databáze nachází v jedné z podporovaných oblastí můžete zvýšit maximální úložiště pro maximálně 4 TB pro jednu databázi nové nebo existující. Všechny ostatní země nelze zvýšit maximální velikost, vyšší než 1 TB. Ceny nezmění, když vyberete 4 TB součástí úložiště.
- Pokud je maximální velikost databáze nastavené na větší než 1 TB, pak jej nelze změnit na 1 TB i v případě, že skutečné úložiště používané je menší než 1 TB. Proto není možné snížit P11 nebo P15 s maximální velikostí větší než 1 TB 1 TB P11 nebo 1 TB P15 nebo snížení výkonu vrstvy, jako je například P1 P6). Toto omezení platí také pro obnovení a scénáře kopírováním včetně bodu v čase, geografické obnovení, dlouhodobý-dlouhodobé zálohování uchovávání a kopie databáze. Jakmile je databáze nakonfigurovaná se maximální velikost větší než 1 TB, musí být spuštěn všechny operace obnovení této databáze do P11 nebo P15 s maximální velikostí větší než 1 TB.
- Pro aktivní geografickou replikací scénáře:
   - Nastavení vztahu geografická replikace: Pokud primární databáze je P11 nebo P15, secondary(ies) musí být také P11 nebo P15; nižší úrovně výkonu byly zamítnuty jako sekundární databáze, protože nejsou schopný zajistit podporu více než 1 TB.
   - Upgrade z primární databáze ve vztahu geografická replikace: Změna maximální velikosti více než 1 TB na primární databázi aktivuje stejné změny v sekundární databázi. Obě upgrady musí být úspěšná změna na primárním vstoupily v platnost. Platí omezení oblast pro možnost více než 1 TB. Je-li sekundární v oblasti, která nepodporuje více než 1 TB, není aktualizován primární.
- Použití službu Import/Export načítání P11/P15 databáze s více než 1 TB není podporováno. Použít SqlPackage.exe k [importovat](sql-database-import.md) a [exportovat](sql-database-export.md) data.


## <a name="next-steps"></a>Další postup

Celkové omezení prostředků, najdete v části [limitů na základě vCore prostředků databáze SQL - izolovaných databází](sql-database-vcore-resource-limits-single-databases.md) a [limitů prostředků na základě DTU databáze SQL - elastické fondy](sql-database-dtu-resource-limits-single-databases.md).
