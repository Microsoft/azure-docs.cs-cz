---
title: Kopírování dat z Cassandry pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak ke zkopírování dat z Cassandry do úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
ms.openlocfilehash: 1347012971d53728d978f378e30684311c88828b
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022271"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Kopírování dat z Cassandry pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Aktuální verze](connector-cassandra.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z databáze Cassandra. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z databáze Cassandra do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Cassandra podporuje:

- Cassandra **verze 2.x a 3.x**.
- Kopírování dat pomocí **základní** nebo **anonymní** ověřování.

>[!NOTE]
>Pro aktivitu běžící na modul Integration Runtime, Cassandra 3.x se podporuje od verze 3.7 IR a vyšší.

## <a name="prerequisites"></a>Požadavky

Ke zkopírování dat z databáze Cassandra, který není veřejně přístupná, budete muset nastavit modul Integration Runtime. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) článku se dozvíte podrobnosti. Prostředí Integration Runtime poskytuje integrované ovladač Cassandra, proto není nutné ručně nainstalovat všechny ovladače při kopírování dat z/do Cassandra.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Cassandra.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Cassandra propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost type musí být nastavená na: **Cassandra** |Ano |
| hostitel |Jeden nebo více IP adres nebo názvů hostitele serveru Cassandra.<br/>Zadejte seznam IP adres nebo názvů hostitele pro připojení ke všem serverům současně. |Ano |
| port |Port TCP, který Cassandra server používá k naslouchání pro připojení klientů. |Ne (výchozí hodnota je 9042) |
| authenticationType. | Typ ověřování používaný pro připojení k databázi Cassandra.<br/>Povolené hodnoty jsou: **Základní**, a **anonymní**. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno uživatelského účtu. |Ano, pokud typ ověřování je nastavena na Basic. |
| heslo |Zadejte heslo pro uživatelský účet. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano, pokud typ ověřování je nastavena na Basic. |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!NOTE]
>Připojení k Cassandra pomocí protokolu SSL se aktuálně nepodporuje.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje Cassandra datové sady.

Pro kopírování dat z Cassandry, nastavte vlastnost typ datové sady na **CassandraTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **CassandraTable** | Ano |
| prostor klíčů |Název prostor klíčů nebo schéma databáze Cassandra. |Ne (když je zadán "dotaz" na "CassandraSource") |
| tableName |Název tabulky v databázi Cassandra. |Ne (když je zadán "dotaz" na "CassandraSource") |

**Příklad:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování


Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem Cassandra.

### <a name="cassandra-as-source"></a>Cassandra jako zdroj

Pro kopírování dat z Cassandry, nastavte typ zdroje v aktivitě kopírování do **CassandraSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **CassandraSource** | Ano |
| query |Použijte vlastní dotaz číst data. |SQL-92 nebo dotazu CQL. Zobrazit [CQL odkaz](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Při použití jazyka SQL, zadejte **keyspace name.table název** představující tabulku, kterou dotaz. |Ne (Pokud je určen "tableName", "keyspace" v datové sadě). |
| consistencyLevel |Úrovně konzistentnosti Určuje, kolik repliky musí odpovědět na požadavek čtení před vrácením data do klientské aplikace. Cassandra ověří zadaný počet replik pro data splňují požadavek na čtení. Zobrazit [konfigurace konzistentnosti dat](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) podrobnosti.<br/><br/>Povolené hodnoty jsou: **JEDEN**, **DVĚ**, **tři**, **KVORA**, **všechny**, **LOCAL_QUORUM**, **EACH_QUORUM**, a **LOCAL_ONE**. |Ne (výchozí hodnota je `ONE`) |

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

## <a name="data-type-mapping-for-cassandra"></a>Datový typ mapování pro Cassandra

Při kopírování dat z Cassandry, se používají následující mapování z datových typů Cassandra do služby Azure Data Factory dočasné datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Typ dat Cassandra | Data factory dočasné datový typ |
|:--- |:--- |
| ASCII |Řetězec |
| BIGINT |Int64 |
| OBJEKT BLOB |Byte] |
| DATOVÝ TYP BOOLEAN |Logická hodnota |
| DECIMAL |Desítkově |
| DOUBLE |Double |
| PLOVOUCÍ DESETINNOU ČÁRKOU |Jednoduchá |
| INET |Řetězec |
| INT |Datový typ Int32 |
| TEXT |Řetězec |
| ČASOVÉ RAZÍTKO |DateTime |
| TIMEUUID |Guid |
| IDENTIFIKÁTOR UUID |Guid |
| VARCHAR |Řetězec |
| VARINT |Desítkově |

> [!NOTE]
> Kolekce typů (mapování, sady, seznam, atd.), najdete v tématu [práci s typy kolekcí Cassandra pomocí virtuální tabulky](#work-with-collections-using-virtual-table) oddílu.
>
> Uživatelem definované typy nejsou podporovány.
>
> Délka řetězce a binární sloupec délky nemůže být větší než 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Práce s kolekcí pomocí virtuální tabulky

Azure Data Factory používá k připojení a zkopírování dat z databáze Cassandra integrované ovladače ODBC. Ovladač pro typy kolekcí, včetně mapy, set a list, renormalizes data do odpovídající virtuální tabulky. Konkrétně Pokud tabulka obsahuje všechny sloupce kolekce, ovladač generuje následující virtuální tabulky:

* A **základní tabulka**, která obsahuje stejná data jako skutečné tabulky s výjimkou kolekce sloupců. Základní tabulka používá stejný název jako skutečné tabulky, který představuje.
* A **virtuální tabulky** pro každý sloupec kolekce, která rozšiřuje vnořené data. Virtuální tabulky, které představují kolekce jsou pojmenovány pomocí názvu tabulky skutečných oddělovač "*vt*" a název sloupce.

Virtuální tabulky odkazují na data v tabulce skutečné, povolení ovladače pro přístup k Nenormalizovaná data. Příklad naleznete v části Podrobnosti. Dotazování a připojení k virtuální tabulky můžete přístup k obsahu Cassandra kolekcí.

### <a name="example"></a>Příklad:

Například následující "ExampleTable" je tabulku databáze Cassandra, která obsahuje sloupec celých čísel primární klíč s názvem "pk_int", textového sloupce se pojmenovaná hodnota, seznam sloupců, mapování sloupců a nastavit sloupec (s názvem "StringSet").

| pk_int | Hodnota | Seznam | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"Ukázková hodnota 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"Ukázková hodnota 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Ovladač vygeneruje více virtuální tabulky k reprezentaci této jediné tabulce. Sloupce cizích klíčů v virtuální tabulky odkazovat na sloupce primárních klíčů v tabulce skutečné a označit řádek skutečné tabulky, který odpovídá řádek virtuální tabulky.

První virtuální tabulky je základní tabulku s názvem "ExampleTable" je uvedeno v následující tabulce: 

| pk_int | Hodnota |
| --- | --- |
| 1 |"Ukázková hodnota 1" |
| 3 |"Ukázková hodnota 3" |

Základní tabulka obsahuje stejná data jako původní tabulky databáze s výjimkou kolekce, které jsou vynechány z této tabulky a rozbalení v jiné virtuální tabulky.

Následující tabulky popisují virtuální tabulky, které znovu normalizovat data ze seznamu, mapy a StringSet sloupců. Sloupce s názvy, které končí řetězcem "_index" nebo "_klíč" označuje pozici velkých dat do původního seznamu nebo mapy. Sloupce s názvy, které končí řetězcem "_value" obsahují rozšířené dat z kolekce.

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

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
