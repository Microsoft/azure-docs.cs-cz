---
title: Škálování prostředků elastického fondu
description: Tato stránka popisuje škálování prostředků pro elastické fondy v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 3/14/2019
ms.openlocfilehash: daca108cfc8bb2e5b2a068170a4a0244c72c9592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462594"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Škálování prostředků elastického fondu v Azure SQL Database

Tento článek popisuje, jak škálovat výpočetní prostředky a prostředky úložiště, které jsou k dispozici pro elastické fondy a sdružené databáze v Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Změna výpočetních prostředků (virtuální jádra nebo DTU)

Po počátečním výběru počtu virtuálních jader nebo eDTU můžete dynamicky škálovat elastický fond nahoru nebo dolů na základě skutečného prostředí pomocí [portálu Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShellu](/powershell/module/az.sql/Get-AzSqlElasticPool), [rozhraní API Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)nebo rozhraní REST [API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Dopad změny úrovně služby nebo změny měřítka výpočetní velikosti

Změna úrovně služby nebo výpočetní velikoste elastického fondu se řídí podobným vzorem jako u jednotlivých databází a hlavně zahrnuje službu provádějící následující kroky:

1. Vytvoření nové instance výpočetních prostředků pro elastický fond  

    Nová instance výpočetních prostředků pro elastický fond se vytvoří s požadovanou úrovní služby a velikostí výpočetních prostředků. Pro některé kombinace změny velikosti úrovně služby a výpočetní velikosti musí být vytvořena replika každé databáze v nové výpočetní instanci, která zahrnuje kopírování dat a může silně ovlivnit celkovou latenci. Bez ohledu na to databáze zůstávají online během tohoto kroku a připojení i nadále přesměrovány do databází v původní výpočetní instance.

2. Přepnutí směrování připojení na novou výpočetní instanci

    Existující připojení k databázím v původní výpočetní instanci jsou vynechány. Všechna nová připojení jsou vytvořena k databázím v nové výpočetní instanci. Pro některé kombinace změny velikosti úrovně služby a výpočetní velikosti databázové soubory jsou odpojeny a znovu připojeny během přepínače.  Bez ohledu na to může mít přepínač za následek krátké přerušení služby, pokud databáze nejsou obvykle k dispozici po dobu kratší než 30 sekund a často pouze několik sekund. Pokud jsou spuštěny dlouho běžící transakce při přerušení připojení, doba trvání tohoto kroku může trvat déle, aby bylo možné obnovit přerušené transakce. [Zrychlené obnovení databáze](sql-database-accelerated-database-recovery.md) může snížit dopad přerušení dlouho běžící transakce.

> [!IMPORTANT]
> Během žádného kroku pracovního postupu nedojde ke ztrátě dat.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latence změny úrovně služby nebo změny měřítka výpočetní velikosti

Odhadovaná latence pro změnu úrovně služby nebo změnu škálování výpočetní velikosti jedné databáze nebo elastického fondu je parametrizována následujícím způsobem:

|Úroveň služeb|Základní jednotná databáze,</br>Standardní (S0-S1)|Základní elastický bazén,</br>standardní (S2-S12), </br>Hyperscale, </br>Univerzální jednotná databáze nebo elastický fond|Premium nebo business critical single database or elastic pool Premium or Business Critical Single database or Elastenou|
|:---|:---|:---|:---|
|**Základní databáze,</br> standardní (S0-S1)**|&bull;&nbsp;Konstantní časová latence nezávislá na použitém prostoru</br>&bull;&nbsp;Obvykle méně než 5 minut|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|
|**Základní elastický </br>fond, Standard (S2-S12), </br>Hyperscale, </br>Univerzální databáze nebo elastický fond**|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|&bull;&nbsp;Konstantní časová latence nezávislá na použitém prostoru</br>&bull;&nbsp;Obvykle méně než 5 minut|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|
|**Premium nebo business critical single database or elastic pool Premium or Business Critical Single database or Elastenou**|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|

> [!NOTE]
>
> - V případě změny úrovně služby nebo změna měřítka výpočetní pro elastický fond, součt u místa používaného ve všech databázích ve fondu by měl být použit k výpočtu odhadu.
> - V případě přesunutí databáze do/z elastického fondu má vliv pouze místo využívaného databází na latenci, nikoli na místo používané elastickým fondem.
>
> [!TIP]
> Chcete-li sledovat probíhající operace, [přečtěte si](https://docs.microsoft.com/rest/api/sql/operations/list)následující informace: Správa operací pomocí rozhraní SQL REST API , Správa operací pomocí rozhraní [PŘÍKAZOVÉHO PŘÍKAZU](/cli/azure/sql/db/op), [Sledování operací pomocí t-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a těchto dvou příkazů prostředí PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) a [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Další důležité informace při změně úrovně služby nebo změna měřítka výpočetní velikosti

- Při zmenšení virtuálních jader nebo eDTU pro elastický fond, fond využité místo musí být menší než maximální povolená velikost cílové úrovně služby a fondu eDTU.
- Při změně měřítka eDTU pro elastický fond se účtuje další náklady na úložiště, pokud (1) maximální velikost úložiště fondu je podporováncílovým fondem a (2) maximální velikost úložiště překročí zahrnutou velikost úložiště cílového fondu. Pokud je například fond standardu 100 eDTU s maximální velikostí 100 GB zmenšen na fond standardu 50 eDTU, platí dodatečné náklady na úložiště, protože cílový fond podporuje maximální velikost 100 GB a jeho zahrnutá velikost úložiště je pouze 50 GB. Takže další velikost úložiště je 100 GB - 50 GB = 50 GB. Ceny dalšího úložiště najdete v tématu [ceny databáze SQL](https://azure.microsoft.com/pricing/details/sql-database/). Pokud je skutečné množství využitého místa menší než zahrnutá částka úložiště, lze se vyhnout této dodatečné ceně snížením maximální velikosti databáze na zahrnutou částku.

### <a name="billing-during-rescaling"></a>Fakturace během změny měřítka

Poplatky se vám účtují za každou hodinu, kdy existuje databáze s použitím nejvyšší úrovně služby + výpočetní velikosti, která byla použita během této hodiny, bez ohledu na využití nebo na to, zda byla databáze aktivní méně než hodinu. Pokud například vytvoříte jednu databázi a o pět minut později ji odstraníte, bude faktura odrážet poplatek za jednu databázovou hodinu.

## <a name="change-elastic-pool-storage-size"></a>Změna velikosti úložiště elastického fondu

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi, aby bylo možné uvolnit nevyužité místo. Další informace najdete [v tématu Správa místa v souborech v Azure SQL Database](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

- Úložiště lze zřídit až do maximálního limitu velikosti:

  - Pro úložiště ve standardních nebo obecných úrovních služeb zvyšte nebo zmenšete velikost v krocích po 10 GB
  - Pro úložiště v úrovních špičkových nebo důležitých podnikových služeb zvyšte nebo zmenšete velikost v přírůstcích po 250 GB
- Úložiště pro elastický fond lze zřídit zvýšením nebo snížením jeho maximální velikosti.
- Cena úložiště pro elastický fond je částka úložiště vynásobená jednotkovou cenou úložiště úrovně služby. Podrobnosti o ceně dalšího úložiště naleznete v tématu [ceny databáze SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi, aby bylo možné uvolnit nevyužité místo. Další informace najdete [v tématu Správa místa v souborech v Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

- Cena eDTU pro elastický fond zahrnuje určité množství úložného prostoru bez dalších nákladů. Další úložiště nad rámec zahrnuté částky lze zřídit za dodatečné náklady až do maximálního limitu velikosti v přírůstcích po 250 GB až 1 TB a pak v krocích po 256 GB nad 1 TB. Zahrnuté částky úložiště a maximální limity velikosti najdete [v tématu Elastický fond: velikosti úložiště a výpočetní velikosti](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Další úložiště pro elastický fond lze zřídit zvýšením jeho maximální velikosti pomocí [portálu Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)nebo [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- Cena dodatečného úložiště pro elastický fond je částka dodatečného úložiště vynásobená jednotkovou cenou dodatečného úložiště úrovně služby. Podrobnosti o ceně dalšího úložiště naleznete v tématu [ceny databáze SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi, aby bylo možné uvolnit nevyužité místo. Další informace najdete [v tématu Správa místa v souborech v Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Další kroky

Celková omezení prostředků naleznete v [tématu SQL Database virtuální chod prostředků – elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md) a [limity prostředků založených na databázi SQL Database – elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md).
