---
title: Kopírování dat z Cassandra pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z Cassandra do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: a3cd3c3ae28ae302e9469a71d00054152a9b5fb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383700"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Kopírování dat z Cassandra pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Aktuální verze](connector-cassandra.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z databáze Cassandra. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Cassandra je podporován pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z databáze Cassandra můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor Cassandra podporuje:

- Cassandra **verze 2. x a 3. x**.
- Kopírování dat pomocí **základního** nebo **anonymního** ověřování.

>[!NOTE]
>V případě aktivity spuštěné v místním prostředí Integration Runtime se Cassandra 3. x podporuje od verze IR 3,7 a vyšší.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime poskytuje integrovaný ovladač Cassandra, takže nemusíte při kopírování dat z/do Cassandra ručně instalovat žádné ovladače.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor Cassandra.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Cassandra jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ |Vlastnost Type musí být nastavená na: **Cassandra** . |Yes |
| Hostitel |Jedna nebo víc IP adres nebo názvů hostitelů Cassandra serverů.<br/>Zadejte čárkami oddělený seznam IP adres nebo názvů hostitelů pro připojení ke všem serverům současně. |Yes |
| port |Port TCP, který server Cassandra používá k naslouchání klientským připojením. |Ne (výchozí hodnota je 9042) |
| authenticationType | Typ ověřování, který se používá pro připojení k databázi Cassandra.<br/>Povolené hodnoty jsou: **Basic** a **Anonymous**. |Yes |
| username |Zadejte uživatelské jméno pro uživatelský účet. |Ano, pokud je authenticationType nastaveno na Basic. |
| heslo |Zadejte heslo pro uživatelský účet. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |Ano, pokud je authenticationType nastaveno na Basic. |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadaný, použije se výchozí Azure Integration Runtime. |No |

>[!NOTE]
>V současné době se připojení k Cassandra pomocí protokolu TLS nepodporuje.

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných Cassandra DataSet.

Chcete-li kopírovat data z Cassandra, nastavte vlastnost Type datové sady na **CassandraTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **CassandraTable** . | Yes |
| prostor klíčů |Název prostoru klíčů nebo schématu v databázi Cassandra. |Ne (Pokud je zadáno "dotaz" pro "CassandraSource") |
| tableName |Název tabulky v databázi Cassandra |Ne (Pokud je zadáno "dotaz" pro "CassandraSource") |

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


Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných Cassandra zdrojem.

### <a name="cassandra-as-source"></a>Cassandra as source

Chcete-li kopírovat data z Cassandra, nastavte typ zdroje v aktivitě kopírování na **CassandraSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **CassandraSource** . | Yes |
| query |Pomocí vlastního dotazu můžete číst data. Dotaz SQL-92 nebo dotaz CQL Viz [odkaz na CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Při použití dotazu SQL zadejte **název prostoru klíčů. název tabulky** , který bude představovat tabulku, kterou chcete dotazovat. |Ne (Pokud je zadaná datová sada "tableName" a "mezerník"). |
| consistencyLevel |Úroveň konzistence určuje, kolik replik musí odpovídat žádosti o čtení před vrácením dat do klientské aplikace. Cassandra zkontroluje zadaný počet replik dat, aby splňovaly požadavky na čtení. Podrobnosti najdete v tématu [Konfigurace konzistence dat](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) .<br/><br/>Povolené hodnoty jsou: **jedna**, **dvě**, **tři**, **kvora**, **vše**, **LOCAL_QUORUM**, **EACH_QUORUM** a **LOCAL_ONE**. |Ne (výchozí nastavení je `ONE` ) |

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

## <a name="data-type-mapping-for-cassandra"></a>Mapování datových typů pro Cassandra

Při kopírování dat z Cassandra se v datových typech Cassandra používají následující mapování k Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování typů schématu a dat](copy-activity-schema-and-type-mapping.md) .

| Datový typ Cassandra | Typ dat interim Data Factory |
|:--- |:--- |
| ASCII |Řetězec |
| BIGINT |Int64 |
| PŘÍZNAKY |Byte [] |
| DATOVÉHO |Logická hodnota |
| NOTACI |Decimal |
| KLEPAT |dvojité |
| Plovák |Jednoduché |
| INET |Řetězec |
| INT |Int32 |
| TEXT |Řetězec |
| ČASOVÉ RAZÍTKO |DateTime |
| TIMEUUID |Identifikátor GUID |
| IDENTIFIKÁTOR |Identifikátor GUID |
| VARCHAR |Řetězec |
| VARINT |Decimal |

> [!NOTE]
> Pro typy kolekcí (mapování, nastavení, seznam atd.) se podívejte na téma [práce s typy kolekce Cassandra pomocí virtuální tabulky](#work-with-collections-using-virtual-table) .
>
> Uživatelsky definované typy nejsou podporovány.
>
> Délka sloupců binárního sloupce a řetězce sloupce nesmí být větší než 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Práce s kolekcemi pomocí virtuální tabulky

Azure Data Factory používá integrovaný ovladač ODBC pro připojení a zkopírování dat z databáze Cassandra. U typů kolekcí, včetně map, nastavení a seznamu, ovladač znovu normalizuje data v odpovídajících virtuálních tabulkách. Konkrétně, pokud tabulka obsahuje všechny sloupce kolekce, vygeneruje ovladač následující virtuální tabulky:

* **Základní tabulka**, která obsahuje stejná data jako skutečná tabulka s výjimkou sloupců kolekce. Základní tabulka používá stejný název jako skutečná tabulka, kterou představuje.
* **Virtuální tabulka** pro každý sloupec kolekce, který rozšiřuje vnořená data. Virtuální tabulky, které představují kolekce, jsou pojmenovány pomocí názvu reálné tabulky, oddělovače "*VT*" a názvu sloupce.

Virtuální tabulky odkazují na data v reálné tabulce a umožňují tak ovladači přístup k denormalizovaným datům. Podrobnosti najdete v části příklad. K obsahu kolekcí Cassandra můžete přistupovat dotazem a připojením k virtuálním tabulkám.

### <a name="example"></a>Příklad

Například následující "ukázková tabulka" je databázová tabulka Cassandra, která obsahuje sloupec primárního klíče s celými čísly s názvem "pk_int", textový sloupec s názvem Value, sloupec seznamu, sloupec mapy a sloupec sady (s názvem "StringSet").

| pk_int | Hodnota | Seznam | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"Ukázková hodnota 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"Ukázková hodnota 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Ovladač by vygeneroval několik virtuálních tabulek, které reprezentují tuto jedinou tabulku. Sloupce cizího klíče ve virtuálních tabulkách odkazují na sloupce primárního klíče v reálné tabulce a označují, na který skutečný řádek tabulky odpovídá řádek virtuální tabulky.

První virtuální tabulka je základní tabulka s názvem "priklad Table" je uvedena v následující tabulce: 

| pk_int | Hodnota |
| --- | --- |
| 1 |"Ukázková hodnota 1" |
| 3 |"Ukázková hodnota 3" |

Základní tabulka obsahuje stejná data jako původní databázová tabulka s výjimkou kolekcí, které jsou vynechány v této tabulce a rozbaleny v jiných virtuálních tabulkách.

V následujících tabulkách jsou uvedeny virtuální tabulky, které znovu normalizují data ze sloupců seznamu, mapy a StringSet. Sloupce s názvy, které končí na "_index" nebo "_key" označují pozici dat v původním seznamu nebo mapě. Sloupce s názvy, které končí řetězcem "_value", obsahují rozšířená data z kolekce.

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

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
