---
title: Kopírování dat z Cassandra pomocí Azure Data Factory
description: Zjistěte, jak zkopírovat data z Cassandra do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 4b7fd2de0762de147ad3ceae0d562a1c78b33dc2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417468"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Kopírování dat z Cassandra pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Aktuální verze](connector-cassandra.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z databáze Cassandra. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Cassandra je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data z databáze Cassandra do libovolného úložiště dat podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor Cassandra podporuje:

- Cassandra **verze 2.x a 3.x**.
- Kopírování dat pomocí **základního** nebo **anonymního** ověřování.

>[!NOTE]
>Pro aktivitu spuštěnou v modulu Runtime integrace s vlastním hostitelem je Cassandra 3.x podporována od infračervenéverze verze 3.7 a vyšší.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Prostředí Integration Runtime poskytuje integrovaný ovladač Cassandra, proto není nutné ručně instalovat žádný ovladač při kopírování dat z nebo do Cassandra.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro konektor Cassandra.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Cassandra jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavena na: **Cassandra** |Ano |
| host |Jedna nebo více IP adres nebo názvy hostitelů serverů Cassandra.<br/>Zadejte seznam adres IP nebo názvů hostitelů oddělených čárkami, který se má současně připojit ke všem serverům. |Ano |
| port |Port TCP, který server Cassandra používá k naslouchání klientským připojením. |Ne (výchozí hodnota je 9042) |
| authenticationType | Typ ověřování používaný pro připojení k databázi Cassandra.<br/>Povolené hodnoty jsou: **Basic**a **Anonymous**. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno uživatelského účtu. |Ano, pokud authenticationType je nastavena na základní. |
| heslo |Zadejte heslo pro uživatelský účet. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). |Ano, pokud authenticationType je nastavena na základní. |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

>[!NOTE]
>V současné době není podporováno připojení k Cassandra pomocí TLS.

**Příklad:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Cassandra.

Chcete-li zkopírovat data z Cassandra, nastavte vlastnost type datové sady na **CassandraTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **CassandraTable** | Ano |
| prostor kláves |Název keyspace nebo schéma v databázi Cassandra. |Ne (pokud je zadán "dotaz" pro "CassandraSource") |
| tableName |Název tabulky v databázi Cassandra. |Ne (pokud je zadán "dotaz" pro "CassandraSource") |

**Příklad:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování


Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem Cassandra.

### <a name="cassandra-as-source"></a>Cassandra jako zdroj

Chcete-li zkopírovat data z Cassandra, nastavte typ zdroje v aktivitě kopírování **cassandrasource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na: **CassandraSource** | Ano |
| query |Ke čtení dat použijte vlastní dotaz. Dotaz SQL-92 nebo cql dotaz. Viz [odkaz na CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Při použití dotazu SQL zadejte **název keyspace name.table,** který bude představovat tabulku, na kterou se chcete dotazovat. |Ne (pokud jsou v datové sadě zadány "tableName" a "keyspace"). |
| consistencyLevel |Úroveň konzistence určuje, kolik replik musí reagovat na požadavek na čtení před vrácením dat do klientské aplikace. Cassandra zkontroluje zadaný počet replik pro data ke splnění požadavku na čtení. Podrobnosti [najdete v tématu Konfigurace konzistence dat.](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html)<br/><br/>Povolené hodnoty jsou: **JEDNA,** **DVĚ,** **TŘI**, **KVORA**, **VŠE**, **LOCAL_QUORUM,** **EACH_QUORUM**a **LOCAL_ONE**. |Ne (výchozí `ONE`hodnota je ) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Mapování datového typu pro Cassandra

Při kopírování dat z Cassandra se používají následující mapování z datových typů Cassandra do dočasných datových typů Azure Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování schématu a datových typů.](copy-activity-schema-and-type-mapping.md)

| Datový typ Cassandra | Dočasný datový typ datové továrny |
|:--- |:--- |
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

## <a name="work-with-collections-using-virtual-table"></a>Práce s kolekcemi pomocí virtuální tabulky

Azure Data Factory používá integrovaný ovladač ODBC pro připojení a kopírování dat z databáze Cassandra. Pro typy kolekce, včetně mapy, set a seznam, ovladač renormalizes data do odpovídající virtuální tabulky. Konkrétně pokud tabulka obsahuje všechny sloupce kolekce, ovladač generuje následující virtuální tabulky:

* **Základní tabulka**, která obsahuje stejná data jako skutečná tabulka s výjimkou sloupců kolekce. Základní tabulka používá stejný název jako skutečná tabulka, kterou představuje.
* **Virtuální tabulka** pro každý sloupec kolekce, která rozbalí vnořená data. Virtuální tabulky, které představují kolekce, jsou pojmenovány pomocí názvu skutečné tabulky, oddělovače "*vt*" a názvu sloupce.

Virtuální tabulky odkazují na data v reálné tabulce, což umožňuje ovladači přístup k nenormalizovaným datům. Podrobnosti najdete v části Příklad. K obsahu kolekcí Cassandra můžete přistupovat dotazováním a připojením k virtuálním tabulkám.

### <a name="example"></a>Příklad

Například následující "ExampleTable" je databázová tabulka Cassandra, která obsahuje celý sloupec primárního klíče s názvem "pk_int", textový sloupec s názvem value, sloupec seznamu, sloupec mapy a soubor sloupec (s názvem "StringSet").

| pk_int | Hodnota | Seznam | Mapa | Sada řetězců |
| --- | --- | --- | --- | --- |
| 1 |"hodnota vzorku 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"hodnota vzorku 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Ovladač by generovat více virtuálních tabulek představují tuto jednu tabulku. Sloupce cizího klíče ve virtuálních tabulkách odkazují na sloupce primárního klíče v reálné tabulce a označují, kterému řádku skutečné tabulky řádek virtuální tabulky odpovídá.

První virtuální tabulka je základní tabulka s názvem "ExampleTable" je uvedena v následující tabulce: 

| pk_int | Hodnota |
| --- | --- |
| 1 |"hodnota vzorku 1" |
| 3 |"hodnota vzorku 3" |

Základní tabulka obsahuje stejná data jako původní databázová tabulka s výjimkou kolekcí, které jsou z této tabulky vynechány a rozbaleny v jiných virtuálních tabulkách.

V následujících tabulkách jsou uvedeny virtuální tabulky, které znovu normalizují data ze sloupců List, Map a StringSet. Sloupce s názvy, které končí "_index" nebo "_key" označují umístění dat v původním seznamu nebo mapě. Sloupce s názvy, které končí "_value" obsahují rozšířená data z kolekce.

**Tabulka "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabulka "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**Tabulka "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
