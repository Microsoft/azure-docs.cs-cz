---
title: Kopírování dat z Teradata Vantage pomocí Azure Data Factory
description: Teradata Konektor služby Data Factory umožňuje kopírovat data z Teradata Vantage do datových úložišť podporovaných data factory jako jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: 1e1d7cc4bb7762d3ebd29e349467f3e33c0887f9
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421224"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Kopírování dat z Teradata Vantage pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
>
> * [Verze 1](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuální verze](connector-teradata.md)

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z Teradata Vantage. Vychází z [přehledu aktivity kopírování](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Teradata je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data z Teradata Vantage do libovolného úložiště dat podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor Teradata podporuje:

- Teradata **verze 14.10, 15.0, 15.10, 16.0, 16.10 a 16.20**.
- Kopírování dat pomocí **základního** ověřování nebo ověřování **systému Windows.**
- Paralelní kopírování ze zdroje Teradata. Podrobnosti naleznete v části [Paralelní kopie z teradata.](#parallel-copy-from-teradata)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Pokud používáte prostředí Runtime integrace hostované ho s vlastním hostitelem, všimněte si, že poskytuje integrovaný ovladač Teradata od verze 3.18. Není nutné ručně instalovat žádný ovladač. Ovladač vyžaduje "Visual C++ Redistributable 2012 Update 4" v počítači runtime integrace s vlastním hostitelem. Pokud ji ještě nemáte nainstalovanou, stáhněte si ji [zde](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor Teradata.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Propojená služba Teradata podporuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **Teradata**. | Ano |
| připojovací řetězec | Určuje informace potřebné pro připojení k instanci Teradata. Viz následující ukázky.<br/>Můžete také umístit heslo v Azure Key `password` Vault a vyžádat konfiguraci z připojovacího řetězce. Další podrobnosti najdete [v přihlašovacích údajích úložiště v azure trezoru klíčů.](store-credentials-in-key-vault.md) | Ano |
| uživatelské jméno | Zadejte uživatelské jméno pro připojení k Teradata. Platí při použití ověřování systému Windows. | Ne |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Můžete také [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). <br>Platí při použití ověřování systému Windows nebo odkazování na heslo v trezoru klíčů pro základní ověřování. | Ne |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

Další vlastnosti připojení, které můžete nastavit v připojovacím řetězci podle případu:

| Vlastnost | Popis | Výchozí hodnota |
|:--- |:--- |:--- |
| Znaková sada | Znaková sada, která má být pro relaci používána. `CharacterSet=UTF16`Např.<br><br/>Tato hodnota může být uživatelem definovaná znaková sada nebo jedna z následujících předdefinovaných znakových sad: <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- Shift-JIS (Windows, DOS kompatibilní, KANJISJIS_0S)<br/>- EUC (kompatibilní s Unixem, KANJIEC_0U)<br/>- Ibm Mainframe (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | Výchozí hodnota `ASCII`je . |
| Velikost maxrespů |Maximální velikost vyrovnávací paměti odpovědí pro požadavky SQL v kilobajtech (KB). `MaxRespSize=‭10485760‬`Např.<br/><br/>Pro teradata databáze verze 16.00 nebo novější, maximální hodnota je 7361536. Pro připojení, která používají starší verze, maximální hodnota je 1048576. | Výchozí hodnota `65536`je . |

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
> Následující datová část je stále podporována. Do budoucna byste však měli použít nový.

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

Tato část obsahuje seznam vlastností podporovaných datovou sadou Teradata. Úplný seznam oddílů a vlastností dostupných pro definování datových sad naleznete v [tématu Datové sady](concepts-datasets-linked-services.md).

Kopírování dat z Teradata jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být `TeradataTable`nastavena na . | Ano |
| database | Název instance Teradata. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |
| tabulka | Název tabulky v instanci Teradata. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

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
> `RelationalTable`datové sady typu je stále podporována. Doporučujeme však použít novou datovou sadu.

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

Tato část obsahuje seznam vlastností podporovaných zdrojem Teradata. Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v [tématu Potrubí](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata jako zdroj

>[!TIP]
>Chcete-li efektivně načíst data z Teradata pomocí dělení dat, další informace z paralelní kopie z části [Teradata.](#parallel-copy-from-teradata)

Chcete-li kopírovat data z Teradata, jsou podporovány následující vlastnosti v části **zdroj aktivity** kopírování:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí `TeradataSource`být nastavena na . | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"`.<br>Když povolíte rozdělené zatížení, je třeba připojit všechny odpovídající vestavěné parametry oddílu v dotazu. Příklady naleznete [paralelní kopie z Teradata](#parallel-copy-from-teradata) části. | Ne (pokud je zadána tabulka v datové sadě) |
| partitionOptions | Určuje možnosti dělení dat použité k načtení dat z Teradata. <br>Povolit hodnoty jsou: **Žádné** (výchozí), **Hash** a **DynamicRange**.<br>Pokud je povolena možnost oddílu `None`(to znamená, že ne ), stupeň paralelismu souběžně načíst data z Teradata je řízen [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) nastavení na aktivitu kopírování. | Ne |
| partitionSettings | Zadejte skupinu nastavení pro dělení dat. <br>Použít, pokud možnost `None`oddílu není . | Ne |
| partitionColumnName | Zadejte název zdrojového sloupce, který bude použit oddílem rozsahu nebo oddílem hash pro paralelní kopírování. Pokud není zadán, primární index tabulky je automaticky rozpoznán a použit jako sloupec oddílu. <br>Použít, pokud je `Hash` `DynamicRange`možnost oddílu nebo . Pokud použijete dotaz k načtení `?AdfHashPartitionCondition` zdrojových dat, háček nebo `?AdfRangePartitionColumnName` v klauzuli WHERE. Viz příklad v paralelní kopírování z části [Teradata.](#parallel-copy-from-teradata) | Ne |
| partitionUpperBound | Maximální hodnota sloupce oddílu pro kopírování dat. <br>Použít, pokud `DynamicRange`je možnost oddílu . Pokud použijete dotaz k načtení zdrojových dat, zavěste `?AdfRangePartitionUpbound` do klauzule WHERE. Příklad naleznete paralelní [kopie z Teradata](#parallel-copy-from-teradata) části. | Ne |
| oddíl LowerBound | Minimální hodnota sloupce oddílu zkopírovat data. <br>Použít, pokud je `DynamicRange`možnost oddílu . Pokud použijete dotaz k načtení `?AdfRangePartitionLowbound` zdrojových dat, zavěste do klauzule WHERE. Příklad naleznete paralelní [kopie z Teradata](#parallel-copy-from-teradata) části. | Ne |

> [!NOTE]
>
> `RelationalSource`typ kopie zdroj je stále podporován, ale nepodporuje nové předdefinované paralelní zatížení z Teradata (možnosti oddílu). Doporučujeme však použít novou datovou sadu.

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

Data Factory Teradata konektor poskytuje vestavěné dělení dat pro paralelní kopírování dat z Teradata. Možnosti dělení dat najdete v **zdrojové** tabulce aktivity kopírování.

![Snímek obrazovky s možnostmi oddílu](./media/connector-teradata/connector-teradata-partition-options.png)

Když povolíte rozdělenou kopii, Data Factory spustí paralelní dotazy proti zdroji Teradata pro načtení dat pomocí oddílů. Paralelní stupeň je [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) řízen nastavením aktivity kopírování. Například pokud nastavíte `parallelCopies` na čtyři, Data Factory současně generuje a spouští čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat z teradata.

Doporučujese povolit paralelní kopírování s dělení dat zejména při načítání velkého množství dat z Teradata. Níže jsou navrženy konfigurace pro různé scénáře. Při kopírování dat do úložiště dat založeného na souborech je připonuto zapisovat do složky jako více souborů (pouze zadejte název složky), v takovém případě je výkon lepší než zápis do jednoho souboru.

| Scénář                                                     | Navrhovaná nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Plné zatížení z velkého stolu.                                   | **Možnost oddílu**: Hash. <br><br/>Během provádění Data Factory automaticky detekuje sloupec PK, použije hodnotu hash proti němu a zkopíruje data podle oddílů. |
| Načtěte velké množství dat pomocí vlastního dotazu.                 | **Možnost oddílu**: Hash.<br>**Dotaz** `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`: .<br>**Sloupec oddílu**: Zadejte sloupec použitý pro použití oddílu hash. Pokud není zadán, Data Factory automaticky detekuje sloupec PK tabulky, kterou jste zadali v datové sadě Teradata.<br><br>Během provádění Data Factory `?AdfHashPartitionCondition` nahradí logiku oddílu hash a odešle teradata. |
| Načtěte velké množství dat pomocí vlastního dotazu, který má celý sloupec s rovnoměrně rozloženou hodnotou pro dělení rozsahu. | **Možnosti oddílu**: Oddíl dynamického rozsahu.<br>**Dotaz** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Sloupec oddílu**: Zadejte sloupec použitý k rozdělení dat. Můžete oddíl proti sloupci s celýdatový typ.<br>**Horní mez oddílu** a **dolní mez oddílu**: Určete, zda chcete filtrovat proti sloupci oddílu, chcete-li načíst data pouze mezi dolní a horní oblastí.<br><br>Během provádění Data Factory `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`nahradí `?AdfRangePartitionLowbound` , a skutečné název sloupce a rozsahy hodnot pro každý oddíl a odešle teradata. <br>Například pokud sloupec oddílu "ID" nastavit s dolní mez jako 1 a horní mez jako 80, s paralelní kopírování nastavit jako 4, Data Factory načte data o 4 oddíly. Jejich ID jsou mezi [1,20], [21, 40], [41, 60], a [61, 80]. |

**Příklad: dotaz s oddílem hash**

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

**Příklad: dotaz s oddílem dynamického rozsahu**

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

Při kopírování dat z Teradata platí následující mapování. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, naleznete v [tématu Schémata a mapování datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ Teradata | Dočasný datový typ datové továrny |
|:--- |:--- |
| Bigint |Int64 |
| Objekt blob |Bajt[] |
| Byte |Bajt[] |
| Bajt |Int16 |
| Char |Řetězec |
| Clob |Řetězec |
| Datum |DateTime |
| Desetinné číslo |Desetinné číslo |
| Double |Double |
| Grafické |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Integer |Int32 |
| Den intervalu |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Interval den od hodiny |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Interval den na minutu |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Interval den až druhý |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Intervalová hodina |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Interval od hodiny do minuty |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Interval hodinu až sekunda |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Intervalová minuta |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Interval minuta až sekunda |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Interval měsíc |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Interval druhý |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Intervalový rok |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Interval rok od měsíce |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Číslo |Double |
| Období (datum) |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Období (čas) |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Období (čas s časovým pásmem) |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Období (časové razítko) |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Období (časové razítko s časovým pásmem) |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| Smallint |Int16 |
| Time |TimeSpan |
| Čas s časovým pásmem |TimeSpan |
| Časové razítko |DateTime |
| Časové razítko s časovým pásmem |DateTime |
| VarByte |Bajt[] |
| Varchar |Řetězec |
| VarGrafika |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |
| XML |Není podporováno. Použít explicitní přetypování ve zdrojovém dotazu. |


## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v datové továrně naleznete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
