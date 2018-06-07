---
title: Přesun dat z Cassandra pomocí služby Data Factory | Microsoft Docs
description: Další informace o tom, jak přesunout data z databáze Cassandra místně pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6e6b9bf194da17ebd03389829ba594bf3fbf1e64
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622097"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Přesun dat z databáze Cassandra místně pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-onprem-cassandra-connector.md)
> * [Verze 2 – Preview](../connector-cassandra.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [Cassandra konektor v V2](../connector-cassandra.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z databáze Cassandra místně. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

Z úložiště dat Cassandra místní může kopírovat data do úložiště dat žádné podporované jímky. Seznam úložišť dat jako jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Objekt pro vytváření dat aktuálně podporuje pouze přesunutí dat z jiného úložiště dat Cassandra k jiným úložištím dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat Cassandra. 

## <a name="supported-versions"></a>Podporované verze
Cassandra konektor podporuje následující verze systému Cassandra: 2.X.

## <a name="prerequisites"></a>Požadavky
Pro službu Azure Data Factory být schopni připojit k vaší databázi Cassandra místní musíte nainstalovat brána pro správu dat na stejném počítači, který je hostitelem databáze nebo na samostatný počítač, aby se zabránilo neslučitelných pro prostředky s databází. Brána pro správu dat je komponenta, která připojuje místní zdroje dat do cloudové služby v zabezpečený a spravovaný. V tématu [Brána pro správu dat](data-factory-data-management-gateway.md) podrobnosti o Brána pro správu dat najdete v článku. V tématu [přesun dat z lokálního prostředí do cloudu](data-factory-move-data-between-onprem-and-cloud.md) podrobné pokyny o nastavení brány datovém kanálu pro přesun dat najdete v článku.

Musíte použít bránu pro připojení k databázi Cassandra i v případě, že databáze je hostovaná v cloudu, například na virtuálním počítači Azure IaaS. Y můžete na stejný virtuální počítač, který je hostitelem databáze může mít bránu nebo na samostatný virtuální počítač stejně dlouho jako brány může připojit k databázi.  

Při instalaci brány se automaticky nainstaluje ovladač Microsoft Cassandra ODBC použít pro připojení k databázi Cassandra. Proto nemusíte ručně nainstalovat všechny ovladače v počítači s bránou při kopírování dat z databáze Cassandra. 

> [!NOTE]
> V tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení nebo brány související s problémy.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště místní Cassandra data pomocí různých nástrojů nebo rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data. 
- Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Příklad s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat z úložiště dat Cassandra místní, naleznete v tématu [JSON příklad: kopírování dat z Cassandra do objektu Blob Azure](#json-example-copy-data-from-cassandra-to-azure-blob) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení konkrétní entity služby Data Factory k úložišti dat Cassandra:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro Cassandra propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **OnPremisesCassandra** |Ano |
| hostitel |Jeden nebo více IP adres nebo názvů hostitelů Cassandra serverů.<br/><br/>Zadejte seznam IP adres nebo názvů hostitelů se připojit na všechny servery současně. |Ano |
| port |Port TCP, který používá Cassandra server naslouchat pro připojení klientů. |Ne, výchozí hodnota: 9042 |
| authenticationType. |Basic nebo Anonymous |Ano |
| uživatelské jméno |Zadejte uživatelské jméno pro uživatelský účet. |Ano, pokud authenticationType je nastaven na Basic. |
| heslo |Zadejte heslo pro uživatelský účet. |Ano, pokud authenticationType je nastaven na Basic. |
| gatewayName |Název brány, která se používá pro připojení k místní databázi Cassandra. |Ano |
| encryptedCredential |Přihlašovací údaje zašifrovaná pomocí brány. |Ne |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

**Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace o umístění dat v úložišti dat. Rámci typeProperties část datové sady typ **CassandraTable** má následující vlastnosti

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| keyspace |Název keyspace nebo schéma v Cassandra databáze. |Ano (Pokud **dotazu** pro **CassandraSource** není definován). |
| tableName |Název tabulky v databázi Cassandra. |Ano (Pokud **dotazu** pro **CassandraSource** není definován). |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti dostupné v rámci typeProperties části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

Pokud je zdroj typu **CassandraSource**, následující vlastnosti jsou k dispozici v rámci typeProperties části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Dotaz SQL 92 nebo CQL dotazu. V tématu [CQL odkaz](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Při použití příkazu jazyka SQL, zadejte **keyspace name.table název** představují tabulky, které mají být zobrazeny. |Ne (pokud jsou definovány tableName a keyspace v sadě dat). |
| consistencyLevel |Úroveň konzistence Určuje, kolik repliky musí odpovědět na požadavek čtení před vrácením dat do klientské aplikace. Cassandra ověří zadaný počet replik pro data, aby pokryl požadavek na čtení. |JEDEN, DVA, TŘI, KVORA, VŠE, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. V tématu [konfigurace konzistenci dat](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) podrobnosti. |Ne. Výchozí hodnota je 1. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Příklad JSON: kopírování dat z Cassandra do objektu Blob Azure
Tento příklad obsahuje ukázkové JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak zkopírovat data z databáze Cassandra místně do Azure Blob Storage. Však lze zkopírovat data do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka obsahuje fragmenty kódu JSON. Podrobné pokyny pro vytvoření objektu pro vytváření dat neobsahuje. V tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) podrobné pokyny najdete v článku.

Ukázka má následující entity objektu pro vytváření dat:

* Propojené služby typu [OnPremisesCassandra](#linked-service-properties).
* Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstup [datovou sadu](data-factory-create-datasets.md) typu [CassandraTable](#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [CassandraSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Cassandra propojené služby:**

Tento příklad používá **Cassandra** propojené služby. V tématu [Cassandra propojená služba](#linked-service-properties) části vlastnostech podporovaných zprostředkovatelem tuto propojenou službu.  

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Propojená služba Azure Storage:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Cassandra vstupní datové sady:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Nastavení **externí** k **true** služba Data Factory informuje, že datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

**Azure Blob výstupní datovou sadu:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Aktivita kopírování v kanálu s Cassandra zdrojový a podřízený objekt Blob:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **CassandraSource** a **podřízený** je typ nastaven na **BlobSink**.

V tématu [vlastnosti typu RelationalSource](#copy-activity-properties) pro seznam vlastností nepodporuje RelationalSource.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]    
    }
}
```

### <a name="type-mapping-for-cassandra"></a>Mapování typu pro Cassandra
| Typ Cassandra | .NET na základě typu |
| --- | --- |
| ASCII |Řetězec |
| BIGINT |Int64 |
| OBJEKT BLOB |Byte] |
| LOGICKÁ HODNOTA |Logická hodnota |
| DECIMAL |Decimal |
| DOUBLE |Double |
| PLOVOUCÍ DESETINNÁ ČÁRKA |Jednoduchá |
| INET |Řetězec |
| INT |Int32 |
| TEXT |Řetězec |
| ČASOVÉ RAZÍTKO |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |Řetězec |
| VARINT |Decimal |

> [!NOTE]
> Kolekce typů (mapy, sady, seznamu atd.), najdete v části [pracovat s typy kolekcí Cassandra pomocí virtuální tabulky](#work-with-collections-using-virtual-table) části.
>
> Uživatelem definované typy nejsou podporovány.
>
> Délka binárního sloupce a sloupce, řetězec délky nemůže být větší než 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Práce s kolekcí pomocí virtuální tabulky
Azure Data Factory používá integrované ovladače ODBC pro připojení k a kopírování dat z databáze Cassandra. Ovladač pro typy kolekcí včetně mapy, sady a seznamem, renormalizes data do odpovídající virtuální tabulky. Konkrétně Pokud tabulka obsahuje všechny sloupce, kolekce, ovladač generuje následující virtuální tabulky:

* A **základní tabulka**, která obsahuje stejná data jako skutečné tabulky s výjimkou kolekce sloupců. Základní tabulka používá stejný název jako skutečné tabulky, která reprezentuje.
* A **virtuální tabulku** pro každý sloupec kolekce, které rozšíří vnořená data. Virtuální tabulky, které představují kolekce jsou pojmenované pomocí názvu skutečné tabulky oddělovač "*vt*" a název sloupce.

Virtuální tabulky odkazovat na data v tabulce skutečné povolení ovladače pro přístup k datům nenormalizované. Příklad naleznete v části Podrobnosti. Dotazování a připojení virtuální tabulky, můžete přístup k obsahu Cassandra kolekcí.

Můžete použít [Průvodce kopírováním](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitivně zobrazit seznam tabulek v databázi Cassandra včetně virtuální tabulky a zobrazte náhled dat, uvnitř. Můžete také vytvořit dotaz v Průvodci kopírováním a ověření zobrazíte výsledek.

### <a name="example"></a>Příklad:
Například následující "ExampleTable" je Cassandra tabulku databáze, která obsahuje celé číslo primární klíče sloupec s názvem "pk_int", o textový sloupec s názvem hodnotu, sloupce seznamu, mapy sloupec a sadu sloupec (s názvem "StringSet").

| pk_int | Hodnota | Seznam | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"Ukázková hodnota 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"Ukázková hodnota 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Ovladač by vygeneroval více virtuální tabulky k reprezentaci této jednu tabulku. Sloupce cizích klíčů v tabulkách virtuální odkazovat sloupců primárních klíčů v tabulce skutečné a označit skutečné tabulky řádek, který odpovídá řádku virtuální tabulky.

První virtuální tabulky je základní tabulka s názvem "ExampleTable" je uvedené v následující tabulce. Základní tabulka obsahuje stejná data jako původní tabulky databáze s výjimkou kolekce, které jsou vynechání z této tabulky a rozšířit ostatní virtuální tabulky.

| pk_int | Hodnota |
| --- | --- |
| 1 |"Ukázková hodnota 1" |
| 3 |"Ukázková hodnota 3" |

Následující tabulky popisují virtuální tabulky, které renormalize data ze seznamu a mapy, StringSet sloupce. Sloupce s názvy, které končí "_index" nebo "_key" označuje pozici dat v rámci původního seznamu nebo mapy. Sloupce s názvy, které končí "_value" obsahují Rozšířená data z kolekce.

#### <a name="table-exampletablevtlist"></a>Tabulka "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletablevtmap"></a>Tabulka "ExampleTable_vt_Map":
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |T |

#### <a name="table-exampletablevtstringset"></a>Tabulka "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Mapování zdroje jímky sloupců
Další informace o mapování sloupců v datové sadě zdrojového sloupce v datové sadě podřízený najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelných číst z relační zdrojů
Při kopírování dat z relačních dat ukládá, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Zásady opakovaných pokusů pro datovou sadu můžete také nakonfigurovat tak, aby řez se znovu spustí, když dojde k chybě. Řez se znovu spustí, buď způsobem, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát řez je spustit. V tématu [Repeatable číst z relačními zdroji](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
