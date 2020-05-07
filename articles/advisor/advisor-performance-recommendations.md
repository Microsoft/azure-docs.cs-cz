---
title: Zlepšení výkonu aplikací Azure pomocí Azure Advisor
description: Využijte poradce k optimalizaci výkonu nasazení Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: ff9b8fb9494c887397947f009b22cdc89d8f70b5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787936"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Zlepšení výkonu aplikací Azure pomocí Azure Advisor

Azure Advisor doporučení k výkonu pomáhá vylepšit rychlost a rychlost reakce vašich důležitých podnikových aplikací. Doporučení k výkonu můžete získat od služby Advisor na kartě **výkon** na řídicím panelu poradce.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Omezte čas DNS na živý v profilu Traffic Manager, aby se převzaly služby při selhání do funkčních koncových bodů rychleji

[Nastavení TTL (Time to Live)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) v profilu Traffic Manager vám umožní určit, jak rychle se mají přepnout koncové body, pokud daný koncový bod přestane reagovat na dotazy. Snížení hodnot TTL znamená, že klienti budou směrováni do funkčních koncových bodů rychleji.

Azure Advisor identifikuje profily Traffic Manager s delší hodnotou TTL nakonfigurovanou a doporučuje nakonfigurovat hodnotu TTL na 20 sekund nebo 60 sekund v závislosti na tom, jestli je profil nakonfigurovaný pro [rychlé převzetí služeb při selhání](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Zlepšení výkonu databáze s využitím funkce SQL DB Advisor

Advisor nabízí konzistentní a konsolidované zobrazení doporučení pro všechny prostředky Azure. Integruje se s SQL Database Advisor a dává vám doporučení pro zlepšení výkonu databáze SQL Azure.SQL Database Advisor vyhodnocuje výkon vašich SQL Azure databází analýzou historie využití. Potom nabízí doporučení, která jsou nejvhodnější pro spuštění typického zatížení databáze.

> [!NOTE]
> Aby bylo možné získat doporučení, musí mít databáze přibližně týden využití a v tomto týdnu musí být konzistentní aktivita. SQL Database Advisor se dá snadněji optimalizovat pro konzistentní vzory dotazů, než pro náhodné nárůsty aktivity.

Další informace o SQL Database Advisor najdete v tématu [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>Vyšší spolehlivost a výkon díky upgradu klientské knihovny služby Storage na nejnovější verzi

Nejnovější verze sady SDK a klientské knihovny služby Storage obsahuje opravy chyb nahlášených zákazníky a aktivně zjištěných v rámci procesu kontroly kvality. Nejnovější verze kromě nových funkcí přináší i optimalizaci spolehlivosti a výkonu, která může zlepšit celkové zkušenosti s používáním služby Azure Storage. Poradce vám poskytne doporučení a kroky pro upgrade na nejnovější verzi sady SDK, pokud používáte starou verzi. Doporučení jsou pro podporované jazyky – C++ a .NET.

## <a name="improve-app-service-performance-and-reliability"></a>Zlepšení výkonu a spolehlivosti App Service

Azure Advisor integruje doporučení osvědčených postupů pro zlepšení App Servicesho prostředí a zjišťování relevantních schopností platforem. Příklady doporučení App Services:
* Detekce instancí, kde jsou prostředky paměti nebo procesoru vyčerpány modulem runtime aplikací s možnostmi zmírnění rizik.
* Detekce instancí, kde Collocating prostředky, jako jsou webové aplikace a databáze, může zvýšit výkon a snížit náklady.

Další informace o App Services doporučeních najdete v tématu [osvědčené postupy pro Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Zabránění omezování vstupně-výstupních operací disku pomocí Managed Disks

Advisor rozpozná virtuální počítače, které patří do účtu úložiště, který dosahuje cíle škálovatelnosti. Tento stav způsobuje, že virtuální počítače jsou náchylné k omezování I/O. Poradce doporučuje, aby používal Managed Disks k tomu, aby se zabránilo snížení výkonu.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Zvýšení výkonu a spolehlivosti disků virtuálních počítačů pomocí Premium Storage

Advisor identifikuje virtuální počítače se standardními disky, které mají ve vašem účtu úložiště velký objem transakcí a doporučuje upgradovat na disky Premium. 

Azure Premium Storage poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače, které spouští úlohy náročné na vstupně-výstupní operace. Disky virtuálních počítačů, které používají účty Premium Storage, ukládají data na jednotky SSD (Solid-State Drive) (SSD). Pro dosažení nejlepšího výkonu pro vaši aplikaci doporučujeme migrovat všechny disky virtuálních počítačů vyžadující vysoké IOPS do služby Premium Storage.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Odebrání zkosení dat v tabulce SQL Data Warehouse pro zvýšení výkonu dotazů

Při spuštění úlohy může zkosit data způsobit zbytečné přesuny dat nebo kritická místa prostředků. Poradce detekuje data distribuce větší než 15% a doporučuje vám, abyste data znovu rozšíříte a znovu navštívili výběry pro distribuční klíč tabulky. Další informace o tom, jak identifikovat a odebrat zkosení, najdete v tématu [řešení potíží s funkcí zkosit](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Umožňuje vytvořit nebo aktualizovat statistiku zastaralých tabulek v tabulce SQL Data Warehouse a zvýšit tak výkon dotazů.

Poradce identifikuje tabulky, které nemají aktuální [statistiku tabulek](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) , a doporučuje vytvořit nebo aktualizovat statistiku tabulky. Optimalizátor dotazů SQL Data Warehouse používá aktuální statické a odhadované mohutnosti nebo počet řádků ve výsledku dotazu, který umožňuje optimalizátoru dotazů vytvořit vysoce kvalitní plán dotazů pro nejrychlejší výkon.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Horizontální navýšení kapacity pro optimalizaci využití mezipaměti v tabulkách SQL Data Warehouse pro zvýšení výkonu dotazů

Azure Advisor zjistí, zda má SQL Data Warehouse procento využité mezipaměti a nízké procento úspěšnosti. Tato podmínka indikuje vysokou vyřazení mezipaměti, což může mít vliv na výkon vašeho SQL Data Warehouse. Poradce vám navrhne, abyste nastavili horizontální navýšení kapacity SQL Data Warehouse a zajistili tak dostatečnou kapacitu mezipaměti pro vaše úlohy.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Převod tabulek SQL Data Warehouse na replikované tabulky a zvýšení výkonu dotazů

Poradce identifikuje tabulky, které nejsou replikované, ale bude výhodná konverze a navrhne, abyste tyto tabulky převedli. Doporučení jsou založená na velikosti replikované tabulky, počtu sloupců, typu distribuce tabulky a počtu oddílů SQL Data Warehouse tabulky. Další heuristiky mohou být k dispozici v doporučení pro kontext. Další informace o tom, jak se toto doporučení stanoví, najdete v tématu [SQL Data Warehouse doporučení](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Pokud chcete získat všechny nejnovější funkce Azure, migrujte svůj účet úložiště na Azure Resource Manager.

Migrujte model nasazení účtu úložiště na Azure Resource Manager (Správce prostředků), abyste mohli využít výhod nasazení šablon, dalších možností zabezpečení a možnosti upgradovat na účet GPv2, abyste mohli využívat nejnovější funkce Azure Storage. Poradce bude identifikovat všechny samostatné účty úložiště, které používají model nasazení Classic a doporučuje migraci na model nasazení Správce prostředků.

> [!NOTE]
> Klasické výstrahy v Azure Monitor byly vyřazeny ze srpna 2019. Doporučujeme vám upgradovat klasický účet úložiště, aby používal Správce prostředků k uchování funkcí upozorňování s novou platformou. Další informace najdete v tématu [vyřazení klasických výstrah](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Návrh účtů úložiště, aby nedošlo k překročení maximálního limitu předplatného

Oblast Azure může podporovat maximálně 250 účtů úložiště na jedno předplatné. Po dosažení tohoto limitu nebudete moct vytvořit žádné další účty úložiště v této kombinaci oblasti nebo předplatného. Advisor zkontroluje vaše předplatná a doporučení pro plochu, abyste mohli navrhovat méně účtů úložiště pro všechny, které jsou blízko dosažení maximálního limitu.

## <a name="consider-increasing-the-size-of-your-vnet-gateway-sku-to-adress-high-p2s-use"></a>Zvažte zvýšení velikosti SKU brány virtuální sítě na adresu vysoká P2S využití.

Každá SKU brány může podporovat jenom zadaný počet souběžných připojení P2S. Pokud se počet připojení blíží limitu brány, může dojít k selhání dalších pokusů o připojení. Zvýšení velikosti brány vám umožní podporovat více souběžných uživatelů P2S. Advisor poskytuje doporučení a kroky, které je potřeba provést.

## <a name="consider-increasing-the-size-of-your-vnet-gateway-sku-to-address-high-cpu"></a>Zvažte zvýšení velikosti SKU brány virtuální sítě na řešení vysokého výkonu procesoru.

V případě vysokého zatížení může Brána sítě VPN vyřadit pakety z důvodu vysokého využití procesoru. Měli byste zvážit upgrade VPN Gateway SKU, protože vaše síť VPN byla konzistentně spuštěna v. Zvětšením velikosti brány VPN se zajistí, že připojení nebudou vyřazena z důvodu vysokého využití procesoru. Doporučení provdes Advisoru k proaktivnímu řešení tohoto problému. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>Zvýšit velikost dávky při načítání pro maximalizaci propustnosti zatížení, komprimace dat a výkonu dotazů

Služba Advisor dokáže zjistit, že můžete zvýšit výkon a propustnost zatížení tím, že zvýšíte velikost dávky při načítání do vaší databáze. Můžete zvážit použití příkazu COPY. Pokud nemůžete použít příkaz COPY, zvažte zvýšení velikosti dávky při použití nástrojů načítání nástrojů, jako je SQLBulkCopy API nebo BCP – dobré pravidlo pro palec je velikost dávky mezi 100 tisíc a 1 milionem řádků. Tím se zvýší propustnost zatížení, komprese dat a výkon dotazů.

## <a name="co-locate-the-storage-account-within-the-same-region-to-minimize-latency-when-loading"></a>Společně umístit účet úložiště ve stejné oblasti, aby se minimalizovala latence při načítání

Poradce dokáže zjistit, že načítáte z oblasti, která se liší od vašeho fondu SQL. Měli byste zvážit načtení z účtu úložiště, který je ve stejné oblasti jako váš fond SQL, abyste minimalizovali latenci při načítání dat. To vám pomůže minimalizovat latenci a zvýšit výkon zatížení.

## <a name="unsupported-kubernetes-version-is-detected"></a>Zjistila se Nepodporovaná verze Kubernetes.

Poradce dokáže zjistit, jestli je zjištěna nepodporovaná verze Kubernetes. Doporučení vám pomůže zajistit, aby cluster Kubernetes běžel s podporovanou verzí.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Optimalizujte výkon svých serverů Azure MySQL, Azure PostgreSQL a Azure MariaDB 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Oprava zatížení procesoru v Azure MySQL, Azure PostgreSQL a serverech Azure MariaDB s kritickými body procesoru
Velmi vysoké využití procesoru po delší dobu může způsobit pomalý výkon pro vaše úlohy. Zvýšení velikosti procesoru vám pomůže optimalizovat modul runtime databázových dotazů a zvýšit celkový výkon. Azure Advisor bude identifikovat servery s vysokým využitím procesoru, které by mohly provozovat omezené zatížení procesoru, a doporučit škálování výpočetních prostředků.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Snížení omezení paměti na serverech Azure MySQL, Azure PostgreSQL a Azure MariaDB nebo přesun do paměťově optimalizované SKU
Poměr přístupů do mezipaměti může mít za následek pomalejší výkon dotazů a zvýšené IOPS. Příčinou může být špatný plán dotazů nebo spuštění úlohy náročné na paměť. Řešení plánu dotazů nebo [zvýšení paměti](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) Azure Database for PostgreSQL databázového serveru, serveru Azure MySQL nebo serveru Azure MariaDB vám pomůže optimalizovat provádění databázových úloh. Azure Advisor identifikuje servery ovlivněné z důvodu této vysoké změny fondu vyrovnávací paměti a doporučuje buď opravit plán dotazů, přejít na vyšší SKU s větší pamětí nebo zvýšit velikost úložiště a získat tak více IOPS.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Použití Azure MySQL nebo repliky pro čtení Azure PostgreSQL k horizontálnímu navýšení kapacity pro úlohy náročné na čtení
Azure Advisor využívá heuristiky založené na úlohách, jako je poměr čtení a zápisů na serveru za posledních sedm dní k identifikaci úloh náročných na čtení. Prostředek Azure Database for PostgreSQL nebo Azure Database for MySQL s velmi vysokým poměrem pro čtení a zápis může vést k tomu, že by došlo k zpomalení výkonu procesoru nebo paměti. Přidání [repliky](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) vám pomůže škálovat čtení na server repliky a zabránit omezením procesoru nebo paměti na primárním serveru. Advisor bude identifikovat servery s těmito vysokými úlohami náročnými na čtení a doporučit přidání  [repliky pro čtení](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas), aby bylo možné přesměrovat některé úlohy čtení.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Škálujte svůj server Azure MySQL, Azure PostgreSQL nebo Azure MariaDB na vyšší SKU, aby nedocházelo k omezením připojení.
Každé nové připojení k vašemu databázovému serveru zabírá určitou paměť. Pokud dojde k selhání připojení k serveru z důvodu [horního limitu](https://docs.microsoft.com/azure/postgresql/concepts-limits) paměti, výkon databázového serveru se sníží. Azure Advisor bude identifikovat servery, na kterých běží s mnoha selháními připojení, a doporučit upgrade limitů připojení serveru, aby bylo možné zajistit větší množství paměti pro váš server, a to tak, že se škáluje výpočetní výkon nebo používá paměťově optimalizované skladové položky, které mají pro

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Škálovat mezipaměť na jinou velikost nebo SKU pro zlepšení výkonu mezipaměti a aplikace

Instance mezipaměti fungují nejlépe, když neběží s vysokým tlakem paměti, vysokým zatížením serveru nebo velkou šířkou pásma sítě, což by mohlo způsobit, že se data nereagují, dojde ke ztrátě dat nebo k nedostupnosti. Advisor bude v těchto podmínkách identifikovat instance mezipaměti a doporučuje použít osvědčené postupy ke snížení zatížení paměti, zatížení serveru nebo šířky pásma sítě nebo škálování na jinou velikost nebo SKU s větší kapacitou.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Přidání oblastí s provozem do účtu Azure Cosmos DB

Poradce detekuje účty Azure Cosmos DB, které mají provoz z oblasti, která není aktuálně nakonfigurovaná, a doporučuje přidání této oblasti. Tím se vylepšit latence pro žádosti přicházející z této oblasti a bude zajištěna dostupnost v případě výpadků oblastí. [Další informace o globální distribuci dat pomocí Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Konfigurace zásad indexování Azure Cosmos DB pomocí zahrnutých nebo vyloučených cest zákazníka

Azure Advisor určí Cosmos DB kontejnery, které používají výchozí zásady indexování, ale můžou využít vlastní zásadu indexování založenou na vzorcích úloh. Výchozí zásada indexování indexuje všechny vlastnosti, ale použití vlastních zásad indexování s explicitními zahrnutými nebo vyloučenými cestami, které se používají ve filtrech dotazů, může snížit ru a úložiště spotřebované při indexování. [Další informace o změnách zásad indexování](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Konfigurace velikosti stránky dotazu Azure Cosmos DB (MaxItemCount) na -1 

Azure Advisor určí Azure Cosmos DB kontejnery, které používají velikost stránky dotazu 100, a doporučuje použití velikosti stránky-1 pro rychlejší kontroly. [Další informace o maximálním počtu položek](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Jak získat přístup k doporučením k výkonu v Advisoru

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pak otevřete [Poradce](https://aka.ms/azureadvisordashboard).

2.  Na řídicím panelu Advisor klikněte na kartu **výkon** .

## <a name="next-steps"></a>Další kroky

Další informace o doporučení Advisoru najdete tady:

* [Úvod do Advisoru](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Doporučení pro náklady na poradce](advisor-cost-recommendations.md)
* [Doporučení pro vysokou dostupnost poradce](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
