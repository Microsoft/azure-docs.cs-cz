---
title: Kopírování dat z Cassandra pomocí Azure Data Factory | Microsoft Docs
description: Postup kopírování dat z Cassandra do úložiště dat podporovaných podřízený pomocí aktivity kopírování v kanál služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
ms.openlocfilehash: a0095ae4aa50845a24cabb981399ac4035afdebe
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051446"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Kopírování dat z Cassandra pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Aktuální verze](connector-cassandra.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z databáze Cassandra. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Z databáze Cassandra může kopírovat data do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Cassandra podporuje:

- Cassandra **verze 2.x a 3.x**.
- Kopírování dat pomocí **základní** nebo **anonymní** ověřování.

>[!NOTE]
>Aktivity systémem Self-hosted integrace Runtime, Cassandra 3.x je podporována, protože IR verze 3.7 a vyšší.

## <a name="prerequisites"></a>Požadavky

Ke zkopírování dat z databáze Cassandra, která není veřejně přístupný, musíte nastavit Self-hosted integrace Runtime. V tématu [Self-hosted integrace Runtime](create-self-hosted-integration-runtime.md) článku se dozvíte podrobnosti. Modul Runtime integrace poskytuje integrované ovladače Cassandra, proto nemusíte ručně nainstalovat všechny ovladače při kopírování dat z/do Cassandra.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke Cassandra konektoru.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Cassandra propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type |Vlastnost typu musí být nastavena na: **Cassandra** |Ano |
| hostitel |Jeden nebo více IP adres nebo názvů hostitelů Cassandra serverů.<br/>Zadejte seznam IP adres nebo názvů hostitelů se připojit na všechny servery současně. |Ano |
| port |Port TCP, který používá Cassandra server naslouchat pro připojení klientů. |Ne (výchozí hodnota je 9042) |
| authenticationType. | Typ ověřování používaný pro připojení k databázi Cassandra.<br/>Povolené hodnoty jsou: **základní**, a **anonymní**. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno pro uživatelský účet. |Ano, pokud authenticationType je nastaven na Basic. |
| heslo |Zadejte heslo pro uživatelský účet. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano, pokud authenticationType je nastaven na Basic. |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je veřejně přístupná data store), můžete použít modul Runtime integrace Self-hosted nebo Runtime integrace Azure. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

>[!NOTE]
>Připojení k Cassandra pomocí protokolu SSL není aktuálně podporována.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje Cassandra datovou sadu.

Ke zkopírování dat z Cassandra, nastavte vlastnost typu datové sady, která **CassandraTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **CassandraTable** | Ano |
| keyspace |Název keyspace nebo schéma v Cassandra databáze. |Ne (když je určena "dotaz" pro "CassandraSource") |
| tableName |Název tabulky v databázi Cassandra. |Ne (když je určena "dotaz" pro "CassandraSource") |

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


Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje Cassandra zdroje.

### <a name="cassandra-as-source"></a>Cassandra jako zdroj

Ke zkopírování dat z Cassandra, nastavte typ zdroje v aktivitě kopírování do **CassandraSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **CassandraSource** | Ano |
| query |Čtení dat pomocí vlastního dotazu. |Dotaz SQL 92 nebo CQL dotazu. V tématu [CQL odkaz](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Při použití příkazu jazyka SQL, zadejte **keyspace name.table název** představují tabulky, které mají být zobrazeny. |Ne (pokud jsou zadané "tableName" a "keyspace" v datové sadě). |
| consistencyLevel |Úroveň konzistence Určuje, kolik repliky musí odpovědět na požadavek čtení před vrácením dat do klientské aplikace. Cassandra ověří zadaný počet replik pro data, aby pokryl požadavek na čtení. V tématu [konfigurace konzistenci dat](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) podrobnosti.<br/><br/>Povolené hodnoty jsou: **jeden**, **dva**, **tři**, **KVORA**, **všechny**, **LOCAL_ KVORA**, **EACH_QUORUM**, a **LOCAL_ONE**. |Ne (výchozí hodnota je `ONE`) |

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

Při kopírování dat z Cassandra, se používají následující mapování Cassandra datových typů k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| Cassandra datový typ | Typ průběžných dat objektu pro vytváření dat |
|:--- |:--- |
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

## <a name="work-with-collections-using-virtual-table"></a>Práce s kolekcí pomocí virtuální tabulky

Azure Data Factory používá integrované ovladače ODBC pro připojení k a kopírování dat z databáze Cassandra. Ovladač pro typy kolekcí včetně mapy, sady a seznamem, renormalizes data do odpovídající virtuální tabulky. Konkrétně Pokud tabulka obsahuje všechny sloupce, kolekce, ovladač generuje následující virtuální tabulky:

* A **základní tabulka**, která obsahuje stejná data jako skutečné tabulky s výjimkou kolekce sloupců. Základní tabulka používá stejný název jako skutečné tabulky, která reprezentuje.
* A **virtuální tabulku** pro každý sloupec kolekce, které rozšíří vnořená data. Virtuální tabulky, které představují kolekce jsou pojmenované pomocí názvu skutečné tabulky oddělovač "*vt*" a název sloupce.

Virtuální tabulky odkazovat na data v tabulce skutečné povolení ovladače pro přístup k datům nenormalizované. Příklad naleznete v části Podrobnosti. Dotazování a připojení virtuální tabulky, můžete přístup k obsahu Cassandra kolekcí.

### <a name="example"></a>Příklad:

Například následující "ExampleTable" je Cassandra tabulku databáze, která obsahuje celé číslo primární klíče sloupec s názvem "pk_int", o textový sloupec s názvem hodnotu, sloupce seznamu, mapy sloupec a sadu sloupec (s názvem "StringSet").

| pk_int | Hodnota | Seznam | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"Ukázková hodnota 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"Ukázková hodnota 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Ovladač by vygeneroval více virtuální tabulky k reprezentaci této jednu tabulku. Sloupce cizích klíčů v tabulkách virtuální odkazovat sloupců primárních klíčů v tabulce skutečné a označit skutečné tabulky řádek, který odpovídá řádku virtuální tabulky.

První virtuální tabulky je, že na základní tabulku s názvem "ExampleTable" je uvedené v následující tabulce: 

| pk_int | Hodnota |
| --- | --- |
| 1 |"Ukázková hodnota 1" |
| 3 |"Ukázková hodnota 3" |

Základní tabulka obsahuje stejná data jako původní tabulky databáze s výjimkou kolekce, které jsou vynechání z této tabulky a rozšířit ostatní virtuální tabulky.

Následující tabulky popisují virtuální tabulky, které renormalize data ze seznamu a mapy, StringSet sloupce. Sloupce s názvy, které končí "_index" nebo "_key" označuje pozici dat v rámci původního seznamu nebo mapy. Sloupce s názvy, které končí "_value" obsahují Rozšířená data z kolekce.

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
| 3 |S1 |T |

**Tabulka "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
