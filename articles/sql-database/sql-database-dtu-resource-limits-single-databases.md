---
title: Prostředek založený na DTU databáze SQL Azure omezuje izolované databáze | Dokumentace Microsoftu
description: Tato stránka popisuje některé běžné limity prostředků založený na DTU pro izolované databáze ve službě Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: standalone-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: e73bb2f1908abd049ea6e2406c0d1db02381695f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455537"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Omezení prostředků pro izolované databáze pomocí nákupní model založený na DTU

Tento článek obsahuje podrobné prostředků limity pro izolované databáze Azure SQL Database pomocí nákupní model založený na DTU.

Založený na DTU nákupní model prostředků omezení pro elastické fondy najdete v tématu [omezení prostředků založený na DTU - elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md). Omezení prostředků založený na virtuálních jádrech najdete v tématu [omezení prostředků na základě virtuálních jader – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založený na virtuálních jádrech - elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md). Další informace týkající se různých tyto modely nákupu najdete v tématu [nákupu modely a úrovní služeb](sql-database-service-tiers.md).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Izolované databáze: Velikosti úložiště a výpočty velikostí

Následující tabulky ukazují prostředky dostupné pro izolované databáze na jednotlivých úrovních služby a vypočítat velikost. Můžete nastavit úroveň služby, výpočetního prostředí a velikost úložiště pro izolovanou databázi pomocí [webu Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [příkazů jazyka Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [ Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases), nebo [rozhraní REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

### <a name="basic-service-tier"></a>Úroveň služeb Basic

| **Vypočítat velikost** | **Basic** |
| :--- | --: |
| Maximální počet DTU | 5 |
| Zahrnuté úložiště (GB) | 2 |
| Maximální počet možností úložiště (GB) | 2 |
| Maximální úložiště OLTP v paměti (GB) |neuvedeno |
| Maximální počet souběžných pracovních procesů (požadavků) | 30 |
| Maximální počet souběžných relací | 300 |
|||

### <a name="standard-service-tier"></a>Úroveň služeb Standard

| **Vypočítat velikost** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maximální počet DTU | 10 | 20 | 50 | 100 |
| Zahrnuté úložiště (GB) | 250 | 250 | 250 | 250 |
| Maximální počet možností úložiště (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno |
| Maximální počet souběžných pracovních procesů (požadavků)| 60 | 90 | 120 | 200 |
| Maximální počet souběžných relací |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Úrovně služeb Standard (pokračování)

| **Vypočítat velikost** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maximální počet DTU | 200 | 400 | 800 | 1600 | 3000 |
| Zahrnuté úložiště (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximální počet možností úložiště (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno |neuvedeno |
| Maximální počet souběžných pracovních procesů (požadavků)| 400 | 800 | 1600 | 3200 |6000 |
| Maximální počet souběžných relací |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Úroveň služeb Premium

| **Vypočítat velikost** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maximální počet DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Zahrnuté úložiště (GB) | 500 | 500 | 500 | 500 | 4 096 | 4 096 |
| Maximální počet možností úložiště (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4 096 | 4 096 |
| Maximální úložiště OLTP v paměti (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximální počet souběžných pracovních procesů (požadavků)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximální počet souběžných relací | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

> [!IMPORTANT]
> Více než 1 TB úložiště na úrovni Premium je aktuálně k dispozici ve všech oblastech kromě následujících: Čína – východ, Čína – sever, Německo – střed, Německo – severovýchod, Velká Británie – sever, Velká Británie – Jih, US DOD – střed, US DOD – východ, státní správy USA – střed a USA (střed) – západ.  V těchto oblastech je úložiště na úrovni Premium omezeno na 1 TB. Viz [Aktuální omezení pro P11–P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="single-database-change-storage-size"></a>Izolované databáze: Změnit velikost úložiště

- Cena za DTU pro izolovanou databázi zahrnuje objem úložiště bez dalších poplatků. Dodatečné úložiště nad rámec objemu zahrnutého v ceně je možné zřídit za poplatek až po limit maximální velikosti, v přírůstcích po 250 GB až 1 TB a potom dokupuje se násobek 256 GB nad rámec 1 TB. Částky zahrnutého úložiště a omezení maximální velikosti najdete v tématu [izolované databáze: Velikosti úložiště a výpočty velikostí](#single-database-storage-sizes-and-compute-sizes).
- Dodatečné úložiště pro izolovanou databázi je možné zřídit zvýšením jeho pomocí maximální velikosti [webu Azure portal](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-storage-size), [příkazů jazyka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure Rozhraní příkazového řádku](/cli/azure/sql/db#az-sql-db-update), nebo [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- Cena dodatečného úložiště pro izolovanou databázi se velikost dodatečného úložiště vynásobí jednotkovou cenu dodatečné úložiště na úrovni služby. Podrobnosti o cenách dodatečného úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Izolované databáze: Změňte počet jednotek Dtu

Po počátečním výběru úrovně služeb, výpočetního prostředí a velikost úložiště, můžete vertikálně izolovanou databázi směrem nahoru nebo dolů dynamicky na základě aktuálních zkušeností pomocí [webu Azure portal](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus), [příkazů jazyka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [ Prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [rozhraní příkazového řádku Azure](/cli/azure/sql/db#az-sql-db-update), nebo [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/databases/update).

Následující video ukazuje dynamické změny služby vrstvy a vypočítat velikost zvýšit dostupné Dtu pro izolovanou databázi.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Změna služby vrstvy a/nebo vypočítat velikost databáze vytvoří replika původní databáze na novou velikost výpočetních a následně se přepnou připojení na repliku. Během tohoto procesu se neztratí žádná data, ale během krátké chvíle, kdy se přepíná na repliku, jsou zakázána připojení k databázi, takže může dojít k vrácení některých probíhajících transakcí zpět. Doba pro přechod se liší, ale je méně než 30 sekund 99 % času. Pokud existují velké množství transakcí za pochodu okamžiku zákazu připojení probíhá jsou zakázané, může být delší dobu pro přechod.

Délka trvání celého procesu vertikálního navyšování kapacity závisí na velikosti a úrovni služeb databáze před změnou a po ní. Například databázi 250 GB, která se mění na, z nebo v rámci úrovně služeb Standard, by se měla dokončit během šesti hodin. Pro databázi stejnou velikost, která se mění velikosti výpočty v rámci úrovně služeb Premium vertikálně navýšit by se měla dokončit během tří hodin.

> [!TIP]
> Monitorování operací v průběhu najdete v tématu: [Správa operací pomocí rozhraní SQL API REST](https://docs.microsoft.com/rest/api/sql/databaseoperations/listbydatabase
), [správě operací pomocí rozhraní příkazového řádku](/cli/azure/sql/db/op), [sledování operací s použitím jazyka T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a tyto dva příkazy Powershellu: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) a [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

- Pokud provádíte upgrade na vyšší úroveň služby nebo vypočítat velikost, maximální velikost databáze se nezvyšuje, pokud explicitně neurčíte větší velikost (maxsize).
- Na starší verzi databáze, databáze používá prostor musí být menší než maximální povolená velikost cílové úrovni služeb a výpočetního prostředí.
- Při downgradu z verze **Premium** k **standardní** vrstvy, náklady na úložiště platí v případě, že (1) na maximální velikost databáze je podporováno v cílovou velikost výpočetních i (2) je maximální velikost překročí zahrnutou velikost úložiště cíle výpočty velikosti. Například pokud je databáze P1 a maximální velikosti 500 GB downsized na S3, pak náklady na úložiště platí od S3 podporuje maximální velikosti 500 GB a jeho velikost zahrnutého úložiště je pouze 250 GB. Ano velikost dodatečného úložiště je 500 GB – 250 GB = 250 GB. Ceny za dodatečné úložiště, najdete v části [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/). Pokud se skutečné množství využité je menší než velikost zahrnutého úložiště, pak toho dalších poplatků se lze vyvarovat snížením maximální velikost databáze na objemu zahrnutého v ceně.
- Když upgradujete databázi s [geografickou replikaci](sql-database-geo-replication-portal.md) povolena, upgradovat její sekundární databáze na úrovni požadované služby a vypočítat velikost před upgradem primární databáze (Obecné pokyny pro zajištění nejlepšího výkonu). Při upgradu na jiný, upgrade sekundární databáze nejprve je povinný.
- Při downgradu databáze s [geografickou replikaci](sql-database-geo-replication-portal.md) povolena, downgradovat na úroveň požadovanou službu její primární databáze a vypočítat velikost před snížením sekundární databáze (Obecné pokyny pro zajištění nejlepšího výkonu). Při downgradu na jinou edici, downgradu primární databáze nejprve se vyžaduje.
- Nabídky služeb pro obnovení se u různých úrovní služeb liší. Pokud přecházíte na **základní** vrstvy, je zkrátit období uchovávání záloh. Zobrazit [záloh Azure SQL Database](sql-database-automated-backups.md).
- Nové vlastnosti databáze se nepoužijí, dokud nebudou změny dokončeny.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Izolované databáze: Omezení pro P11 a P15 při maximální velikosti větší než 1 TB

Následující požadavky a omezení platí pro databáze P11 a P15 s maximální velikostí větší než 1 TB:

- Pokud vyberete možnost maximální velikosti větší než 1 TB, při vytváření databáze (s použitím hodnotu 4 TB nebo 4096 GB), příkazu pro vytvoření selže s chybou, pokud je databáze zřízena v nepodporované oblasti.
- U existující databáze P11 a P15 žijí v podporované oblasti, můžete zvýšit maximální úložiště přesáhne 1 TB dokupuje se násobek 256 GB až 4 TB. Chcete-li zjistit, jestli podporuje větší velikost ve vaší oblasti, použijte [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) funkce nebo zkontrolujte velikost databáze na webu Azure Portal. Upgrade existující P11 nebo P15 databáze lze provést pouze pomocí hlavního přihlášení na úrovni serveru nebo členové databázové role dbmanager.
- Pokud se operace upgradu provádí v podporované oblasti je ihned aktualizovat konfiguraci. Během procesu upgradu zůstává online databáze. Však nemůže využít v plné výši úložiště přesáhne 1 TB úložiště, dokud skutečné databázové soubory se upgradovaly na nový maximální velikost. Délka čas potřebný závisí na velikosti databáze se upgraduje.
- Při vytváření nebo aktualizaci databáze P11 nebo P15, můžete zvolit pouze maximální velikost 1 TB nebo 4 TB dokupuje se násobek 256 GB. Při vytváření P11 nebo P15 je předem vybraná výchozí možnost úložiště o velikosti 1 TB. U databáze nachází v jednom z podporovaných oblastí můžete zvýšit maximální úložiště až do maximálního počtu 4 TB pro nové nebo existující izolované databáze. Pro všechny ostatní oblasti nelze zvýšit maximální velikost 1 TB. Cena se nemění, když vyberete 4 TB zahrnutého úložiště.
- Pokud nastavena maximální velikost databáze je větší než 1 TB, pak jej nelze změnit na 1 TB i v případě, že skutečné využití úložiště je nižší než 1 TB. Proto nelze downgradovat P11 nebo P15 s maximální velikostí větší než 1 TB na 1 TB P11 nebo TB P15 1 nebo snížení výpočetních velikost, například P1 – P6). Toto omezení platí také pro obnovení a kopírování scénářů, jako jsou třeba bodu v čase, geografické obnovení, dlouhé-dlouhodobé zálohování uchovávání a kopie databáze. Jakmile je databáze nakonfigurovaná s maximální velikostí větší než 1 TB, musí být spuštěn všechny operace obnovení databáze do P11 nebo P15 s maximální velikostí větší než 1 TB.
- Pro scénáře aktivní geografickou replikaci:
  - Nastavení relaci geografické replikace: Pokud je primární databáze P11 nebo P15, secondary(ies) musí být také P11 nebo P15; nižší velikostí výpočetních odmítají jako sekundární databáze, protože nejsou schopný zajistit podporu více než 1 TB.
  - Upgrade primární databázi v relaci geografické replikace: Změna maximální velikosti větší než 1 TB na primární databázi aktivuje stejnou změnu v sekundární databázi. I upgrady musí být úspěšná, aby se změny na primárním se projeví. Platí omezení oblasti pro možnost více než 1 TB. Je-li sekundární v oblasti, která nepodporuje více než 1 TB, není aktualizován primární.
- Použití služby Import/Export pro načítání databáze P11 a P15 s více než 1 TB není podporováno. Použít SqlPackage.exe k [importovat](sql-database-import.md) a [exportovat](sql-database-export.md) data.

## <a name="next-steps"></a>Další postup

- Zobrazit [nejčastější dotazy k SQL Database](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Zobrazit [Přehled prostředků omezuje na server služby SQL Database](sql-database-resource-limits-database-server.md) informace o omezeních na úrovni serveru a předplatné.
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).
- Informace o jednotkách Dtu a Edtu najdete v tématu [jednotkách Dtu a Edtu](sql-database-service-tiers.md#dtu-based-purchasing-model).
- Informace o omezení velikosti databáze tempdb, naleznete v tématu [limity SQL databáze tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
