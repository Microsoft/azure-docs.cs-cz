---
title: Získat Azure Cosmos DB metriky z Azure Monitor
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: 762c910336fa2b50a46eda23cf66d8a7aa383c52
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241229"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Monitorování a ladění Azure Cosmos DB metriky z Azure Monitor

Azure Cosmos DB metriky můžete zobrazit z rozhraní Azure Monitor API. Azure Monitor poskytuje několik způsobů, jak pracovat s metrikami, včetně Azure Portal, přístupu k nim prostřednictvím REST API nebo jejich dotazování pomocí PowerShellu nebo rozhraní příkazového řádku. Azure Cosmos DB metriky jsou číselné hodnoty nízké latence, které se shromažďují ve výchozím nastavení četnosti jedné minuty. Tyto metriky můžete také agregovat. Tyto metriky jsou schopné podporovat scénáře v reálném čase.  

Tento článek popisuje různé metriky Azure Cosmos DB, které můžete zobrazit z Azure Monitor pomocí Azure Portal. Pokud vás zajímá běžné případy použití a jak se Azure Cosmos DB metriky znovu využívaly k analýze a ladění těchto problémů, přečtěte si téma [monitorování a ladění pomocí metrik v článku Azure Cosmos DB](use-metrics.md) . Použijete některý z existujících účtů Azure Cosmos a zobrazíte různé metriky na úrovni databáze, kontejneru, oblasti, žádosti nebo operace. Ujistěte se, že máte účet Azure Cosmos s ukázkovými daty a provádíte operace CRUD na těchto datech.

## <a name="view-metrics-from-azure-portal"></a>Zobrazit metriky z Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. V levém navigačním panelu vyberte **monitor** a vyberte **metriky**.

   ![Podokno metrik v Azure Monitor](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. V podokně **metriky** > **Vyberte prostředek** > zvolte požadované **předplatné**a **skupinu prostředků**. Jako **typ prostředku**vyberte **Azure Cosmos DB účty**, zvolte jeden ze stávajících účtů Azure Cosmos a pak vyberte **použít**. 

   ![Vyberte účet Cosmos DB, pro který chcete zobrazit metriky.](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Dále můžete vybrat metriku ze seznamu dostupných metrik. Můžete vybrat metriky specifické pro jednotky žádosti, úložiště, latenci, dostupnost, Cassandra a další. Podrobné informace o všech dostupných metrikách v tomto seznamu najdete v části [metriky podle kategorií](#metrics-by-category) tohoto článku. V tomto příkladu vybereme **jednotky žádosti** a jako hodnotu agregace určíte **průměr** . 

   Kromě těchto podrobností můžete také vybrat **časový rozsah** a **časovou členitost** metrik. V poli Max (maximum) si můžete zobrazit metriky za posledních 30 dní.  Po použití filtru se v závislosti na vašem filtru zobrazí graf. Pro vybrané období můžete zobrazit průměrný počet spotřebovaných jednotek žádostí za minutu.  

   ![Vyberte metriku z Azure Portal](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Přidání filtrů do metrik

Můžete také filtrovat metriky a graf zobrazený podle konkrétního typu **CollectionName**, **DatabaseName**, **typem operace OperationType**, **region**a **StatusCode**. Chcete-li filtrovat metriky, vyberte možnost **Přidat filtr** a zvolte požadovanou vlastnost, například **typem operace OperationType** , a vyberte hodnotu, jako je například **dotaz**. V grafu se pak zobrazí jednotky žádosti spotřebované pro operaci dotazování pro vybrané období. Operace provedené prostřednictvím uložené procedury nejsou protokolovány, takže nejsou k dispozici v rámci metriky typem operace OperationType.

![Přidejte filtr pro výběr členitosti metriky.](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

Metriky můžete seskupit pomocí možnosti **použít rozdělení** . Například můžete seskupit jednotky žádostí na typ operace a zobrazit graf pro všechny operace najednou, jak je znázorněno na následujícím obrázku: 

![Přidat použít dělicí filtr](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Metriky podle kategorie

### <a name="request-metrics"></a>Metrika žádosti
            
|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace) |Popis|Dimenze| Časové členitosti| Zastaralé mapování metrik | Použití |
|---|---|---|---| ---| ---| ---|
| TotalRequests (celkový počet požadavků) | Počet (počet) | Počet provedených požadavků| DatabaseName, CollectionName, region, StatusCode| Vše | TotalRequests, HTTP 2xx, http 3xx, HTTP 400, HTTP 401, interní chyba serveru, nedostupný služba, omezené požadavky, průměrný počet požadavků za sekundu | Slouží k monitorování požadavků na stavový kód v kontejneru při minutové členitosti. Chcete-li získat průměrné požadavky za sekundu, použijte agregaci Count za minutu a dělenou 60. |
| MetadataRequests (žádosti o metadata) |Počet (počet) | Počet požadavků na metadata Azure Cosmos DB uchovává kontejner systémových metadat pro každý účet, který umožňuje vytvořit výčet kolekcí, databází atd. a jejich konfigurací zdarma. | DatabaseName, CollectionName, region, StatusCode| Vše| |Slouží k monitorování omezení z důvodu požadavků na metadata.|
| MongoRequests (žádosti Mongo) | Počet (počet) | Počet provedených požadavků Mongo | DatabaseName, CollectionName, region, příkaz, ErrorCode| Vše |Frekvence požadavků na dotaz Mongo, frekvence požadavků na aktualizace Mongo, rychlost žádosti o odstranění Mongo, rychlost žádosti o vložení Mongo, počet požadavků na zadání Mongo| Slouží k monitorování chyb požadavků Mongo, využití na typ příkazu. |

### <a name="request-unit-metrics"></a>Metriky jednotek požadavku

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Popis|Dimenze| Časové členitosti| Zastaralé mapování metrik | Použití |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (poplatek za požadavek Mongo) | Počet (celkem) |Spotřebované jednotky žádosti Mongo| DatabaseName, CollectionName, region, příkaz, ErrorCode| Vše |Poplatek za požadavek na dotaz Mongo, poplatek za požadavek na aktualizaci Mongo, poplatek za požadavek na odstranění, Mongo vložení žádosti, poplatek za požadavek na počet Mongo| Používá se k monitorování ru prostředků Mongo za minutu.|
| TotalRequestUnits (celkový počet jednotek žádosti)| Počet (celkem) | Spotřebované jednotky žádosti| DatabaseName, CollectionName, region, StatusCode |Vše| TotalRequestUnits| Používá se k monitorování celkového využití RU při minutové členitosti. K získání průměrného RU za sekundu za sekundu použijte celkovou agregaci za minutu a rozdělte je 60.|
| ProvisionedThroughput (zřízená propustnost)| Počet (maximum) |Zřízená propustnost při členitosti kontejneru| DatabaseName, ContainerName| 5 MIN| | Slouží k monitorování zřízené propustnosti na kontejner.|

### <a name="storage-metrics"></a>Metriky úložiště

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Popis|Dimenze| Časové členitosti| Zastaralé mapování metrik | Použití |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (úložiště k dispozici) |Bajty (celkem) | Celkové dostupné úložiště hlášené v členitosti 5 minut na oblast| DatabaseName, CollectionName, region| 5 MIN| Dostupné úložiště| Minimální členitost, která se používá k monitorování dostupné kapacity úložiště (platí jenom pro kolekce pevných úložišť), by měla být 5 minut.| 
| Využití dat (využití dat) |Bajty (celkem) |Celkové využití dat nahlášené v rozlišení 5 minut na oblast| DatabaseName, CollectionName, region| 5 MIN |Velikost dat | Používá se k monitorování celkového využití dat v kontejneru a oblasti. minimální členitost by měla být 5 minut.|
| IndexUsage (využití indexu) | Bajty (celkem) |Celkové využití indexu hlášené v rozlišení 5 minut na oblast| DatabaseName, CollectionName, region| 5 MIN| Velikost indexu| Používá se k monitorování celkového využití dat v kontejneru a oblasti. minimální členitost by měla být 5 minut. |
| DocumentQuota (kvóta dokumentu) | Bajty (celkem) | Celková kvóta úložiště vykazovaná na základě rozlišení 5 minut na oblast.| DatabaseName, CollectionName, region| 5 MIN |Kapacita úložiště| V případě, že se má monitorovat celková kvóta v kontejneru a oblasti, minimální členitost by měla být 5 minut.|
| DocumentCount (počet dokumentů) | Počet (celkem) |Celkový počet dokumentů hlášených v rozlišení 5 minut na oblast| DatabaseName, CollectionName, region| 5 MIN |Počet dokumentů|Používá se k monitorování počtu dokumentů v kontejneru a oblasti. minimální členitost by měla být 5 minut.|

### <a name="latency-metrics"></a>Metriky latence

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Popis|Dimenze| Časové členitosti| Použití |
|---|---|---|---| ---| ---|
| ReplicationLatency (latence replikace)| Milisekundy (minimální, maximální, průměrná) | Latence replikace p99 napříč zdrojovou a cílovou oblastí pro geograficky povolený účet| SourceRegion, TargetRegion| Vše | Slouží k monitorování latence replikace p99 mezi dvěma oblastmi pro geograficky replikovaný účet. |


### <a name="availability-metrics"></a>Metriky dostupnosti

|Metrika (zobrazovaný název metriky) |Jednotka (typ agregace)|Popis| Časové členitosti| Zastaralé mapování metrik | Použití |
|---|---|---|---| ---| ---|
| ServiceAvailability (dostupnost služby)| Procento (minimum, maximum) | Dostupnost žádostí o účet při jedné hodinové členitosti| 1H | Dostupnost služby | Představuje procento celkového počtu předaných požadavků. Požadavek se považuje za neúspěšný, protože došlo k chybě systému, pokud je stavový kód 410, 500 nebo 503, který slouží k monitorování dostupnosti účtu při hodinové členitosti. |


### <a name="cassandra-api-metrics"></a>rozhraní API Cassandra metriky

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Popis|Dimenze| Časové členitosti| Použití |
|---|---|---|---| ---| ---|
| CassandraRequests (žádosti Cassandra) | Počet (počet) | Počet provedených požadavků rozhraní API Cassandra| DatabaseName, CollectionName, ErrorCode, region, typem operace OperationType, ResourceType| Vše| Používá se k monitorování požadavků Cassandra při minutové členitosti. Chcete-li získat průměrné požadavky za sekundu, použijte agregaci Count za minutu a dělenou 60.|
| CassandraRequestCharges (poplatky za žádosti Cassandra) | Počet (Sum, min, Max, AVG) | Jednotky žádostí spotřebované rozhraní API Cassandra požadavky| DatabaseName, CollectionName, oblast, typem operace OperationType, ResourceType| Vše| Slouží k monitorování ru používaných za minutu účtem rozhraní API Cassandra.|
| CassandraConnectionClosures (uzávěry připojení Cassandra) |Počet (počet) |Počet uzavřených připojení Cassandra| ClosureReason, oblast| Vše | Slouží k monitorování připojení mezi klienty a Azure Cosmos DB rozhraní API Cassandra.|

## <a name="next-steps"></a>Další postup

* [Zobrazení a monitorování metrik z podokna metriky účtu Azure Cosmos DB](use-metrics.md)

* [Protokolování diagnostiky v Azure Cosmos DB](logging.md)
