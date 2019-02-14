---
title: Přesun dat z Cassandry pomocí služby Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z místní databáze Cassandra pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 38d5d469c920cafa33e0cc5b37846df2dc6d6ab9
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236405"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Přesun dat z místní databáze Cassandra pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-onprem-cassandra-connector.md)
> * [Verze 2 (aktuální verze)](../connector-cassandra.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Cassandra konektor ve verzi V2](../connector-cassandra.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunu dat z místní databáze Cassandra. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

Kopírování dat z do místního úložiště dat Cassandra do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky, najdete v článku [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Data factory aktuálně podporuje pouze přesouvá data z úložiště dat Cassandra do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat Cassandra.

## <a name="supported-versions"></a>Podporované verze
Cassandra konektor podporuje následující verze Cassandra: 2.x a 3.x. Pro aktivitu běžící na modul Integration Runtime, Cassandra 3.x se podporuje od verze 3.7 IR a vyšší.

## <a name="prerequisites"></a>Požadavky
Služba Azure Data Factory se moct připojit k místní databázi Cassandra musíte nainstalovat bránu správy dat ve stejném počítači, který je hostitelem databáze nebo na samostatném počítači, aby se zabránilo soutěží o prostředky s databází. Brána správy dat je komponenta, která se připojuje místních zdrojů dat ke cloudovým službám způsobem, zabezpečení a správě. Zobrazit [brána správy dat](data-factory-data-management-gateway.md) , kde najdete podrobnosti o brána správy dat. Zobrazit [přesun dat z místních do cloudu](data-factory-move-data-between-onprem-and-cloud.md) najdete podrobné pokyny o nastavení brány datového kanálu pro přesun dat.

Musíte použít bránu pro připojení k databázi Cassandra i v případě, že databáze je hostována v cloudu, například na virtuálním počítači Azure IaaS. Y můžete bránu může mít na stejném virtuálním počítači, který je hostitelem databáze nebo na samostatný virtuální počítač až brána lze připojit k databázi.

Když bránu instalujete, automaticky nainstaluje ovladač Microsoft Cassandra ODBC používá pro připojení k databázi Cassandra. Proto není nutné ručně nainstalovat všechny ovladače na počítači brány a kopírování dat z databáze Cassandra.

> [!NOTE]
> Naleznete v tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení/bránou související problémy.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště dat místní Cassandra pomocí různých nástrojů a rozhraní API.

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data.
- Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory.
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování.
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup.

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON. Tady je příklad s definice JSON entit služby Data Factory, které se používají ke kopírování dat z úložiště dat v místním Cassandra najdete v části [příklad JSON: Kopírování dat z Cassandry do objektů Blob v Azure](#json-example-copy-data-from-cassandra-to-azure-blob) části tohoto článku.

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory konkrétní do úložiště dat Cassandra:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro Cassandra propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavená na: **OnPremisesCassandra** |Ano |
| hostitel |Jeden nebo více IP adres nebo názvů hostitele serveru Cassandra.<br/><br/>Zadejte seznam IP adres nebo názvů hostitele pro připojení ke všem serverům současně. |Ano |
| port |Port TCP, který Cassandra server používá k naslouchání pro připojení klientů. |Ne, výchozí hodnota: 9042 |
| authenticationType. |Základní nebo anonymní |Ano |
| uživatelské jméno |Zadejte uživatelské jméno uživatelského účtu. |Ano, pokud typ ověřování je nastavena na Basic. |
| heslo |Zadejte heslo pro uživatelský účet. |Ano, pokud typ ověřování je nastavena na Basic. |
| gatewayName |Název brány, který se používá pro připojení k místní databázi Cassandra. |Ano |
| encryptedCredential |Přihlašovací údaje zašifrované pomocí brány. |Ne |

>[!NOTE]
>Připojení k Cassandra pomocí protokolu SSL se aktuálně nepodporuje.

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny datové sady typy (Azure SQL, Azure blob, tabulky Azure, atd.).

**TypeProperties** oddílu se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. TypeProperties části datové sady typu **CassandraTable** má následující vlastnosti

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| prostor klíčů |Název prostor klíčů nebo schéma databáze Cassandra. |Ano (Pokud **dotazu** pro **CassandraSource** není definován). |
| tableName |Název tabulky v databázi Cassandra. |Ano (Pokud **dotazu** pro **CassandraSource** není definován). |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti v části typeProperties aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

Pokud je zdroj typu **CassandraSource**, v části typeProperties jsou k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Použijte vlastní dotaz číst data. |SQL-92 nebo dotazu CQL. Zobrazit [CQL odkaz](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Při použití jazyka SQL, zadejte **keyspace name.table název** představující tabulku, kterou dotaz. |Ne (pokud jsou definovány tableName a prostor klíčů pro datovou sadu). |
| consistencyLevel |Úrovně konzistentnosti Určuje, kolik repliky musí odpovědět na požadavek čtení před vrácením data do klientské aplikace. Cassandra ověří zadaný počet replik pro data splňují požadavek na čtení. |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Zobrazit [konfigurace konzistentnosti dat](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) podrobnosti. |Ne. Výchozí hodnota je 1. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Příklad JSON: Kopírování dat z Cassandry do objektů Blob v Azure
V tomto příkladu obsahuje ukázky JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak kopírovat data z místní databáze Cassandra do služby Azure Blob Storage. Ale data je možné zkopírovat do libovolné jímky uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka poskytuje fragmenty kódu JSON. Neobsahuje podrobné pokyny pro vytvoření datové továrny. Zobrazit [přesun dat mezi místními umístěními a cloudu](data-factory-move-data-between-onprem-and-cloud.md) najdete podrobné pokyny.

Ukázka obsahuje následující entit datové továrny:

* Propojené služby typu [OnPremisesCassandra](#linked-service-properties).
* Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [CassandraTable](#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [CassandraSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Cassandra propojené služby:**

V tomto příkladu **Cassandra** propojenou službu. Zobrazit [Cassandra propojená služba](#linked-service-properties) části vlastnostech podporovaných tuto propojenou službu.

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

**Vstupní datová sada Cassandra:**

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

Nastavení **externí** k **true** služby Data Factory informuje, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1).

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

**Aktivita kopírování v kanálu s Cassandra zdroje a jímky objektu Blob:**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **CassandraSource** a **jímky** je typ nastaven na **BlobSink**.

Zobrazit [vlastnosti typu RelationalSource](#copy-activity-properties) pro seznam vlastností, které jsou podporovány RelationalSource.

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
| Typ Cassandra | Základní typ .net |
| --- | --- |
| ASCII |String |
| BIGINT |Int64 |
| BLOB |Byte[] |
| DATOVÝ TYP BOOLEAN |Logická hodnota |
| DECIMAL |Decimal |
| DOUBLE |Double |
| PLOVOUCÍ DESETINNOU ČÁRKOU |Single |
| INET |String |
| INT |Int32 |
| TEXT |String |
| ČASOVÉ RAZÍTKO |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |String |
| VARINT |Decimal |

> [!NOTE]
> Kolekce typů (mapování, sady, seznam, atd.), najdete v tématu [práci s typy kolekcí Cassandra pomocí virtuální tabulky](#work-with-collections-using-virtual-table) oddílu.
>
> Uživatelem definované typy nejsou podporovány.
>
> Délka řetězce a binární sloupec délky nemůže být větší než 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Práce s kolekcí pomocí virtuální tabulky
Azure Data Factory používá k připojení a zkopírování dat z databáze Cassandra integrované ovladače ODBC. Ovladač pro typy kolekcí, včetně mapy, set a list, renormalizes data do odpovídající virtuální tabulky. Konkrétně Pokud tabulka obsahuje všechny sloupce kolekce, ovladač generuje následující virtuální tabulky:

* A **základní tabulka**, která obsahuje stejná data jako skutečné tabulky s výjimkou kolekce sloupců. Základní tabulka používá stejný název jako skutečné tabulky, který představuje.
* A **virtuální tabulky** pro každý sloupec kolekce, která rozšiřuje vnořené data. Virtuální tabulky, které představují kolekce jsou pojmenovány pomocí názvu tabulky skutečných oddělovač "*vt*" a název sloupce.

Virtuální tabulky odkazují na data v tabulce skutečné, povolení ovladače pro přístup k Nenormalizovaná data. Příklad naleznete v části Podrobnosti. Dotazování a připojení k virtuální tabulky můžete přístup k obsahu Cassandra kolekcí.

Můžete použít [Průvodce kopírováním](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitivně zobrazte seznam tabulek v databázi Cassandra včetně virtuální tabulky a zobrazte náhled dat uvnitř. Můžete také vytvořit dotaz v Průvodci kopírováním a ověření k zobrazení výsledku.

### <a name="example"></a>Příklad:
Například následující "ExampleTable" je tabulku databáze Cassandra, která obsahuje sloupec celých čísel primární klíč s názvem "pk_int", textového sloupce se pojmenovaná hodnota, seznam sloupců, mapování sloupců a nastavit sloupec (s názvem "StringSet").

| pk_int | Hodnota | Seznam | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"Ukázková hodnota 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"Ukázková hodnota 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Ovladač vygeneruje více virtuální tabulky k reprezentaci této jediné tabulce. Sloupce cizích klíčů v virtuální tabulky odkazovat na sloupce primárních klíčů v tabulce skutečné a označit řádek skutečné tabulky, který odpovídá řádek virtuální tabulky.

První virtuální tabulky je základní tabulku s názvem "ExampleTable" je uvedeno v následující tabulce. Základní tabulka obsahuje stejná data jako původní tabulky databáze s výjimkou kolekce, které jsou vynechány z této tabulky a rozbalení v jiné virtuální tabulky.

| pk_int | Hodnota |
| --- | --- |
| 1 |"Ukázková hodnota 1" |
| 3 |"Ukázková hodnota 3" |

Následující tabulky popisují virtuální tabulky, které znovu normalizovat data ze seznamu, mapy a StringSet sloupců. Sloupce s názvy, které končí řetězcem "_index" nebo "_klíč" označuje pozici velkých dat do původního seznamu nebo mapy. Sloupce s názvy, které končí řetězcem "_value" obsahují rozšířené dat z kolekce.

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
| 3 |S1 |t |

#### <a name="table-exampletablevtstringset"></a>Tabulka "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Mapování zdroje do jímky sloupce
Další informace o mapování sloupců v datové sadě zdroje do sloupců v datové sadě jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení z relačních zdrojů
Při kopírování dat z relačních dat ukládá, mějte opakovatelnosti aby se zabránilo neúmyslnému výsledků. Ve službě Azure Data Factory můžete znovu spustit řezu ručně. Zásady opakování pro datovou sadu můžete také nakonfigurovat tak, aby určitý řez se znovu spustí, když dojde k chybě. V obou případech se znovu spustí určitý řez, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát spustit určitý řez. Zobrazit [Repeatable z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.
