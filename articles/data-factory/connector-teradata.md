---
title: Kopírování dat z Teradata pomocí Azure Data Factory | Dokumentace Microsoftu
description: Další informace o konektoru Teradata služby Data Factory, která umožňuje kopírování dat z databáze Teradata do úložišť dat podporovaných službou Data Factory jako jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 63f28c8b6eaceed12e1f76e9c0c5984e3b63b500
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561434"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Kopírování dat z Teradata pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, který používáte:"]
>
> * [Verze 1](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuální verze](connector-teradata.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z databáze Teradata. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z databáze Teradata do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Teradata podporuje:

- Teradata **verze 14.10 15.0, 15.10, 16.0, 16.10 a 16.20**.
- Kopírování dat pomocí **základní** nebo **Windows** ověřování.
- Paralelní kopírování ze zdroje Teradata. Zobrazit [paralelní kopírování z Teradata](#parallel-copy-from-teradata) část s podrobnostmi.

> [!NOTE]
>
> Azure Data Factory upgradovat konektoru Teradata od v3.18 modul Integration Runtime, které možnost integrované ovladač ODBC a nabízí možnosti flexibilního připojení i out-of-box paralelní kopírování pro zvýšení výkonu. Všechny stávající úlohy pomocí konektoru předchozí Teradata možnost zprostředkovatelem dat .NET pro Teradata je stále podporovány jako-se, když byly navrženy používat novou do budoucna. Upozorňujeme, že nová cesta vyžaduje jinou sadu propojené služby, datové sady/kopie zdroje. Na podrobnosti o konfiguraci najdete v příslušné části.

## <a name="prerequisites"></a>Požadavky

Pokud vaše Teradata není veřejně přístupná, budete muset nastavit modul Integration Runtime. Další informace o modulu integration runtime najdete v tématu [modul Integration Runtime](create-self-hosted-integration-runtime.md). Prostředí Integration Runtime poskytuje integrované ovladače Teradata od verze 3.18, proto není nutné ručně nainstalovat všechny ovladače. Vyžaduje ovladač "Visual C++ Redistributable 2012 s aktualizací 4" na počítači pro místní prostředí IR stahování [tady](https://www.microsoft.com/en-sg/download/details.aspx?id=30679) Pokud ještě nemáte nainstalovaný.

Pro místní prostředí IR verze nižší než 3.18, je potřeba nainstalovat [.NET Data Provider pro Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) verzi 14 nebo vyšší na počítači prostředí Integration Runtime. 

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Teradata.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Teradata propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **Teradata** | Ano |
| connectionString | Určuje informace potřebné pro připojení k instanci databáze Teradata. Podívejte se na následující ukázky.<br/>Heslo můžete také vložit do služby Azure Key Vault a o přijetí změn `password` konfigurace z připojovacího řetězce. Odkazovat na [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md) článku s dalšími podrobnostmi. | Ano |
| username jméno | Zadejte uživatelské jméno pro připojení k databázi Teradata. Platí při použití ověřování Windows. | Ne |
| password | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Můžete také nastavit [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). <br>Použije při použití ověřování Windows, nebo odkazující na hesla ve službě Key Vault pro základní ověřování. | Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Modul Integration Runtime je povinný, jak je uvedeno v [požadavky](#prerequisites). |Ano |

**Příklad: použití základního ověřování**

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

**Příklad: použití ověřování Windows**

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
> Pokud používáte Teradata možnost propojenou službu .NET Data Provider pro Teradata spolu s produktem následující datová část, je stále podporovány jako-se, když byly navrženy používat novou do budoucna.

**Předchozí datové části:**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností podporovaných datovou sadu Teradata.

Ke zkopírování dat z Teradata, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **TeradataTable** | Ano |
| database | Název databáze Teradata. | Ne (když je zadán zdroj aktivity "dotaz") |
| table | Název tabulky v databázi Teradata. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

> [!NOTE]
>
> Pokud jste používali datovou sadu typu "RelationalTable" následujícím způsobem, je stále podporovány jako-se, když byly navrženy používat novou do budoucna.

**Předchozí datové části:**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem Teradata.

### <a name="teradata-as-source"></a>Teradata jako zdroj

> [!TIP]
>
> Další informace z [paralelní kopírování z Teradata](#parallel-copy-from-teradata) část o tom, jak načíst data z efektivně pomocí dělení dat Teradata.

Ke zkopírování dat z Teradata, jsou podporovány následující vlastnosti v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **TeradataSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM MyTable"`.<br>Když povolíte dělené zatížení, budete muset připojit odpovídající integrované oddílu parametry v dotazu. Podívejte se na příklady v [paralelní kopírování z Teradata](#parallel-copy-from-teradata) oddílu. | Ne (když je tabulka v datové sadě zadán) |
| partitionOptions | Určuje data dělení možnosti používané k načtení dat z Teradata. <br>Povolit hodnoty jsou: **Žádný** (výchozí), **Hash** a **DynamicRange**.<br>Pokud je povolená možnost oddílu (ne ' None'), také nakonfigurujte **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** nastavení aktivity kopírování, například jako 4, který určuje míru paralelní současně načíst data z Teradata databáze. | Ne |
| partitionSettings | Zadejte skupinu nastavení pro dělení dat. <br>Použít, když je možnost rozdělení `None`. | Ne |
| partitionColumnName | Zadejte název zdrojového sloupce **v typu celé číslo** , který se použije rozdělením rozsah pro paralelní kopírování. Pokud není zadán, bude primární klíč tabulky automaticky zjistil a použít jako sloupec oddílu. <br>Použít, když je možnost rozdělení `Hash` nebo `DynamicRange`. Pokud používáte dotaz pro načtení zdrojová data, připojit `?AdfHashPartitionCondition` nebo `?AdfRangePartitionColumnName` v klauzuli WHERE. Viz příklad v [paralelní kopírování z Teradata](#parallel-copy-from-teradata) oddílu. | Ne |
| partitionUpperBound | Maximální hodnota sloupce oddílu mohli zkopírovat data. <br>Použít, když je možnost rozdělení `DynamicRange`. Pokud používáte dotaz pro načtení zdrojová data, připojit `?AdfRangePartitionUpbound` v klauzuli WHERE. Viz příklad v [paralelní kopírování z Teradata](#parallel-copy-from-teradata) oddílu. | Ne |
| PartitionLowerBound | Minimální hodnota sloupce oddílu mohli zkopírovat data. <br>Použít, když je možnost rozdělení `DynamicRange`. Pokud používáte dotaz pro načtení zdrojová data, připojit `?AdfRangePartitionLowbound` v klauzuli WHERE. Viz příklad v [paralelní kopírování z Teradata](#parallel-copy-from-teradata) oddílu. | Ne |

> [!NOTE]
>
> Pokud jste používali zdroj kopie zadejte "RelationalSource", je stále podporovány jako-se, ale nepodporuje novou integrovanou paralelně načtěte z Teradata (Možnosti oddílu). Byly navrženy pro použití tohoto nového objektu do budoucna.

**Příklad: kopírování dat pomocí základního dotazu bez oddílů**

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

Data factory Teradata konektoru obsahuje integrované dělení ke zkopírování dat z Teradata paralelně s vynikajícím výkonem dat. Zjistíte, že možnosti dělení dat v aktivitě kopírování -> Teradata zdroje:

![Možnosti oddílu](./media/connector-teradata/connector-teradata-partition-options.png)

Když povolíte dělené kopírování, služby data factory spouští paralelní dotazy Teradata zdroj k načtení dat oddíly. Paralelní míra je nakonfigurovaná a ovládat **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** nastavení aktivity kopírování. Například pokud nastavíte `parallelCopies` jako čtyři, současně vytvoří objekt pro vytváření dat a čtyři spouští dotazy založené na zadaný oddíl možnosti a nastavení, všechny části načítání dat z databáze Teradata.

Byly navrženy povolit paralelní kopírování s daty rozdělení do oddílů, zejména v případě, že načtete velké množství dat z databáze Teradata. Tady jsou navrhované konfigurace pro různé scénáře:

| Scénář                                                     | Doporučené nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Úplné načtení z velké tabulky                                   | **Možnost Rozdělit**: Hodnota hash. <br><br/>Během provádění data Factory automaticky rozpoznat sloupce PK, použít hodnotu hash proti nim a zkopíruje data oddíly. |
| Načtení velkého objemu dat pomocí vlastního dotazu                 | **Možnost Rozdělit**: Hodnota hash.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Sloupec oddílu**: Zadejte sloupec použitý pro oddíl použít hodnotu hash. Pokud není zadán, ADF automaticky zjistí PK sloupce tabulky, ve které jste zadali v datové sadě Teradata.<br><br>Během provádění, nahraďte objekt pro vytváření dat `?AdfHashPartitionCondition` s hash oddílu logiku a odeslat Teradata. |
| Načtení velkého objemu dat pomocí vlastního dotazu s sloupec celých čísel se rovnoměrně distribuovaných hodnotou rozsahu dělení | **Možnosti oddílu**: Dynamický rozsah oddílu.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Sloupec oddílu**: Zadejte sloupec použitý k dělení dat. Můžete dělit na sloupec s datovým typem celé číslo.<br>**Oddíl horní mez** a **oddílu dolní mez**: Určete, jestli chcete filtrovat proti sloupec oddílu pouze načíst data mezi horní a dolní rozsahu.<br><br>Během provádění, nahraďte objekt pro vytváření dat `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, a `?AdfRangePartitionLowbound` skutečný název sloupce a hodnotu rozsahy pro každou oddílů a odeslat Teradata. <br>Například pokud vaše sloupce oddílu "ID" nastavit s dolní mez jako 1 a horní mez jako 80 sadou paralelní kopie jako 4, ADF načíst data 4 oddíly s ID mezi [1,20], [21, 40], [41, 60] a [61, 80]. |

**Příklad: dotazu s oddílem hash**

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

**Příklad: dotaz s dynamický rozsah oddílu**

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

## <a name="data-type-mapping-for-teradata"></a>Datový typ mapování pro Teradata

Při kopírování dat z Teradata, se používají následující mapování z Teradata datových typů na Azure Data Factory dočasné datové typy. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Typ dat Teradata | Data factory dočasné datový typ |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Integer |Int32 |
| Interval Day |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Interval Day To Hour |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Interval Day To Minute |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Interval Day To Second |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Interval Hour |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Interval Hour To Minute |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Interval Hour To Second |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Interval Minute |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Interval Minute To Second |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Interval Month |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Interval Second |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Interval Year |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Interval Year To Month |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Číslo |Double |
| Období (datum) |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Období (čas) |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Období (čas s časovým pásmem) |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Období (časové razítko) |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Období (časové razítko s časovým pásmem) |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |
| Xml |Nepodporuje se. Použijte explicitní přetypování v dotaz na zdroj. |


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
