---
title: Škálování prostředků jednoúčelové databáze
description: Tento článek popisuje, jak škálovat výpočetní prostředky a prostředky úložiště dostupné pro jednu databázi v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 02/22/2021
ms.openlocfilehash: 5852899175f9cc9f2725b875c6e1ce9fd682768d
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625259"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Škálování prostředků jednoúčelové databáze ve službě Azure SQL Database

Tento článek popisuje, jak škálovat výpočetní prostředky a prostředky úložiště dostupné pro Azure SQL Database v zřízené výpočetní úrovni. [Výpočetní úroveň bez serveru](serverless-tier-overview.md) navíc poskytuje výpočetní automatické škálování a účtuje se za sekundu za použití výpočtů.

Po počátečním výběru počtu virtuální jádra nebo DTU můžete dynamicky škálovat jednu databázi na základě aktuálního prostředí pomocí [Azure Portal](single-database-manage.md#the-azure-portal), [jazyka Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [PowerShellu](/powershell/module/az.sql/set-azsqldatabase), rozhraní příkazového [řádku Azure](/cli/azure/sql/db#az-sql-db-update)nebo [REST API](/rest/api/sql/databases/update).

Následující video ukazuje dynamicky se měnící úroveň služby a výpočetní velikost, aby se zvýšila dostupnost DTU pro izolovanou databázi.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](file-space-manage.md).

## <a name="impact"></a>Dopad

Změna úrovně služby nebo výpočetní velikosti hlavně zahrnuje službu, která provádí následující kroky:

1. Vytvořte novou instanci služby COMPUTE pro databázi. 

    Vytvoří se nová instance COMPUTE s požadovanou úrovní služeb a výpočetní velikostí. V případě některých kombinací úrovně služeb a výpočtů velikosti se musí replika databáze vytvořit v nové výpočetní instanci, která zahrnuje kopírování dat a může silně ovlivnit celkovou latenci. Bez ohledu na to, že databáze zůstane v průběhu tohoto kroku online, připojení budou dál směrována do databáze v původní výpočetní instanci.

2. Přepněte směrování připojení k nové výpočetní instanci.

    Existující připojení k databázi v původní výpočetní instanci jsou vyhozena. Všechna nová připojení jsou navázána na databázi v nové instanci Compute. V případě některých kombinací úrovně služby a velikosti výpočetních souborů jsou soubory databáze odpojeny a připojeny během přepínače.  Přepínač může mít za následek krátké přerušení služby, když databáze není k dispozici, obvykle po dobu kratší než 30 sekund a často jenom pár sekund. Pokud existují dlouhotrvající transakce běžící po vyřazení připojení, může trvat delší dobu trvání tohoto kroku, aby bylo možné obnovit přerušené transakce. [Urychlené obnovení databáze](../accelerated-database-recovery.md) může snížit dopad přerušení dlouho probíhajících transakcí.

> [!IMPORTANT]
> Během žádného kroku pracovního postupu nebudou ztracena žádná data. Ujistěte se, že jste implementovali nějakou [logiku opakování](troubleshoot-common-connectivity-issues.md) v aplikacích a součástech, které při změně úrovně služby používají Azure SQL Database.

## <a name="latency"></a>Latence

Odhadovaná latence změny úrovně služby, škálování výpočetní velikosti izolované databáze nebo elastického fondu, přesun databáze do nebo z elastického fondu nebo přesun databáze mezi elastickými fondy je parametrizovaný takto:

|Úroveň služby|Jednoduchá databáze úrovně Basic,</br>Standard (S0-S1)|Elastický fond úrovně Basic,</br>Standard (S2-S12), </br>Pro obecné účely izolovanou databázi nebo elastický fond|Izolovaná databáze nebo elastický fond úrovně Premium nebo Pro důležité obchodní informace|Hyperškálování
|:---|:---|:---|:---|:---|
|**Basic Single Database </br> Standard (S0-S1)**|&bull;&nbsp;Konstantní latence v čase nezávisle na využitém prostoru</br>&bull;&nbsp;Obvykle méně než 5 minut|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|
|**Základní elastický fond, </br> Standard (S2-S12), pro obecné účely izolovanou </br> databázi nebo elastický fond**|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;U izolovaných databází konstantní časová prodleva nezávislá na využitém prostoru</br>&bull;&nbsp;Obvykle méně než 5 minut pro izolované databáze</br>&bull;&nbsp;Pro elastické fondy úměrné počtu databází|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|
|**Izolovaná databáze nebo elastický fond úrovně Premium nebo Pro důležité obchodní informace**|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|
|**Hyperškálování**|N/A|N/A|N/A|&bull;&nbsp;Konstantní latence v čase nezávisle na využitém prostoru</br>&bull;&nbsp;Obvykle méně než 2 minuty|

> [!NOTE]
> Pro Standard (S2-S12) a Pro obecné účely databáze je navíc latence pro přesun databáze do elastického fondu nebo mezi elastickými fondy úměrná velikosti databáze, pokud databáze používá úložiště[PFS](../../storage/files/storage-files-introduction.md)(Premium File Share).
>
> Pokud chcete zjistit, jestli databáze používá úložiště PFS, spusťte v kontextu databáze následující dotaz. Pokud je hodnota ve sloupci AccountType `PremiumFileStorage` nebo `PremiumFileStorage-ZRS` , databáze používá úložiště PFS.

[!NOTE]
 Redundantní vlastnost zóny zůstane při škálování od Pro důležité obchodní informace do Pro obecné účely úrovně stejná jako výchozí. Latence pro tento downgrade v případě, že je povolená redundance zóny a latence přechodu na redundanci zóny pro Pro obecné účely úroveň bude úměrná velikosti databáze.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> Informace o monitorování probíhajících operací najdete v tématech: [Správa operací pomocí příkazu SQL REST API](/rest/api/sql/operations/list), [Správa operací pomocí](/cli/azure/sql/db/op)rozhraní příkazového řádku, [monitorování operací pomocí T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a těchto dvou příkazů PowerShellu: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) a [stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Rušení změn

Operaci změny měřítka úrovně služby nebo operace přeškálování výpočetních prostředků je možné zrušit.

### <a name="the-azure-portal"></a>Azure Portal

V okně Přehled databáze přejděte na **oznámení** a klikněte na dlaždici, která indikuje, že se jedná o probíhající operaci:

![Probíhající operace](./media/single-database-scale/ongoing-operations.png)

Potom klikněte na tlačítko s popiskem **zrušit tuto operaci**.

![Zrušit probíhající operaci](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

Z příkazového řádku PowerShellu nastavte `$resourceGroupName` , `$serverName` a a `$databaseName` pak spusťte následující příkaz:

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
- Při přechodu z úrovně **Premium** na úroveň **Standard** se platí dodatečné náklady na úložiště, pokud je maximální velikost databáze podporována v cílové výpočetní velikosti a (2) maximální velikost překračuje zahrnutou velikost úložiště cílové výpočetní velikosti. Pokud je například databáze P1 s maximální velikostí 500 GB zmenšován do S3, platí dodatečné náklady na úložiště, protože S3 podporuje maximální velikost 1 TB a velikost zahrnutého úložiště je pouze 250 GB. Navíc je velikost dodatečného úložiště 500 GB – 250 GB = 250 GB. Ceny dodatečného úložiště najdete v tématu [Azure SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/). Pokud je skutečné množství využitého místa menší než zahrnuté množství úložiště, je možné tyto dodatečné náklady vyvarovat snížením maximální velikosti databáze na zahrnutou částku.
- Při upgradu databáze s povolenou [geografickou replikací](active-geo-replication-configure-portal.md) Upgradujte své sekundární databáze na požadovanou úroveň služby a výpočetní velikost před upgradem primární databáze (Obecné pokyny pro nejlepší výkon). Při upgradu na jinou edici je nutné nejprve upgradovat sekundární databázi.
- Když se downgrade databáze s povolenou [geografickou replikací](active-geo-replication-configure-portal.md) , downgrade primárních databází na požadovanou úroveň služby a výpočetní velikost před přechodem na sekundární databázi (Obecné pokyny pro nejlepší výkon). Při downgradu na jinou edici je požadavek, aby byla primární databáze nejprve downgradovaná.
- Nabídky služeb pro obnovení se u různých úrovní služby liší. Pokud se chystáte přejít na úroveň **Basic** , je k dispozici nižší doba uchovávání záloh. Viz [zálohy Azure SQL Database](automated-backups-overview.md).
- Nové vlastnosti databáze nejsou aplikovány, dokud nebudou změny dokončeny.
- Když je pro škálování databáze potřeba kopírování dat (viz [latence](#latency)) při změně úrovně služby, vysoké využití prostředků souběžně s operací škálování může způsobit delší dobu škálování. S [urychleným obnovením databáze (ADR)](/sql/relational-databases/accelerated-database-recovery-concepts)není vrácení dlouhotrvajících transakcí v nevýznamném zdroji zpoždění, ale vysoké využití prostředků může mít méně výpočetní prostředky, úložiště a šířku pásma sítě pro škálování, zejména pro menší výpočetní velikosti.

## <a name="billing"></a>Fakturace

Fakturuje se vám každá hodina existence databáze na nejvyšší úrovni služby + výpočetní velikost, která během této hodiny platila, bez ohledu na využití nebo na to, jestli je databáze aktivní kratší dobu než hodinu. Pokud třeba vytvoříte izolovanou databázi a za 5 minut ji odstraníte, bude se vám účtovat poplatek za jednu hodinu databáze.

## <a name="change-storage-size"></a>Změna velikosti úložiště

### <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

- Úložiště se dá zřídit až do limitu maximální velikosti úložiště dat s použitím přírůstku o velikosti 1 GB. Minimální konfigurovatelné úložiště dat je 1 GB. Pro jednotlivé [databáze](resource-limits-vcore-single-databases.md) a [elastické fondy](resource-limits-vcore-elastic-pools.md) pro omezení maximální velikosti úložiště dat v každém cíli služby viz stránky dokumentace k omezením prostředků.
- Úložiště dat pro izolovanou databázi je možné zřídit zvýšením nebo snížením jeho maximální velikosti pomocí [Azure Portal](https://portal.azure.com), [jazyka Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [PowerShellu](/powershell/module/az.sql/set-azsqldatabase), rozhraní příkazového [řádku Azure](/cli/azure/sql/db#az-sql-db-update)nebo [REST API](/rest/api/sql/databases/update). Pokud je hodnota maximální velikosti zadána v bajtech, musí být násobkem 1 GB (1073741824 bajtů).
- Množství dat, které lze uložit v datových souborech databáze, je omezeno nakonfigurovanou maximální velikostí úložiště dat. Kromě tohoto úložiště Azure SQL Database automaticky přiděluje 30% větší úložiště, které se má použít pro transakční protokol.
- Azure SQL Database pro databázi automaticky přiděluje 32 GB za vCore `tempdb` . `tempdb` se nachází v místním úložišti SSD ve všech úrovních služby.
- Cena za úložiště pro izolovanou databázi nebo elastický fond je součtem hodnot úložiště dat a objemů transakčního protokolu vynásobené jednotkou ceny za službu úložiště ve vrstvě služeb. `tempdb`Cena za je zahrnutá v ceně. Podrobnosti o cenách za úložiště najdete v článku [Azure SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

- Cena DTU pro jednu databázi zahrnuje určité množství úložiště bez dalších poplatků. Dodatečné úložiště nad rámec zahrnuté částky se dá zřídit za dodatečné náklady až do limitu maximální velikosti v přírůstcích po 250 GB až do 1 TB a potom v přírůstcích po 256 GB po 1 TB. Zahrnuté množství úložišť a omezení maximální velikosti najdete v tématu izolovaná [databáze: velikosti úložiště a výpočetní velikosti](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Dodatečné úložiště pro izolovanou databázi lze zřídit zvýšením jeho maximální velikosti pomocí Azure Portal, [jazyka Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [PowerShellu](/powershell/module/az.sql/set-azsqldatabase), rozhraní příkazového [řádku Azure](/cli/azure/sql/db#az-sql-db-update)nebo [REST API](/rest/api/sql/databases/update).
- Cena za dodatečné úložiště pro izolovanou databázi je dodatečná hodnota úložiště vynásobená dodatečnou jednotkou ceny za službu Storage úrovně služby. Podrobnosti o ceně dodatečného úložiště najdete v tématu [Azure SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](file-space-manage.md).

### <a name="geo-replicated-database"></a>Geograficky replikovaná databáze

Chcete-li změnit velikost databáze replikované sekundární databáze, změňte velikost primární databáze. Tato změna se pak replikuje a implementuje i v sekundární databázi.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Omezení P11 a P15, pokud je maximální velikost větší než 1 TB

Ve všech oblastech je aktuálně k dispozici více než 1 TB úložiště na úrovni Premium s výjimkou: Čína – východ, Čína – sever, Německo – střed a Německo – severovýchod. V těchto oblastech je maximální velikost úložiště na úrovni Premium omezená na 1 TB. Následující hlediska a omezení se vztahují na databáze P11 a P15 s maximální velikostí větší než 1 TB:

- Pokud byla maximální velikost databáze P11 nebo P15 nastavena na hodnotu větší než 1 TB, pak ji lze obnovit nebo zkopírovat pouze do databáze P11 nebo P15.  Následně je možné databázi znovu škálovat na jinou výpočetní velikost, a to za předpokladu, že velikost přiděleného místa v době operace přeškálování nepřekračuje omezení maximální velikosti nové výpočetní velikosti.
- Scénáře aktivní geografické replikace:
  - Nastavení vztahu geografické replikace: Pokud je primární databáze P11 nebo P15, sekundární (y) musí být také P11 nebo P15. Nižší výpočetní velikost je odmítnuta jako sekundární, protože nepodporují více než 1 TB.
  - Upgrade primární databáze v relaci geografické replikace: Změna maximální velikosti na více než 1 TB v primární databázi spustí stejnou změnu v sekundární databázi. Aby se změny na primárním počítači projevily, musí být oba upgrady úspěšné. Omezení oblastí pro možnost použít více než 1 TB. Pokud je sekundární v oblasti, která nepodporuje více než 1 TB, primární upgrade se neupgraduje.
- Používání služby Import/export pro načítání databází P11/P15 s více než 1 TB se nepodporuje. K [importu](database-import.md) a [exportu](database-export.md) dat použijte SqlPackage.exe.

## <a name="next-steps"></a>Další kroky

Celkové omezení prostředků najdete v tématu [Azure SQL Database omezení prostředků na základě Vcore – izolované databáze](resource-limits-vcore-single-databases.md) a [Azure SQL Database omezení prostředků na bázi DTU – jednotlivé databáze](resource-limits-dtu-single-databases.md).
