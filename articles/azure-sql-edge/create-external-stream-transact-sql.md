---
title: Vytvoření externího datového proudu (Transact-SQL) – Azure SQL Edge (Preview)
description: Přečtěte si o příkazu CREATE EXTERNAL STREAM ve službě Azure SQL Edge (Preview).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e1f672a62ee7687fec9cea96ca03240c893ba95
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233337"
---
# <a name="create-external-stream-transact-sql"></a>VYTVOŘIT externí datový proud (Transact-SQL)

Objekt externího datového proudu má dvojí účel vstupu i výstupu. Dá se použít jako vstup pro dotazování streamování dat ze služeb zpracování událostí, jako je Azure Event nebo IoT Hub, nebo jako výstup, který určuje, kde a jak se mají ukládat výsledky z dotazu streamování.

EXTERNÍ datový proud lze také určit a vytvořit jako výstup i vstup pro služby, jako je například centrum událostí nebo úložiště objektů BLOB. To je pro zřetězení scénářů, kdy dotaz streamování ukládá výsledky do externího datového proudu jako výstup a jiný dotaz streamování čte ze stejného externího datového proudu jako vstup. 


## <a name="syntax"></a>Syntaxe

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>Argumenty


- [EXTERNÍ ZDROJ DAT](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FORMÁT EXTERNÍHO SOUBORU](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **Location**: Určuje název skutečných dat nebo umístění ve zdroji dat. V případě hraničního centra nebo objektu datového proudu Kafka určuje umístění název centra Edge nebo Kafka, ze kterého se má číst nebo do kterého zapisovat.
- **INPUT_OPTIONS**: Zadejte možnosti jako páry klíč-hodnota pro služby, jako jsou události a centra IoT, které jsou vstupy pro streamování dotazů.
    - CONSUMER_GROUP: centra událostí a IoT omezují počet čtenářů v jedné skupině příjemců (na 5). Když necháte toto pole prázdné, použije se skupina příjemců $Default.
      - Platí pro centra událostí a IOT.
    - TIME_POLICY: popisuje, zda mají být události předávány, nebo upravit dobu události při opožděných událostech nebo událostech mimo pořadí, která překročí hodnotu tolerance.
      - Platí pro centra událostí a IOT.
    - LATE_EVENT_TOLERANCE: maximální přijatelná časová prodleva. Zpoždění představuje rozdíl mezi časovým razítkem události a systémovými hodinami.
      - Platí pro centra událostí a IOT.
    - OUT_OF_ORDER_EVENT_TOLERANCE: události se můžou po odeslání ze vstupu do dotazu streamování dorazit mimo pořadí. Tyto události se dají přijmout tak, jak jsou, nebo můžete zvolit, že se má nastavit období pro změnu uspořádání.
      - Platí pro centra událostí a IOT.
- **OUTPUT_OPTIONS**: Zadejte možnosti jako páry klíč-hodnota pro podporované služby, které jsou výstupem pro streamování dotazů. 
  - REJECT_POLICY: DROP | Pokud dojde k chybám převodu dat, zkuste znovu nakládat zásady zpracování chyb dat. 
    - Platí pro všechny podporované výstupy. 
  - MINIMUM_ROWS:  
    Minimální počet řádků vyžadovaných pro dávku zapsaných do výstupu. V případě Parquet bude každá dávka vytvářet nový soubor. 
    - Platí pro všechny podporované výstupy. 
  - MAXIMUM_TIME:  
    Maximální doba čekání na dávku Po uplynutí této doby bude dávka zapsána do výstupu i v případě, že požadavek na minimální řádky není splněn. 
    - Platí pro všechny podporované výstupy. 
  - PARTITION_KEY_COLUMN:  
    Sloupec, který se používá pro klíč oddílu. 
    - Platí pro centrum událostí a Service Bus téma. 
  - PROPERTY_COLUMNS:  
    Čárkami oddělený seznam názvů výstupních sloupců, které budou připojeny k zprávám jako vlastní vlastnosti, pokud jsou k dispozici.  
    - Týká se centra událostí a Service Bus tématu a fronty. 
  - SYSTEM_PROPERTY_COLUMNS:  
    Kolekce dvojic název-hodnota ve formátu JSON názvů systémových vlastností a výstupních sloupců, které se mají naplnit Service Busmi zprávami např. {"MessageId": "Sloupec1"; "PartitionKey": "Sloupec2"} 
    - Platí pro Service Bus téma a frontu. 
  - PARTITION_KEY:  
    Název výstupního sloupce obsahujícího klíč oddílu. Klíč oddílu je jedinečný identifikátor oddílu v dané tabulce, který tvoří první část primárního klíče entity. Jedná se o řetězcovou hodnotu, která může mít velikost až 1 KB. 
    - Platí pro Table Storage a Azure Functions 
  - ROW_KEY:  
    Název výstupního sloupce obsahujícího klíč řádku Klíč řádku je jedinečný identifikátor pro entitu v daném oddílu. Tvoří druhou část primárního klíče entity. Klíč řádku je řetězcová hodnota, která může mít velikost až 1 KB. 
    - Platí pro Table Storage a Azure Functions 
  - BATCH_SIZE:  
    Představuje počet transakcí pro úložiště tabulek, kde maximum může přejít až k 100 záznamů. Pro Azure Functions to představuje velikost dávky v bajtech odeslaných do funkce na volání – výchozí hodnota je 256 kB. 
    - Platí pro Table Storage a Azure Functions 
  - MAXIMUM_BATCH_COUNT:  
    Maximální počet událostí odeslaných funkci na volání funkce Azure Functions – výchozí hodnota je 100. Pro SQL Database to představuje maximální počet záznamů odeslaných s každou hromadnou operací vložení – výchozí hodnota je 10 000. 
    - Platí pro SQL Database a Azure Functions 
  - STAGING_AREA: externí objekt zdroje dat, který Blob Storage pracovní oblast pro příjem dat s vysokou propustností do SQL Data Warehouse 
    - Platí pro SQL Data Warehouse


## <a name="examples"></a>Příklady

### <a name="example-1---edgehub"></a>Příklad 1 – EdgeHub

Typ: vstup nebo výstup<br>
Parametry:
- Vstup nebo výstup
  - Alias 
  - Formát serializace události 
  - Kódování 
- Pouze vstup: 
  - Typ komprese události 

Syntaxe:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Příklad 2 – Azure SQL Database, Azure SQL Edge, SQL Server

Typ: výstup<br>
Parametry:
- Alias pro výstup  
- Databáze (požadováno pro SQL Database) 
- Server (požadováno pro SQL Database) 
- Uživatelské jméno (povinné pro SQL Database) 
- Heslo (požadováno pro SQL Database) 
- Table 
- Sloučí všechny vstupní oddíly do jednoho nebo zděděného schématu oddílu předchozího kroku dotazu nebo vstupu (vyžaduje se pro SQL Database). 
- Maximální počet dávek 

Syntaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Database/Edge 
CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = ‘<DatabaseName>.<SchemaName>.<TableName>’ 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>Příklad 3 – Kafka

Typ: vstup<br>
Parametry:

- Kafka Bootstrap Server 
- Název tématu Kafka 
- Počet zdrojových oddílů 

Syntaxe:

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = ' kafka://<kafkaserver>:<ipaddress>’, 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = ‘<KafkaTopicName>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>Příklad 4 – BLOB Storage

Typ: vstup nebo výstup<br>
Parametry:
- Vstup nebo výstup:
  - Alias 
  - Účet úložiště 
  - Klíč účtu úložiště 
  - Kontejner 
  - Vzor cesty 
  - Formát data 
  - Formát času 
  - Formát serializace události 
  - Kódování 
- Pouze vstup: 
  - Oddíly (vstup) 
  - Typ komprese události (vstup) 
- Pouze výstup: 
  - Minimální počet řádků (výstup) 
  - Maximální doba (výstup) 
  - Režim ověřování (výstup) 

Syntaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = 'AccountKey'; 
 
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredNameMSI 
WITH IDENTITY = 'Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = ‘<path_pattern>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 1’, 
  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60’ 
); 
```

### <a name="example-5---event-hub"></a>Příklad 5 – centrum událostí

Typ: vstup nebo výstup<br>
Parametry:
- Vstup nebo výstup:
  - Alias 
  - Obor názvů Service Bus 
  - Název centra událostí 
  - Název zásad centra událostí 
  - Klíč zásad centra událostí 
  - Formát serializace události 
  - Kódování 
- Pouze vstup: 
  - Skupina uživatelů centra událostí 
  - Typ komprese události 
- Pouze výstup: 
  - Sloupec klíče oddílu 
  - Sloupce vlastností 

Syntaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = ‘<topicname>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      ‘CONSUMER_GROUP: FirstConsumerGroup’, 
          
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()’ 
);
```

### <a name="example-6---iot-hub"></a>Příklad 6 – IOT hub

Typ: vstup<br>
Parametry:

- Alias vstupu 
- IoT Hub 
- Koncový bod 
- Název zásady sdíleného přístupu 
- Klíč zásad sdíleného přístupu 
- Skupina uživatelů 
- Formát serializace události 
- Kódování 
- Typ komprese události 

Syntaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net’, 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘ENDPOINT: Messaging, 
      CONSUMER_GROUP: ‘FirstConsumerGroup’ 
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>Příklad 7 – Azure synapse Analytics (dříve SQL Data Warehouse)

Typ: výstup<br>
Parametry:
- Alias pro výstup 
- Databáze 
- Server 
- Uživatelské jméno 
- Heslo 
- Table 
- Pracovní oblast (pro kopírování) 

Syntaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = ‘<TableName>’ 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source’, 
); 
```


### <a name="example-8---table-storage"></a>Příklad 8 – úložiště tabulek

Typ: výstup<br>
Parametry:
- Alias pro výstup 
- Účet úložiště 
- Klíč účtu úložiště 
- Název tabulky 
- Klíč oddílu 
- Klíč řádku 
- Velikost dávky 

Syntaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = ‘Storage account Key’, 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>Příklad 9 Service Bus tématu (stejné vlastnosti jako Queue)

Typ: výstup<br>
Parametry:
- Alias pro výstup 
- Obor názvů Service Bus 
- Název tématu 
- Název zásad tématu 
- Klíč zásad tématu 
- Sloupce vlastností 
- Sloupce systémových vlastností 
- Formát serializace události 
- Kódování 

Syntaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = ‘<topic_name>’, 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()’ 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>Příklad 10 – Cosmos DB

Typ: výstup<br>
Parametry:
- Alias pro výstup 
- Account ID 
- Klíč účtu 
- Databáze 
- Název kontejneru 
- ID dokumentu 


Syntaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = ‘Storage Account Key’, 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = ' cosmosdb://accountid.documents.azure.com:443/ database’, 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = ‘<container/documentID>’ 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>Příklad 11 Power BI

Typ: výstup<br>
Parametry:
- Alias pro výstup 
- Název datové sady 
- Název tabulky 


Syntaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = ‘Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/’, 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>Příklad 12 – funkce Azure

Typ: výstup<br>
Parametry:
- Function App 
- Funkce 
- Klíč 
- Maximální velikost dávky 
- Maximální počet dávek 

Syntaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = ‘Function Key’, 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: 'Drop'      
     PARTITION_KEY: ‘<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


## <a name="see-also"></a>Viz také

- [ALTER EXTERNAL STREAM (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [Vynechat externí datový proud (Transact-SQL)](drop-external-stream-transact-sql.md) 

