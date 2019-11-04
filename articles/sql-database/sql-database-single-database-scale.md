---
title: Škálování jednoho databázového prostředku – Azure SQL Database | Microsoft Docs
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
ms.date: 04/26/2019
ms.openlocfilehash: d2571b04f10bbbd3a461e553a56904abb3b46588
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496027"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Škálování jednoho databázového prostředku v Azure SQL Database

Tento článek popisuje, jak škálovat výpočetní prostředky a prostředky úložiště dostupné pro Azure SQL Database v zřízené výpočetní úrovni. [Výpočetní úroveň bez serveru](sql-database-serverless.md) navíc poskytuje výpočetní automatické škálování a platby za sekundu pro využité výpočetní prostředky.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

## <a name="change-compute-size-vcores-or-dtus"></a>Změna velikosti výpočtů (virtuální jádra nebo DTU)

Po počátečním výběru počtu virtuální jádra nebo DTU můžete dynamicky škálovat jednu databázi na základě aktuálního prostředí pomocí [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShellu](/powershell/module/az.sql/set-azsqldatabase), rozhraní příkazového [řádku Azure](/cli/azure/sql/db#az-sql-db-update)nebo [REST API ](https://docs.microsoft.com/rest/api/sql/databases/update).

Následující video ukazuje dynamicky se měnící úroveň služby a výpočetní velikost, aby se zvýšila dostupnost DTU pro izolovanou databázi.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Dopad změny úrovně služby nebo změna velikosti výpočetní velikosti

Změna úrovně služby nebo výpočetní velikosti hlavně zahrnuje službu, která provádí následující kroky:

1. Vytvořit novou výpočetní instanci pro databázi  

    Vytvoří se nová instance COMPUTE s požadovanou úrovní služeb a výpočetní velikostí. V případě některých kombinací úrovně služeb a výpočtů velikosti se musí replika databáze vytvořit v nové výpočetní instanci, která zahrnuje kopírování dat a může silně ovlivnit celkovou latenci. Bez ohledu na to, že databáze zůstane v průběhu tohoto kroku online, připojení budou dál směrována do databáze v původní výpočetní instanci.

2. Přepnout směrování připojení na novou výpočetní instanci

    Existující připojení k databázi v původní výpočetní instanci jsou vyhozena. Všechna nová připojení jsou navázána na databázi v nové instanci Compute. V případě některých kombinací úrovně služby a velikosti výpočetních souborů jsou soubory databáze odpojeny a připojeny během přepínače.  Přepínač může mít za následek krátké přerušení služby, když databáze není k dispozici, obvykle po dobu kratší než 30 sekund a často jenom pár sekund. Pokud při vyřazení připojení dojde k nespuštěným transakcím, může trvat delší dobu trvání tohoto kroku, aby bylo možné obnovit přerušené transakce. [Urychlené obnovení databáze](sql-database-accelerated-database-recovery.md) může snížit dopad přerušení dlouho probíhajících transakcí.

> [!IMPORTANT]
> Během žádného kroku pracovního postupu nebudou ztracena žádná data. Ujistěte se, že jste implementovali nějakou [logiku opakování](sql-database-connectivity-issues.md) v aplikacích a součástech, které při změně úrovně služby používají Azure SQL Database.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latence změny úrovně služby nebo změna velikosti výpočetní velikosti

Odhadovaná latence změny úrovně služby nebo změna velikosti výpočetní velikosti jedné databáze nebo elastického fondu je parametrizovaná takto:

|Úroveň služby|Jednoduchá databáze úrovně Basic,</br>Standard (S0-S1)|Elastický fond úrovně Basic,</br>Standard (S2-S12), </br>Hyperškálovatelný </br>Pro obecné účely izolovanou databázi nebo elastický fond|Izolovaná databáze nebo elastický fond úrovně Premium nebo Pro důležité obchodní informace|
|:---|:---|:---|:---|
|**Základní samostatná databáze</br> Standard (S0-S1)**|&bull; &nbsp;konstantní časovou latenci nezávisle na využitém prostoru</br>&bull; &nbsp;obvykle, méně než 5 minut|&bull; &nbsp;latence úměrná prostoru databáze použitému v důsledku kopírování dat</br>&bull; &nbsp;obvykle, méně než 1 minuta za GB využitého místa|&bull; &nbsp;latence úměrná prostoru databáze použitému v důsledku kopírování dat</br>&bull; &nbsp;obvykle, méně než 1 minuta za GB využitého místa|
|**Základní elastický fond, </br>Standard (S2-S12), </br>AutoScale, </br>Pro obecné účely jedna databáze nebo elastický fond**|&bull; &nbsp;latence úměrná prostoru databáze použitému v důsledku kopírování dat</br>&bull; &nbsp;obvykle, méně než 1 minuta za GB využitého místa|&bull; &nbsp;konstantní časovou latenci nezávisle na využitém prostoru</br>&bull; &nbsp;obvykle, méně než 5 minut|&bull; &nbsp;latence úměrná prostoru databáze použitému v důsledku kopírování dat</br>&bull; &nbsp;obvykle, méně než 1 minuta za GB využitého místa|
|**Izolovaná databáze nebo elastický fond úrovně Premium nebo Pro důležité obchodní informace**|&bull; &nbsp;latence úměrná prostoru databáze použitému v důsledku kopírování dat</br>&bull; &nbsp;obvykle, méně než 1 minuta za GB využitého místa|&bull; &nbsp;latence úměrná prostoru databáze použitému v důsledku kopírování dat</br>&bull; &nbsp;obvykle, méně než 1 minuta za GB využitého místa|&bull; &nbsp;latence úměrná prostoru databáze použitému v důsledku kopírování dat</br>&bull; &nbsp;obvykle, méně než 1 minuta za GB využitého místa|

> [!TIP]
> Informace o monitorování probíhajících operací najdete v tématu [: Správa operací pomocí příkazu SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list), [Správa operací pomocí](/cli/azure/sql/db/op)rozhraní příkazového řádku a [monitorování operací pomocí T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a těchto dvou příkazů PowerShellu: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) a [ Stop – AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>Rušení změn úrovně služeb nebo operací přeškálování výpočetních prostředků

Operaci změny měřítka úrovně služby nebo operace přeškálování výpočetních prostředků je možné zrušit.

#### <a name="azure-portal"></a>Portál Azure

V okně Přehled databáze přejděte na **oznámení** a klikněte na dlaždici, která indikuje, že se jedná o probíhající operaci:

![Probíhající operace](media/sql-database-single-database-scale/ongoing-operations.png)

Potom klikněte na tlačítko s popiskem **zrušit tuto operaci**.

![Zrušit probíhající operaci](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

Z příkazového řádku PowerShellu nastavte `$ResourceGroupName`, `$ServerName`a `$DatabaseName`a pak spusťte následující příkaz:

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Další předpoklady při změně úrovně služby nebo změna velikosti výpočetní velikosti

- Pokud provádíte upgrade na vyšší úroveň služby nebo výpočetní velikost, nezvýší se maximální velikost databáze, pokud explicitně neurčíte větší velikost (MaxSize).
- Aby bylo možné downgradovat databázi, musí být využité místo v databázi menší než maximální povolená velikost cílové úrovně služby a výpočetní velikosti.
- Při přechodu z úrovně **Premium** na úroveň **Standard** se platí dodatečné náklady na úložiště, pokud je maximální velikost databáze podporována v cílové výpočetní velikosti a (2) maximální velikost překračuje zahrnutou velikost úložiště cílové výpočetní velikosti. Pokud je například databáze P1 s maximální velikostí 500 GB zmenšován do S3, platí dodatečné náklady na úložiště, protože S3 podporuje maximální velikost 1 TB a velikost zahrnutého úložiště je pouze 250 GB. Navíc je velikost dodatečného úložiště 500 GB – 250 GB = 250 GB. Ceny dodatečného úložiště najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/). Pokud je skutečné množství využitého místa menší než zahrnuté množství úložiště, je možné tyto dodatečné náklady vyvarovat snížením maximální velikosti databáze na zahrnutou částku.
- Při upgradu databáze s povolenou [geografickou replikací](sql-database-geo-replication-portal.md) Upgradujte své sekundární databáze na požadovanou úroveň služby a výpočetní velikost před upgradem primární databáze (Obecné pokyny pro nejlepší výkon). Při upgradu na jinou je třeba nejprve upgradovat sekundární databázi.
- Když se downgrade databáze s povolenou [geografickou replikací](sql-database-geo-replication-portal.md) , downgrade primárních databází na požadovanou úroveň služby a výpočetní velikost před přechodem na sekundární databázi (Obecné pokyny pro nejlepší výkon). Když se downgrade na jinou edici, je nutné nejprve downgradovat primární databázi.
- Nabídky služeb pro obnovení se u různých úrovní služby liší. Pokud přecházíte na úroveň **Basic** , je k dispozici nižší doba uchovávání záloh. Viz [zálohy Azure SQL Database](sql-database-automated-backups.md).
- Nové vlastnosti databáze se nepoužijí, dokud nebudou změny dokončeny.

### <a name="billing-during-compute-rescaling"></a>Fakturace během výpočetního škálování

Fakturuje se vám každá hodina existence databáze na nejvyšší úrovni služby + výpočetní velikost, která během této hodiny platila, bez ohledu na využití nebo na to, jestli je databáze aktivní kratší dobu než hodinu. Pokud třeba vytvoříte izolovanou databázi a za 5 minut ji odstraníte, bude se vám účtovat poplatek za jednu hodinu databáze.

## <a name="change-storage-size"></a>Změna velikosti úložiště

### <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

- Úložiště se dá zřídit až do limitu maximální velikosti s využitím přírůstcích po 1 GB. Minimální konfigurovatelné úložiště dat je 5 GB.
- Úložiště pro izolovanou databázi je možné zřídit zvýšením nebo snížením jeho maximální velikosti pomocí [Azure Portal](https://portal.azure.com), [jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShellu](/powershell/module/az.sql/set-azsqldatabase), rozhraní příkazového [řádku Azure](/cli/azure/sql/db#az-sql-db-update)nebo [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- SQL Database automaticky přiděluje 30% dodatečného úložiště pro soubory protokolu a 32 GB na vCore pro databázi TempDB, ale ne až 384GB. Databáze TempDB je umístěná na připojené SSD ve všech úrovních služby.
- Cena za úložiště pro izolovanou databázi je součtem částek úložiště dat a protokolů úložiště vynásobené jednotkovou cenou za jednotku služby. Cena databáze TempDB je zahrnutá v ceně vCore. Podrobnosti o ceně dodatečného úložiště najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

- Cena DTU pro jednu databázi zahrnuje určité množství úložiště bez dalších poplatků. Dodatečné úložiště nad rámec zahrnuté částky se dá zřídit za dodatečné náklady až do limitu maximální velikosti v přírůstcích po 250 GB až do 1 TB a potom v přírůstcích po 256 GB po 1 TB. Zahrnuté množství úložišť a omezení maximální velikosti najdete v tématu izolovaná [databáze: velikosti úložiště a výpočetní velikosti](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Dodatečné úložiště pro izolovanou databázi lze zřídit zvýšením jeho maximální velikosti pomocí Azure Portal, [jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShellu](/powershell/module/az.sql/set-azsqldatabase), rozhraní příkazového [řádku Azure](/cli/azure/sql/db#az-sql-db-update)nebo [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- Cena za dodatečné úložiště pro izolovanou databázi je dodatečná hodnota úložiště vynásobená dodatečnou jednotkou ceny za službu Storage úrovně služby. Podrobnosti o ceně dodatečného úložiště najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Omezení P11 a P15, pokud je maximální velikost větší než 1 TB

Ve všech oblastech je aktuálně k dispozici více než 1 TB úložiště na úrovni Premium s výjimkou: Čína – východ, Čína – sever, Německo – střed, Německo – severovýchod, Středozápadní USA, US DoD oblasti a státní správy USA – střed. V těchto oblastech je úložiště na úrovni Premium omezeno na 1 TB. Následující hlediska a omezení se vztahují na databáze P11 a P15 s maximální velikostí větší než 1 TB:

- Pokud byla maximální velikost databáze P11 nebo P15 nastavena na hodnotu větší než 1 TB, pak ji lze obnovit nebo zkopírovat pouze do databáze P11 nebo P15.  V důsledku toho může být databáze znovu škálovaná na jinou výpočetní velikost, a to za předpokladu, že množství místa přidělené v době operace změny škálování nepřekračuje omezení maximální velikosti nové výpočetní velikosti.
- Scénáře aktivní geografické replikace:
  - Nastavení vztahu geografické replikace: Pokud je primární databáze P11 nebo P15, sekundární (y) musí být také P11 nebo P15; nižší výpočetní velikost je odmítnuta jako sekundární, protože nepodporují více než 1 TB.
  - Upgrade primární databáze v relaci geografické replikace: Změna maximální velikosti na více než 1 TB v primární databázi spustí stejnou změnu v sekundární databázi. Aby se změny na primárním počítači projevily, musí být oba upgrady úspěšné. Omezení oblastí pro možnost použít více než 1 TB. Pokud je sekundární v oblasti, která nepodporuje více než 1 TB, primární verze se neupgraduje.
- Používání služby Import/export pro načítání databází P11/P15 s více než 1 TB se nepodporuje. K [importu](sql-database-import.md) a [exportu](sql-database-export.md) dat použijte SqlPackage. exe.

## <a name="next-steps"></a>Další kroky

Celkové omezení prostředků najdete v tématu [SQL Database omezení prostředků na základě Vcore – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [SQL Database omezení prostředků na základě DTU – elastické fondy](sql-database-dtu-resource-limits-single-databases.md).
