---
title: Migrace z DTU na virtuální jádra
description: Migrujte databázi v Azure SQL Database z modelu DTU do modelu vCore. Migrace na vCore je podobná upgradu nebo downgrade mezi úrovní Standard a Premium.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/09/2021
ms.openlocfilehash: 332a2273a377268a425619a0cdaa5f4780b46e73
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361651"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrace Azure SQL Database z modelu založeného na DTU do modelu založeného na vCore
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek popisuje, jak migrovat databázi v Azure SQL Database z [nákupního modelu založeného na DTU](service-tiers-dtu.md) do [nákupního modelu založeného na Vcore](service-tiers-vcore.md). 

## <a name="migrate-a-database"></a>Migrace databáze

Migrace databáze z nákupního modelu založeného na DTU do nákupního modelu založeného na vCore se podobá škálování mezi cíli služeb v úrovních služeb Basic, Standard a Premium, s podobnou [dobou trvání](single-database-scale.md#latency) a [minimálním prostojem](scale-resources.md) na konci procesu migrace. Databázi migrovánou do nákupního modelu založeného na vCore lze kdykoli migrovat zpět do modelu nákupu založeného na DTU, a to s výjimkou databází migrovaných do vrstvy služby s [škálováním](service-tier-hyperscale.md) na úrovni služeb. 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>Výběr úrovně služby a cíle služby vCore

Pro většinu DTU až do vCorech scénářů migrace se databáze a elastické fondy v základních a standardních úrovních služby namapují na úroveň služby [pro obecné účely](service-tier-general-purpose.md) . Databáze a elastické fondy v úrovni služby Premium budou namapovány na úroveň služby [pro důležité obchodní informace](service-tier-business-critical.md) . V závislosti na scénáři a požadavcích aplikace se dá úroveň služby pro [škálování](service-tier-hyperscale.md) často použít jako cíl migrace pro izolované databáze ve všech úrovních služby DTU.

Chcete-li zvolit cíl služby nebo výpočetní velikost pro migrovaná databáze v modelu vCore, můžete použít jednoduché, ale přibližné 100 pravidlo, které je v úrovni Basic nebo *Standard vyžadováno alespoň* 1 Vcore a každý 125 DTU na úrovni Premium vyžaduje *aspoň* 1 Vcore. 

> [!TIP]
> Toto pravidlo je přibližné, protože nebere v úvahu generování hardwaru používané pro databázi DTU nebo elastický fond. 

V modelu DTU se dá k databázi nebo elastickému fondu použít jakékoli dostupné [hardwarové generování](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model) . Kromě toho máte k dispozici pouze nepřímou kontrolu nad počtem virtuální jádra (logických procesorů), a to tak, že vyberete vyšší nebo nižší hodnoty DTU nebo eDTU. 

U modelu vCore musí zákazníci provést explicitní volbu pro generování hardwaru i počet virtuální jádra (logických procesorů). Model DTU tyto možnosti nenabízí, ale generování hardwaru a počet logických procesorů použitých pro každou databázi a elastický fond se zveřejňují prostřednictvím zobrazení dynamické správy. Díky tomu je možné přesněji určit cílový vCore služby. 

Následující přístup používá tyto informace k určení cíle služby vCore s podobným přidělením prostředků, aby po migraci na model vCore získala podobnou úroveň výkonu.

### <a name="dtu-to-vcore-mapping"></a>Mapování DTU na vCore

Níže uvedený dotaz T-SQL, který se spustí v kontextu databáze DTU, který se má migrovat, vrátí pro každou generaci hardwaru v modelu vCore shodný (možná zlomek) virtuální jádra. Po zaokrouhlení tohoto čísla na nejbližší počet virtuální jádra dostupných pro [databáze](resource-limits-vcore-single-databases.md) a [elastické fondy](resource-limits-vcore-elastic-pools.md) v každém generování hardwaru v modelu Vcore mohou zákazníci zvolit cíl služby Vcore, který je nejblíženou shodou pro svou databázi DTU nebo elastický fond. 

Příklady scénářů migrace s použitím tohoto přístupu jsou popsané v části [Příklady](#dtu-to-vcore-migration-examples) .

Tento dotaz spusťte v kontextu databáze, kterou chcete migrovat, a ne v `master` databázi. Při migraci elastického fondu spusťte dotaz v kontextu jakékoli databáze ve fondu.

```SQL
WITH dtu_vcore_map AS
(
SELECT rg.slo_name,
       DATABASEPROPERTYEX(DB_NAME(), 'Edition') AS dtu_service_tier,
       CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG7%' THEN 'Gen5'
       END AS dtu_hardware_gen,
       s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
       CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
      AND
      rg.database_id = DB_ID()
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       dtu_service_tier,
       CASE WHEN dtu_service_tier = 'Basic' THEN 'General Purpose'
            WHEN dtu_service_tier = 'Standard' THEN 'General Purpose or Hyperscale'
            WHEN dtu_service_tier = 'Premium' THEN 'Business Critical or Hyperscale'
       END AS vcore_service_tier,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>Další faktory

Kromě počtu virtuální jádra (logických procesorů) a generace hardwaru mohou mít různé faktory vliv na výběr cíle služby vCore:

- Mapování T-SQL dotazu odpovídá cílům DTU a vCore služeb z důvodu jejich kapacity procesoru, proto budou výsledky pro úlohy vázané na procesor přesnější.
- Pro stejnou generaci hardwaru a stejný počet omezení prostředků propustnosti virtuální jádra, IOPS a transakčního protokolu pro databáze vCore jsou často vyšší než pro databáze DTU. U úloh vázaných na vstupně-výstupní operace může být možné snížit počet virtuální jádra v modelu vCore, aby se dosáhlo stejné úrovně výkonu. Limity zdrojů pro DTU a databáze vCore v absolutních hodnotách jsou zpřístupněny v zobrazení [Sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) . Porovnání těchto hodnot mezi databází DTU, která se má migrovat, a databází vCore s využitím přibližně odpovídajícího cíle služby vám pomůže vybrat cíl služby vCore přesněji.
- Dotaz mapování také vrátí množství paměti na jádro pro migraci databáze DTU nebo elastického fondu a pro každou generaci hardwaru v modelu vCore. Zajištění podobné nebo vyšší celkové paměti po migraci na vCore je důležité pro úlohy, které vyžadují dostatečnou mezipaměť dat velké paměti, aby dosáhly dostatečného výkonu, nebo úlohy, které vyžadují velké množství paměti pro zpracování dotazů. Pro takové úlohy, v závislosti na skutečném výkonu, může být potřeba zvýšit počet virtuální jádra, aby se dosáhlo dostatečně celkového množství paměti.
- Při volbě cíle služby vCore by se mělo vzít v úvahu [historické využití prostředků](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) databáze DTU. Databáze DTU s konzistentním využitím prostředků procesoru můžou potřebovat méně virtuální jádra než počet vrácený dotazem mapování. Databáze DTU, kde konzistentně vysoké využití procesoru způsobuje nedostatečný výkon úlohy, mohou vyžadovat více virtuální jádra, než je dotaz vrácen.
- Pokud migrujete databáze s přerušovaným nebo nepředvídatelným vzorcem použití, zvažte použití výpočetní vrstvy bez [serveru](serverless-tier-overview.md) . Počítejte s tím, že maximální počet souběžných pracovních procesů (požadavků) v bez serveru je 75% limitu zajištěných pro stejný počet virtuální jádra nakonfigurovaných na stejný počet. Maximální dostupná velikost paměti v neserveru je navíc 3 GB, což je maximální počet nakonfigurovaných virtuální jádra. například maximální velikost paměti je 120 GB, pokud jsou nakonfigurovány virtuální jádra max. 40.   
- V modelu vCore se podporovaná maximální velikost databáze může lišit v závislosti na generaci hardwaru. U rozsáhlých databází ověřte Podporované maximální velikosti v modelu vCore pro izolované [databáze](resource-limits-vcore-single-databases.md) a [elastické fondy](resource-limits-vcore-elastic-pools.md).
- U elastických fondů mají modely [DTU](resource-limits-dtu-elastic-pools.md) a [Vcore](resource-limits-vcore-elastic-pools.md) rozdíly v maximálním podporovaném počtu databází na jeden fond. To je vhodné vzít v úvahu při migraci elastických fondů s mnoha databázemi.
- Některé generace hardwaru nemusí být k dispozici v každé oblasti. Ověřte dostupnost v části [generování hardwaru](service-tiers-vcore.md#hardware-generations).

> [!IMPORTANT]
> Výše uvedené pokyny ke změně velikosti DTU na vCore najdete v tématu o počátečním odhadu cíle cílové databázové služby.
>
> Optimální konfigurace cílové databáze je závislá na úlohách. Proto po migraci dosáhnete optimálního poměru ceny a výkonu, abyste mohli využít flexibilitu modelu vCore k přizpůsobení počtu virtuální jádra, [generování hardwaru](service-tiers-vcore.md#hardware-generations), [služby](service-tiers-vcore.md#service-tiers) a [výpočetních](service-tiers-vcore.md#compute-tiers) vrstev a také vyladění dalších parametrů konfigurace databáze, jako je například [Maximální stupeň paralelismu](/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing).
> 

### <a name="dtu-to-vcore-migration-examples"></a>Příklady vCore migrace DTU

> [!NOTE]
> Hodnoty v níže uvedených příkladech slouží jenom pro ilustraci. Skutečné hodnoty vrácené v popsaných scénářích se mohou lišit.
> 

**Migrace standardní databáze S9**

Dotaz mapování vrátí následující výsledek (některé sloupce nejsou zobrazeny pro zkrácení):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24,00|Gen5|5,40|16,800|7|24,000|5,05|

Zjistili jsme, že databáze DTU má 24 logických procesorů (virtuální jádra), s 5,4 GB paměti na vCore a používá Gen5 hardware. Přímo se shoduje s tím, že Pro obecné účely 24 databáze vCore na hardwaru Gen5, tj. v cíli služby **GP_Gen5_24** Vcore.

**Migrace standardní databáze S0**

Dotaz mapování vrátí následující výsledek (některé sloupce nejsou zobrazeny pro zkrácení):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0,25|COMPUTE GEN4 –|0,42|0,250|7|0,425|5,05|

Zjistili jsme, že databáze DTU má ekvivalent 0,25 logických procesorů (virtuální jádra), 0,42 GB paměti na vCore a používá COMPUTE GEN4 – hardware. Nejmenší vCore služby v COMPUTE GEN4 – a Gen5 hardwarových generacích, **GP_Gen4_1** a **GP_Gen5_2**, poskytují více výpočetních prostředků než standardní databáze S0, takže přímá shoda není možná. Vzhledem k tomu, že se COMPUTE GEN4 – hardware [vyřadí](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/), je preferovaná možnost **GP_Gen5_2** . Kromě toho platí, že pokud je úloha vhodná pro výpočetní vrstvu bez [serveru](serverless-tier-overview.md) , **GP_S_Gen5_1** by byla blíže shodná.

**Migrace databáze Premium P15**

Dotaz mapování vrátí následující výsledek (některé sloupce nejsou zobrazeny pro zkrácení):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42,00|Gen5|4,86|29,400|7|42,000|5,05|

Zjistili jsme, že databáze DTU má 42 logických procesorů (virtuální jádra), 4,86 GB paměti na vCore a používá Gen5 hardware. I když se nejedná o cíl služby vCore s 42 jádry, cíl služby **BC_Gen5_40** je velmi blízko jak z hlediska kapacity procesoru, tak paměti a je dobrou shodou.

**Migrace elastického fondu úrovně Basic 200 eDTU**

Dotaz mapování vrátí následující výsledek (některé sloupce nejsou zobrazeny pro zkrácení):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4,00|Gen5|5,40|2,800|7|4,000|5,05|

Zjistili jsme, že elastický fond DTU má 4 logické procesory (virtuální jádra) s 5,4 GB paměti na vCore a používá Gen5 hardware. Přímá shoda v modelu vCore je **GP_Gen5_4** elastický fond. Tento cíl služby však podporuje maximálně 200 databází na jeden fond, zatímco elastický fond úrovně Basic 200 eDTU podporuje až 500 databází. Pokud má elastický fond, který má být migrován, více než 200 databází, musí **GP_Gen5_6** být Vcore cílovým cílem služby, který podporuje až 500 databází.

## <a name="migrate-geo-replicated-databases"></a>Migrace geograficky replikovaných databází

Migrace z modelu založeného na DTU do nákupního modelu založeného na vCore se podobá upgradu nebo downgrade vztahů geografické replikace mezi databázemi v úrovních služeb Standard a Premium. Během migrace nemusíte zastavit geografickou replikaci, ale musíte dodržovat tato pravidla sekvencování:

- Při upgradu musíte nejdřív upgradovat sekundární databázi a potom upgradovat primární.
- Když se downgrade, obrátí se pořadí: Nejdřív musíte nejprve downgradovat primární databázi a pak snížit její sekundární verzi.

Pokud používáte geografickou replikaci mezi dvěma elastickými fondy, doporučujeme, abyste jeden fond určili jako primární a druhý jako sekundární. V takovém případě byste měli při migraci elastických fondů použít stejné pokyny k sekvencování. Pokud ale máte elastické fondy, které obsahují jak primární, tak sekundární databáze, považovat fond s vyšším využitím jako primární a podle toho pravidla sekvencování řídí.  

Následující tabulka poskytuje pokyny pro konkrétní scénáře migrace:

|Aktuální úroveň služby|Cílová úroveň služby|Typ migrace|Akce uživatele|
|---|---|---|---|
|Standard|Obecné účely|Boku|Může migrovat v libovolném pořadí, ale musí zajistit odpovídající velikost vCore, jak je popsáno výše.|
|Premium|Pro důležité obchodní informace|Boku|Může migrovat v libovolném pořadí, ale musí zajistit odpovídající velikost vCore, jak je popsáno výše.|
|Standard|Pro důležité obchodní informace|Upgrade|Nejprve je třeba migrovat sekundární|
|Pro důležité obchodní informace|Standard|Downgrade|Nejprve je třeba migrovat primární|
|Premium|Obecné účely|Downgrade|Nejprve je třeba migrovat primární|
|Obecné účely|Premium|Upgrade|Nejprve je třeba migrovat sekundární|
|Pro důležité obchodní informace|Obecné účely|Downgrade|Nejprve je třeba migrovat primární|
|Obecné účely|Pro důležité obchodní informace|Upgrade|Nejprve je třeba migrovat sekundární|
||||

## <a name="migrate-failover-groups"></a>Migrace skupin převzetí služeb při selhání

Migrace skupin převzetí služeb při selhání s více databázemi vyžaduje samostatnou migraci primárních a sekundárních databází. Během tohoto procesu platí stejné podmínky a pravidla sekvence. Po převedení databází na nákupní model založený na vCore zůstane skupina převzetí služeb při selhání platná se stejnými nastaveními zásad.

### <a name="create-a-geo-replication-secondary-database"></a>Vytvoření sekundární databáze geografické replikace

Sekundární databázi geografické replikace můžete vytvořit pouze pomocí stejné úrovně služby, jakou jste použili pro primární databázi. Pro databáze s vysokou rychlostí generování protokolů doporučujeme vytvořit geografickou sekundární stejnou velikost služby COMPUTE jako primární.

Pokud vytváříte geograficky sekundární v elastickém fondu pro jednu primární databázi, ujistěte se, že `maxVCore` nastavení fondu odpovídá výpočetní velikosti primární databáze. Pokud vytváříte geografickou sekundární hodnotu pro primární objekt v jiném elastickém fondu, doporučujeme, aby fondy měly stejné `maxVCore` nastavení.

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>Použít kopírování databáze k migraci z DTU do vCore

Můžete zkopírovat jakoukoli databázi s výpočetní velikostí založenou na DTU do databáze s výpočetní velikostí založenou na vCore bez omezení nebo speciální sekvencování, pokud cílová výpočetní velikost podporuje maximální velikost databáze zdrojové databáze. Kopie databáze vytvoří snímek dat pro počáteční čas operace kopírování a nesynchronizuje data mezi zdrojem a cílem.

## <a name="next-steps"></a>Další kroky

- Konkrétní velikosti a možnosti velikosti úložiště, které jsou dostupné pro jednotlivé databáze, najdete v tématu [SQL Database omezení prostředků na základě Vcore pro jednotlivé databáze](resource-limits-vcore-single-databases.md).
- Konkrétní velikosti a možnosti velikosti úložiště dostupné pro elastické fondy najdete v tématu [SQL Database omezení prostředků na základě Vcore pro elastické fondy](resource-limits-vcore-elastic-pools.md).