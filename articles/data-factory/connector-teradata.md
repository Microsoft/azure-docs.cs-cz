---
title: Kopírování dat z Teradata Vantage pomocí Azure Data Factory
description: Konektor Teradata služby Data Factory umožňuje kopírovat data z Teradata Vantage do úložišť dat, která aplikace Data Factory podporuje jako jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 4074c50aa17bf804696060134e37055a18bd0137
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680094"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Kopírování dat z Teradata Vantage pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
>
> * [Verze 1](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuální verze](connector-teradata.md)

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z Teradata Vantage. Sestaví se na [Přehled aktivit kopírování](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Teradata se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data z Teradata Vantage můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor Teradata podporuje:

- Teradata **verze 14,10, 15,0, 15,10, 16,0, 16,10 a 16,20**.
- Kopírování dat pomocí **základního** ověřování nebo ověřování **systému Windows** .
- Paralelní kopírování ze zdroje Teradata. Podrobnosti najdete v části [paralelní kopírování z Teradata](#parallel-copy-from-teradata) .

> [!NOTE]
>
> Po vydání místního prostředí Integration runtime v 3.18 Azure Data Factory upgradován konektor Teradata. Všechna existující zatížení, která používají předchozí konektor Teradata, jsou stále podporována. Pro nové úlohy je ale vhodné použít nový. Všimněte si, že nová cesta vyžaduje jinou sadu propojených služeb, datových sad a zdrojů kopírování. Podrobnosti o konfiguraci najdete v příslušných oddílech.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Modul runtime integrace poskytuje integrovaný ovladač Teradata od verze 3,18. Nemusíte ručně instalovat žádný ovladač. Ovladač vyžaduje "Visual C++ Redistributable 2012 Update 4" na počítači místního prostředí Integration runtime. Pokud ho ještě nemáte nainstalovanou, [Stáhněte si ho odsud.](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)

Pro všechny verze prostředí Integration runtime v místním prostředí, která je starší než 3,18, nainstalujte na počítači prostředí Integration runtime [rozhraní .net zprostředkovatel dat pro Teradata](https://go.microsoft.com/fwlink/?LinkId=278886), verze 14 nebo novější. 

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor Teradata.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Propojená služba Teradata podporuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavena na **Teradata**. | Ano |
| Vlastnosti | Určuje informace potřebné pro připojení k instanci Teradata. Přečtěte si následující ukázky.<br/>Můžete také vložit heslo do Azure Key Vault a načíst `password` konfiguraci z připojovacího řetězce. Další podrobnosti najdete [v tématu uložení přihlašovacích údajů v Azure Key Vault](store-credentials-in-key-vault.md) . | Ano |
| uživatelské jméno | Zadejte uživatelské jméno pro připojení ke službě Teradata. Platí při použití ověřování systému Windows. | Ne |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Můžete také zvolit odkaz na [tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). <br>Platí při použití ověřování systému Windows nebo odkazování na heslo v Key Vault pro základní ověřování. | Ne |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadaný, použije se výchozí Azure Integration Runtime. |Ano |

Další vlastnosti připojení můžete nastavit v připojovacím řetězci pro váš případ:

| Vlastnost | Popis | Výchozí hodnota |
|:--- |:--- |:--- |
| CharacterSet | Znaková sada, která se má použít pro relaci. Například `CharacterSet=UTF16`.<br><br/>Tato hodnota může být uživatelsky definovaná znaková sada nebo jedna z následujících předem definovaných znakových sad: <br/>– ASCII<br/>– UTF8<br/>– UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>-Shift-JIS (Windows, kompatibilní s DOS, KANJISJIS_0S)<br/>-EUC (kompatibilní s Unixem, KANJIEC_0U)<br/>– Sálový sálový IBM (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>-BIG5 (TCHBIG5_1R0)<br/>-GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | Výchozí hodnota je `ASCII`. |
| MaxRespSize |Maximální velikost vyrovnávací paměti odpovědí pro požadavky SQL, v kilobajtech (aktualizací KB). Například `MaxRespSize=‭10485760‬`.<br/><br/>V případě databáze Teradata verze 16,00 nebo novější je maximální hodnota 7361536. Pro připojení, která používají starší verze, je maximální hodnota 1048576. | Výchozí hodnota je `65536`. |

**Příklad použití základního ověřování**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad použití ověřování systému Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Následující datová část je stále podporována. Ale dál byste měli použít nový.

**Předchozí datová část:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
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

V této části najdete seznam vlastností podporovaných datovou sadou Teradata. Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v tématu [datové sady](concepts-datasets-linked-services.md).

Chcete-li kopírovat data z Teradata, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na `TeradataTable`. | Ano |
| databáze | Název instance Teradata. | Ne (když je zadán zdroj aktivity "query") |
| stolní | Název tabulky v instanci Teradata. | Ne (když je zadán zdroj aktivity "query") |

**Příklad:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> datová sada typu `RelationalTable` je stále podporována. Doporučujeme však použít novou datovou sadu.

**Předchozí datová část:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

V této části najdete seznam vlastností podporovaných zdrojem Teradata. Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata jako zdroj

>[!TIP]
>Pokud chcete načíst data z Teradata efektivně pomocí dělení dat, přečtěte si další informace z oddílu [paralelní kopírování z Teradata](#parallel-copy-from-teradata) .

Chcete-li kopírovat data z Teradata, v části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na `TeradataSource`. | Ano |
| query | Pro čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"`.<br>Pokud povolíte rozdělené zatížení, musíte v dotazu připojit všechny odpovídající předdefinované parametry oddílu. Příklady najdete v části [paralelní kopírování z Teradata](#parallel-copy-from-teradata) . | Ne (Pokud je zadaná tabulka v datové sadě) |
| partitionOptions | Určuje možnosti dělení dat používané při načítání dat z Teradata. <br>Povolené hodnoty jsou: **none** (default), **hash** a **DynamicRange**.<br>Pokud je povolená možnost oddílu (to znamená, že není `None`), stupeň paralelismu na souběžně načtená data z Teradata se řídí nastavením [`parallelCopies`](copy-activity-performance.md#parallel-copy) v aktivitě kopírování. | Ne |
| partitionSettings | Určete skupinu nastavení pro dělení dat. <br>Použijte, pokud není možnost oddílu `None`. | Ne |
| partitionColumnName | Zadejte název zdrojového sloupce, který bude použit oddíl rozsahu nebo oddíl hash pro paralelní kopírování. Pokud není zadaný, primární index tabulky se automaticky zjistí a použije se jako sloupec partition. <br>Použijte, pokud je možnost oddílu `Hash` nebo `DynamicRange`. Použijete-li dotaz k načtení zdrojových dat, `?AdfHashPartitionCondition` vidlice nebo `?AdfRangePartitionColumnName` v klauzuli WHERE. Viz příklad v části [paralelní kopírování z Teradata](#parallel-copy-from-teradata) . | Ne |
| partitionUpperBound | Maximální hodnota sloupce oddílu pro kopírování dat. <br>Použijte, pokud je možnost oddílu `DynamicRange`. Pokud použijete dotaz k načtení zdrojových dat, zapojte `?AdfRangePartitionUpbound` v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Teradata](#parallel-copy-from-teradata) . | Ne |
| partitionLowerBound | Minimální hodnota sloupce oddílu pro kopírování dat. <br>Použijte, pokud je možnost oddílu `DynamicRange`. Pokud použijete dotaz k načtení zdrojových dat, zapojte `?AdfRangePartitionLowbound` v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Teradata](#parallel-copy-from-teradata) . | Ne |

> [!NOTE]
>
> zdroj kopie typu `RelationalSource` je stále podporován, ale nepodporuje nové integrované paralelní načítání z Teradata (možnosti oddílu). Doporučujeme však použít novou datovou sadu.

**Příklad: kopírování dat pomocí základního dotazu bez oddílu**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Paralelní kopírování z Teradata

Konektor Data Factory Teradata poskytuje integrované datové oddíly pro kopírování dat z Teradata paralelně. Možnosti dělení dat najdete ve **zdrojové** tabulce aktivity kopírování.

![Snímek obrazovky s možnostmi oddílů](./media/connector-teradata/connector-teradata-partition-options.png)

Pokud povolíte dělenou kopii, Data Factory spustí paralelní dotazy na zdroj Teradata pro načtení dat podle oddílů. Paralelní míra je řízena nastavením [`parallelCopies`](copy-activity-performance.md#parallel-copy) u aktivity kopírování. Pokud jste například nastavili `parallelCopies` na čtyři, Data Factory souběžně generuje a spustí čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat z Teradata.

Navrhnete, abyste umožnili paralelní kopírování s vytvářením oddílů dat, zejména při načítání velkého množství dat z Teradata. Následují Doporučené konfigurace pro různé scénáře. Při kopírování dat do úložiště dat založeného na souborech je znovu zaškrtnuto, aby bylo možné zapisovat do složky jako více souborů (zadejte pouze název složky). v takovém případě je výkon lepší než zápis do jednoho souboru.

| Scénář                                                     | Navrhovaná nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Úplné načtení z velké tabulky                                   | **Parametr partition**: hash. <br><br/>Během provádění Data Factory automaticky detekuje sloupec PK, aplikuje na něj hodnotu hash a kopíruje data podle oddílů. |
| Načtení velkého množství dat pomocí vlastního dotazu.                 | **Parametr partition**: hash.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Sloupec oddílu**: Určete sloupec použitý pro použití oddílu hash. Pokud není zadán, Data Factory automaticky detekuje sloupec PK tabulky, kterou jste zadali v datové sadě Teradata.<br><br>Během provádění Data Factory nahradí `?AdfHashPartitionCondition` logikou oddílu hash a odesílá je do Teradata. |
| Načtěte velké množství dat pomocí vlastního dotazu, který má sloupec s celými čísly s rovnoměrně distribuovanou hodnotou pro dělení rozsahu. | **Možnosti oddílu**: dynamický oddíl rozsahu.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partition – sloupec**: Určete sloupec, který se používá k dělení dat. Můžete rozdělit na sloupec s datovým typem Integer.<br>**Horní hranice oddílu** a **dolní mez oddílu**: Určete, jestli chcete filtrovat podle sloupce oddílu, aby se načetla data jenom mezi dolním a horním rozsahem.<br><br>Během provádění Data Factory nahradí `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`a `?AdfRangePartitionLowbound` skutečný název sloupce a rozsahy hodnot pro každý oddíl a odesílá je do Teradata. <br>Pokud například sloupec oddílu "ID" nastaví s dolní hranicí jako 1 a horní mez jako 80, s paralelní kopií nastavenou na 4, Data Factory načte data po 4 oddíly. Jejich ID jsou mezi [1, 20], [21, 40], [41, 60] a [61, 80] v uvedeném pořadí. |

**Příklad: dotazování pomocí oddílu hash**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Příklad: dotaz s dynamickým oddílem rozsahu**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Mapování datových typů pro Teradata

Při kopírování dat z Teradata platí následující mapování. Další informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ Teradata | Data Factory pomocný datový typ |
|:--- |:--- |
| BigInt |Int64 |
| Objekt blob |Byte [] |
| Bytové |Byte [] |
| ByteInt |Int16 |
| char |Řetězec |
| Datový typ CLOB |Řetězec |
| Datum |DateTime |
| Notaci |Notaci |
| Klepat |Klepat |
| Objekty |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Integer |Uvedena |
| Den intervalu |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Každý den v intervalu hodiny |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval mezi dny a minutou |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Druhý den intervalu |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Hodina intervalu |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval – hodina až minuta |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Hodina intervalu sekund |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Minuta intervalu |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval – minuta sekunda |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Měsíc intervalu |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval sekund |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval – rok |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Interval od roku do měsíce |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Číslo |Klepat |
| Tečka (datum) |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Tečka (čas) |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Období (čas s časovým pásmem) |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Tečka (časové razítko) |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| Tečka (časové razítko s časovým pásmem) |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Čas s časovým pásmem |TimeSpan |
| Časové razítko |DateTime |
| Časové razítko s časovým pásmem |DateTime |
| VarByte |Byte [] |
| VarChar |Řetězec |
| VarGraphic |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |
| XML |Nepodporuje se. Použije explicitní přetypování ve zdrojovém dotazu. |


## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
