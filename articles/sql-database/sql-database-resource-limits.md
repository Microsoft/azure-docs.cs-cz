---
title: Limity prostředků Azure SQL Database – přehled | Dokumentace Microsoftu
description: Tato stránka popisuje některé běžné limity prostředků založený na DTU pro izolované databáze ve službě Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 6f6fa1ebc086530f138d32ee5a9c799b5bfbbdeb
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412106"
---
# <a name="overview-azure-sql-database-resource-limits"></a>Limity prostředků Azure SQL Database – přehled 

Tento článek obsahuje přehled prostředků Azure SQL Database omezuje a poskytuje informace o co se stane, když jsou zásahů nebo překročení těchto omezení prostředků.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Jaký je maximální počet serverů a databází?

| Maximum | Hodnota |
| :--- | :--- |
| Databází na serveru | 5000 |
| Výchozí počet serverů na předplatné v libovolné oblasti | 20 |
| Maximální počet serverů na předplatné v libovolné oblasti | 200 |
| DTU nebo eDTU kvóta na serveru | 54,000 |
| vCore kvóta na serveru | 540 |
| Maximální počet fondů na serveru | počet je omezen počet Dtu nebo virtuálních jader |
|||

> [!NOTE]
> K získání další /eDTU kvóty DTU, vCore kvóty nebo více serverů než výchozí dobu, můžete odeslat novou žádost o podporu na webu Azure Portal pro předplatné se typ problému "Quota". DTU / limitu eDTU kvótu a databáze na serveru omezuje počet elastických fondů na jeden server. 

> [!IMPORTANT]
> Jako počet databází, které se blíží limitu na server, mohou nastat následující:
> - Zvyšování latence ve spuštěném dotazy v hlavní databázi.  To zahrnuje zobrazení statistik využití prostředků, jako je například sys.resource_stats.
> - Zvyšuje latenci při operacích správy a vykreslování portálu přehledů, které se týkají databáze na serveru.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Co se stane, když je dosaženo omezení prostředků databáze?

### <a name="compute-dtus-and-edtus--vcores"></a>COMPUTE (počet jednotek Dtu a Edtu / virtuálních jader)

Jakmile výpočetní využití databáze (měří podle počtu jednotek Dtu a Edtu nebo virtuálních jader) je vysoké, zvyšuje latence dotazu a může dokonce vypršet časový limit. Dotazů za těchto podmínek může být zařazené do fronty služby a jsou k dispozici prostředky pro spuštění jako prostředek stane zdarma.
Při zjištění vysokou výpočetní využití, možnosti omezení rizik:

- Zvýšení úrovně výkonu databáze nebo elastického fondu pro databázi poskytnout další výpočetní prostředky. Zobrazit [škálování izolované databáze prostředků](sql-database-single-database-scale.md) a [škálování elastického fondu prostředků](sql-database-elastic-pool-scale.md).
- Optimalizace dotazů, aby se snížilo využití prostředků každého dotazu. Další informace najdete v tématu [dotazu ladění/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Úložiště

Když databáze využité dosáhne limitu maximální velikost, vloží databáze a aktualizací, které zvyšují velikost dat a klienti obdrží [chybová zpráva](sql-database-develop-error-messages.md). Databáze VYBERE a odstraní se nadále probíhat úspěšně.

Pokud dochází k vysoké využití, možnosti omezení rizik:

- Zvýšení maximální velikosti databáze nebo elastického fondu nebo přidat další úložiště. Zobrazit [škálování izolované databáze prostředků](sql-database-single-database-scale.md) a [škálování elastického fondu prostředků](sql-database-elastic-pool-scale.md).
- Pokud je databáze v elastickém fondu, pak také databázi lze přesunout mimo fond tak, aby její prostor úložiště se nesdílí s jinými databázemi.
- Zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Relace a pracovních procesů (požadavků) 

Maximální počet relací a pracovní procesy jsou určeny službu úroveň a úroveň výkonu (počet jednotek Dtu a Edtu). Nové požadavky byly zamítnuty, když je dosaženo omezení relace nebo pracovního procesu a klienti obdrží chybovou zprávu. Když počet připojení, které jsou k dispozici, které mohou být řízena aplikace, je často obtížné odhadnout a řídit počet souběžných pracovních procesů. To platí zejména během špiček zatížení kdy je dosaženo omezení prostředků databáze a pracovní procesy hromadí kvůli delší dobu spouštění dotazů. 

Pokud dochází k vysoké využití relace nebo pracovního procesu, možnosti omezení rizik:
- Zvyšuje služby vrstvy nebo úrovně výkonu databáze nebo elastického fondu. Zobrazit [škálování izolované databáze prostředků](sql-database-single-database-scale.md) a [škálování elastického fondu prostředků](sql-database-elastic-pool-scale.md).
- Optimalizace dotazů, aby se snížilo využití prostředků každého dotazu, je-li příčinou využití zvýšenou pracovního procesu je z důvodu kolize pro výpočetní prostředky. Další informace najdete v tématu [dotazu ladění/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Další postup

- Zobrazit [nejčastější dotazy k SQL Database](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).
- Informace o jednotkách Dtu a Edtu najdete v tématu [jednotkách Dtu a Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- Informace o omezení velikosti databáze tempdb, naleznete v tématu https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
