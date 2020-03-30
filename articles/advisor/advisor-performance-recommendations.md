---
title: Zlepšení výkonu aplikací Azure pomocí Azure Advisoru
description: Pomocí poradce optimalizujte výkon nasazení Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 405ec395feeb33b8511b9b915151b2ed9503c371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443059"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Zlepšení výkonu aplikací Azure pomocí Azure Advisoru

Doporučení pro výkon Azure Advisor pomáhají zlepšit rychlost a odezvu vašich důležitých podnikových aplikací. Doporučení k výkonu můžete získat od poradce na kartě **Výkon** na řídicím panelu Poradce.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Zkraťte dobu dns pro život v profilu Traffic Manageru a rychleji přejit služby při selhání do zdravých koncových bodů

[Nastavení Time to Live (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) v profilu Traffic Manageru umožňuje určit, jak rychle se mají přepínat koncové body, pokud daný koncový bod přestane odpovídat na dotazy. Snížení hodnot TTL znamená, že klienti budou směrovány do fungujícíkoncové body rychleji.

Azure Advisor identifikuje profily Traffic Manageru s delší mnostvím TTL a doporučuje konfiguraci TTL na 20 sekund nebo 60 sekund v závislosti na tom, jestli je profil nakonfigurován pro [rychlé převzetí služeb při selhání](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Zlepšení výkonu databáze s využitím funkce SQL DB Advisor

Poradce vám poskytuje konzistentní a konsolidované zobrazení doporučení pro všechny vaše prostředky Azure. Integruje se s SQL Database Advisor, aby vám doporučení pro zlepšení výkonu databáze SQL Azure.SQL Database Advisor vyhodnotí výkon databází SQL Azure analýzou historie využití. Potom nabízí doporučení, které jsou nejvhodnější pro spuštění typické úlohy databáze.

> [!NOTE]
> Chcete-li získat doporučení, databáze musí mít asi týden používání a v rámci tohoto týdne musí být některé konzistentní aktivity. SQL Database Advisor můžete optimalizovat snadněji pro konzistentní vzorky dotazů než pro náhodné shluky aktivity.

Další informace o nástroji SQL Database Advisor naleznete v [tématu SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Zlepšete výkon a spolehlivost služby App Service

Azure Advisor integruje doporučení osvědčených postupů pro zlepšení vašeho prostředí pro služby App Services a zjišťování příslušných funkcí platformy. Příklady doporučení pro služby App Services jsou:
* Detekce instancí, kde jsou paměť nebo prostředky procesoru vyčerpány runtimes aplikace s možnostmi zmírnění.
* Detekce instancí, kde scvokování prostředků, jako jsou webové aplikace a databáze, může zlepšit výkon a snížit náklady.

Další informace o doporučeních služby App Services najdete [v tématu Doporučené postupy pro Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Použití spravovaných disků k zabránění omezení vstupně-nosů disku

Poradce identifikuje virtuální počítače, které patří do účtu úložiště, který dosahuje svého cíle škálovatelnosti. Tento stav umožňuje tyto virtuální chod y náchylné k omezení vstupně-no. Poradce jim doporučí, aby používali spravované disky, aby zabránili snížení výkonu.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Zlepšete výkon a spolehlivost disků virtuálních počítačů pomocí služby Premium Storage

Advisor identifikuje virtuální počítače se standardními disky, které mají velký objem transakcí na vašem účtu úložiště a doporučuje upgrade na disky premium. 

Azure Premium Storage poskytuje vysoce výkonnou diskovou podporu s nízkou latencí pro virtuální počítače, ve kterých běží úlohy náročné na vstupně-výstupní služby. Disky virtuálních počítačů, které používají účty úložiště premium, ukládají data na ssd discích.Virtual machine disky that use premium storage accounts store store data on SSD dis Chcete-li dosáhnout nejlepšího výkonu pro vaši aplikaci, doporučujeme migrovat všechny disky virtuálních počítačů, které vyžadují vysoké viposlužby, do úložiště premium.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Odebrání zkosení dat v tabulce datového skladu SQL pro zvýšení výkonu dotazu

Zkosení dat může způsobit zbytečné přesundat nebo problémová místa prostředků při spuštění úlohy. Poradce zjistí zkosení distribučních dat větší než 15 % a doporučí, abyste data znovu distribuovali a znovu se vrátili k výběru distribučního klíče tabulky. Další informace o identifikaci a odebrání zkosení najdete [v tématu řešení potíží se zkosením](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Vytvoření nebo aktualizace zastaralých statistik tabulky v tabulce datového skladu SQL pro zvýšení výkonu dotazu

Advisor identifikuje tabulky, které nemají aktuální [statistiky tabulek](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) a doporučuje vytvořit nebo aktualizovat statistiky tabulky. Optimalizace dotazů datového skladu SQL používá aktuální statice k odhadu mohutnost nebo počet řádků ve výsledku dotazu, který umožňuje optimalizaci dotazů vytvořit vysoce kvalitní plán dotazů pro nejrychlejší výkon.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Škálování nahoru pro optimalizaci využití mezipaměti v tabulkách datového skladu SQL pro zvýšení výkonu dotazů

Azure Advisor zjistí, jestli váš SQL Data Warehouse má vysoké procento použití mezipaměti a nízké procento přístupů. Tato podmínka označuje vysoké mezipaměti vyřazovací služby, což může mít vliv na výkon vašeho datového skladu SQL. Advisor navrhuje škálovat sql datový sklad, abyste zajistili přidělení dostatečné kapacity mezipaměti pro vaše úlohy.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Převod tabulek datového skladu SQL na replikované tabulky pro zvýšení výkonu dotazu

Advisor identifikuje tabulky, které nejsou replikované tabulky, ale bude mít prospěch z převodu a navrhuje převést tyto tabulky. Doporučení jsou založena na velikosti replikované tabulky, počtu sloupců, typu distribuce tabulky a počtu oddílů tabulky SQL Data Warehouse. Další heuristiky mohou být uvedeny v doporučení pro kontext. Další informace o tom, jak je toto doporučení určeno, naleznete v [tématu Doporučení datového skladu SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrace účtu úložiště do Správce prostředků Azure a získejte všechny nejnovější funkce Azure

Migrujte model nasazení účtu úložiště do Správce prostředků Azure (Správce prostředků) a využijte využití nasazení šablon, dalších možností zabezpečení a možnosti upgradu na účet GPv2 pro využití nejnovějších funkcí Azure Storage. Poradce identifikuje všechny účty samostatného úložiště, které používají model klasického nasazení, a doporučuje migraci do modelu nasazení Správce prostředků.

> [!NOTE]
> Klasické výstrahy ve službě Azure Monitor byly vyřazeny v srpnu 2019. Doporučujeme upgradovat svůj klasický účet úložiště tak, aby pomocí Správce prostředků zachoval funkce upozornění s novou platformou. Další informace naleznete v [tématu Classic Alerts Retirement](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Navrhněte účty úložiště, abyste zabránili dosažení maximálního limitu předplatného

Oblast Azure může podporovat maximálně 250 účtů úložiště na jedno předplatné. Po dosažení limitu nebudete moci vytvořit žádné další účty úložiště v této kombinaci oblasti/předplatného. Poradce zkontroluje vaše předplatná a doporučení povrchu, abyste měli navrhnout méně účtů úložiště pro všechny, které se blíží dosažení maximálního limitu.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Optimalizace výkonu serverů Azure MySQL, Azure PostgreSQL a Azure MariaDB 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Oprava tlaku procesoru serverů Azure MySQL, Azure PostgreSQL a Azure MariaDB pomocí kritických bodů procesoru
Velmi vysoké využití procesoru po delší dobu může způsobit pomalý výkon dotazu pro vaše úlohy. Zvětšení velikosti procesoru pomůže optimalizovat běhový čas databázových dotazů a zlepšit celkový výkon. Azure Advisor identifikuje servery s vysokým využitím procesoru, které pravděpodobně běží úlohy omezené na procesor a doporučuje škálování výpočetních prostředků.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Snižte omezení paměti na serverech Azure MySQL, Azure PostgreSQL a Azure MariaDB nebo přejděte na skladovou položku optimalizovanou pro paměť
Nízký poměr přístupů do mezipaměti může mít za následek pomalejší výkon dotazu a zvýšené viposlužby. To může být způsobeno chybný plán dotazu nebo spuštění úlohy náročné na paměť. Oprava plánu dotazů nebo [zvětšení paměti](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) databázového serveru Azure Database for PostgreSQL, databázového serveru Azure MySQL nebo serveru Azure MariaDB pomůže optimalizovat provádění úlohy databáze. Azure Advisor identifikuje servery ovlivněné z důvodu této změny fondu vysoké vyrovnávací paměti konve a doporučuje buď opravu plánu dotazu, přechod na vyšší skladovou položku s větší pamětí nebo zvýšení velikosti úložiště získat více videa videa.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Použití repliky čtení Azure MySQL nebo Azure PostgreSQL pro škálování čtení pro úlohy náročné na čtení
Azure Advisor využívá heuristiky založené na úlohách, jako je poměr čtení a zápisů na serveru za posledních sedm dní k identifikaci úloh náročných na čtení. Vaše databáze Azure pro prostředek PostgreSQL nebo databáze Azure pro prostředek MySQL s velmi vysokým poměrem čtení a zápisů může mít za následek konflikty procesoru nebo paměti, což vede ke snížení výkonu dotazu. Přidání [repliky](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) pomůže při horizontálním navýšení kapacity čtení na server repliky, brání cpu nebo omezení paměti na primárním serveru. Poradce identifikuje servery s tak vysokými úlohami náročnými na čtení a doporučuje přidání [repliky](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) pro čtení, aby se některé úlohy čtení přetížily.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Škálování serveru Azure MySQL, Azure PostgreSQL nebo Azure MariaDB na vyšší skladovou položku, abyste zabránili omezením připojení
Každé nové připojení k databázovému serveru zabírá určitou paměť. Výkon databázového serveru se snižuje, pokud připojení k serveru selhávají z důvodu [horního limitu](https://docs.microsoft.com/azure/postgresql/concepts-limits) paměti. Azure Advisor identifikuje servery spuštěné s mnoha chybami připojení a doporučuje upgradovat omezení připojení serveru, aby bylo možné poskytovat více paměti serveru, a to tak, že nawebuje výpočetní prostředky nebo pomocí su skutu optimalizovaného pro paměť, které mají více výpočetních prostředků na jádro.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Škálování mezipaměti na jinou velikost nebo skladovou položku pro zlepšení výkonu mezipaměti a aplikací

Instance mezipaměti fungují nejlépe, když nejsou spuštěny pod vysokým tlakem paměti, vysokým zatížením serveru nebo vysokou šířkou pásma sítě, což může způsobit, že přestane reagovat, dojde ke ztrátě dat nebo k nedostupnosti. Poradce identifikuje instance mezipaměti v těchto podmínkách a doporučuje použít osvědčené postupy ke snížení tlaku paměti, zatížení serveru nebo šířky pásma sítě nebo škálování na jinou velikost nebo skladovou položku s větší kapacitou.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Přidání oblastí s provozem na účet Azure Cosmos DB

Poradce zjistí účty Azure Cosmos DB, které mají provoz z oblasti, která není aktuálně nakonfigurovaná, a doporučuje přidat tuto oblast. Tím se zlepší latence pro požadavky přicházející z této oblasti a zajistí dostupnost v případě výpadků oblasti. [Další informace o globální distribuci dat pomocí Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Konfigurace zásad indexování Azure Cosmos DB s cestami zahrnutými zákazníky nebo s vyloučenými cestami

Azure Advisor identifikuje kontejnery Cosmos DB, které používají výchozí zásady indexování, ale mohou těžit z vlastní zásady indexování založené na vzoru pracovního vytížení. Výchozí indexování zásad indexuje všechny vlastnosti, ale pomocí vlastní indexování zásady s explicitní zahrnuty nebo vyloučené cesty používané ve filtrech dotazů může snížit ru a úložiště spotřebované pro indexování. [Další informace o úpravách zásad rejstříku](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Konfigurace velikosti stránky dotazu Azure Cosmos DB (MaxItemCount) na -1 

Azure Advisor identifikuje kontejnery Azure Cosmos DB, které používají velikost stránky dotazu 100 a doporučuje použití velikosti stránky -1 pro rychlejší prohledávací. [Další informace o maximálním počtu položek](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Jak získat přístup k doporučením výkonu v advisor

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete [poradce](https://aka.ms/azureadvisordashboard).

2.  Na řídicím panelu Poradce klikněte na kartu **Výkon.**

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních poradců najdete v tématu:

* [Úvod do poradce](advisor-overview.md)
* [Začínáme s poradcem](advisor-get-started.md)
* [Doporučení nákladů poradce](advisor-cost-recommendations.md)
* [Doporučení poradce pro vysokou dostupnost](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení poradce](advisor-security-recommendations.md)
* [Doporučení poradce pro provozní excelenci](advisor-operational-excellence-recommendations.md)
