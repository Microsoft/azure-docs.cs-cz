---
title: Vytvoření externího datového proudu (Transact-SQL) – Azure SQL Edge
description: Přečtěte si o příkazu CREATE EXTERNAL STREAM v Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 203abe2b6def478dc1747dd4ce638b5b62707612
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101659218"
---
# <a name="create-external-stream-transact-sql"></a>VYTVOŘIT externí datový proud (Transact-SQL)

Objekt externího datového proudu má dvojí účel vstupního i výstupního datového proudu. Dá se použít jako vstup pro dotazování streamových dat ze služeb pro zpracování událostí, jako je Azure Event hub, Azure IoT Hub (nebo centra Edge) nebo Kafka, nebo se dá použít jako výstup, který určuje, kde a jak se mají ukládat výsledky z dotazu streamování.

EXTERNÍ datový proud lze také určit a vytvořit jako výstup i vstup pro služby, jako je například centrum událostí nebo úložiště objektů BLOB. To usnadňuje zřetězení scénářů, ve kterých dotaz streamování ukládá výsledky do externího datového proudu jako výstup a jiný dotaz streamování čte ze stejného externího datového proudu jako vstup.

Azure SQL Edge aktuálně podporuje jenom následující zdroje dat jako vstupy a výstupy streamu.

| Typ zdroje dat | Vstup | Výstup | Description |
|------------------|-------|--------|------------------|
| Centrum Azure IoT Edge | Y | Y | Zdroj dat pro čtení a zápis streamovaná data do centra Azure IoT Edge. Další informace najdete v tématu [IoT Edge hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).|
| SQL Database | N | Y | Připojení zdroje dat, které zapisuje streamovaná data do SQL Database. Databáze může být místní databáze ve službě Azure SQL Edge nebo Vzdálená databáze ve SQL Server nebo Azure SQL Database.|
| Kafka | Y | N | Zdroj dat pro čtení dat streamování z tématu Kafka. Podpora Kafka není k dispozici pro ARM64 verze Azure SQL Edge.|



## <a name="syntax"></a>Syntaxe

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional 
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name, 
  LOCATION = location_name, 
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional 
  [<optional_input_options>],
  [<optional_output_options>], 
  TAGS = <tag_column_value>

<optional_input_options> ::= 
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::= 
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ] 
  | TIME_POLICY: [ time_policy ] 
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ] 
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]
  
<optional_output_options> ::= 
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::= 
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ] 
   | MINIMUM_ROWS: [ row_value ] 
   | MAXIMUM_TIME: [ time_value_minutes] 
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ] 
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | PARTITION_KEY: [ partition_key_name ] 
   | ROW_KEY: [ row_key_name ] 
   | BATCH_SIZE: [ batch_size_value ] 
   | MAXIMUM_BATCH_COUNT: [ batch_value ] 
   | STAGING_AREA: [ blob_data_source ]
 
<tag_column_value> ::= -- Reserved for Future Usage
); 
```

## <a name="arguments"></a>Argumenty

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **Location**: Určuje název skutečných dat nebo umístění ve zdroji dat. 
   - V případě objektů Edge nebo Kafka datových proudů určuje umístění název centra Edge nebo Kafka pro čtení nebo zápis do.
   - Pro objekty SQL Stream (SQL Server, Azure SQL Database nebo Azure SQL Edge) umístění Určuje název tabulky. Pokud je datový proud vytvořen ve stejné databázi a schématu jako cílová tabulka, stačí pouze název tabulky. V opačném případě musíte plně kvalifikovat (<název_databáze. schema_name. TABLE_NAME) název tabulky.
   - Pro umístění objektu služby Azure Blob Storage Stream odkazuje na vzor cesty, který se má použít uvnitř kontejneru objektů BLOB. Další informace o této funkci najdete v tématu (/articles/Stream-Analytics/Stream-Analytics-define-Outputs.MD # BLOB-Storage-and-Azure-Data-Lake-Gen2).

- **INPUT_OPTIONS**: Zadejte možnosti jako páry klíč-hodnota pro služby, jako je například Kafka, IoT Edge centrum, které jsou vstupy pro streamování dotazů.
    - ODDÍLY: počet oddílů definovaných pro téma. Maximální počet oddílů, které lze použít, je omezen na 32.
      - Platí pro vstupní datové proudy Kafka
    - CONSUMER_GROUP: centra událostí a IoT omezují počet čtenářů v jedné skupině příjemců (na 5). Když necháte toto pole prázdné, použije se skupina příjemců $Default.
      - Vyhrazeno pro budoucí použití. Neplatí pro Azure SQL Edge.  
    - TIME_POLICY: popisuje, zda mají být události předávány, nebo upravit dobu události při opožděných událostech nebo událostech mimo pořadí, která překročí hodnotu tolerance.
      - Vyhrazeno pro budoucí použití. Neplatí pro Azure SQL Edge.
    - LATE_EVENT_TOLERANCE: maximální přijatelná časová prodleva. Zpoždění představuje rozdíl mezi časovým razítkem události a systémovými hodinami.
      - Vyhrazeno pro budoucí použití. Neplatí pro Azure SQL Edge.
    - OUT_OF_ORDER_EVENT_TOLERANCE: události se můžou po odeslání ze vstupu do dotazu streamování dorazit mimo pořadí. Tyto události se dají přijmout tak, jak jsou, nebo můžete zvolit, že se má nastavit období pro změnu uspořádání.
      - Vyhrazeno pro budoucí použití. Neplatí pro Azure SQL Edge.
        
- **OUTPUT_OPTIONS**: Zadejte možnosti jako páry klíč-hodnota pro podporované služby, které jsou výstupem pro streamování dotazů. 
  - REJECT_POLICY: DROP | Pokud dojde k chybám převodu dat, zkuste znovu nakládat zásady zpracování chyb dat. 
    - Platí pro všechny podporované výstupy. 
  - MINIMUM_ROWS:  
    Minimální počet řádků vyžadovaných pro dávku zapsaných do výstupu. V případě Parquet bude každá dávka vytvářet nový soubor. 
    - Platí pro všechny podporované výstupy. 
  - MAXIMUM_TIME:  
    Maximální čekací doba v minutách pro dávku Po uplynutí této doby bude dávka zapsána do výstupu i v případě, že požadavek na minimální řádky není splněn. 
    - Platí pro všechny podporované výstupy. 
  - PARTITION_KEY_COLUMN:  
    Sloupec, který se používá pro klíč oddílu. 
    - Vyhrazeno pro budoucí použití. Neplatí pro Azure SQL Edge.
  - PROPERTY_COLUMNS:  
    Čárkami oddělený seznam názvů výstupních sloupců, které budou připojeny k zprávám jako vlastní vlastnosti, pokud jsou k dispozici.  
    - Vyhrazeno pro budoucí použití. Neplatí pro Azure SQL Edge. 
  - SYSTEM_PROPERTY_COLUMNS:  
    Kolekce dvojic název-hodnota ve formátu JSON názvů systémových vlastností a výstupních sloupců, které se mají naplnit Service Busmi zprávami např. {"MessageId": "Sloupec1"; "PartitionKey": "Sloupec2"} 
    - Vyhrazeno pro budoucí použití. Neplatí pro Azure SQL Edge. 
  - PARTITION_KEY:  
    Název výstupního sloupce obsahujícího klíč oddílu. Klíč oddílu je jedinečný identifikátor oddílu v dané tabulce, který tvoří první část primárního klíče entity. Jedná se o řetězcovou hodnotu, která může mít velikost až 1 KB. 
    - Vyhrazeno pro budoucí použití. Neplatí pro Azure SQL Edge.
  - ROW_KEY:  
    Název výstupního sloupce obsahujícího klíč řádku Klíč řádku je jedinečný identifikátor pro entitu v daném oddílu. Tvoří druhou část primárního klíče entity. Klíč řádku je řetězcová hodnota, která může mít velikost až 1 KB. 
    - Vyhrazeno pro budoucí použití. Neplatí pro Azure SQL Edge.
  - BATCH_SIZE:  
    Představuje počet transakcí pro úložiště tabulek, kde maximum může přejít až k 100 záznamů. Pro Azure Functions to představuje velikost dávky v bajtech odeslaných do funkce na volání – výchozí hodnota je 256 kB. 
    - Vyhrazeno pro budoucí použití. Neplatí pro Azure SQL Edge. 
  - MAXIMUM_BATCH_COUNT:  
    Maximální počet událostí odeslaných funkci na volání funkce Azure Functions – výchozí hodnota je 100. Pro SQL Database to představuje maximální počet záznamů odeslaných s každou hromadnou operací vložení – výchozí hodnota je 10 000. 
    - Platí pro všechny výstupy založené na SQL serveru 
  - STAGING_AREA: externí objekt zdroje dat, který Blob Storage pracovní oblast pro příjem dat s vysokou propustností do Azure synapse Analytics 
    - Vyhrazeno pro budoucí použití. Neplatí pro Azure SQL Edge.


## <a name="examples"></a>Příklady

### <a name="example-1---edgehub"></a>Příklad 1 – EdgeHub

Typ: vstup nebo výstup<br>

Syntaxe:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = JSON, 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   FILE_FORMAT = myFileFormat, 
   LOCATION = '<mytopicname>', 
   OUTPUT_OPTIONS =   
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Příklad 2 – Azure SQL Database, Azure SQL Edge, SQL Server

Typ: výstup<br>

Syntaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = '<my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient 
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop'
); 
```

### <a name="example-3---kafka"></a>Příklad 3 – Kafka

Typ: vstup<br>

Syntaxe:
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = JSON, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
); 

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
    FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 5'
); 
```

## <a name="see-also"></a>Viz také

- [Vynechat externí datový proud (Transact-SQL)](drop-external-stream-transact-sql.md)
