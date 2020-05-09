---
title: Škálování prostředků jednoúčelové databáze
description: Tento článek popisuje, jak škálovat výpočetní prostředky a prostředky úložiště dostupné pro jednu databázi v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/30/2020
ms.openlocfilehash: a13b860e01e7ef295df629d79dfa44700b5f0d02
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691597"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Škálování jednoho databázového prostředku v Azure SQL Database

Tento článek popisuje, jak škálovat výpočetní prostředky a prostředky úložiště dostupné pro Azure SQL Database v zřízené výpočetní úrovni. [Výpočetní úroveň bez serveru](sql-database-serverless.md) navíc poskytuje výpočetní automatické škálování a platby za sekundu pro využité výpočetní prostředky.

Po počátečním výběru počtu virtuální jádra nebo DTU můžete dynamicky škálovat jednu databázi na základě aktuálního prostředí pomocí [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShellu](/powershell/module/az.sql/set-azsqldatabase), rozhraní příkazového [řádku Azure](/cli/azure/sql/db#az-sql-db-update)nebo [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).

Následující video ukazuje dynamicky se měnící úroveň služby a výpočetní velikost, aby se zvýšila dostupnost DTU pro izolovanou databázi.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](sql-database-file-space-management.md).

## <a name="impact"></a>Dopad

Změna úrovně služby nebo výpočetní velikosti hlavně zahrnuje službu, která provádí následující kroky:

1. Vytvořit novou výpočetní instanci pro databázi  

    Vytvoří se nová instance COMPUTE s požadovanou úrovní služeb a výpočetní velikostí. V případě některých kombinací úrovně služeb a výpočtů velikosti se musí replika databáze vytvořit v nové výpočetní instanci, která zahrnuje kopírování dat a může silně ovlivnit celkovou latenci. Bez ohledu na to, že databáze zůstane v průběhu tohoto kroku online, připojení budou dál směrována do databáze v původní výpočetní instanci.

2. Přepnout směrování připojení na novou výpočetní instanci

    Existující připojení k databázi v původní výpočetní instanci jsou vyhozena. Všechna nová připojení jsou navázána na databázi v nové instanci Compute. V případě některých kombinací úrovně služby a velikosti výpočetních souborů jsou soubory databáze odpojeny a připojeny během přepínače.  Přepínač může mít za následek krátké přerušení služby, když databáze není k dispozici, obvykle po dobu kratší než 30 sekund a často jenom pár sekund. Pokud při vyřazení připojení dojde k nespuštěným transakcím, může trvat delší dobu trvání tohoto kroku, aby bylo možné obnovit přerušené transakce. [Urychlené obnovení databáze](sql-database-accelerated-database-recovery.md) může snížit dopad přerušení dlouho probíhajících transakcí.

> [!IMPORTANT]
> Během žádného kroku pracovního postupu nebudou ztracena žádná data. Ujistěte se, že jste implementovali nějakou [logiku opakování](sql-database-connectivity-issues.md) v aplikacích a součástech, které při změně úrovně služby používají Azure SQL Database.

## <a name="latency"></a>Latence 

Odhadovaná latence změny úrovně služby, škálování výpočetní velikosti izolované databáze nebo elastického fondu, přesun databáze do nebo z elastického fondu nebo přesun databáze mezi elastickými fondy je parametrizovaný takto:

|Úroveň služeb|Jednoduchá databáze úrovně Basic,</br>Standard (S0-S1)|Elastický fond úrovně Basic,</br>Standard (S2-S12), </br>Hyperškálovatelný </br>Pro obecné účely izolovanou databázi nebo elastický fond|Izolovaná databáze nebo elastický fond úrovně Premium nebo Pro důležité obchodní informace|
|:---|:---|:---|:---|
|**Basic Single Database</br> Standard (S0-S1)**|&bull;&nbsp;Konstantní latence v čase nezávisle na využitém prostoru</br>&bull;&nbsp;Obvykle méně než 5 minut|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|
|**Základní elastický fond </br>, Standard (S2-S12) </br>, škálovatelná </br>, pro obecné účely izolovaná databáze nebo elastický fond**|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Konstantní latence v čase nezávisle na využitém prostoru</br>&bull;&nbsp;Obvykle méně než 5 minut|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|
|**Izolovaná databáze nebo elastický fond úrovně Premium nebo Pro důležité obchodní informace**|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|

> [!NOTE]
> Pro Standard (S2-S12) a Pro obecné účely databáze je navíc latence pro přesun databáze do elastického fondu nebo mezi elastickými fondy úměrná velikosti databáze, pokud databáze používá úložiště[PFS](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)(Premium File Share).
>
> Pokud chcete zjistit, jestli databáze používá úložiště PFS, spusťte v kontextu databáze následující dotaz. Pokud je `PremiumFileStorage`hodnota ve sloupci AccountType, databáze používá úložiště PFS.
 
```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> Informace o monitorování probíhajících operací najdete v tématech: [Správa operací pomocí příkazu SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list), [Správa operací pomocí](/cli/azure/sql/db/op)rozhraní příkazového řádku, [monitorování operací pomocí T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a těchto dvou příkazů PowerShellu: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) a [stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Rušení změn

Operaci změny měřítka úrovně služby nebo operace přeškálování výpočetních prostředků je možné zrušit.

#### <a name="azure-portal"></a>portál Azure

V okně Přehled databáze přejděte na **oznámení** a klikněte na dlaždici, která indikuje, že se jedná o probíhající operaci:

![Probíhající operace](media/sql-database-single-database-scale/ongoing-operations.png)

Potom klikněte na tlačítko s popiskem **zrušit tuto operaci**.

![Zrušit probíhající operaci](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

Z příkazového řádku PowerShellu nastavte `$resourceGroupName`, `$serverName`a `$databaseName`a pak spusťte následující příkaz:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Další aspekty

- Pokud provádíte upgrade na vyšší úroveň služby nebo výpočetní velikost, nezvýší se maximální velikost databáze, pokud explicitně neurčíte větší velikost (MaxSize).
- Aby bylo možné downgradovat databázi, musí být využité místo v databázi menší než maximální povolená velikost cílové úrovně služby a výpočetní velikosti.
- Při přechodu z úrovně **Premium** na úroveň **Standard** se platí dodatečné náklady na úložiště, pokud je maximální velikost databáze podporována v cílové výpočetní velikosti a (2) maximální velikost překračuje zahrnutou velikost úložiště cílové výpočetní velikosti. Pokud je například databáze P1 s maximální velikostí 500 GB zmenšován do S3, platí dodatečné náklady na úložiště, protože S3 podporuje maximální velikost 1 TB a velikost zahrnutého úložiště je pouze 250 GB. Navíc je velikost dodatečného úložiště 500 GB – 250 GB = 250 GB. Ceny dodatečného úložiště najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/). Pokud je skutečné množství využitého místa menší než zahrnuté množství úložiště, je možné tyto dodatečné náklady vyvarovat snížením maximální velikosti databáze na zahrnutou částku.
- Při upgradu databáze s povolenou [geografickou replikací](sql-database-geo-replication-portal.md) Upgradujte své sekundární databáze na požadovanou úroveň služby a výpočetní velikost před upgradem primární databáze (Obecné pokyny pro nejlepší výkon). Při upgradu na jinou edici je nutné nejprve upgradovat sekundární databázi.
- Když se downgrade databáze s povolenou [geografickou replikací](sql-database-geo-replication-portal.md) , downgrade primárních databází na požadovanou úroveň služby a výpočetní velikost před přechodem na sekundární databázi (Obecné pokyny pro nejlepší výkon). Při downgradu na jinou edici je nutné, aby byla primární databáze nejprve downgradovaná.
- Nabídky služeb pro obnovení se u různých úrovní služby liší. Pokud přecházíte na úroveň **Basic** , je k dispozici nižší doba uchovávání záloh. Viz [zálohy Azure SQL Database](sql-database-automated-backups.md).
- Nové vlastnosti databáze se nepoužijí, dokud nebudou změny dokončeny.

## <a name="billing"></a>Fakturace 

Fakturuje se vám každá hodina existence databáze na nejvyšší úrovni služby + výpočetní velikost, která během této hodiny platila, bez ohledu na využití nebo na to, jestli je databáze aktivní kratší dobu než hodinu. Pokud třeba vytvoříte izolovanou databázi a za 5 minut ji odstraníte, bude se vám účtovat poplatek za jednu hodinu databáze.

## <a name="change-storage-size"></a>Změna velikosti úložiště

### <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

- Úložiště se dá zřídit až do limitu maximální velikosti úložiště dat, a to s využitím 1 GB přírůstků. Minimální konfigurovatelné úložiště dat je 1 GB. Pro jednotlivé [databáze](sql-database-vcore-resource-limits-single-databases.md) a [elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md) pro omezení maximální velikosti úložiště dat v každém cíli služby viz stránky dokumentace k omezením prostředků.
- Úložiště dat pro izolovanou databázi je možné zřídit zvýšením nebo snížením jeho maximální velikosti pomocí [Azure Portal](https://portal.azure.com), [jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShellu](/powershell/module/az.sql/set-azsqldatabase), rozhraní příkazového [řádku Azure](/cli/azure/sql/db#az-sql-db-update)nebo [REST API](https://docs.microsoft.com/rest/api/sql/databases/update). Pokud je hodnota maximální velikosti zadána v bajtech, musí být násobkem 1 GB (1073741824 bajtů).
- Množství dat, které lze uložit v datových souborech databáze, je omezeno nakonfigurovanou maximální velikostí úložiště dat. Kromě tohoto úložiště SQL Database automaticky přiděluje 30% větší úložiště, které se má použít pro transakční protokol.
- SQL Database pro `tempdb` databázi automaticky přiděluje 32 GB za Vcore. `tempdb`se nachází v místním úložišti SSD ve všech úrovních služby.
- Cena za úložiště pro izolovanou databázi nebo elastický fond je součtem hodnot úložiště dat a objemů transakčního protokolu vynásobené jednotkou ceny za službu úložiště ve vrstvě služeb. Cena za `tempdb` je zahrnutá v ceně. Podrobnosti o cenách za úložiště najdete v článku [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

- Cena DTU pro jednu databázi zahrnuje určité množství úložiště bez dalších poplatků. Dodatečné úložiště nad rámec zahrnuté částky se dá zřídit za dodatečné náklady až do limitu maximální velikosti v přírůstcích po 250 GB až do 1 TB a potom v přírůstcích po 256 GB po 1 TB. Zahrnuté množství úložišť a omezení maximální velikosti najdete v tématu izolovaná [databáze: velikosti úložiště a výpočetní velikosti](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Dodatečné úložiště pro izolovanou databázi lze zřídit zvýšením jeho maximální velikosti pomocí Azure Portal, [jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShellu](/powershell/module/az.sql/set-azsqldatabase), rozhraní příkazového [řádku Azure](/cli/azure/sql/db#az-sql-db-update)nebo [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- Cena za dodatečné úložiště pro izolovanou databázi je dodatečná hodnota úložiště vynásobená dodatečnou jednotkou ceny za službu Storage úrovně služby. Podrobnosti o ceně dodatečného úložiště najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](sql-database-file-space-management.md).

### <a name="geo-replicated-database"></a>Geograficky replikovaná databáze

Chcete-li změnit velikost databáze replikované sekundární databáze, změňte velikost primární databáze. Tato změna se pak replikuje a implementuje i v sekundární databázi. 

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Omezení P11 a P15, pokud je maximální velikost větší než 1 TB

Ve všech oblastech je aktuálně k dispozici více než 1 TB úložiště na úrovni Premium s výjimkou: Čína – východ, Čína – sever, Německo – střed, Německo – severovýchod, Středozápadní USA, US DoD oblasti a státní správy USA – střed. V těchto oblastech je maximální velikost úložiště na úrovni Premium omezená na 1 TB. Následující hlediska a omezení se vztahují na databáze P11 a P15 s maximální velikostí větší než 1 TB:

- Pokud byla maximální velikost databáze P11 nebo P15 nastavena na hodnotu větší než 1 TB, pak ji lze obnovit nebo zkopírovat pouze do databáze P11 nebo P15.  V důsledku toho může být databáze znovu škálovaná na jinou výpočetní velikost, a to za předpokladu, že množství místa přidělené v době operace změny škálování nepřekračuje omezení maximální velikosti nové výpočetní velikosti.
- Scénáře aktivní geografické replikace:
  - Nastavení vztahu geografické replikace: Pokud je primární databáze P11 nebo P15, sekundární (y) musí být také P11 nebo P15; nižší výpočetní velikost je odmítnuta jako sekundární, protože nepodporují více než 1 TB.
  - Upgrade primární databáze v relaci geografické replikace: Změna maximální velikosti na více než 1 TB v primární databázi spustí stejnou změnu v sekundární databázi. Aby se změny na primárním počítači projevily, musí být oba upgrady úspěšné. Omezení oblastí pro možnost použít více než 1 TB. Pokud je sekundární v oblasti, která nepodporuje více než 1 TB, primární verze se neupgraduje.
- Používání služby Import/export pro načítání databází P11/P15 s více než 1 TB se nepodporuje. K [importu](sql-database-import.md) a [exportu](sql-database-export.md) dat použijte SqlPackage. exe.

## <a name="next-steps"></a>Další kroky

Celkové omezení prostředků najdete v tématu [SQL Database omezení prostředků na základě Vcore – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [SQL Database omezení prostředků na základě DTU – elastické fondy](sql-database-dtu-resource-limits-single-databases.md).
