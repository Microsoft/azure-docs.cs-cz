---
title: Omezení prostředků serveru Azure SQL Database
description: Tento článek poskytuje přehled omezení prostředků Azure SQL Database serveru pro izolované databáze a elastické fondy. Poskytuje také informace o tom, co se stane, když dojde k dosažení nebo překročení těchto omezení prostředků.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 04/18/2019
ms.openlocfilehash: b358e69df1df579e91a9098c120c7e6b4e3f2ead
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687499"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>SQL Database omezení prostředků pro Azure SQL Database Server

Tento článek poskytuje přehled SQL Databasech omezení prostředků pro SQL Database Server, který spravuje izolované databáze a elastické fondy. Poskytuje také informace o tom, co se stane, když dojde k dosažení nebo překročení těchto omezení prostředků.

> [!NOTE]
> Omezení pro spravované instance najdete v tématu [SQL Database omezení prostředků pro spravované instance](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Maximum omezení prostředků

| Prostředek | Omezení |
| :--- | :--- |
| Databáze na server | 5000 |
| Výchozí počet serverů na předplatné v libovolné oblasti | 20 |
| Maximální počet serverů na předplatné v libovolné oblasti | 200 |  
| Kvóta DTU/eDTU na server | 54 000 |  
| vCore kvóta na server/instanci | 540 |
| Maximální počet fondů na server | Omezeno počtem DTU nebo virtuální jádra. Pokud je například každý fond 1000 DTU, může server podporovat fondy 54.|
|||

> [!NOTE]
> Pokud chcete získat větší kvótu DTU/eDTU, kvótu vCore nebo více serverů než výchozí množství, můžete odeslat novou žádost o podporu v Azure Portal pro předplatné s typem problému "kvóta". Kvóta DTU/eDTU a omezení databáze na server omezuje počet elastických fondů na jeden server.
> [!IMPORTANT]
> Vzhledem k tomu, že počet databází se blíží limitu pro SQL Database Server, může dojít k následujícím akcím:
> - Zvýšení latence při spouštění dotazů na hlavní databázi.  Patří sem zobrazení statistik využití prostředků, jako je například sys. resource_stats.
> - Zvýšení latence při operacích správy a v pohledech na portál pro vykreslování, které zahrnují vytváření výčtu databází na serveru.

### <a name="storage-size"></a>Velikost úložiště
- Pro izolované databáze rources prosím pro omezení velikosti úložiště podle cenové úrovně [omezení prostředků založené na DTU](sql-database-dtu-resource-limits-single-databases.md) nebo [omezení prostředků na základě Vcore](sql-database-vcore-resource-limits-single-databases.md) .

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Co se stane, když se dosáhnou omezení prostředků databáze

### <a name="compute-dtus-and-edtus--vcores"></a>COMPUTE (DTU a eDTU/virtuální jádra)

V případě, že využití výpočetní databáze (měřené od DTU a eDTU nebo virtuální jádra) bude vysoké, zvýšení latence dotazu a může dokonce vyprší časový limit. Za těchto podmínek můžou být dotazy ve službě zařazené do fronty a k dispozici jsou prostředky pro spuštění, protože prostředek bude zadarmo.
Pokud se setkáte s vysokým využitím výpočetních prostředků, zahrnují možnosti zmírnění:

- Zvýšením výpočetní velikosti databáze nebo elastického fondu poskytnete databázi s více výpočetními prostředky. Viz téma [škálování prostředků jedné databáze](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).
- Optimalizace dotazů pro snížení využití prostředků každého dotazu. Další informace najdete v tématu [ladění a hinty dotazů](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Když využité místo v databázi dosáhne limitu maximální velikosti, vkládání a aktualizace databáze, které zvyšují velikost dat, selžou a klienti obdrží [chybovou zprávu](sql-database-develop-error-messages.md). Databáze vybere a odstraní nadále úspěšné.

Při zjištění vysokého využití místa mají tyto možnosti omezení:

- Zvýšení maximální velikosti databáze nebo elastického fondu nebo přidání více úložišť. Viz téma [škálování prostředků jedné databáze](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).
- Pokud se databáze nachází v elastickém fondu, pak je možné databázi přesunout mimo fond, aby její prostor úložiště nebyl sdílen s ostatními databázemi.
- Zmenšete databázi pro uvolnění nevyužitého místa. Další informace najdete v tématu [Správa místa v souboru v Azure SQL Database](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Relace a pracovní procesy (požadavky)

Maximální počet relací a pracovních procesů závisí na úrovni služby a velikosti výpočtů (DTU a eDTU). Nové žádosti jsou odmítnuty, když jsou dosažena omezení relace nebo pracovního procesu, a klienti obdrží chybovou zprávu. I když může být počet dostupných připojení řízen aplikací, počet souběžných pracovních procesů je často těžší odhadnout a řídit. To platí hlavně během období špičky zatížení při dosažení limitů prostředků databáze a pracovníci se budou moct dopracovat z důvodu delšího spouštění dotazů.

Pokud se setkáte s vysokým využitím relace nebo pracovního procesu, zahrnují možnosti zmírňování:

- Zvýšení úrovně služby nebo výpočetní velikosti databáze nebo elastického fondu. Viz téma [škálování prostředků jedné databáze](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).
- Optimalizujte dotazy, aby se snížilo využití prostředků každého dotazu, pokud příčinou zvýšení využití pracovního procesu je spor pro výpočetní prostředky. Další informace najdete v tématu [ladění a hinty dotazů](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="transaction-log-rate-governance"></a>Řízení sazeb transakčního protokolu 
Řízení sazeb za transakční protokol je proces v Azure SQL Database, který slouží k omezení vysoké sazby příjmu pro úlohy, jako jsou hromadné vložení, výběr a sestavení indexů. Tato omezení jsou sledována a vyhodnocena na úrovni druhé druhé na míru četnosti vytváření záznamů protokolu, což omezuje propustnost bez ohledu na to, kolik IOs může být pro datové soubory vystaveno.  Frekvence generování protokolu transakcí aktuálně škáluje lineárně až do bodu, který je závislý na hardwaru, přičemž maximální povolený počet protokolů je 96 MB/s s modelem nákupu vCore. 

> [!NOTE]
> Skutečné fyzické soubory protokolu IOs na transakce se neřídí ani nepodporují. 

Sazby za protokol jsou nastavené tak, aby je bylo možné dosáhnout a udržovat v nejrůznějších scénářích, zatímco celkový systém může udržovat svou funkčnost s minimalizovaným dopadem na uživatelské zatížení. Zásady správného řízení protokolu zajistí, že zálohy protokolu transakcí zůstávají v rámci publikované možnosti obnovování SLA.  Tento zásady správného řízení taky brání nadměrnému počtu nevyřízených položek na sekundárních replikách.

Po vygenerování záznamů protokolu se každá operace vyhodnotí a vyhodnotí, jestli by se měla zpozdit, aby se zachovala maximální požadovaná rychlost protokolu (MB/s za sekundu). Zpoždění se přidávají, když se záznamy protokolu vyprázdní do úložiště, místo toho se uplatní zásady správného řízení protokolu během generování rychlosti protokolu.

Skutečné sazby za generování protokolů, které jsou uvedené v době běhu, můžou být ovlivněné pomocí mechanismů zpětné vazby, ale dočasné snížení přípustných sazeb protokolu, aby systém mohl stabilizovat. Správa místa souboru protokolu, což nezpůsobuje nevyužité podmínky místa v protokolu a mechanismy replikace skupin dostupnosti dočasně snižují celkový počet systémových omezení. 

Tvarování provozu správce míry přenosu dat se prochází prostřednictvím následujících typů čekání (zveřejněné v [Sys. DM _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Typ čekání | Poznámky |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Omezení databáze |
| POOL_LOG_RATE_GOVERNOR | Omezení fondu |
| INSTANCE_LOG_RATE_GOVERNOR | Omezení na úrovni instance |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Řízení zpětné vazby, fyzická replikace skupiny dostupnosti v Premium/Pro důležité obchodní informace Neudržovat |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Řízení zpětné vazby, omezování sazeb pro zamezení nedostatku místa v protokolu |
|||

Pokud se setkáte s limitem přenosové rychlosti, která brání požadovanou škálovatelnost, vezměte v úvahu následující možnosti:
- Pokud chcete získat maximální rychlost protokolu 96 MB/s, můžete škálovat až na větší úroveň. 
- Pokud jsou data načítána do přechodného režimu, tj. přípravná data v procesu ETL, lze je načíst do tempdb (což je minimální protokolně). 
- V případě analytických scénářů se načtěte do clusterované tabulky columnstore. Tím se sníží požadovaná rychlost protokolu z důvodu komprimace. Tato technika zvýší využití procesoru a vztahuje se pouze na datové sady, které využívají clusterované indexy columnstore. 

## <a name="next-steps"></a>Další kroky

- Informace o obecných omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md).
- Informace o DTU a eDTU najdete v tématu [DTU a eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Informace o omezení velikosti databáze tempdb najdete [v tématu databáze tempdb v Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
