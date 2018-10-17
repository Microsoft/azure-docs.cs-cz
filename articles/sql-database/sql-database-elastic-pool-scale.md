---
title: Škálování elastického fondu zdrojů – Azure SQL Database | Dokumentace Microsoftu
description: Tato stránka popisuje škálování prostředků pro elastické fondy Azure SQL Database.
services: sql-database
ms.service: sql-database
subservice: elastic-pool
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: b659a7b49eadee59f2eee09320a35a0cf3fab225
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353064"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Škálování elastického fondu prostředků ve službě Azure SQL Database

Tento článek popisuje, jak škálovat výpočetní a úložné prostředky dostupné pro elastické fondy a databáze ve fondu ve službě Azure SQL Database.

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>nákupní model založený na virtuálních jádrech: změnit velikost úložiště elastického fondu

- Úložiště lze zřídit až do limitu maximální velikost:

  - Pro úložiště úrovně Standard zvětšit nebo zmenšit velikost v přírůstcích po 10 GB
  - Pro Premium storage zvětšit nebo zmenšit velikost v přírůstcích po 250 GB
- Zvýšením nebo snížením své maximální velikosti se dá zřídit úložiště pro elastický fond.
- Cena úložiště pro elastický fond je velikost úložiště vynásobí jednotkovou cenu úložiště na úrovni služby. Podrobnosti o cenách dodatečného úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>nákupní model založený na virtuálních jádrech: Změna elastického fondu výpočetních prostředků (virtuálních jader)

Můžete zvýšit nebo snížit velikost výpočetního do elastického fondu podle potřeby pomocí prostředků [webu Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [rozhraní příkazového řádku Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), nebo [ Rozhraní REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/elasticpools_update).

- Když změny měřítka virtuálních jader v elastickém fondu, jsou vynechány stručně připojení k databázi. Toto chování je stejné chování, která nastane, pokud se změny měřítka Dtu pro izolovanou databázi. Podrobnosti o dobu trvání a dopad přerušená připojení pro databázi během mění se škálování operací, najdete v části [změna výpočetních prostředků (Dtu)](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus).
- Doba trvání, chcete-li změnit fond virtuálních jader může záviset na celkové množství prostor úložiště využitý všechny databáze ve fondu. Obecně platí, mění se škálování latence předběhli aktuální fázi 90 minut nebo méně na každých 100 GB. Například pokud používá celkové místo všech databází ve fondu je 200 GB, očekávaná latence pro změny měřítka fondu je 3 hodiny nebo i rychleji. V některých případech v rámci úrovně Standard nebo Basic mění se škálování latence může být v části 5 minut bez ohledu na množství využité místo.
- Obecně platí, doba trvání, chcete-li změnit min virtuálních jader na databázi nebo maximální počet virtuálních jader na databázi je pět minut nebo méně.
- Když downsizing fondu virtuálních jader, musí být menší než maximální povolenou velikost cílové služby vrstvy a fondu virtuální jádra místo fondu používá.

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>Nákupní model založený na DTU: změnit velikost úložiště elastického fondu

- Cena eDTU pro elastický fond obsahuje objem úložiště bez dalších poplatků. Dodatečné úložiště nad rámec objemu zahrnutého v ceně je možné zřídit za poplatek až po limit maximální velikosti, v přírůstcích po 250 GB až 1 TB a potom dokupuje se násobek 256 GB nad rámec 1 TB. Částky zahrnutého úložiště a omezení maximální velikosti najdete v tématu [elastického fondu: velikosti úložiště a výpočty velikostí](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Dodatečné úložiště pro elastický fond je možné zřídit zvýšením jeho pomocí maximální velikosti [webu Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [rozhraní příkazového řádku Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), nebo [rozhraní REST API ](https://docs.microsoft.com/rest/api/sql/elasticpools/elasticpools_update).
- Cena dodatečného úložiště pro elastický fond je velikost dodatečného úložiště vynásobí jednotkovou cenu dodatečné úložiště na úrovni služby. Podrobnosti o cenách dodatečného úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>Nákupní model založený na DTU: Změna elastický fond, výpočetní prostředky (Edtu)

Při zvětšování a zmenšování prostředků k dispozici do elastického fondu podle potřeby pomocí prostředků [webu Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [rozhraní příkazového řádku Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), nebo [ Rozhraní REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/elasticpools_update).

- Když změny měřítka jednotky Edtu fondu, jsou vynechány stručně připojení k databázi. Toto chování je stejné chování, která nastane, pokud se změny měřítka Dtu pro izolovanou databázi. Podrobnosti o dobu trvání a dopad přerušená připojení pro databázi během mění se škálování operací, najdete v části [změna výpočetních prostředků (Dtu)](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus).
- Doba trvání, chcete-li změnit jednotky Edtu fondu může záviset na celkové množství prostor úložiště využitý všechny databáze ve fondu. Obecně platí, mění se škálování latence předběhli aktuální fázi 90 minut nebo méně na každých 100 GB. Například pokud používá celkové místo všech databází ve fondu je 200 GB, očekávaná latence pro změny měřítka fondu je 3 hodiny nebo i rychleji. V některých případech v rámci úrovně Standard nebo Basic mění se škálování latence může být v části 5 minut bez ohledu na množství využité místo.
- Obecně platí, doba trvání, chcete-li změnit minimální počet Edtu na databázi nebo maximálního počtu Edtu na databázi je pět minut nebo méně.
- Při downsizing Edtu pro elastický fond, musí být menší než maximální povolenou velikost cílové služby vrstvy a fondu Edtu fondu použít místo.
- Když změny měřítka Edtu pro elastický fond, náklady na úložiště platí v případě, že (1) na maximální velikost fondu úložiště jsou podporovány cílový fond, a (2) je maximální velikost úložiště překračuje velikost zahrnutého úložiště cílový fond. Například pokud se 100 jednotkami eDTU fondu úrovně Standard s maximální velikostí 100 GB downsized na standardní fond s 50 eDTU, pak náklady na úložiště platí od cílový fond podporuje maximální velikost 100 GB a jeho velikost zahrnutého úložiště je pouze 50 GB. Ano velikost dodatečného úložiště je 100 GB – 50 GB = 50 GB. Ceny za dodatečné úložiště, najdete v části [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/). Pokud se skutečné množství využité je menší než velikost zahrnutého úložiště, pak toho dalších poplatků se lze vyvarovat snížením maximální velikost databáze na objemu zahrnutého v ceně.

## <a name="next-steps"></a>Další postup

Celkové omezení prostředků najdete v tématu [omezení prostředků založený na virtuálních jádrech SQL Database – elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md) a [omezení prostředků založený na DTU databáze SQL – elastických fondů](sql-database-dtu-resource-limits-elastic-pools.md).
