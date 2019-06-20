---
title: Získat metriky služby Azure Cosmos DB ze služby Azure Monitor
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 2eb61a6b9afa3cabf1733be120dfbdacb7de4534
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276792"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Monitorovat a ladit metrik služby Azure Cosmos DB ze služby Azure Monitor

Můžete zobrazit metriky služby Azure Cosmos DB z rozhraní API služby Azure Monitor. Platforma Azure Monitor poskytuje několik způsobů, jak pracovat s metriky, včetně webu Azure portal, je přístup přes rozhraní REST API nebo dotazování je pomocí Powershellu nebo rozhraní příkazového řádku. Metriky Azure Cosmos DB s nízkou latencí číselných hodnot v minutových intervalech shromažďují ve výchozím nastavení, můžete také agregovat tyto metriky. Tyto metriky jsou schopný zajistit podporu scénáře v reálném čase.  

Tento článek popisuje různé metriky služby Azure Cosmos DB, si můžete zobrazit ze služby Azure Monitor, pomocí webu Azure portal. Pokud vás zajímá v běžných případů použití a jak metrik služby Azure Cosmos DB znovu použít k analýze a ladění těchto problémů naleznete v tématu [monitorování a ladění s využitím metrik ve službě Azure Cosmos DB](use-metrics.md) článku. Budete používat jednu z existujících účtů Azure Cosmos a zobrazit různé metriky databáze, kontejner, oblast, požadavek nebo operace úrovně. Ano Ujistěte se, že máte účet Azure Cosmos s ukázkovými daty a provádění operací CRUD se na těchto datech.

## <a name="view-metrics-from-azure-portal"></a>Zobrazení metrik z webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Vyberte **monitorování** v levém navigačním panelu a vyberte **metriky**.

   ![Podokno metriky ve službě Azure Monitor](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. Z **metriky** podokně > **vyberte prostředek** > zvolte požadovaný **předplatné**, a **skupiny prostředků**. Pro **typ prostředku**vyberte **účty služby Azure Cosmos DB**, zvolte jednu z existujících účtů Azure Cosmos a vyberte **použít**. 

   ![Zvolte účet služby Cosmos DB, pokud chcete zobrazit metriky](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Metriky můžete dále vybrat ze seznamu dostupných metrik. Můžete vybrat konkrétní jednotek žádostí, úložiště, latence, dostupnosti, Cassandra a jiné metriky. Další podrobnosti o všech dostupných metrik v tomto seznamu najdete v tématu [metriky pomocí kategorií](#metrics-by-category) části tohoto článku. V tomto příkladu vybereme **jednotky žádostí** a **Avg** jako hodnota agregace. 

   Kromě těchto podrobností můžete také vybrat **časový rozsah** a **časové intervaly** metrik. Na maximum můžete zobrazit metriky za posledních 30 dnů.  Po použití tohoto filtru, zobrazí se graf na základě vašeho filtru. Zobrazí průměrný počet jednotek žádostí spotřebovaných za minutu pro vybrané období.  

   ![Zvolte metriku na webu Azure Portal](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Přidání filtrů k metrikám

Můžete také filtrovat metriky a graf zobrazí konkrétní **CollectionName**, **DatabaseName**, **typem operace OperationType**, **oblasti**, a **StatusCode**. Chcete-li filtrovat metriky, vyberte **přidat filtr** a zvolte požadovanou vlastnost, jako **typem operace OperationType** , jako například vyberte hodnotu **dotazu**. Graf zobrazí jednotky žádostí spotřebovávat v operaci dotazu pro vybrané období. Operace prováděné prostřednictvím uložené procedury se neprotokolují, takže už nejsou k dispozici pod typem operace OperationType metriku.

![Přidejte filtr k výběru metriky členitosti](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

Metriky můžete seskupit pomocí **použít rozdělení** možnost. Můžete například seskupit jednotek žádostí za typ operace a zobrazit graf pro všechny operace najednou, jak je znázorněno na následujícím obrázku: 

![Přidat použít dělení filtr](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Metriky podle kategorie

### <a name="request-metrics"></a>Metrika žádosti
            
|Metriky (metriky zobrazovaný název)|Jednotka (typ agregace) |Popis|Dimenze| Čas přírůstcích| Starší verze metriky mapování | Využití |
|---|---|---|---| ---| ---| ---|
| TotalRequests (celkový počet požadavků) | Count (počet) | Počet zpracovaných požadavků| Název databáze, StatusCode CollectionName, oblasti,| Vše | TotalRequests Http 2xx, 3xx Http, kód Http 400, Http 401, se interní chyba serveru, služba není dostupná, omezuje požadavky, průměrných požadavků za sekundu | Používá ke sledování požadavků za stavový kód, shromažďování minutovými intervaly. Pokud chcete získat průměrných požadavků za sekundu, použití agregace počet za minutu a dělit 60. |
| MetadataRequests (žádostí o Metadata) |Count (počet) | Počet žádostí o metadata. Azure Cosmos DB udržuje kolekci metadat systému pro každý účet, který umožňuje vytvořit výčet kolekce, databáze atd., a jejich konfigurací, které jsou zdarma. | Název databáze, StatusCode CollectionName, oblasti,| Vše| |Používá k monitorování omezení z důvodu požadavků na metadata.|
| MongoRequests (Mongo požadavků) | Count (počet) | Počet zpracovaných požadavků Mongo | Název databáze, CollectionName, oblast, CommandName, kód chyby| Vše |Frekvence požadavků dotaz mongo, Mongo aktualizace frekvence, Mongo odstranit frekvence požadavků, Mongo vložit frekvence požadavků, Mongo počet požadavek frekvence žádostí| Slouží k monitorování Mongo žádost o chyby, zadejte použití jeden příkaz. |

### <a name="request-unit-metrics"></a>Metriky jednotek žádosti

|Metriky (metriky zobrazovaný název)|Jednotka (typ agregace)|Popis|Dimenze| Čas přírůstcích| Starší verze metriky mapování | Využití |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo poplatek za žádost) | Počet (celkem) |Spotřebované jednotky žádostí mongo| Název databáze, CollectionName, oblast, CommandName, kód chyby| Vše |Zátěž žádostí dotaz mongo, Mongo aktualizace poplatek, Mongo odstranit zátěž žádostí, Mongo vložit zátěž žádostí, Mongo počet požadavek poplatek za žádost| Používá k monitorování prostředků Mongo ru za jednu minutu.|
| TotalRequestUnits (Celkový požadavek jednotky)| Počet (celkem) | Požadavku že spotřebované jednotky| Název databáze, StatusCode CollectionName, oblasti, |Vše| TotalRequestUnits| Používá k monitorování využití celkový počet RU na minutovými intervaly. Chcete-li získat průměrnou RU spotřebovaných za sekundu, pomocí celkový agregace na chvíli a dělit 60.|
| ProvisionedThroughput (zřízená propustnost)| Počet (Maximum) |Zřízená propustnost v kolekci členitosti| DatabaseName CollectionName| 5 MIN| | Používá k monitorování zřízené propustnosti na kolekci.|

### <a name="storage-metrics"></a>Metriky úložiště

|Metriky (metriky zobrazovaný název)|Jednotka (typ agregace)|Popis|Dimenze| Čas přírůstcích| Starší verze metriky mapování | Využití |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (úložiště k dispozici) |Bajtů (celkem) | Celkový úložiště k dispozici hlášené v intervalech 5 minut na oblast| DatabaseName CollectionName, oblast| 5 MIN| Dostupné úložiště| Slouží k monitorování úložiště k dispozici minimální členitost kapacity (platí pouze pro kolekce dlouhodobého úložiště) by měl být 5 minut.| 
| DataUsage (využití dat) |Bajtů (celkem) |Celkové množství dat využití hlášené v intervalech 5 minut na oblast| DatabaseName CollectionName, oblast| 5 MIN |Velikost dat | Slouží k monitorování využití celkové množství dat v kolekci a oblasti, minimální členitost by měl být 5 minut.|
| IndexUsage (použití indexu) | Bajtů (celkem) |Celkové využití Index hlášené v intervalech 5 minut na oblast| DatabaseName CollectionName, oblast| 5 MIN| Velikost indexu| Slouží k monitorování využití celkové množství dat v kolekci a oblasti, minimální členitost by měl být 5 minut. |
| DocumentQuota (kvóty dokumentu) | Bajtů (celkem) | Kvóta celkový úložiště Ohlášeno za 5 minut členitosti v jedné oblasti.| DatabaseName CollectionName, oblast| 5 MIN |Kapacita úložiště| Slouží k monitorování celkovou kvótu v kolekci a oblasti, minimální členitost by měl být 5 minut.|
| DocumentCount (počet dokumentů) | Počet (celkem) |Celkového počtu dokumentů hlášené v intervalech 5 minut na oblast| DatabaseName CollectionName, oblast| 5 MIN |Počet dokumentů|Slouží k monitorování počet dokumentů v kolekci a oblasti, minimální členitost by měl být 5 minut.|

### <a name="latency-metrics"></a>Metriky latence

|Metriky (metriky zobrazovaný název)|Jednotka (typ agregace)|Popis|Dimenze| Čas přírůstcích| Využití |
|---|---|---|---| ---| ---|
| ReplicationLatency (latence replikace)| Milisekundy (Minimum, Maximum, průměr) | Latencí P99 replikace mezi zdrojovou a cílovou oblastí pro účet povolenou geografickou| SourceRegion TargetRegion| Vše | Používá k monitorování P99 latence replikace mezi jakékoli dvě oblasti geograficky replikovaného účtu. |


### <a name="availability-metrics"></a>Metriky dostupnosti

|Metriky (metriky zobrazovaný název) |Jednotka (typ agregace)|Popis| Čas přírůstcích| Starší verze metriky mapování | Využití |
|---|---|---|---| ---| ---|
| ServiceAvailability (dostupnost služeb)| Procento (Minimum, Maximum) | Dostupnost účtu požadavky na jednu hodinu členitosti| 1H | Dostupnost služby | Představuje procento součtu předané požadavky. Žádost se považuje za bylo možné provést kvůli chybě systému, pokud se 410, stavový kód 500 nebo 503 slouží k monitorování dostupnosti účet na hodinu členitosti. |


### <a name="cassandra-api-metrics"></a>Metriky rozhraní Cassandra API

|Metriky (metriky zobrazovaný název)|Jednotka (typ agregace)|Popis|Dimenze| Čas přírůstcích| Využití |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra požadavků) | Count (počet) | Počet zpracovaných požadavků Apache Cassandra API| DatabaseName, CollectionName, kód chyby, oblast, typem operace OperationType, typ prostředku| Vše| Používá ke sledování požadavků Cassandra v minutovými intervaly. Pokud chcete získat průměrných požadavků za sekundu, použití agregace počet za minutu a dělit 60.|
| CassandraRequestCharges (Cassandra požadavek poplatky) | Počet (Sum, Min, Max, Avg) | Požadované jednotky spotřebované žádostmi o rozhraní Cassandra API| DatabaseName, CollectionName, Region, OperationType, ResourceType| Vše| Používá k monitorování jednotek ru za minutu používá účet Cassandra API.|
| CassandraConnectionClosures (uzávěry Cassandra připojení) |Count (počet) |Počet Cassandra ukončená připojení| ClosureReason oblasti| Vše | Používá k monitorování připojení mezi klienty a rozhraní Azure Cosmos DB Cassandra API.|

## <a name="next-steps"></a>Další postup

* [Sledování a zobrazení metrik z podokna metriky účtu služby Azure Cosmos DB](use-metrics.md)

* [Protokolování diagnostiky ve službě Azure Cosmos DB](logging.md)
