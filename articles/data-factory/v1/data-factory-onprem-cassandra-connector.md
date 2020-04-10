---
title: Přesunutí dat z Cassandra pomocí datové továrny
description: Přečtěte si, jak přesunout data z místní databáze Cassandra pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f96680f1ea91434c84d6606e3637c68c1cb5a84
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991497"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Přesunutí dat z místní databáze Cassandra pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-onprem-cassandra-connector.md)
> * [Verze 2 (aktuální verze)](../connector-cassandra.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Konektor Cassandra ve verzi 2](../connector-cassandra.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat z místní databáze Cassandra. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

Můžete zkopírovat data z místního úložiště dat Cassandra do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky naleznete v tabulce [Podporovaná úložiště dat.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data Factory aktuálně podporuje pouze přesunutí dat z úložiště dat Cassandra do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat Cassandra.

## <a name="supported-versions"></a>Podporované verze
Konektor Cassandra podporuje následující verze Cassandra: 2.x a 3.x. Pro aktivitu spuštěnou v modulu Runtime integrace s vlastním hostitelem je Cassandra 3.x podporována od infračervenéverze verze 3.7 a vyšší.

## <a name="prerequisites"></a>Požadavky
Aby se služba Azure Data Factory mohla připojit k místní databázi Cassandra, musíte nainstalovat bránu pro správu dat na stejném počítači, který je hostitelem databáze, nebo do samostatného počítače, abyste se vyhnuli konkurenci o prostředky s databází. Brána pro správu dat je komponenta, která zabezpečeně a spravovaným způsobem připojuje místní zdroje dat ke cloudovým službám. Podrobnosti o bráně pro správu dat najdete v článku [Brána pro správu](data-factory-data-management-gateway.md) dat. Podrobný návod k nastavení datového kanálu brány pro přesun dat najdete v článku [Přesun dat z místního](data-factory-move-data-between-onprem-and-cloud.md) do cloudu.

Bránu musíte použít k připojení k databázi Cassandra i v případě, že databáze je hostovaná v cloudu, například na virtuálním počítači Azure IaaS. Y Můžete mít bránu na stejném virtuálním počítači, který hostuje databázi nebo na samostatném virtuálním počítači, pokud se brána může připojit k databázi.

Při instalaci brány automaticky nainstaluje ovladač Microsoft Cassandra ODBC, který slouží k připojení k databázi Cassandra. Proto není nutné ručně instalovat žádný ovladač v počítači brány při kopírování dat z databáze Cassandra.

> [!NOTE]
> Tipy týkající se řešení problémů s připojením nebo bránou najdete v [tématu Poradce při potížích](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) s bránou.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z místního úložiště dat Cassandra pomocí různých nástrojů nebo rozhraní API.

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.
- K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON. Ukázka s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat z místního úložiště dat Cassandra, najdete v [tématu JSON příklad: Kopírování dat z Cassandra do Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) části tohoto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro úložiště dat Cassandra:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu Cassandra.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavena **na: OnPremisesCassandra** |Ano |
| host |Jedna nebo více IP adres nebo názvy hostitelů serverů Cassandra.<br/><br/>Zadejte seznam adres IP nebo názvů hostitelů oddělených čárkami, který se má současně připojit ke všem serverům. |Ano |
| port |Port TCP, který server Cassandra používá k naslouchání klientským připojením. |Ne, výchozí hodnota: 9042 |
| authenticationType |Základní nebo Anonymní |Ano |
| uživatelské jméno |Zadejte uživatelské jméno uživatelského účtu. |Ano, pokud authenticationType je nastavena na základní. |
| heslo |Zadejte heslo pro uživatelský účet. |Ano, pokud authenticationType je nastavena na základní. |
| název brány |Název brány, která se používá pro připojení k místní databázi Cassandra. |Ano |
| šifrované pověření |Pověření zašifrované bránou. |Ne |

>[!NOTE]
>V současné době není podporováno připojení k Cassandra pomocí TLS.

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl TypeProperties pro datovou sadu typu **CassandraTable** má následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| prostor kláves |Název keyspace nebo schéma v databázi Cassandra. |Ano (Pokud **dotaz** pro **CassandraSource** není definován). |
| tableName |Název tabulky v databázi Cassandra. |Ano (Pokud **dotaz** pro **CassandraSource** není definován). |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti, které jsou k dispozici v typeProperties části aktivity se liší s každým typem aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

Pokud je zdroj typu **CassandraSource**, jsou v části typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Dotaz SQL-92 nebo cql dotaz. Viz [odkaz na CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Při použití dotazu SQL zadejte **název keyspace name.table,** který bude představovat tabulku, na kterou se chcete dotazovat. |Ne (pokud jsou definovány tableName a keyspace na datové sadě). |
| consistencyLevel |Úroveň konzistence určuje, kolik replik musí reagovat na požadavek na čtení před vrácením dat do klientské aplikace. Cassandra zkontroluje zadaný počet replik pro data ke splnění požadavku na čtení. |JEDNA, DVA, TŘI, KVORum, VŠECHNY, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Podrobnosti [najdete v tématu Konfigurace konzistence dat.](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) |Ne. Výchozí hodnota je ONE. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Příklad JSON: Kopírování dat z Cassandra do objektu blob Azure
Tento příklad obsahuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak zkopírovat data z místní databáze Cassandra do úložiště objektů blob Azure. Data však můžete zkopírovat do libovolného jímky [uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

> [!IMPORTANT]
> Tato ukázka obsahuje úryvky JSON. Neobsahuje podrobné pokyny pro vytvoření datové továrny. Podrobné pokyny najdete v článku [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

Ukázka má následující entity datové továrny:

* Propojená služba typu [OnPremisesCassandra](#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [CassandraTable](#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [CassandraSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Cassandra propojená služba:**

Tento příklad používá propojenou službu **Cassandra.** Vlastnosti podporované touto propojenou službou naleznete v části [Propojené služby Cassandra.](#linked-service-properties)

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

**Propojené služby Azure Storage:**

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

**Cassandra vstupní datová sada:**

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

Nastavení **externí** **na hodnotu true** informuje službu Data Factory, že datová sada je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

**Výstupní datová sada objektu Blob Azure:**

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

**Zkopírujte aktivitu v kanálu se zdrojem Cassandra a jímkou blob:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **CassandraSource** a typ **jímky** je nastaven na **Objekt blobSink**.

Viz [Vlastnosti typu Relační zdroj](#copy-activity-properties) pro seznam vlastností podporovaných zdrojem relačních zdrojů.

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

### <a name="type-mapping-for-cassandra"></a>Mapování typů pro Cassandra
| Typ Cassandra | Typ založený na rozhraní .NET |
| --- | --- |
| ASCII |Řetězec |
| Bigint |Int64 |
| Blob |Bajt[] |
| Boolean |Logická hodnota |
| Desetinných |Desetinné číslo |
| Dvojité |Double |
| Float |Single |
| Inet |Řetězec |
| INT |Int32 |
| TEXT |Řetězec |
| Časové razítko |DateTime |
| TIMEUUID |Identifikátor GUID |
| Uuid |Identifikátor GUID |
| Varchar |Řetězec |
| PŘÍPRAVEK VARINT |Desetinné číslo |

> [!NOTE]
> Typy kolekcí (mapa, sada, seznam atd.) naleznete v části [Práce s typy kolekcí Cassandra pomocí](#work-with-collections-using-virtual-table) oddílu virtuální tabulky.
>
> Uživatelem definované typy nejsou podporovány.
>
> Délka délky binárního sloupce a sloupce řetězce nesmí být větší než 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Práce s kolekcemi pomocí virtuální tabulky
Azure Data Factory používá integrovaný ovladač ODBC pro připojení a kopírování dat z databáze Cassandra. Pro typy kolekce, včetně mapy, set a seznam, ovladač renormalizes data do odpovídající virtuální tabulky. Konkrétně pokud tabulka obsahuje všechny sloupce kolekce, ovladač generuje následující virtuální tabulky:

* **Základní tabulka**, která obsahuje stejná data jako skutečná tabulka s výjimkou sloupců kolekce. Základní tabulka používá stejný název jako skutečná tabulka, kterou představuje.
* **Virtuální tabulka** pro každý sloupec kolekce, která rozbalí vnořená data. Virtuální tabulky, které představují kolekce, jsou pojmenovány pomocí názvu skutečné tabulky, oddělovače "*vt*" a názvu sloupce.

Virtuální tabulky odkazují na data v reálné tabulce, což umožňuje ovladači přístup k nenormalizovaným datům. Podrobnosti najdete v části Příklad. K obsahu kolekcí Cassandra můžete přistupovat dotazováním a připojením k virtuálním tabulkám.

Pomocí Průvodce [kopírováním](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) můžete intuitivně zobrazit seznam tabulek v databázi Cassandra včetně virtuálních tabulek a zobrazit náhled dat uvnitř. Můžete také vytvořit dotaz v Průvodci kopírováním a ověřit, chcete-li zobrazit výsledek.

### <a name="example"></a>Příklad
Například následující "ExampleTable" je databázová tabulka Cassandra, která obsahuje celý sloupec primárního klíče s názvem "pk_int", textový sloupec s názvem value, sloupec seznamu, sloupec mapy a soubor sloupec (s názvem "StringSet").

| pk_int | Hodnota | Seznam | Mapa | Sada řetězců |
| --- | --- | --- | --- | --- |
| 1 |"hodnota vzorku 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"hodnota vzorku 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Ovladač by generovat více virtuálních tabulek představují tuto jednu tabulku. Sloupce cizího klíče ve virtuálních tabulkách odkazují na sloupce primárního klíče v reálné tabulce a označují, kterému řádku skutečné tabulky řádek virtuální tabulky odpovídá.

První virtuální tabulka je základní tabulka s názvem "ExampleTable" je zobrazena v následující tabulce. Základní tabulka obsahuje stejná data jako původní databázová tabulka s výjimkou kolekcí, které jsou z této tabulky vynechány a rozbaleny v jiných virtuálních tabulkách.

| pk_int | Hodnota |
| --- | --- |
| 1 |"hodnota vzorku 1" |
| 3 |"hodnota vzorku 3" |

V následujících tabulkách jsou uvedeny virtuální tabulky, které znovu normalizují data ze sloupců List, Map a StringSet. Sloupce s názvy, které končí "_index" nebo "_key" označují umístění dat v původním seznamu nebo mapě. Sloupce s názvy, které končí "_value" obsahují rozšířená data z kolekce.

#### <a name="table-exampletable_vt_list"></a>Tabulka "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>Tabulka "ExampleTable_vt_Map":
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>Tabulka "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj pro jímací sloupce
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete [v tématu Mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení ze relačních zdrojů
Při kopírování dat z úložišť relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete znovu spustit řez ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu tak, aby řez je znovu spustit, když dojde k selhání. Při opětovném spuštění řezu v obou směrech je třeba se ujistit, že stejná data jsou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [Opakovatelné čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
