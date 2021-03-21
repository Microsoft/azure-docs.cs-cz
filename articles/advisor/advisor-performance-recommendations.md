---
title: Zlepšení výkonu aplikací Azure pomocí služby Advisor
description: Využijte doporučení k výkonu v Azure Advisor ke zlepšení rychlosti a odezvy vašich důležitých podnikových aplikací.
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 69fd86226375073c71903af5704ef3a635eeaffb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579915"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>Zlepšení výkonu aplikací Azure pomocí Azure Advisor

Doporučení k výkonu v Azure Advisor mohou pomoci zlepšit rychlost a rychlost reakce vašich důležitých podnikových aplikací. Doporučení k výkonu můžete získat od služby Advisor na kartě **výkon** na řídicím panelu poradce.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Snižte čas na živý účet DNS v profilu Traffic Manager, aby se převzaly služby při selhání do funkčních koncových bodů rychleji.

[Nastavení TTL (Time-to-Live)](../traffic-manager/traffic-manager-performance-considerations.md) můžete v profilu Azure Traffic Manager použít k určení, jak rychle přepnout koncové body, pokud daný koncový bod přestane reagovat na dotazy. Pokud omezíte hodnoty TTL, budou klienti směrováni do funkčních koncových bodů rychleji.

Azure Advisor identifikuje profily Traffic Manager, u kterých je nakonfigurované delší hodnoty TTL. V závislosti na tom, jestli je profil nakonfigurovaný pro [rychlé převzetí služeb při selhání](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/), doporučuje nakonfigurovat hodnotu TTL na 20 sekund nebo 60 sekund.

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>Zvýšení výkonu databáze pomocí SQL Database Advisor (dočasně zakázáno)

Azure Advisor poskytuje konzistentní a konsolidované zobrazení doporučení pro všechny prostředky Azure. Integruje se s SQL Database Advisor a dává vám doporučení pro zlepšení výkonu databází. SQL Database Advisor vyhodnocuje výkon vašich databází analýzou historie využití. Potom nabízí doporučení, která jsou nejvhodnější pro spuštění typického zatížení databáze.

> [!NOTE]
> Než budete moct získat doporučení, je nutné, aby se databáze používala přibližně týden a v tomto týdnu musí být konzistentní aktivita. SQL Database Advisor se dá snadněji optimalizovat pro konzistentní vzory dotazů, než pro náhodné nárůsty aktivity.

Další informace najdete v tématu [SQL Database Advisor](../azure-sql/database/database-advisor-implement-performance-recommendations.md).

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>Upgrade klientské knihovny pro úložiště na nejnovější verzi, aby se zajistila vyšší spolehlivost a výkon

Nejnovější verze sady Client Library SDK pro úložiště obsahuje opravy problémů hlášených zákazníky a proaktivní identifikaci prostřednictvím našeho procesu QA. Nejnovější verze také přináší optimalizaci spolehlivosti a výkonu společně s novými funkcemi, které mohou zlepšit vaše celkové prostředí pomocí Azure Storage. Pokud používáte starou verzi, poskytuje poradce doporučení a kroky nutné k upgradu na nejnovější verzi sady SDK. Doporučení jsou pro podporované jazyky: C++ a .NET.

## <a name="improve-app-service-performance-and-reliability"></a>Zlepšení výkonu a spolehlivosti App Service

Azure Advisor integruje doporučení pro zlepšení vašich App Servicech zkušeností a zjišťování relevantních schopností platforem. Příklady doporučení App Service:
* Detekce instancí, kde jsou prostředky paměti nebo procesoru vyčerpány modulem runtime aplikace a možnostmi zmírnění rizik.
* Detekce instancí, kde společně umístěné prostředky, jako jsou webové aplikace a databáze, může zvýšit výkon a snížit náklady.

Další informace najdete v tématu [osvědčené postupy pro Azure App Service](../app-service/app-service-best-practices.md).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Použití spravovaných disků k zabránění omezování vstupně-výstupních operací disku

Advisor identifikuje virtuální počítače, které patří do účtu úložiště, který dosahuje cíle škálovatelnosti. Tento stav způsobuje, že virtuální počítače jsou náchylné k omezování I/O. Doporučuje se, aby nástroj Advisor používal spravované disky k tomu, aby se zabránilo snížení výkonu.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Zvýšení výkonu a spolehlivosti disků virtuálních počítačů pomocí Premium Storage

Advisor identifikuje virtuální počítače se standardními disky, které mají velký objem transakcí v účtu úložiště. Doporučuje se upgradovat na disky Premium. 

Azure Premium Storage poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače, které spouští úlohy náročné na vstupně-výstupní operace. Disky virtuálních počítačů, které používají účty Premium Storage ukládají data na jednotky SSD (Solid-State Drive) (SSD). Pro dosažení nejlepšího výkonu pro vaši aplikaci doporučujeme migrovat všechny disky virtuálních počítačů, které pro Premium Storage vyžadují vysoké IOPS.

## <a name="remove-data-skew-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Odebrání zkosení dat v tabulkách Azure synapse Analytics ke zvýšení výkonu dotazů

Při spuštění úlohy může zkosit data způsobit zbytečné přesuny dat nebo kritická místa prostředků. Advisor detekuje povýšení distribučních dat o více než 15%. Doporučuje se znovu distribuovat data a znovu navštívit vaše výběry pro distribuční klíč tabulky. Další informace o tom, jak identifikovat a odebrat zkosení, najdete v tématu [řešení potíží s funkcí zkosit](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-in-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Umožňuje vytvořit nebo aktualizovat statistiku zastaralých tabulek v tabulkách Azure synapse Analytics a zvýšit tak výkon dotazů.

Poradce identifikuje tabulky, které nemají aktuální [statistiku tabulek](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md) , a doporučuje vytvořit nebo aktualizovat statistiku. Optimalizátor dotazů ve službě Azure synapse Analytics používá aktuální statistiku k odhadu mohutnosti nebo počtu řádků ve výsledcích dotazu. Tyto odhady umožňují, aby Optimalizátor dotazů vytvořil plán dotazů, který poskytuje nejrychlejší výkon.

## <a name="improve-mysql-connection-management"></a>Vylepšení správy připojení k MySQL

Analýza Advisor může znamenat, že vaše aplikace připojující se k serveru MySQL nemusí efektivně spravovat připojení. Tato podmínka by mohla vést k zbytečným spotřebám prostředků a celkové vyšší latenci aplikace. Pokud chcete zlepšit správu připojení, doporučujeme snížit počet krátkodobých připojení a eliminovat zbytečná nečinná připojení. Tato vylepšení můžete udělat konfigurací Pooler připojení na straně serveru, jako je ProxySQL.


## <a name="scale-up-to-optimize-cache-utilization-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Horizontální navýšení kapacity pro optimalizaci využití mezipaměti v tabulkách Azure synapse Analytics ke zvýšení výkonu dotazů

Azure Advisor zjistí, jestli vaše tabulky Azure synapse Analytics mají vysoké procento využití mezipaměti a procento nízkého počtu přístupů. Tato podmínka indikuje vysokou vyřazení mezipaměti, které může ovlivnit výkon vaší instance Azure synapse Analytics. Poradce doporučuje, abyste nastavili horizontální navýšení kapacity vaší instance Azure synapse Analytics, abyste zajistili dostatečnou kapacitu mezipaměti pro vaše úlohy.

## <a name="convert-azure-synapse-analytics-tables-to-replicated-tables-to-increase-query-performance"></a>Převod tabulek Azure synapse Analytics na replikované tabulky a zvýšení výkonu dotazů

Poradce identifikuje tabulky, které nejsou replikované tabulky, ale může využít převod. Navrhne, abyste tyto tabulky převedli. Doporučení jsou založená na:
- Velikost replikované tabulky 
- Počet sloupců 
- Typ distribuce tabulky 
- Počet oddílů v tabulce Azure synapse Analytics 

Další heuristiky mohou být k dispozici v doporučení pro kontext. Další informace o tom, jak se toto doporučení stanoví, najdete v tématu [doporučení ke službě Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>Pokud chcete získat nejnovější funkce Azure, migrujte svůj účet úložiště na Azure Resource Manager.

Migrujte model nasazení účtu úložiště na Azure Resource Manager, abyste mohli využít výhody:
- Nasazení šablon.
- Další možnosti zabezpečení
- Možnost upgradovat na účet GPv2, abyste mohli používat nejnovější funkce Azure Storage. 

Služba Advisor identifikuje všechny samostatné účty úložiště, které používají model nasazení Classic, a doporučuje migraci na model nasazení Správce prostředků.

> [!NOTE]
> Klasické výstrahy v Azure Monitor byly vyřazeny v srpnu 2019. Doporučujeme vám upgradovat klasický účet úložiště, aby používal Správce prostředků k uchování funkcí upozorňování s novou platformou. Další informace najdete v tématu [vyřazení klasických výstrah](../azure-monitor/alerts/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Navrhněte svoje účty úložiště, abyste zabránili dosažení maximálního limitu předplatného.

Oblast Azure podporuje maximálně 250 účtů úložiště na jedno předplatné. Po dosažení tohoto limitu nebudete moci vytvořit účty úložiště v této kombinaci oblasti nebo předplatného. Advisor kontroluje vaše předplatná a poskytuje doporučení pro návrh k menšímu počtu účtů úložiště pro každé předplatné nebo oblast, která se blíží maximálnímu limitu.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>Zvažte zvýšení velikosti VPN Gateway SKU pro řešení vysokého využití P2S

Jednotlivé skladové položky Azure VPN Gateway můžou podporovat jenom určitý počet souběžných připojení P2S. Pokud se počet připojení blíží limitu brány, další pokusy o připojení můžou selhat. Pokud zvětšíte velikost brány, budete moct podporovat více souběžných uživatelů P2S. Advisor poskytuje doporučení a pokyny pro zvýšení velikosti brány.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>Zvažte zvýšení velikosti VPN Gateway SKU pro řešení vysokého výkonu procesoru

V případě vysokého zatížení může vaše brána VPN vyřadit pakety z důvodu vysokého využití procesoru. Zvažte možnost upgradovat VPN Gateway SKU. Zvýšení velikosti brány VPN zajistí, že připojení nebudou vyřazena z důvodu vysokého využití procesoru. Poradce poskytuje doporučení k proaktivnímu řešení tohoto problému. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>Maximalizace propustnosti nahrávání, komprese dat a výkonu dotazů zvětšením velikosti dávky při nahrávání

Služba Advisor detekuje, jestli můžete zvýšit výkon a propustnost zatížení tím, že zvýšíte velikost dávky při načítání do vaší databáze. Můžete zvážit použití příkazu COPY. Pokud nemůžete použít příkaz COPY, zvažte zvýšení velikosti dávky při použití nástrojů načítání, jako je SQLBulkCopy API nebo BCP. Dobrým obecným pravidlem je použití velikosti dávky mezi 100 000 a 1 000 000 řádky. Zvýšení velikosti dávky zvýší propustnost zatížení, kompresi dat a výkon dotazů.

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>Společně umístit účet úložiště ve stejné oblasti, aby se minimalizovala latence při načítání

Advisor detekuje, jestli načítáte z oblasti, která se liší od vyhrazeného fondu SQL. Zvažte načtení z účtu úložiště, který je ve stejné oblasti jako vyhrazený fond SQL, aby se minimalizovala latence při načítání dat. Tato změna vám pomůže minimalizovat latenci a zvýšit výkon zatížení.

## <a name="use-a-supported-kubernetes-version"></a>Použít podporovanou verzi Kubernetes

Advisor detekuje nepodporované verze Kubernetes.

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>Optimalizace výkonu Azure Database for MySQL, Azure Database for PostgreSQL a Azure Database for MariaDB serverů

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>Opravte tlak procesoru vašich Azure Database for MySQL, Azure Database for PostgreSQL a Azure Database for MariaDB serverů s kritickými nároky na procesor.
Vysoké využití procesoru po delší dobu může způsobit pomalý výkon pro vaše úlohy. Zvýšení velikosti procesoru vám pomůže optimalizovat modul runtime databázových dotazů a zvýšit celkový výkon. Advisor identifikuje servery s vysokým využitím procesoru, které by zřejmě běžely s využitím procesoru s omezenou kapacitou, a doporučuje škálovat výpočetní výkon.

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Snížení omezení paměti na vašich Azure Database for MySQL, Azure Database for PostgreSQL a Azure Database for MariaDB serverech nebo přesun do paměťově optimalizované SKU
Poměr přístupů do mezipaměti může mít za následek pomalejší výkon dotazů a zvýšené IOPS. Tato podmínka může být způsobena nesprávným plánem dotazů nebo úlohou náročnými na paměť. Oprava plánu dotazů nebo [zvýšení paměti](../postgresql/concepts-pricing-tiers.md) Azure Database for PostgreSQL, Azure Database for MySQL nebo Azure Database for MariaDB Server, pomůže optimalizovat provádění databázových úloh. Azure Advisor identifikuje servery ovlivněné touto vysokou změnou fondu vyrovnávací paměti. Doporučuje se provést jednu z následujících akcí: 
- Oprava plánu dotazu
- Přejít na SKU, která má více paměti 
- Zvyšte velikost úložiště a získejte více IOPS.

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Použití Azure Database for MySQL nebo Azure Database for PostgreSQL čtení repliky k horizontálnímu navýšení kapacity pro úlohy náročné na čtení
Advisor používá heuristiky založené na úlohách, jako je poměr čtení a zápisů na serveru během posledních sedmi dnů, pro identifikaci úloh náročných na čtení. Azure Database for PostgreSQL nebo Azure Database for MySQL prostředek s vysokým poměrem pro čtení a zápis může vést ke vzniku kolizí s PROCESORem nebo pamětí a vést k zpomalení výkonu dotazů. Přidání [repliky](../postgresql/howto-read-replicas-portal.md) pomůže škálovat čtení na server repliky a zabránit omezením na procesor nebo paměť na primárním serveru. Poradce identifikuje servery s úlohami náročnými na čtení a doporučuje přidat [repliku čtení](../postgresql/concepts-read-replicas.md) pro přesměrování některých úloh čtení.


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Škálování Azure Database for MySQL, Azure Database for PostgreSQL nebo Azure Database for MariaDB serveru na vyšší SKLADOVOU položku, aby nedocházelo k omezením připojení
Každé nové připojení k vašemu databázovému serveru zabírá paměť. Pokud dojde k selhání připojení k serveru z důvodu [horního limitu](../postgresql/concepts-limits.md) paměti, výkon databázového serveru se sníží. Azure Advisor identifikuje servery běžící s mnoha selháními připojení. Doporučuje se upgradovat limity pro připojení vašeho serveru a poskytnout tak více paměti serveru tím, že provedete jednu z následujících akcí:
- Horizontální navýšení kapacity 
- Používejte paměťově optimalizované skladové položky, které mají více výpočetních prostředků na jádro.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Škálovat mezipaměť na jinou velikost nebo SKU pro zlepšení výkonu mezipaměti a aplikace

Instance mezipaměti fungují nejlépe, když neběží s vysokým tlakem paměti, vysokým zatížením serveru nebo velkou šířkou pásma sítě. Tyto podmínky můžou způsobit, že se přestane reagovat, dojde ke ztrátě dat nebo nebude k dispozici. Poradce identifikuje instance mezipaměti v těchto podmínkách. Doporučuje se provést jednu z následujících akcí:
- Použijte osvědčené postupy pro snížení zatížení paměti, zatížení serveru nebo šířky pásma sítě.
- Škálujte na jinou velikost nebo SKU, které mají větší kapacitu.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Přidání oblastí s provozem do účtu Azure Cosmos DB

Advisor detekuje účty Azure Cosmos DB, které mají provoz z oblasti, která není aktuálně nakonfigurovaná. Doporučuje přidání této oblasti. Tím se zlepšuje latence pro žádosti přicházející z této oblasti a v případě výpadků oblastí zajišťuje dostupnost. [Přečtěte si další informace o globální distribuci dat pomocí Azure Cosmos DB.](../cosmos-db/distribute-data-globally.md)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>Konfigurace zásad indexování Azure Cosmos DB pomocí vlastních zahrnutých nebo vyloučených cest

Advisor identifikuje kontejnery Azure Cosmos DB, které používají výchozí zásady indexování, ale můžou využít vlastní zásady indexování. Toto určení je založené na vzoru úloh. Výchozí zásada indexování indexuje všechny vlastnosti. Vlastní zásada indexování s explicitními zahrnutými nebo vyloučenými cestami, které se používají ve filtrech dotazů, může snížit ru a úložiště spotřebované při indexování. [Přečtěte si další informace o změnách zásad indexování.](../cosmos-db/index-policy.md)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Nastavení velikosti stránky dotazu na Azure Cosmos DB (MaxItemCount) na hodnotu-1 

Azure Advisor identifikuje kontejnery Azure Cosmos DB, které používají velikost stránky dotazu 100. Pro rychlejší kontroly doporučuje použití velikosti stránky-1. [Přečtěte si další informace o MaxItemCount.](../cosmos-db/sql-api-query-metrics.md)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>Zvažte použití funkce akcelerovaného zápisu v clusteru HBA pro zlepšení výkonu clusteru.
Analýza Azure Advisor v systémových protokolech za posledních 7 dnů a zjistí, jestli cluster zjistil následující scénáře:
1. Vysoká latence z hlediska času synchronizace WAL 
2. Vysoký počet žádostí o zápis (minimálně 3 intervaly v délce 1 hodiny s více než 1000 žádostí o zápis (prům.)/sekunda/uzel)

Tato zjištění indikují, že cluster trpí vysokou latencí při zapisování. Důvodem může být to, že ve vašem clusteru bude provedeno velké zatížení. Pokud chcete zvýšit výkon clusteru, můžete zvážit použití funkce akcelerovaného zápisu, kterou poskytuje služba Azure HDInsight HBA. Funkce Akcelerované zápisy pro clustery HDInsight Apache HBase připojuje prémiové disky spravované přes SSD ke každému pracovnímu uzlu (RegionServer) místo použití cloudového úložiště. Ve výsledku pak pro aplikace zajišťuje nízkou latenci při zapisování a lepší odolnost. Další informace o této funkci najdete v článku [Další](../hdinsight/hbase/apache-hbase-accelerated-writes.md#how-to-enable-accelerated-writes-for-hbase-in-hdinsight)

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>Kontrola mezipaměti Azure Průzkumník dat cache – perioda (zásady) pro lepší výkon (Preview)
Toto doporučení ukazuje tabulky Azure Data Exploreru s velkým počtem dotazů, které se dívají nazpět před nakonfigurovanou dobu (zásady) uložení v mezipaměti (zobrazí se 10 tabulek s nejvyšším procentem dotazů, které přistupují k datům mimo mezipaměť). Doporučená akce pro zvýšení výkonu clusteru: Omezte dotazy na tuto tabulku na minimální nezbytný časový rozsah (v rámci definovaných zásad). Případně, pokud se vyžadují data z celého časového rozsahu, prodlužte dobu uložení v mezipaměti na doporučenou hodnotu.

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>Zvýšení výkonu optimalizací velikosti dočasné tabulky MySQL
Analýza Advisor indikuje, že váš server MySQL může vydávat zbytečné vstupně-výstupní režii kvůli nízkým nastavením parametrů pro dočasné tabulky. To může vést ke zbytečným transakcím na disku a snížení výkonu. Pokud chcete snížit počet transakcí na disku, doporučujeme zvýšit hodnoty parametrů tmp_table_size a max_heap_table_size. [Další informace](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>Distribuce dat ve skupině serverů za účelem distribuce zatížení mezi uzly
Poradce identifikuje skupiny serverů, na kterých nebyla data distribuována, ale zůstává v koordinátorovi. Na základě tohoto doporučení doporučuje poradce, aby Citus výhody distribuce dat na pracovních uzlech pro skupiny serverů. Tím se vylepšit výkon dotazů pomocí prostředku každého uzlu ve skupině serverů. [Další informace](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="improve-user-experience-and-connectivity-by-deploying-vms-closer-to-windows-virtual-desktop-deployment-location"></a>Vylepšení uživatelského prostředí a připojení díky nasazení virtuálních počítačů blíže k umístění nasazení virtuálního klienta Windows
Zjistili jsme, že se vaše virtuální počítače nacházejí v jiné oblasti, než ze které se připojují vaši uživatelé pomocí služby Windows Virtual Desktop, nebo v příliš vzdálené oblasti. To může způsobit delší dobu odezvy připojení a ovlivnit celkové uživatelské prostředí služby WVD. Při vytváření virtuálních počítačů pro fondy hostitelů byste se měli snažit používat oblasti blíže uživatelům. Těsná blízkost zajistí trvalou spokojenost se službou WVD a celkově vyšší kvalitu prostředí. [Další informace o latenci připojení najdete tady](../virtual-desktop/connection-latency.md).

## <a name="upgrade-to-the-latest-version-of-the-immersive-reader-sdk"></a>Upgrade na nejnovější verzi sady SDK Asistivní čtečky
V tomto předplatném jsme identifikovali prostředky využívající zastaralé verze sady SDK Asistivní čtečky. Použitím nejnovější verze sady SDK Asistivní čtečky získáte aktualizované funkce zabezpečení, funkce pro zajištění výkonu a rozšířenou sadu funkcí pro přizpůsobení a vylepšení prostředí integrace.
Přečtěte si další informace o [sadě moderního čtecího zařízení](../cognitive-services/immersive-reader/index.yml).

## <a name="improve-vm-performance-by-changing-the-maximum-session-limit"></a>Zvýšení výkonu virtuálních počítačů změnou maximálního limitu relací

Advisor detekuje, že máte fond hostitelů, který má nejprve nastavenou hloubku jako algoritmus vyrovnávání zatížení, a maximální počet relací fondu hostitelů je větší nebo roven 999999. Při vyrovnávání zatížení do hloubky se na základě limitu maximálního počtu relací určuje maximální počet uživatelů, kteří můžou mít souběžné relace s jedním hostitelem relací. Pokud je maximální počet relací příliš vysoký, budou všechny uživatelské relace směrovány na stejného hostitele relace a to způsobí problémy s výkonem a spolehlivostí. Proto když nakonfigurujete fond hostitelů tak, aby měl při prvním vyrovnávání zatížení určitou hloubku, musíte nastavit vhodný maximální počet relací podle konfigurace nasazení a kapacity virtuálních počítačů. 

Další informace o vyrovnávání zatížení na virtuálním počítači s Windows najdete v tématu [Konfigurace metody vyrovnávání zatížení pro virtuální počítače s Windows](../virtual-desktop/troubleshoot-set-up-overview.md).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Jak získat přístup k doporučením k výkonu v Advisoru

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pak otevřete [Poradce](https://aka.ms/azureadvisordashboard).

2.  Na řídicím panelu Advisor vyberte kartu **výkon** .

## <a name="next-steps"></a>Další kroky

Další informace o doporučení Advisoru najdete tady:

* [Úvod do Advisoru](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Skóre Advisoru](azure-advisor-score.md)
* [Doporučení Advisoru týkající se cen](advisor-cost-recommendations.md)
* [Doporučení pro spolehlivost Advisoru](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
* [REST API Advisoru](/rest/api/advisor/)