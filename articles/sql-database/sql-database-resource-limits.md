---
title: Přehled omezení prostředků Azure SQL Database | Microsoft Docs
description: Tato stránka popisuje některé běžné limitů prostředků na základě DTU pro izolované databáze ve službě Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 6806b0c5b5e5ac5e1189f628786f0c8f9b223395
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750947"
---
# <a name="overview-azure-sql-database-resource-limits"></a>Omezení prostředků Azure SQL Database – přehled 

Tento článek obsahuje přehled prostředků databáze SQL Azure omezuje a obsahuje informace týkající se co se stane, když jsou dosáhl nebo překročení limitů těchto prostředků.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Jaký je maximální počet serverů a databází?

| Maximum | Hodnota |
| :--- | :--- |
| Databáze na serveru | 5000 |
| Výchozí počet serverů za předplatné v libovolné oblasti | 20 |
| Maximální počet serverů za předplatné v libovolné oblasti | 200 |
|||

> [!NOTE]
> Pokud chcete získat další kvóty serveru než výchozí dobu, lze odeslat novou žádost o podporu na portálu Azure pro odběr s typem problém "Kvóty".

> [!IMPORTANT]
> Jako počet databází, se blíží limitu na serveru, může objevit následující:
> - Zvyšování latence v provádění dotazů hlavní databázi.  To zahrnuje zobrazení statistiky využití prostředků, jako je například sys.resource_stats.
> - Zvýšení latence v operacích správy a vykreslování portálu hlediska, které se týkají databáze na serveru.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Co se stane, když databáze prostředků limitu?

### <a name="compute-dtus-and-edtus--vcores"></a>Výpočetní (Dtu a Edtu / vCores)

Když se stane vysoké využití výpočetních databáze (měřeno Dtu a Edtu nebo vCores), latence dotazu zvyšuje a můžete i vypršení časového limitu. Za těchto podmínek dotazy může být zařazeny do fronty pomocí služby a jsou poskytl uvolní prostředky pro spuštění jako prostředek.
Při zjištění vysokou výpočetní využití, tyto možnosti omezení rizik:

- Zvýšení úrovně výkonu databáze nebo elastický fond poskytnout další výpočetní prostředky databáze. V tématu [škálování izolovaných databází prostředky](sql-database-single-database-scale.md) a [škálování elastického fondu prostředků](sql-database-elastic-pool-scale.md).
- Optimalizace dotazy ke snížení využití prostředků každý dotaz. Další informace najdete v tématu [dotaz optimalizace/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Úložiště

Pokud místo databáze používá dosáhne omezení maximální velikosti, vloží databáze a dojde k selhání aktualizací, které se zvyšují velikost dat a klienti obdrží [chybová zpráva](sql-database-develop-error-messages.md). Databáze VYBERE a odstranění pokračujte proběhla úspěšně.

Při zjištění vysoké využití, tyto možnosti omezení rizik:

- Zvyšuje maximální velikost databáze nebo elastického fondu nebo přidejte další úložiště. V tématu [škálování izolovaných databází prostředky](sql-database-single-database-scale.md) a [škálování elastického fondu prostředků](sql-database-elastic-pool-scale.md).
- Pokud se databáze nachází v elastickém fondu, pak případně databázi lze přesunout mimo fondu tak, aby jeho prostorem úložiště není sdílený s jiné databáze.

### <a name="sessions-and-workers-requests"></a>Relace a pracovníci (počet požadavků) 

Určuje maximální počet relací a pracovních procesů a výkonu úrovně služby (Dtu a Edtu). Nové požadavky byly zamítnuty, když relace nebo pracovní limitu a klienti obdrží chybovou zprávu. Když počet připojení, které jsou k dispozici je řízena aplikace, je často těžší odhadnout a řídit počet souběžných pracovních procesů. To platí hlavně během období zatížení ve špičce, když databáze prostředků limitu a pracovníci hromadí kvůli delší spuštěné dotazy. 

Při zjištění vysoké využití relace nebo pracovního procesu, možnosti omezení rizik:
- Zvýšení úrovně služby vrstvě nebo výkonu databáze nebo elastického fondu. V tématu [škálování izolovaných databází prostředky](sql-database-single-database-scale.md) a [škálování elastického fondu prostředků](sql-database-elastic-pool-scale.md).
- Optimalizace dotazy ke snížení využití prostředků každý dotaz, je-li příčinou využití vyšší pracovníka způsobeno kolizí pro výpočetní prostředky. Další informace najdete v tématu [dotaz optimalizace/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

Při zjištění vysoké využití relace nebo pracovního procesu, možnosti omezení rizik:
- Zvýšení úrovně služby vrstvě nebo výkonu databáze. V tématu [škálování izolovaných databází prostředky](sql-database-single-database-scale.md) a [škálování elastického fondu prostředků](sql-database-elastic-pool-scale.md).
- Optimalizace dotazy ke snížení využití prostředků každý dotaz, je-li příčinou využití vyšší pracovníka způsobeno kolizí pro výpočetní prostředky. Další informace najdete v tématu [dotaz optimalizace/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Další postup

- V tématu [nejčastější dotazy k databázi SQL](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md).
- Informace o Dtu a Edtu najdete v tématu [Dtu a Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- Informace o databázi tempdb velikosti omezení naleznete v tématu https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
