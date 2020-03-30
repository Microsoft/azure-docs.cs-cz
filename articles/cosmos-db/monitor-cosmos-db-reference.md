---
title: Odkaz na data monitorování Azure Cosmos DB | Dokumenty společnosti Microsoft
description: Odkaz na protokola a metriky pro data monitorování z Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d243224192b5761af45d387690f5fb41b84481e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588718"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Referenční informace o monitorování dat ve službě Azure Cosmos DB
Tento článek obsahuje referenční informace o datech protokolů a metrik, která se shromažďují za účelem analýzy výkonu a dostupnosti služby Azure Cosmos DB. Podrobnosti o shromažďování a analýze dat monitorování pro Azure Cosmos DB najdete v tématu [Monitoring Cosmos](monitor-cosmos-db.md) DB.


## <a name="resource-logs"></a>Protokoly prostředků
V následující tabulce jsou uvedeny vlastnosti pro protokoly prostředků Azure Cosmos DB, když se shromažďují v protokolech monitorování Azure nebo azure storage. V protokolech monitorování Azure se shromažďují v tabulce **AzureDiagnostics** s hodnotou **ResourceProvider** *společnosti MICROSOFT. DOCUMENTDB*. 

| Pole nebo vlastnost úložiště Azure | Azure Monitor Logs, vlastnost | Popis |
| --- | --- | --- |
| **Čas** | **TimeGenerated** | Datum a čas (UTC), kdy došlo k operaci. |
| **Resourceid** | **Zdrojů** | Účet Azure Cosmos DB, pro které jsou povoleny protokoly.|
| **Kategorie** | **Kategorie** | Pro protokoly Azure Cosmos DB, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** jsou dostupné typy protokolů. |
| **název_operace** | **Název operace** | Název operace. Tato hodnota může být některou z následujících operací: Vytvořit, Aktualizovat, Číst, ReadFeed, Odstranit, Nahradit, Spustit, SqlQuery, Dotaz, JSQuery, Head, HeadFeed nebo Upsert.   |
| **Vlastnosti** | neuvedeno | Obsah tohoto pole je popsán v následujících řádcích. |
| **activityId** | **activityId_g** | Jedinečný identifikátor GUID pro protokolotou operaci. |
| **Useragent** | **userAgent_s** | Řetězec, který určuje agenta klienta uživatele, který provádí požadavek. Formát je {user agent name}/{version}.|
| **requestResourceType** | **requestResourceType_s** | Typ prostředku, ke které se přistupuje. Tato hodnota může být libovolný z následujících typů prostředků: Databáze, Kontejner, Dokument, Příloha, Uživatel, Oprávnění, StoredProcedure, Trigger, UserDefinedFunction nebo Offer. |
| **Statuscode** | **statusCode_s** | Stav odezvy operace. |
| **requestResourceId** | **ResourceId** | ResourceId, která se týkající se požadavku. Hodnota může přejděte na databaseRid, collectionRid nebo documentRid v závislosti na provedené operaci.|
| **adresa klientIpAddress** | **clientIpAddress_s** | IP adresa klienta. |
| **requestCharge** | **requestCharge_s** | Počet ru, které jsou používány operace |
| **collectionRid** | **collectionId_s** | Jedinečné ID kolekce.|
| **Doba trvání** | **duration_s** | Doba trvání operace v milisekundách. |
| **requestLength** | **requestLength_s** | Délka požadavku v bajtech. |
| **responseDélka** | **responseLength_s** | Délka odpovědi v bajtů.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Tato hodnota není prázdná, pokud se [tokeny prostředků](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) používají k ověřování. Hodnota odkazuje na ID prostředku uživatele. |

Seznam všech kategorií protokolů Azure Monitor a odkazy na přidružené schémata najdete v [tématu azure monitor protokoly kategorií a schémat .](../azure-monitor/platform/diagnostic-logs-schema.md) 

## <a name="metrics"></a>Metriky
V následujících tabulkách jsou uvedeny metriky platformy shromážděné pro Azure CosmOS DB. Všechny metriky jsou uloženy ve **standardních metrikách**oboru názvů Cosmos DB .

Seznam všech metrik podpory Azure Monitoru (včetně CosmosDB) najdete v tématu [Azure Monitor podporované metriky](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Metriky požadavků
            
|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace) |Popis|Dimenze| Časové granularity| Starší metrické mapování | Využití |
|---|---|---|---| ---| ---| ---|
| TotalRequests (celkový počet požadavků) | Počet (počet) | Počet posudků| Název databáze, Název_kolekce, Oblast, Stavový kód| Všechny | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Chyba interního serveru, Služba není k dispozici, omezené požadavky, průměrné požadavky za sekundu | Používá se ke sledování požadavků na stavový kód, kontejner na minutu rozlišovací schopnost. Chcete-li získat průměrné požadavky za sekundu, použijte počet agregace v minutě a vydělit 60. |
| Požadavky na metadata (požadavky metadat) |Počet (počet) | Počet požadavků metadat. Azure Cosmos DB udržuje kontejner metadat systému pro každý účet, který umožňuje výčet kolekcí, databází atd., a jejich konfigurace, zdarma. | Název databáze, Název_kolekce, Oblast, Stavový kód| Všechny| |Slouží ke sledování omezení z důvodu požadavků metadat.|
| MongoRequests (Mongo žádosti) | Počet (počet) | Počet posudků Mongo | Název databáze, Název_kolekce, Oblast, CommandName, ErrorCode| Všechny |Rychlost požadavků na dotazy Mongo, míra požadavků na aktualizaci Mongo, rychlost odstranění požadavků Mongo, míra požadavků vložení Mongo, míra požadavků počtu Mongo,| Používá se ke sledování chyb požadavků Mongo, použití na typ příkazu. |

#### <a name="request-unit-metrics"></a>Metriky jednotky požadavku

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Popis|Dimenze| Časové granularity| Starší metrické mapování | Využití |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Poplatek za požadavek Mongo) | Počet (celkem) |Spotřebované jednotky požadavku Mongo| Název databáze, Název_kolekce, Oblast, CommandName, ErrorCode| Všechny |Mongo Poplatek za žádost o dotaz, Mongo Update Request Charge, Mongo Delete Request Charge, Mongo Insert Request Charge, Mongo Count Request Charge| Slouží ke sledování r. prostředků Mongo za minutu.|
| TotalRequestUnits (celkové jednotky požadavků)| Počet (celkem) | Spotřebované jednotky požadavku| Název databáze, Název_kolekce, Oblast, Stavový kód |Všechny| Jednotky TotalRequest| Používá se ke sledování využití total ru v jedné minutě rozlišovací schopnost. Chcete-li získat průměrnou spotřebovanou ru za sekundu, použijte celkovou agregaci v minutě a vydělte 60.|
| Zřízená průchodnost (zřízená propustnost)| Počet (maximálně) |Zřízená propustnost při rozlišovací schopnosti kontejneru| Název_databáze, Název_kontejneru| 5M| | Slouží ke sledování zřízené propustnost i na kontejner.|

#### <a name="storage-metrics"></a>Metriky úložiště

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Popis|Dimenze| Časové granularity| Starší metrické mapování | Využití |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (dostupné úložiště) |Počet bajtů (celkem) | Celkové dostupné úložiště vykázané při rozlišovací schopnosti 5 minut na oblast| Název_databáze, Název_kolekce, Oblast| 5M| Úložiště k dispozici| Používá se ke sledování dostupné skladovací kapacity (platí pouze pro kolekce pevných úložišť) Minimální rozlišovací schopnost by měla být 5 minut.| 
| Využití dat (využití dat) |Počet bajtů (celkem) |Celkové využití dat vykázané při rozlišovací schopnosti 5 minut na oblast| Název_databáze, Název_kolekce, Oblast| 5M |Velikost dat | Používá se ke sledování celkového využití dat v kontejneru a oblasti, minimální rozlišovací schopnost by měla být 5 minut.|
| Využití indexu (využití indexu) | Počet bajtů (celkem) |Celkové využití indexu vykázané při rozlišovací schopnosti 5 minut na oblast| Název_databáze, Název_kolekce, Oblast| 5M| Velikost indexu| Používá se ke sledování celkového využití dat v kontejneru a oblasti, minimální rozlišovací schopnost by měla být 5 minut. |
| DocumentQuota (kvóta dokumentu) | Počet bajtů (celkem) | Celková kvóta úložiště hlášená při rozlišovací schopnosti 5 minut na oblast.| Název_databáze, Název_kolekce, Oblast| 5M |Kapacita úložiště| Používá se ke sledování celkové kvóty v kontejneru a oblasti, minimální rozlišovací schopnost by měla být 5 minut.|
| DocumentCount (počet dokumentů) | Počet (celkem) |Celkový počet dokumentů hlášených při rozlišovací schopnosti 5 minut na oblast| Název_databáze, Název_kolekce, Oblast| 5M |Počet dokumentů|Používá se ke sledování počtu dokumentů v kontejneru a oblasti, minimální rozlišovací schopnost by měla být 5 minut.|

#### <a name="latency-metrics"></a>Metriky latence

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Popis|Dimenze| Časové granularity| Využití |
|---|---|---|---| ---| ---|
| ReplicationLatency (latence replikace)| Milisekundy (minimální, maximální, průměrné) | Latence replikace P99 napříč zdrojovými a cílovými oblastmi pro geograficky povolený účet| SourceRegion, Cílová oblast| Všechny | Používá se ke sledování latence replikace P99 mezi libovolnými dvěma oblastmi pro geograficky replikovaný účet. |
| Latence na straně serveru| Milisekundy (průměr) | Čas, který server zpracoval ke zpracování požadavku. | CollectionName, ConnectionMode, DatabaseName, OperationType, PublicAPIType, Region | Všechny | Slouží ke sledování latence požadavku na serveru Azure Cosmos DB. |



#### <a name="availability-metrics"></a>Metriky dostupnosti

|Metrika (zobrazovaný název metriky) |Jednotka (typ agregace)|Popis| Časové granularity| Starší metrické mapování | Využití |
|---|---|---|---| ---| ---|
| Dostupnost služby (dostupnost služby)| Procento (minimální, maximální) | Dostupnost požadavků na účet v rozlišovací schopnost jedné hodiny| 1H | Dostupnost služby | Představuje procento celkového počtu předaných požadavků. Požadavek se považuje za neúspěšný z důvodu systémové chyby, pokud je stavový kód 410, 500 nebo 503 Používá se ke sledování dostupnosti účtu v hodinové rozlišovací schopnosti. |


#### <a name="cassandra-api-metrics"></a>Metriky rozhraní CASSAndSA API

|Metrika (zobrazovaný název metriky)|Jednotka (typ agregace)|Popis|Dimenze| Časové granularity| Využití |
|---|---|---|---| ---| ---|
| CassandraŽádosti (Cassandra žádosti) | Počet (počet) | Počet požadavků rozhraní CASSANDRA API| Název_databáze, název_kolekce, kód errorcode, oblast, typ operace, typ_prostředku| Všechny| Používá se ke sledování požadavků Cassandry v jedné chvíli granularity. Chcete-li získat průměrné požadavky za sekundu, použijte počet agregace v minutě a vydělit 60.|
| CassandraRequestCharges (Cassandra požadovat poplatky) | Počet (Součet, Min, Max, Vzdusi) | Jednotky požadavků spotřebované požadavky rozhraní CASSAndAndRo API| Název_databáze, Název_kolekce, Oblast, Typ operace, Typ prostředku| Všechny| Používá se ke sledování ru používá za minutu pomocí účtu rozhraní API Cassandra.|
| CassandraConnectionClosures (Uzavření připojení Cassandra) |Počet (počet) |Počet uzavřených spojení Cassandra| ClosureReason, Region| Všechny | Slouží ke sledování připojení mezi klienty a rozhraní mandatního rozhraní Azure Cosmos DB Cassandra.|

## <a name="see-also"></a>Viz také

- Viz [Monitorování Azure Cosmos DB](monitor-cosmos-db.md) pro popis monitorování Azure Cosmos DB.
- Podrobnosti o monitorování prostředků Azure najdete v tématu [Sledování prostředků Azure pomocí Azure Monitoru.](../azure-monitor/insights/monitor-azure-resource.md)
