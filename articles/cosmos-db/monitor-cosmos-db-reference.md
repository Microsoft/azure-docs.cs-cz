---
title: Referenční informace k datům monitorování Azure Cosmos DB | Microsoft Docs
description: Reference k protokolům a metrikám pro monitorování dat z Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d243224192b5761af45d387690f5fb41b84481e6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588718"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Referenční informace o monitorování Azure Cosmos DB dat
Tento článek obsahuje referenční informace o datech protokolů a metrik, která se shromažďují za účelem analýzy výkonu a dostupnosti služby Azure Cosmos DB. Podrobnosti o shromažďování a analýze dat monitorování pro Azure Cosmos DB najdete v tématu [monitorování Cosmos DB](monitor-cosmos-db.md) .


## <a name="resource-logs"></a>Protokoly prostředků
V následující tabulce jsou uvedeny vlastnosti Azure Cosmos DBch protokolů prostředků při jejich shromažďování v protokolech Azure Monitor nebo Azure Storage. V Azure Monitor protokoly se shromažďují v tabulce **AzureDiagnostics** s hodnotou **ResourceProvider** *společnosti Microsoft. DOCUMENTDB*. 

| Azure Storage pole nebo vlastnost | Vlastnost protokolů Azure Monitor | Popis |
| --- | --- | --- |
| **interval** | **TimeGenerated** | Datum a čas (UTC), kdy došlo k chybě operace. |
| **Prostředku** | **Prostředek** | Účet Azure Cosmos DB, pro kterou jsou povolené protokoly.|
| **kategorií** | **Kategorie** | Pro protokoly Azure Cosmos DB jsou dostupné typy protokolů, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** . |
| **operationName** | **OperationName** | Název operace Tato hodnota může být některý z následujících operací: vytvoření, aktualizace, čtení, ReadFeed, odstranění, nahradit, Execute, SqlQuery, dotazu, JSQuery, Head, HeadFeed nebo Upsert.   |
| **vlastnosti** | neuvedeno | Obsah tohoto pole jsou popsány v řádcích, které následují. |
| **activityId** | **activityId_g** | Jedinečný identifikátor GUID pro protokolovaných operací. |
| **userAgent** | **userAgent_s** | Řetězec, který určuje uživatelský agent klienta, který provádí požadavek. Formát je {uživatelské jméno agenta} / {version}.|
| **requestResourceType** | **requestResourceType_s** | Typ prostředku, u níž. Tato hodnota může být některý z těchto typů prostředků: databáze, kontejner, dokument, přílohy, uživatele, oprávnění, uložené procedury StoredProcedure, aktivační událost, UserDefinedFunction nebo nabídky. |
| **statusCode** | **statusCode_s** | Stav odpovědi operace. |
| **requestResourceId** | **Prostředku** | ID prostředku, které se vztahují k požadavku. Hodnota může odkazovat na databaseRid, collectionRid nebo documentRid v závislosti na operaci provést.|
| **clientIpAddress** | **clientIpAddress_s** | IP adresa klienta. |
| **requestCharge** | **requestCharge_s** | Počet rezervovaných jednotek, které jsou používány operace |
| **collectionRid** | **collectionId_s** | Jedinečný Identifikátor pro kolekci.|
| **úkolu** | **duration_s** | Doba trvání operace v milisekundách. |
| **requestLength** | **requestLength_s** | Délka požadavku v bajtech. |
| **responseLength** | **responseLength_s** | Délka odpovědi v bytech.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Tato hodnota není prázdná, pokud se pro ověřování používají [tokeny prostředků](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) . Hodnota odkazuje na ID prostředku uživatele. |

Seznam všech kategorií protokolu Azure Monitor a odkazy na související schémata najdete v tématu [protokoly Azure monitor kategorií a schémat](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Metriky
V následujících tabulkách jsou uvedeny metriky platforem shromážděné pro službu Azure CosmOS DB. Všechny metriky jsou uložené v oboru názvů **Cosmos DB Standardní metriky**.

Seznam všech metrik podpory Azure Monitor (včetně CosmosDB) najdete v článku [Azure monitor podporované metriky](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Metrika žádosti
            
|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace) |Popis|Dimenze| Časové členitosti| Zastaralé mapování metrik | Využití |
|---|---|---|---| ---| ---| ---|
| TotalRequests (celkový počet požadavků) | Počet (počet) | Počet provedených požadavků| DatabaseName, CollectionName, region, StatusCode| Vše | TotalRequests, HTTP 2xx, http 3xx, HTTP 400, HTTP 401, interní chyba serveru, nedostupný služba, omezené požadavky, průměrný počet požadavků za sekundu | Slouží k monitorování požadavků na stavový kód v kontejneru při minutové členitosti. Chcete-li získat průměrné požadavky za sekundu, použijte agregaci Count za minutu a dělenou 60. |
| MetadataRequests (žádosti o metadata) |Počet (počet) | Počet požadavků na metadata Azure Cosmos DB uchovává kontejner systémových metadat pro každý účet, který umožňuje vytvořit výčet kolekcí, databází atd. a jejich konfigurací zdarma. | DatabaseName, CollectionName, region, StatusCode| Vše| |Slouží k monitorování omezení z důvodu požadavků na metadata.|
| MongoRequests (žádosti Mongo) | Počet (počet) | Počet provedených požadavků Mongo | DatabaseName, CollectionName, region, příkaz, ErrorCode| Vše |Frekvence požadavků na dotaz Mongo, frekvence požadavků na aktualizace Mongo, rychlost žádosti o odstranění Mongo, rychlost žádosti o vložení Mongo, počet požadavků na zadání Mongo| Slouží k monitorování chyb požadavků Mongo, využití na typ příkazu. |

#### <a name="request-unit-metrics"></a>Metriky jednotek požadavku

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Popis|Dimenze| Časové členitosti| Zastaralé mapování metrik | Využití |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (poplatek za požadavek Mongo) | Počet (celkem) |Spotřebované jednotky žádosti Mongo| DatabaseName, CollectionName, region, příkaz, ErrorCode| Vše |Poplatek za požadavek na dotaz Mongo, poplatek za požadavek na aktualizaci Mongo, poplatek za požadavek na odstranění, Mongo vložení žádosti, poplatek za požadavek na počet Mongo| Používá se k monitorování ru prostředků Mongo za minutu.|
| TotalRequestUnits (celkový počet jednotek žádosti)| Počet (celkem) | Spotřebované jednotky žádosti| DatabaseName, CollectionName, region, StatusCode |Vše| TotalRequestUnits| Používá se k monitorování celkového využití RU při minutové členitosti. K získání průměrného RU za sekundu za sekundu použijte celkovou agregaci za minutu a rozdělte je 60.|
| ProvisionedThroughput (zřízená propustnost)| Počet (maximum) |Zřízená propustnost při členitosti kontejneru| DatabaseName, ContainerName| 5 000 000| | Slouží k monitorování zřízené propustnosti na kontejner.|

#### <a name="storage-metrics"></a>Metriky úložiště

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Popis|Dimenze| Časové členitosti| Zastaralé mapování metrik | Využití |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (úložiště k dispozici) |Bajty (celkem) | Celkové dostupné úložiště hlášené v členitosti 5 minut na oblast| DatabaseName, CollectionName, region| 5 000 000| Úložiště k dispozici| Minimální členitost, která se používá k monitorování dostupné kapacity úložiště (platí jenom pro kolekce pevných úložišť), by měla být 5 minut.| 
| Využití dat (využití dat) |Bajty (celkem) |Celkové využití dat nahlášené v rozlišení 5 minut na oblast| DatabaseName, CollectionName, region| 5 000 000 |Velikost dat | Používá se k monitorování celkového využití dat v kontejneru a oblasti. minimální členitost by měla být 5 minut.|
| IndexUsage (využití indexu) | Bajty (celkem) |Celkové využití indexu hlášené v rozlišení 5 minut na oblast| DatabaseName, CollectionName, region| 5 000 000| Velikost indexu| Používá se k monitorování celkového využití dat v kontejneru a oblasti. minimální členitost by měla být 5 minut. |
| DocumentQuota (kvóta dokumentu) | Bajty (celkem) | Celková kvóta úložiště vykazovaná na základě rozlišení 5 minut na oblast.| DatabaseName, CollectionName, region| 5 000 000 |Kapacita úložiště| V případě, že se má monitorovat celková kvóta v kontejneru a oblasti, minimální členitost by měla být 5 minut.|
| DocumentCount (počet dokumentů) | Počet (celkem) |Celkový počet dokumentů hlášených v rozlišení 5 minut na oblast| DatabaseName, CollectionName, region| 5 000 000 |Počet dokumentů|Používá se k monitorování počtu dokumentů v kontejneru a oblasti. minimální členitost by měla být 5 minut.|

#### <a name="latency-metrics"></a>Metriky latence

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Popis|Dimenze| Časové členitosti| Využití |
|---|---|---|---| ---| ---|
| ReplicationLatency (latence replikace)| Milisekundy (minimální, maximální, průměrná) | Latence replikace p99 napříč zdrojovou a cílovou oblastí pro geograficky povolený účet| SourceRegion, TargetRegion| Vše | Slouží k monitorování latence replikace p99 mezi dvěma oblastmi pro geograficky replikovaný účet. |
| Latence na straně serveru| Milisekundy (průměr) | Čas, který server zavedl ke zpracování žádosti. | CollectionName, ConnectionMode, DatabaseName, typem operace OperationType, PublicAPIType, region | Vše | Slouží k monitorování latence žádosti na serveru Azure Cosmos DB. |



#### <a name="availability-metrics"></a>Metriky dostupnosti

|Metrika (zobrazovaný název metriky) |Jednotka (typ agregace)|Popis| Časové členitosti| Zastaralé mapování metrik | Využití |
|---|---|---|---| ---| ---|
| ServiceAvailability (dostupnost služby)| Procento (minimum, maximum) | Dostupnost žádostí o účet při jedné hodinové členitosti| 1H | Dostupnost služby | Představuje procento celkového počtu předaných požadavků. Požadavek se považuje za neúspěšný, protože došlo k chybě systému, pokud je stavový kód 410, 500 nebo 503, který slouží k monitorování dostupnosti účtu při hodinové členitosti. |


#### <a name="cassandra-api-metrics"></a>rozhraní API Cassandra metriky

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Popis|Dimenze| Časové členitosti| Využití |
|---|---|---|---| ---| ---|
| CassandraRequests (žádosti Cassandra) | Počet (počet) | Počet provedených požadavků rozhraní API Cassandra| DatabaseName, CollectionName, ErrorCode, region, typem operace OperationType, ResourceType| Vše| Používá se k monitorování požadavků Cassandra při minutové členitosti. Chcete-li získat průměrné požadavky za sekundu, použijte agregaci Count za minutu a dělenou 60.|
| CassandraRequestCharges (poplatky za žádosti Cassandra) | Počet (Sum, min, Max, AVG) | Jednotky žádostí spotřebované rozhraní API Cassandra požadavky| DatabaseName, CollectionName, oblast, typem operace OperationType, ResourceType| Vše| Slouží k monitorování ru používaných za minutu účtem rozhraní API Cassandra.|
| CassandraConnectionClosures (uzávěry připojení Cassandra) |Počet (počet) |Počet uzavřených připojení Cassandra| ClosureReason, oblast| Vše | Slouží k monitorování připojení mezi klienty a Azure Cosmos DB rozhraní API Cassandra.|

## <a name="see-also"></a>Viz také

- Popis Azure Cosmos DB monitorování najdete v tématu [monitorování Azure Cosmos DB](monitor-cosmos-db.md) .
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) .
