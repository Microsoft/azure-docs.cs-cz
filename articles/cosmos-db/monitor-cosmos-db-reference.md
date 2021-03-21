---
title: Monitorování referenčních dat Azure Cosmos DB | Microsoft Docs
description: Důležité referenční materiály, které jsou potřeba při monitorování protokolů a metrik v Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/07/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: 5f542b35110a6d967640ad91faead75f6cc0e0c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593292"
---
# <a name="monitoring-azure-cosmos-db-data-reference"></a>Referenční informace o Azure Cosmos DB dat monitorování

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Tento článek obsahuje referenční informace o datech protokolů a metrik, která se shromažďují za účelem analýzy výkonu a dostupnosti služby Azure Cosmos DB. Podrobnosti o shromažďování a analýze dat monitorování pro Azure Cosmos DB najdete v článku [monitorování Azure Cosmos DB](monitor-cosmos-db.md) .

## <a name="metrics"></a>Metriky

Všechny metriky, které odpovídají Azure Cosmos DB, jsou uloženy v oboru názvů **Cosmos DB Standardní metriky**. Seznam všech metrik podpory Azure Monitor (včetně Azure Cosmos DB) najdete v tématu [Azure monitor podporované metriky](../azure-monitor/essentials/metrics-supported.md). V této části jsou uvedené všechny automaticky shromážděné metriky platforem pro Azure Cosmos DB.  

### <a name="request-metrics"></a>Metriky žádostí

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace) |Description|Dimenze| Časové členitosti| Zastaralé mapování metrik | Využití |
|---|---|---|---| ---| ---| ---|
| TotalRequests (celkový počet požadavků) | Počet (počet) | Počet provedených požadavků| DatabaseName, CollectionName, region, StatusCode| Vše | TotalRequests, HTTP 2xx, http 3xx, HTTP 400, HTTP 401, interní chyba serveru, nedostupný služba, omezené požadavky, průměrný počet požadavků za sekundu | Slouží k monitorování požadavků na stavový kód v kontejneru při minutové členitosti. Chcete-li získat průměrné požadavky za sekundu, použijte agregaci Count za minutu a dělenou 60. |
| MetadataRequests (žádosti o metadata) |Počet (počet) | Počet požadavků na metadata Azure Cosmos DB uchovává kontejner systémových metadat pro každý účet, který umožňuje vytvořit výčet kolekcí, databází atd. a jejich konfigurací zdarma. | DatabaseName, CollectionName, region, StatusCode| Vše| |Slouží k monitorování omezení z důvodu požadavků na metadata.|
| MongoRequests (žádosti Mongo) | Počet (počet) | Počet provedených požadavků Mongo | DatabaseName, CollectionName, region, příkaz, ErrorCode| Vše |Frekvence požadavků na dotaz Mongo, frekvence požadavků na aktualizace Mongo, rychlost žádosti o odstranění Mongo, rychlost žádosti o vložení Mongo, počet požadavků na zadání Mongo| Slouží k monitorování chyb požadavků Mongo, využití na typ příkazu. |

### <a name="request-unit-metrics"></a>Metriky jednotek požadavku

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Description|Dimenze| Časové členitosti| Zastaralé mapování metrik | Využití |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (poplatek za požadavek Mongo) | Počet (celkem) |Spotřebované jednotky žádosti Mongo| DatabaseName, CollectionName, region, příkaz, ErrorCode| Vše |Poplatek za požadavek na dotaz Mongo, poplatek za požadavek na aktualizaci Mongo, poplatek za požadavek na odstranění, Mongo vložení žádosti, poplatek za požadavek na počet Mongo| Používá se k monitorování ru prostředků Mongo za minutu.|
| TotalRequestUnits (celkový počet jednotek žádosti)| Počet (celkem) | Spotřebované jednotky žádosti| DatabaseName, CollectionName, region, StatusCode |Vše| TotalRequestUnits| Používá se k monitorování celkového využití RU při minutové členitosti. K získání průměrného RU za sekundu za sekundu použijte celkovou agregaci za minutu a rozdělte je 60.|
| ProvisionedThroughput (zřízená propustnost)| Počet (maximum) |Zřízená propustnost při členitosti kontejneru| DatabaseName, ContainerName| 5M| | Slouží k monitorování zřízené propustnosti na kontejner.|

### <a name="storage-metrics"></a>Metriky úložiště

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Description|Dimenze| Časové členitosti| Zastaralé mapování metrik | Využití |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (úložiště k dispozici) |Bajty (celkem) | Celkové dostupné úložiště hlášené v členitosti 5 minut na oblast| DatabaseName, CollectionName, region| 5M| Dostupné úložiště| Minimální členitost, která se používá k monitorování dostupné kapacity úložiště (platí jenom pro kolekce pevných úložišť), by měla být 5 minut.| 
| Využití dat (využití dat) |Bajty (celkem) |Celkové využití dat nahlášené v rozlišení 5 minut na oblast| DatabaseName, CollectionName, region| 5M |Velikost dat | Používá se k monitorování celkového využití dat v kontejneru a oblasti. minimální členitost by měla být 5 minut.|
| IndexUsage (využití indexu) | Bajty (celkem) |Celkové využití indexu hlášené v rozlišení 5 minut na oblast| DatabaseName, CollectionName, region| 5M| Velikost indexu| Používá se k monitorování celkového využití dat v kontejneru a oblasti. minimální členitost by měla být 5 minut. |
| DocumentQuota (kvóta dokumentu) | Bajty (celkem) | Celková kvóta úložiště vykazovaná na základě rozlišení 5 minut na oblast.| DatabaseName, CollectionName, region| 5M |Kapacita úložiště| V případě, že se má monitorovat celková kvóta v kontejneru a oblasti, minimální členitost by měla být 5 minut.|
| DocumentCount (počet dokumentů) | Počet (celkem) |Celkový počet dokumentů hlášených v rozlišení 5 minut na oblast| DatabaseName, CollectionName, region| 5M |Počet dokumentů|Používá se k monitorování počtu dokumentů v kontejneru a oblasti. minimální členitost by měla být 5 minut.|

### <a name="latency-metrics"></a>Metriky latence

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Description|Dimenze| Časové členitosti| Využití |
|---|---|---|---| ---| ---|
| ReplicationLatency (latence replikace)| Milisekundy (minimální, maximální, průměrná) | Latence replikace p99 napříč zdrojovou a cílovou oblastí pro geograficky povolený účet| SourceRegion, TargetRegion| Vše | Slouží k monitorování latence replikace p99 mezi dvěma oblastmi pro geograficky replikovaný účet. |
| Latence na straně serveru| Milisekundy (průměr) | Čas, který server zavedl ke zpracování žádosti. | CollectionName, ConnectionMode, DatabaseName, typem operace OperationType, PublicAPIType, region | Vše | Slouží k monitorování latence žádosti na serveru Azure Cosmos DB. |

### <a name="availability-metrics"></a>Metriky dostupnosti

|Metrika (zobrazovaný název metriky) |Jednotka (typ agregace)|Description| Časové členitosti| Zastaralé mapování metrik | Využití |
|---|---|---|---| ---| ---|
| ServiceAvailability (dostupnost služby)| Procento (minimum, maximum) | Dostupnost žádostí o účet při jedné hodinové členitosti| 1H | Dostupnost služby | Představuje procento celkového počtu předaných požadavků. Požadavek se považuje za neúspěšný, protože došlo k chybě systému, pokud je stavový kód 410, 500 nebo 503, který slouží k monitorování dostupnosti účtu při hodinové členitosti. |

### <a name="cassandra-api-metrics"></a>rozhraní API Cassandra metriky

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Description|Dimenze| Časové členitosti| Využití |
|---|---|---|---| ---| ---|
| CassandraRequests (žádosti Cassandra) | Počet (počet) | Počet provedených požadavků rozhraní API Cassandra| DatabaseName, CollectionName, ErrorCode, region, typem operace OperationType, ResourceType| Vše| Používá se k monitorování požadavků Cassandra při minutové členitosti. Chcete-li získat průměrné požadavky za sekundu, použijte agregaci Count za minutu a dělenou 60.|
| CassandraRequestCharges (poplatky za žádosti Cassandra) | Počet (Sum, min, Max, AVG) | Jednotky žádostí spotřebované rozhraní API Cassandraem | DatabaseName, CollectionName, oblast, typem operace OperationType, ResourceType| Vše| Slouží k monitorování ru používaných za minutu účtem rozhraní API Cassandra.|
| CassandraConnectionClosures (uzávěry připojení Cassandra) |Počet (počet) |Počet uzavřených připojení Cassandra| ClosureReason, oblast| Vše | Slouží k monitorování připojení mezi klienty a Azure Cosmos DB rozhraní API Cassandra.|

Další informace najdete [v seznamu všech metrik platforem podporovaných v Azure monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="resource-logs"></a>Protokoly prostředků

V následující tabulce jsou uvedeny vlastnosti protokolu prostředků v Azure Cosmos DB. Protokoly prostředků se shromažďují do protokolů Azure Monitor nebo Azure Storage. V Azure Monitor se protokoly shromažďují v tabulce **AzureDiagnostics** pod poskytovatelem prostředků * * název `MICROSOFT.DOCUMENTDB` .

| Azure Storage pole nebo vlastnost | Vlastnost protokolů Azure Monitor | Description |
| --- | --- | --- |
| **interval** | **TimeGenerated** | Datum a čas (UTC), kdy došlo k operaci. |
| **Prostředku** | **Prostředek** | Účet Azure Cosmos DB, pro který jsou povoleny protokoly.|
| **kategorií** | **Kategorie** | Pro Azure Cosmos DB jsou dostupné typy protokolů, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests**, **CassandraRequests**, **GremlinRequests** . |
| **operationName** | **OperationName** | Název operace Název operace může být  `Create` ,,,, `Update` `Read` `ReadFeed` `Delete` , `Replace` , `Execute` , `SqlQuery` , `Query` , `JSQuery` , `Head` , `HeadFeed` nebo `Upsert` .   |
| **vlastnosti** | Není k dispozici | Obsah tohoto pole je popsán v následujících řádcích. |
| **activityId** | **activityId_g** | Jedinečný identifikátor GUID pro protokolované operaci. |
| **userAgent** | **userAgent_s** | Řetězec, který určuje uživatelského agenta klienta, ze kterého byl požadavek odeslán. Formát uživatelského agenta je `{user agent name}/{version}` .|
| **requestResourceType** | **requestResourceType_s** | Typ dostupného prostředku Tato hodnota může být databáze, kontejner, dokument, příloha, uživatel, oprávnění, uložená procedura, aktivační událost, uživatelem definovaná funkce nebo nabídka. |
| **statusCode** | **statusCode_s** | Stav odpovědi operace. |
| **requestResourceId** | **ResourceId** | ResourceId, který se vztahuje k žádosti. V závislosti na provedené operaci může tato hodnota ukazovat na `databaseRid` , `collectionRid` nebo `documentRid` .|
| **clientIpAddress** | **clientIpAddress_s** | IP adresa klienta. |
| **requestCharge** | **requestCharge_s** | Počet RU/s, které tato operace používá |
| **collectionRid** | **collectionId_s** | Jedinečné ID pro kolekci.|
| **úkolu** | **duration_d** | Doba trvání operace v milisekundách. |
| **requestLength** | **requestLength_s** | Délka požadavku (v bajtech). |
| **responseLength** | **responseLength_s** | Délka odpovědi v bajtech|
| **resourceTokenPermissionId** | **resourceTokenPermissionId_s** | Tato vlastnost označuje ID oprávnění tokenu prostředku, které jste zadali. Další informace o oprávněních najdete v článku [zabezpečený přístup k datům](./secure-access-to-data.md#permissions) . |
| **resourceTokenPermissionMode** | **resourceTokenPermissionMode_s** | Tato vlastnost označuje režim oprávnění, který jste nastavili při vytváření tokenu prostředku. Režim oprávnění může mít hodnoty jako "vše" nebo "číst". Další informace o oprávněních najdete v článku [zabezpečený přístup k datům](./secure-access-to-data.md#permissions) . |
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Tato hodnota není prázdná, pokud se pro ověřování používají [tokeny prostředků](./secure-access-to-data.md#resource-tokens) . Hodnota odkazuje na ID prostředku uživatele. |
| **responseLength** | **responseLength_s** | Délka odpovědi v bajtech|

Seznam všech kategorií protokolu Azure Monitor a odkazy na související schémata najdete v tématu [protokoly Azure monitor kategorií a schémat](../azure-monitor/essentials/resource-logs-schema.md). 

## <a name="azure-monitor-logs-tables"></a>Tabulky protokolů Azure Monitor

Azure Cosmos DB používá tabulky Kusto z protokolů Azure Monitor. Pomocí Log Analytics můžete zadávat dotazy na tyto tabulky. Seznam Kustoch použití Bales najdete v článku [referenční informace k tabulce Azure monitor protokoly](/azure/azure-monitor/reference/tables/tables-resourcetype#azure-cosmos-db) .

## <a name="see-also"></a>Viz také

- Popis Azure Cosmos DB monitorování najdete v tématu [monitorování Azure Cosmos DB](monitor-cosmos-db.md) .
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md) .