---
title: Prostředků na základě DTU databáze SQL Azure omezuje izolované databáze | Microsoft Docs
description: Tato stránka popisuje některé běžné limitů prostředků na základě DTU pro izolované databáze ve službě Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 32e797003b7358c1f853d4c3dabf9d810ece43ab
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313329"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Omezení prostředků pro izolované databáze pomocí modelu na základě DTU nákupu 

Tento článek obsahuje podrobné prostředků limity pro elastické fondy Azure SQL Database pomocí modelu na základě DTU nákupu.

Na základě DTU nákupní model limitů prostředků pro elastické fondy, najdete v části [limitů prostředků na základě DTU - elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md). Omezení zdrojů na základě vCore, najdete v části [limitů prostředků na základě vCore - izolovaných databází](sql-database-vcore-resource-limits-single-databases.md) a [limitů prostředků na základě vCore - elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Izolované databáze: velikosti úložiště a úrovně výkonu

Pro izolované databáze v následujících tabulkách prostředky k dispozici pro jednu databázi na úrovni jednotlivých služeb a výkonu. Můžete nastavit úroveň služby, úroveň výkonu a velikost úložiště pro jednu databázi pomocí [portál Azure](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [prostředí PowerShell](sql-database-servers-databases-manage.md#powershell-manage-logical-servers-and-databases), [rozhraní příkazového řádku Azure](sql-database-servers-databases-manage.md#azure-cli-manage-logical-servers-and-databases), nebo [Rozhraní REST API](sql-database-servers-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="basic-service-tier"></a>Úroveň služeb Basic
| **Úroveň výkonu** | **Basic** |
| :--- | --: |
| Maximální počet DTU | 5 |
| Zahrnuté úložiště (GB) | 2 |
| Maximální počet možností úložiště (GB) | 2 |
| Maximální úložiště OLTP v paměti (GB) |neuvedeno |
| Maximální počet souběžných pracovních procesů (počet požadavků) | 30 |
| Maximální počet souběžných relací | 300 |
|||

### <a name="standard-service-tier"></a>Úroveň služeb Standard
| **Úroveň výkonu** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| Maximální počet DTU | 10 | 20 | 50 | 100 |
| Zahrnuté úložiště (GB) | 250 | 250 | 250 | 250 |
| Maximální počet možností úložiště (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno |
| Maximální počet souběžných pracovních procesů (počet požadavků)| 60 | 90 | 120 | 200 |
| Maximální počet souběžných relací |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Úroveň služby na úrovni Standard (pokračování)
| **Úroveň výkonu** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| Maximální počet DTU | 200 | 400 | 800 | 1600 | 3000 |
| Zahrnuté úložiště (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximální počet možností úložiště (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno |neuvedeno |
| Maximální počet souběžných pracovních procesů (počet požadavků)| 400 | 800 | 1600 | 3200 |6000 |
| Maximální počet souběžných relací |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Úroveň služeb Premium 
| **Úroveň výkonu** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| Maximální počet DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Zahrnuté úložiště (GB) | 500 | 500 | 500 | 500 | 4 096 | 4 096 |
| Maximální počet možností úložiště (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4 096 | 4 096 |
| Maximální úložiště OLTP v paměti (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximální počet souběžných pracovních procesů (počet požadavků)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximální počet souběžných relací | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> Více než 1 TB místa v úrovni Premium je nyní k dispozici ve všech oblastech s následující výjimkou: Spojené království – sever, – Západ střední USA, UK South2, východní Čína, USDoDCentral, Německo – střed, indiánský motiv verze pro státní správu USDoDEast, USA, nám verze pro státní správu – jih centrální, Německo – severovýchod, Čína Severní, verze pro státní správu USA – východ. V ostatních oblastech je úložiště na úrovni Premium omezeno na 1 TB. Viz [Aktuální omezení pro P11–P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="single-database-change-storage-size"></a>Izolované databáze: Změna velikosti úložiště

- Cena jednotky DTU pro jednu databázi zahrnuje množství úložiště bez dalších poplatků. Nad rámec zahrnuté množství dodatečné úložiště se dá zřídit další náklady až do limitu maximální velikost v přírůstcích po 250 GB až 1 TB a pak v přírůstcích po 256 GB nad rámec 1 TB. Objemy zahrnuté úložiště a omezení maximální velikosti najdete v tématu [jedné databáze: velikosti úložiště a úrovně výkonu](#single-database-storage-sizes-and-performance-levels).
- Dodatečné úložiště pro izolované databáze může být zřízen zvýšením jeho maximální velikost pomocí [portál Azure](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure Rozhraní příkazového řádku](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update).
- Cena dodatečné úložiště pro jednu databázi je velikost úložiště navíc násobí hodnotou jednotkové ceny dodatečné úložiště vrstvy služby. Podrobnosti na cenu dodatečné úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Izolované databáze: změnit počet jednotek Dtu

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

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Izolované databáze: omezení P11 a P15 při maximální velikosti větší než 1 TB

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

- V tématu [nejčastější dotazy k databázi SQL](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md).
- Informace o Dtu a Edtu najdete v tématu [Dtu a Edtu](sql-database-what-is-a-dtu.md).
- Informace o databázi tempdb velikosti omezení naleznete v tématu https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
