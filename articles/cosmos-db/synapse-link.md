---
title: Odkaz na Azure synapse pro Azure Cosmos DB, výhody a kdy ji použít
description: Přečtěte si o Azure Cosmos DB odkaz na službu Azure synapse. Synapse Link umožňuje spouštět analýzy v reálném čase (HTAP) s využitím Azure synapse Analytics pro provozní data v Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 1b8c0c5bf533765e589e022233af14855b26d29c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656937"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>Co je Azure Synapse Link pro Azure Cosmos DB?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Odkaz na Azure synapse pro Azure Cosmos DB je cloudová funkce hybridního transakčního a analytického zpracování (HTAP), která umožňuje spouštět analýzy téměř v reálném čase nad provozními daty v Azure Cosmos DB. Propojení Azure synapse vytvoří úzkou plynulou integraci mezi Azure Cosmos DB a Azure synapse Analytics.

Pomocí [Azure Cosmos DBho analytického úložiště](analytical-store-introduction.md), plně izolovaného úložiště sloupců, neumožňuje propojení Azure synapse žádné analýzy extrakce a transformace (ETL) ve [službě Azure synapse Analytics](../synapse-analytics/overview-what-is.md) proti vaším provozním datům ve velkém měřítku. Obchodní analytici, datové inženýri a odborníci přes data teď můžou pomocí synapse Sparku nebo synapse SQL pracovat téměř v reálném čase business intelligence, analýz a kanálů strojového učení. Je toho možné dosáhnout bez dopadu na výkon transakčních úloh v Azure Cosmos DB. 

Na následujícím obrázku vidíte integraci Azure Synapseu s využitím Azure Cosmos DB a Azure synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Diagram architektury pro integraci Azure synapse Analytics s Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Výhodnější

Aby bylo možné analyzovat velké provozní datové sady a zároveň minimalizovat dopad na výkon důležitých transakčních úloh, tradičně jsou provozní data v Azure Cosmos DB extrahována a zpracována pomocí kanálů extrakce, transformace a načítání (ETL). Kanály ETL vyžadují mnoho vrstev přesunu dat, což má za následek mnohem náročnou provozní složitost a dopad na výkon vašich transakčních úloh. Zároveň se tím zvyšuje latence, která analyzuje provozní data od času počátku.

V porovnání s tradičními řešeními založenými na ETL nabízí Azure synapse odkaz na Azure Cosmos DB několik výhod, jako jsou:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Snížení složitosti bez úloh ETL ke správě

Azure synapse Link umožňuje přímý přístup k Azure Cosmos DB analytickému úložišti pomocí Azure synapse Analytics bez složitého přesunu dat. Jakékoli aktualizace provozních dat jsou viditelné v analytickém úložišti téměř v reálném čase bez úloh ETL nebo změny kanálu. Pomocí analytického úložiště z Azure synapse Analytics můžete spustit rozsáhlou škálu analýz bez další transformace dat.

### <a name="near-real-time-insights-into-your-operational-data"></a>Přehledy o provozních datech téměř v reálném čase

Pomocí odkazu Azure synapse teď můžete získat přehled o provozních datech téměř v reálném čase. Systémy založené na ETL mají za následek vyšší latenci při analýze provozních dat, a to kvůli mnoha vrstvám potřebných k extrakci, transformaci a načítání provozních dat. Díky nativní integraci Azure Cosmos DB analytického úložiště s Azure synapse Analytics můžete analyzovat provozní data téměř v reálném čase a umožnit tak nové obchodní scénáře. 

### <a name="no-impact-on-operational-workloads"></a>Žádný dopad na provozní úlohy

Pomocí služby Azure synapse Link můžete spouštět analytické dotazy proti Azure Cosmos DB analytickému úložišti (samostatné úložiště sloupců), zatímco transakce jsou zpracovávány pomocí zřízené propustnosti pro transakční úlohy (transakční úložiště založené na řádku).  Analytické úlohy se obsluhují nezávisle na transakčním provozu, a to bez využívání jakékoli propustnosti zřízené pro vaše provozní data.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Optimalizováno pro úlohy s velkými objemy analýz

Azure Cosmos DB analytické úložiště je optimalizované pro zajištění škálovatelnosti, pružnosti a výkonu pro analytické úlohy bez jakékoli závislosti na výpočetních časech. Technologie úložiště je samoobslužně spravovaná pro optimalizaci vašich úloh analýzy. Díky integrované podpoře Azure synapse Analytics poskytuje přístup k této vrstvě úložiště jednoduchost a vysoký výkon.

### <a name="cost-effective"></a>Cenově výhodné

S odkazem na Azure synapse můžete získat cenově optimalizované a plně spravované řešení pro provozní analýzu. Eliminuje nadbytečné vrstvy úložiště a výpočetních prostředků vyžadovaných v tradičních kanálech ETL pro analýzu provozních dat. 

Azure Cosmos DB analytické úložiště sleduje cenový model založený na spotřebě, který je založený na operacích úložiště dat a analytické operace čtení a zápisu a na provedených dotazech. Nevyžaduje, abyste zřídili žádnou propustnost, stejně jako v současnosti za transakční úlohy. Přístup k datům s vysoce elastickými výpočetními moduly z Azure synapse Analytics přináší celkové náklady na spuštění úložiště a výpočetní výkon.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Analýza pro místně dostupná, globálně distribuovaná, vícenásobná zápisy ve více oblastech

Analytické dotazy můžete efektivně spouštět proti nejbližší místní kopii dat v Azure Cosmos DB. Azure Cosmos DB poskytuje nejmodernější možnosti pro spouštění globálně distribuovaných analytických úloh spolu s transakčními úlohami aktivním aktivním způsobem.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>Povolení scénářů HTAP pro vaše provozní data

Odkaz synapse spojuje Azure Cosmos DB analytické úložiště s podporou Azure synapse Analytics runtime. Tato integrace umožňuje vytvářet cloudová nativní HTAP řešení (hybridní transakční a analytické zpracování), která generují přehledy na základě aktualizací v reálném čase pro vaše provozní data prostřednictvím velkých datových sad. Odemkne nové obchodní scénáře, aby vyvolala výstrahy na základě živých trendů, sestavování téměř v reálném čase a podnikového prostředí na základě chování uživatele.

### <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analytické úložiště

Azure Cosmos DB analytické úložiště je uživatelsky orientovanou reprezentace vašich provozních dat v Azure Cosmos DB. Tento analytický obchod je vhodný pro rychlé a nákladově efektivní dotazy na velké sady provozních dat bez kopírování dat a vlivu na výkon transakčních úloh.

Analytické úložiště automaticky v reálném čase přebírá rychlé vkládání, aktualizace a odstraňování v transakčních úlohách, jako plně spravovaná funkce (Automatická synchronizace) Azure Cosmos DB. Není vyžadován žádný kanál změn ani ETL. 

Pokud máte globálně distribuovaný Azure Cosmos DB účet, po povolení analytického úložiště pro kontejner bude k dispozici ve všech oblastech pro daný účet. Další informace o analytickém úložišti najdete v článku [Azure Cosmos DB analytické úložiště – přehled](analytical-store-introduction.md) .

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integrace se službou Azure synapse Analytics

S odkazem na synapse se teď můžete přímo připojit k vašim Azure Cosmos DBm kontejnerům z Azure synapse Analytics a přistupovat k analytickému úložišti bez oddělených konektorů. Služba Azure synapse Analytics aktuálně podporuje synapse propojení s [Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) a [neserverovým fondem SQL](../synapse-analytics/sql/on-demand-workspace-overview.md).

Data z Azure Cosmos DBho analytického úložiště můžete zadávat současně s interoperabilitou různých analytických časů, které Azure synapse Analytics podporuje. K analýze provozních dat nejsou potřeba žádné další transformace dat. Data analytického úložiště můžete dotazovat a analyzovat pomocí:

* Synapse Apache Spark s plnou podporou pro Scala, Python, SparkSQL a C#. Synapse Spark je centrální na datové strojírenství a scénáře pro datové vědy

* Fond SQL bez serveru s jazykem T-SQL a podporou pro známé nástroje BI (například Power BI Premium atd.)

> [!NOTE]
> Z Azure synapse Analytics máte přístup k analytickým i transakčním úložištím v kontejneru Azure Cosmos DB. Pokud ale chcete spouštět rozsáhlé analýzy nebo kontrolovat vaše provozní data, doporučujeme použít analytické úložiště, abyste se vyhnuli dopadu na transakční úlohy.

> [!NOTE]
> Analýzy s nízkou latencí můžete spouštět v oblasti Azure připojením kontejneru Azure Cosmos DB k synapse modulu runtime v této oblasti.

Tato integrace umožňuje pro různé uživatele následující scénáře HTAP:

* Inženýr BI, který chce modelovat a publikovat Power BI sestavu pro přístup k živým provozním datům v Azure Cosmos DB přímo prostřednictvím synapse SQL.

* Analytik dat, který chce z provozních dat v kontejneru Azure Cosmos DB odvodit přehledy pomocí dotazování pomocí synapse SQL, číst data ve velkém měřítku a kombinovat tato zjištění s ostatními zdroji dat.

* Odborník na data, který chce používat synapse Spark k nalezení funkce pro zlepšení modelu a výuku tohoto modelu bez provádění komplexních datových technik. Můžou také zapisovat výsledky modelu po odvození do Azure Cosmos DB pro bodování dat v reálném čase pomocí Sparku synapse.

* Inženýr dat, který chce zpřístupnit data pro příjemce tím, že vytvoří tabulky SQL nebo Spark přes kontejnery Azure Cosmos DB bez manuálních procesů ETL.

Další informace o podpoře Azure synapse Analytics runtime pro Azure Cosmos DB najdete v tématu [Azure synapse Analytics pro Cosmos DB podporu](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="security"></a>Zabezpečení

Synapse Link vám umožní spouštět analýzy téměř v reálném čase v rámci důležitých dat v Azure Cosmos DB. Je důležité zajistit, aby byla důležitá obchodní data bezpečně ukládána napříč transakčními i analytickými obchody. Odkaz na službu Azure synapse pro Azure Cosmos DB je navržený tak, aby splňoval tyto požadavky na zabezpečení prostřednictvím následujících funkcí:

* **Izolace sítě pomocí soukromých koncových bodů** – k datům v transakčních a analytických úložištích můžete řídit síťový přístup nezávisle. Izolace sítě se provádí pomocí samostatných spravovaných privátních koncových bodů pro každé úložiště v rámci spravovaných virtuálních sítí v pracovních prostorech Azure synapse. Další informace najdete v článku postup [Konfigurace privátních koncových bodů pro analytické úložiště](analytical-store-private-endpoints.md) .

* **Šifrování dat pomocí klíčů spravovaných zákazníkem** – data můžete bez problémů šifrovat napříč transakčními a analytickými úložišti, a to pomocí automatického a transparentního klíče spravovaného zákazníky. Další informace najdete v článku postup [konfigurace klíčů spravovaných zákazníkem](how-to-setup-cmk.md) .

* **Zabezpečená správa klíčů** – přístup k datům v analytickém úložišti z synapse a synapse serverů SQL bez serveru vyžaduje správu Azure Cosmos DB klíčů v rámci pracovních prostorů synapse Analytics. Místo používání Azure Cosmos DB klíčů účtu, které jsou vložené v úlohách Sparku nebo ve skriptech SQL, poskytuje propojení Azure synapse bezpečnější možnosti.

  * Pokud používáte synapse fondy SQL serverů bez serveru, můžete dotazovat se na Azure Cosmos DB analytické úložiště pomocí předběžného Vytváření přihlašovacích údajů SQL, který ukládá klíče účtu a odkazuje na ně ve `OPENROWSET` funkci. Další informace najdete v článku [dotazování s fondem SQL bez serveru v článku s odkazem na Azure synapse](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) .

  * Při použití synapse Spark můžete ukládat klíče účtu v propojených objektech služby, které ukazují na databázi Azure Cosmos DB a odkazovat na ně v konfiguraci Sparku za běhu. Další informace najdete v tématu [kopírování dat do vyhrazeného fondu SQL pomocí Apache Spark](../synapse-analytics/synapse-link/how-to-copy-to-sql-pool.md) článku.

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Kdy použít pro Azure Cosmos DB odkaz na Azure synapse?

Odkaz na synapse se doporučuje v následujících případech:

* Pokud jste zákazníkem Azure Cosmos DB a chcete provozovat analýzy, BI a strojové učení přes vaše provozní data. V takových případech odkaz synapse poskytuje pokročilejší analytické prostředí, aniž by to ovlivnilo zřízenou propustnost transakčního úložiště. Například:

  * Pokud používáte analýzy nebo BI na vašich Azure Cosmos DB provozních datech přímo pomocí samostatných konektorů ještě dnes, nebo

  * Pokud spouštíte procesy ETL k extrakci provozních dat do samostatného analytického systému.
 
V takových případech odkaz synapse poskytuje pokročilejší analytické prostředí, aniž by to ovlivnilo zřízenou propustnost transakčního úložiště.

Odkaz synapse se nedoporučuje, pokud hledáte tradiční požadavky na datový sklad, jako je vysoká souběžnost, Správa úloh a trvalost agregací napříč různými zdroji dat. Další informace najdete v tématu [běžné scénáře, které mohou být napájeny pomocí služby Azure synapse Link pro Azure Cosmos DB](synapse-link-use-cases.md).

## <a name="limitations"></a>Omezení

* Azure Synapse Link pro Azure Cosmos DB podporuje rozhraní SQL API a rozhraní API služby Azure Cosmos DB pro MongoDB. Pro rozhraní Gremlin API, rozhraní API Cassandra a rozhraní API pro tabulky se nepodporuje.

* Analytické úložiště se dá povolit jenom pro nové kontejnery. Pokud chcete použít analytické úložiště pro existující kontejnery, migrujte data z existujících kontejnerů do nových kontejnerů pomocí [nástrojů pro migraci Azure Cosmos DB](cosmosdb-migrationchoices.md). Můžete povolit synapse odkaz na nové a existující účty Azure Cosmos DB.

* U kontejnerů se zapnutým analytickým úložištěm se v současnosti nepodporuje automatické zálohování a obnovování vašich dat v analytickém úložišti. Když je na databázovém účtu povoleno propojení synapse, Azure Cosmos DB bude pokračovat v automatickém ukládání [záloh](./online-backup-and-restore.md) vašich dat do transakčního úložiště (pouze) kontejnerů v naplánovaných intervalech zálohování, jako vždycky. Je důležité si uvědomit, že když se na nový účet obnoví kontejner se zapnutým analytickým úložištěm, kontejner se obnoví jenom v transakčním úložišti a není povolený žádný analytický obchod.

* Přístup k úložišti Azure Cosmos DB Analytics s zřízeným synapse SQL není momentálně k dispozici.

## <a name="pricing"></a>Ceny

Model fakturace propojení Azure synapse zahrnuje náklady vzniklé pomocí Azure Cosmos DBho analytického úložiště a modulu runtime synapse. Další informace najdete v článcích o cenách služby [Azure Cosmos DB Analytics Store](analytical-store-introduction.md#analytical-store-pricing) a [cenách Azure synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/) .

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících dokumentech:

* [Přehled analýzy Azure Cosmos DBho úložiště](analytical-store-introduction.md)

* [Začínáme s Azure Synapse Linkem pro Azure Cosmos DB](configure-synapse-link.md)
 
* [Co se podporuje v modulu runtime Azure Synapse Analytics](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Nejčastější dotazy k Azure Synapse Linku pro Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Případy použití Azure Synapse Linku pro Azure Cosmos DB](synapse-link-use-cases.md)
