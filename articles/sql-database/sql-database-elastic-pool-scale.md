---
title: Škálování elastického fondu prostředků – Azure SQL Database | Microsoft Docs
description: Tato stránka popisuje škálování prostředky pro elastické fondy ve službě Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 0d15382f413485ccc287bac945b3c88eb748a9f6
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313315"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Škálování elastického fondu prostředků ve službě Azure SQL Database

Tento článek popisuje, jak se škálovat výpočetní a úložnou kapacitu pro elastické fondy a databází ve fondu k dispozici ve službě Azure SQL Database. 

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>na základě vCore nákupní model: změnit velikost úložiště elastického fondu

- Úložiště může být zřízen až do limitu maximální velikost: 
 - Pro standardní úložiště zvýšení nebo snížení velikosti v přírůstcích po 10 GB
 - Pro storage úrovně Premium zvětšit nebo zmenšit velikost v přírůstcích po 250 GB
- Úložiště pro elastický fond se dá zřídit zvýšením nebo snížením své maximální velikosti.
- Cena úložiště pro elastický fond je množství úložiště násobí hodnotou jednotkové ceny úložiště vrstvy služby. Podrobnosti na cenu dodatečné úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>na základě vCore nákupní model: Změna elastického fondu výpočetní prostředky (vCores)

Můžete zvýšit nebo snížit úroveň výkonu na elastickém fondu podle prostředků vyžaduje použití [portál Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [rozhraní příkazového řádku Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), nebo [Rozhraní REST API](/rest/api/sql/elasticpools/update).

- Pokud změny měřítka vCores fondu, jsou stručně vyřadit připojení databáze. Toto je stejné chování jako nastane, když změny měřítka Dtu pro jednu databázi (ne ve fondu). Podrobnosti o doba trvání a dopad vynechaných připojení pro databázi během změny měřítka operací, najdete v části [změny měřítka Dtu pro jednu databázi](#single-database-change-storage-size). 
- Můžete nastavit velikost fondu vCores pro dobu trvání závisí na celkovém množství prostoru úložiště používané všechny databáze ve fondu. Obecně platí, rescaling latence zobrazí průměr 90 minut nebo méně na 100 GB. Například pokud celkové místo používá všechny databáze ve fondu je 200 GB, pak očekávané latence pro změny měřítka fondu je 3 hodiny nebo méně. V některých případech v rámci úroveň Standard nebo Basic může být rescaling latence bez ohledu na množství prostoru v části pět minut.
- Obecně platí, doba trvání změnit vCores minimum za databáze nebo max vCores na databázi je pět minut nebo méně.
- Při downsizing vCores fondu, musí být menší než maximální povolená velikost vCores vrstvy a fond služby cílové místo fondu používá.

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>Na základě DTU nákupní model: změnit velikost úložiště elastického fondu

- Cena eDTU fondu elastické databáze zahrnuje množství úložiště bez dalších poplatků. Nad rámec zahrnuté množství dodatečné úložiště se dá zřídit další náklady až do limitu maximální velikost v přírůstcích po 250 GB až 1 TB a pak v přírůstcích po 256 GB nad rámec 1 TB. Objemy zahrnuté úložiště a omezení maximální velikosti najdete v tématu [elastického fondu: velikosti úložiště a úrovně výkonu](#elastic-pool-storage-sizes-and-performance-levels).
- Dodatečné úložiště pro elastický fond může být zřízen zvýšením jeho maximální velikost pomocí [portál Azure](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [rozhraní příkazového řádku Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), nebo [REST API ](/rest/api/sql/elasticpools/update).
- Cena dodatečné úložiště pro elastický fond je velikost úložiště navíc násobí hodnotou jednotkové ceny dodatečné úložiště vrstvy služby. Podrobnosti na cenu dodatečné úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>Na základě DTU nákupní model: Změna elastického fondu výpočetní prostředky (Edtu)

Můžete zvýšit nebo snížit fondu elastické databáze podle prostředků vyžaduje použití dostupné prostředky [portál Azure](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [rozhraní příkazového řádku Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), nebo [ Rozhraní REST API](/rest/api/sql/elasticpools/update).

- Pokud změny měřítka fondu, jsou stručně vyřadit připojení databáze. Toto je stejné chování jako nastane, když změny měřítka Dtu pro jednu databázi (ne ve fondu). Podrobnosti o doba trvání a dopad vynechaných připojení pro databázi během změny měřítka operací, najdete v části [změny měřítka Dtu pro jednu databázi](#single-database-change-storage-size). 
- Můžete nastavit velikost fondu pro dobu trvání závisí na celkovém množství prostoru úložiště používané všechny databáze ve fondu. Obecně platí, rescaling latence zobrazí průměr 90 minut nebo méně na 100 GB. Například pokud celkové místo používá všechny databáze ve fondu je 200 GB, pak očekávané latence pro změny měřítka fondu je 3 hodiny nebo méně. V některých případech v rámci úroveň Standard nebo Basic může být rescaling latence bez ohledu na množství prostoru v části pět minut.
- Obecně platí, doba trvání, chcete-li změnit minimální počet jednotek Edtu na databázi nebo max Edtu na databázi je pět minut nebo méně.
- Při downsizing fondu, musí být menší než maximální povolená velikost cílové služby vrstvy a fond Edtu fondu používá místo.
- Pokud změny měřítka fondu, náklady na úložiště navíc platí v případě, že cílový fond podporuje (1) je maximální velikost fondu úložiště, a (2) je maximální velikost úložiště přesahuje úložiště zahrnuté množství cílový fond. Například pokud 100 eDTU a maximální velikost 100 GB standardní fond je downsized na 50 eDTU standardní fond, pak náklady na úložiště navíc platí vzhledem k tomu, že cílový fond podporuje maximální velikost 100 GB a jeho součástí úložiště je jenom 50 GB. Velikost úložiště, takže je 100 GB – 50 GB = 50 GB. Ceny navíc úložiště, najdete v části [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/). Pokud skutečná velikost využitého místa je menší než množství součástí úložiště, pak tato zpoplatněné se vyhnout snížením maximální velikost databáze na zahrnuté množství. 

## <a name="next-steps"></a>Další postup

Celkové omezení prostředků, najdete v části [limitů na základě vCore prostředků databáze SQL - elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md) a [limitů prostředků na základě DTU databáze SQL - elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md).
