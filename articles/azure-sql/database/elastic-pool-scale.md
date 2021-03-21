---
title: Škálování prostředků elastického fondu
description: Tato stránka popisuje škálování prostředků pro elastické fondy v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 947d842860452425f8b30fbdaf9558c2a94a89a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92781205"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Škálování prostředků elastického fondu v Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek popisuje, jak škálovat výpočetní prostředky a prostředky úložiště dostupné pro elastické fondy a databáze ve fondu v Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Změna výpočetních prostředků (virtuální jádra nebo DTU)

Po počátečním výběru počtu virtuální jádra nebo eDTU můžete dynamicky škálovat elastický fond na základě aktuálního prostředí pomocí [Azure Portal](elastic-pool-manage.md#azure-portal), [PowerShellu](/powershell/module/az.sql/Get-AzSqlElasticPool), rozhraní příkazového [řádku Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)nebo [REST API](/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Dopad změny úrovně služby nebo změna velikosti výpočetní velikosti

Změna úrovně služby nebo výpočetní velikosti elastického fondu vypadá podobně jako u samostatných databází a hlavně zahrnuje službu, která provádí následující kroky:

1. Vytvořit novou výpočetní instanci pro elastický fond  

    Vytvoří se nová instance COMPUTE pro elastický fond s požadovanou úrovní služeb a výpočetní velikostí. U některých kombinací úrovně služeb a výpočtů velikosti se musí replika každé databáze vytvořit v nové výpočetní instanci, která zahrnuje kopírování dat a může velmi ovlivnit celkovou latenci. Bez ohledu na to, že databáze zůstávají v průběhu tohoto kroku online, připojení budou dál směrována na databáze v původní výpočetní instanci.

2. Přepnout směrování připojení na novou výpočetní instanci

    Existující připojení k databázím v původní výpočetní instanci jsou vyřazena. Všechna nová připojení jsou navázána na databáze v nové instanci Compute. V případě některých kombinací úrovně služby a velikosti výpočetních souborů jsou soubory databáze odpojeny a připojeny během přepínače.  Přepínač může mít za následek krátké přerušení služby, když nejsou databáze k dispozici, obvykle po dobu kratší než 30 sekund a často jenom za pár sekund. Pokud při vyřazení připojení dojde k nespuštěným transakcím, může trvat delší dobu trvání tohoto kroku, aby bylo možné obnovit přerušené transakce. [Urychlené obnovení databáze](../accelerated-database-recovery.md) může snížit dopad přerušení dlouho probíhajících transakcí.

> [!IMPORTANT]
> Během žádného kroku pracovního postupu nebudou ztracena žádná data.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latence změny úrovně služby nebo změna velikosti výpočetní velikosti

Odhadovaná latence změny úrovně služby, škálování výpočetní velikosti izolované databáze nebo elastického fondu, přesun databáze do nebo z elastického fondu nebo přesun databáze mezi elastickými fondy je parametrizovaný takto:

|Úroveň služby|Jednoduchá databáze úrovně Basic,</br>Standard (S0-S1)|Elastický fond úrovně Basic,</br>Standard (S2-S12), </br>Pro obecné účely izolovanou databázi nebo elastický fond|Izolovaná databáze nebo elastický fond úrovně Premium nebo Pro důležité obchodní informace|Hyperškálování
|:---|:---|:---|:---|:---|
|**Basic Single Database </br> Standard (S0-S1)**|&bull;&nbsp;Konstantní latence v čase nezávisle na využitém prostoru</br>&bull;&nbsp;Obvykle méně než 5 minut|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|
|**Základní elastický fond, </br> Standard (S2-S12), pro obecné účely izolovanou </br> databázi nebo elastický fond**|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;U izolovaných databází konstantní časová prodleva nezávislá na využitém prostoru</br>&bull;&nbsp;Obvykle méně než 5 minut pro izolované databáze</br>&bull;&nbsp;Pro elastické fondy úměrné počtu databází|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|
|**Izolovaná databáze nebo elastický fond úrovně Premium nebo Pro důležité obchodní informace**|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|&bull;&nbsp;Latence úměrná k místu databáze použitému v důsledku kopírování dat</br>&bull;&nbsp;Obvykle se za GB využitého místa méně než 1 minuta.|
|**Hyperškálování**|N/A|N/A|N/A|&bull;&nbsp;Konstantní latence v čase nezávisle na využitém prostoru</br>&bull;&nbsp;Obvykle méně než 2 minuty|

> [!NOTE]
>
> - V případě změny úrovně služby nebo změně měřítka výpočetních prostředků pro elastický fond je vhodné použít pro výpočet odhadu místo využité ve všech databázích ve fondu.
> - V případě přesunu databáze do nebo z elastického fondu stačí pouze místo, které databáze používá, vliv na latenci, nikoli na místo využité elastickým fondem.
> - U elastických fondů Standard a Pro obecné účely je latence přesunu databáze do nebo z elastického fondu nebo mezi elastickými fondy úměrná velikosti databáze, pokud elastický fond používá úložiště[PFS](../../storage/files/storage-files-introduction.md)(Premium File Share). Pokud chcete zjistit, jestli fond používá úložiště PFS, spusťte následující dotaz v kontextu jakékoli databáze ve fondu. Pokud je hodnota ve sloupci AccountType `PremiumFileStorage` nebo `PremiumFileStorage-ZRS` , fond používá úložiště PFS.

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

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Další předpoklady při změně úrovně služby nebo změna velikosti výpočetní velikosti

- V případě, že možnost virtuální jádra nebo eDTU pro elastický fond, musí být prostor využitý fondem menší než maximální povolená velikost cílové úrovně služby a fondu eDTU.
- Při změně měřítka eDTU pro elastický fond platí dodatečné náklady na úložiště, pokud (1) maximální velikost úložiště fondu je podporována cílovým fondem a (2) maximální velikost úložiště překračuje zahrnutý objem úložiště cílového fondu. Pokud je například standardní fond 100 eDTU s maximální velikostí 100 GB zmenšován do fondu 50 eDTU úrovně Standard, budou platit dodatečné náklady na úložiště, protože cílový fond podporuje maximální velikost 100 GB a zahrnuté úložiště je pouze 50 GB. Navíc je velikost dodatečného úložiště 100 GB – 50 GB = 50 GB. Ceny dodatečného úložiště najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/). Pokud je skutečné množství využitého místa menší než zahrnuté množství úložiště, je možné tyto dodatečné náklady vyvarovat snížením maximální velikosti databáze na zahrnutou částku.

### <a name="billing-during-rescaling"></a>Fakturace během přeškálování

Fakturuje se vám každá hodina existence databáze na nejvyšší úrovni služby + výpočetní velikost, která během této hodiny platila, bez ohledu na využití nebo na to, jestli je databáze aktivní kratší dobu než hodinu. Pokud třeba vytvoříte izolovanou databázi a za 5 minut ji odstraníte, bude se vám účtovat poplatek za jednu hodinu databáze.

## <a name="change-elastic-pool-storage-size"></a>Změna velikosti úložiště elastického fondu

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](file-space-manage.md).

### <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

- Úložiště je možné zřídit až do maximální velikosti:

  - Pro úložiště v úrovních služby Standard nebo pro obecné účely zvyšte nebo zmenšete velikost v přírůstcích po dobu 10 GB.
  - Pro úložiště v úrovních Premium nebo pro důležité obchodní služby můžete zvýšit nebo snížit velikost v 250 až GB.
- Úložiště pro elastický fond se dá zřídit zvýšením nebo snížením jeho maximální velikosti.
- Cena za úložiště pro elastický fond je množství úložiště vynásobené jednotkou cena za jednotku úložiště ve vrstvě služeb. Podrobnosti o ceně dodatečného úložiště najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

- Cena eDTU pro elastický fond zahrnuje určité množství úložiště bez dalších poplatků. Dodatečné úložiště nad rámec zahrnuté částky se dá zřídit za dodatečné náklady až do limitu maximální velikosti v přírůstcích po 250 GB až do 1 TB a potom v přírůstcích po 256 GB po 1 TB. Zahrnuté množství úložišť a omezení maximální velikosti najdete v tématu [elastický fond: velikosti úložiště a velikosti výpočtů](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Dodatečné úložiště pro elastický fond se dá zřídit zvýšením jeho maximální velikosti pomocí [Azure Portal](elastic-pool-manage.md#azure-portal), [PowerShellu](/powershell/module/az.sql/Get-AzSqlElasticPool), rozhraní příkazového [řádku Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)nebo [REST API](/rest/api/sql/elasticpools/update).
- Cena za dodatečné úložiště pro elastický fond je množství dodatečného úložiště vynásobené dodatečnou jednotkou ceny za službu Storage úrovně služby. Podrobnosti o ceně dodatečného úložiště najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](file-space-manage.md).

## <a name="next-steps"></a>Další kroky

Celkové omezení prostředků najdete v tématu [SQL Database omezení prostředků na základě Vcore – elastické fondy](resource-limits-vcore-elastic-pools.md) a [SQL Database omezení prostředků na základě DTU – elastické fondy](resource-limits-dtu-elastic-pools.md).