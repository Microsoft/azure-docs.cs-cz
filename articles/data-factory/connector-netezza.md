---
title: Kopírování dat z Netezza pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z Netezza do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: jingwang
ms.openlocfilehash: 4b12c1f24f389634004de3d487a693bc588a7241
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374350"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Kopírování dat z Netezza pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z Netezza. Článek se vytvoří na [aktivitě kopírování v Azure Data Factory](copy-activity-overview.md), která představuje obecný přehled aktivity kopírování.

>[!TIP]
>V případě scénáře migrace dat z Netezza do Azure se dozvíte víc o tom, jak [pomocí Azure Data Factory migrovat data z místního serveru Netezza do Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Netezza je podporován pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)


Data z Netezza můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky, najdete v tématu [podporované úložiště a formáty dat](copy-activity-overview.md#supported-data-stores-and-formats).

Konektor Netezza podporuje paralelní kopírování ze zdroje. Podrobnosti najdete v části [paralelní kopírování z Netezza](#parallel-copy-from-netezza) .

Azure Data Factory poskytuje integrovaný ovladač, který umožňuje připojení. Pro použití tohoto konektoru nemusíte ručně instalovat žádný ovladač.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

Kanál, který používá aktivitu kopírování, můžete vytvořit pomocí sady .NET SDK, sady Python SDK, Azure PowerShell, REST API nebo šablony Azure Resource Manager. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu aktivity kopírování](quickstart-create-data-factory-dot-net.md) .

Následující části obsahují podrobné informace o vlastnostech, které můžete použít k definování Data Factory entit specifických pro konektor Netezza.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Netezza jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** musí být nastavená na **Netezza**. | Yes |
| připojovací řetězec | Připojovací řetězec ODBC, který se má připojit k Netezza. <br/>Můžete také do Azure Key Vault umístit heslo a načíst konfiguraci z `pwd` připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. | Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , který se má použít pro připojení k úložišti dat. Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadaný, použije se výchozí Azure Integration Runtime. |No |

Typický připojovací řetězec je `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>` . Následující tabulka obsahuje popis dalších vlastností, které můžete nastavit:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| SecurityLevel | Úroveň zabezpečení, kterou ovladač používá pro připojení k úložišti dat. Ovladač podporuje připojení SSL s jednosměrným ověřováním pomocí protokolu SSL verze 3. <br>Příklad: `SecurityLevel=preferredSecured`. Podporované hodnoty jsou:<br/>- **Pouze nezabezpečené** (**onlyUnSecured**): ovladač nepoužívá protokol SSL.<br/>- **Upřednostňované nezabezpečené (preferredUnSecured) (výchozí)**: Pokud server nabízí možnost výběru, ovladač nepoužívá protokol SSL. <br/>- **Upřednostňované zabezpečené (preferredSecured)**: Pokud server nabízí možnost výběru, použije ovladač protokol SSL. <br/>- **Pouze zabezpečené (onlySecured)**: ovladač se nepřipojí, pokud není k dispozici připojení SSL. | No |
| Soubor_certifikátu_CÚ | Úplná cesta k certifikátu SSL, který používá server. Příklad: `CaCertFile=<cert path>;`| Ano, pokud je povolený protokol SSL |

**Příklad**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: uložení hesla v Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

V této části najdete seznam vlastností, které datová sada Netezza podporuje.

Úplný seznam sekcí a vlastností, které jsou k dispozici pro definování datových sad, naleznete v tématu [datové sady](concepts-datasets-linked-services.md).

Chcete-li kopírovat data z Netezza, nastavte vlastnost **Type** datové sady na **NetezzaTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **NetezzaTable** . | Yes |
| schema | Název schématu. |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky |Ne (Pokud je zadáno "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem Tato vlastnost je podporována z důvodu zpětné kompatibility. `schema` `table` Pro nové zatížení použijte a. | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

V této části najdete seznam vlastností, které zdroj Netezza podporuje.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v tématu [kanály](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza as source

>[!TIP]
>Pokud chcete data z Netezza efektivně načíst pomocí dělení dat, další informace najdete v části [paralelní kopírování z Netezza](#parallel-copy-from-netezza) .

Chcete-li kopírovat data z Netezza, nastavte typ **zdroje** v aktivitě kopírování na **NetezzaSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **NetezzaSource**. | Yes |
| query | Pro čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"` | Ne (Pokud je zadáno "tableName" v datové sadě |
| partitionOptions | Určuje možnosti dělení dat, které se používají k načtení dat z Netezza. <br>Povolené hodnoty jsou: **none** (výchozí), **dataslice** a **DynamicRange**.<br>Pokud je povolená možnost oddílu (to znamená, ne `None` ), stupeň paralelismu na souběžně načtená data z databáze Netezza se řídí [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) nastavením aktivity kopírování. | No |
| partitionSettings | Určete skupinu nastavení pro dělení dat. <br>Použijte, pokud není možnost oddílu `None` . | No |
| partitionColumnName | Zadejte název zdrojového sloupce **v typu Integer** , který bude použit pro vytváření oddílů rozsahu pro paralelní kopírování. Pokud není zadaný, primární klíč tabulky se automaticky detekuje a použije se jako sloupec partition. <br>Použijte, pokud je parametr partition `DynamicRange` . Pokud použijete dotaz k načtení zdrojových dat, zapojte `?AdfRangePartitionColumnName` v klauzuli WHERE. Viz příklad v části [paralelní kopírování z Netezza](#parallel-copy-from-netezza) . | No |
| partitionUpperBound | Maximální hodnota sloupce oddílu pro kopírování dat. <br>Použijte, pokud je možnost oddílu `DynamicRange` . Použijete-li dotaz k načtení zdrojových dat, zapojte `?AdfRangePartitionUpbound` v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Netezza](#parallel-copy-from-netezza) . | No |
| partitionLowerBound | Minimální hodnota sloupce oddílu pro kopírování dat. <br>Použijte, pokud je parametr partition `DynamicRange` . Použijete-li dotaz k načtení zdrojových dat, zapojte `?AdfRangePartitionLowbound` v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Netezza](#parallel-copy-from-netezza) . | No |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Paralelní kopírování z Netezza

Konektor Data Factory Netezza poskytuje integrované datové oddíly pro kopírování dat z Netezza paralelně. Možnosti dělení dat najdete ve **zdrojové** tabulce aktivity kopírování.

![Snímek obrazovky s možnostmi oddílů](./media/connector-netezza/connector-netezza-partition-options.png)

Když povolíte dělenou kopii, Data Factory spustí paralelní dotazy na váš zdroj Netezza a načte data podle oddílů. Paralelní míra je řízena [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) nastavením aktivity kopírování. Pokud jste například nastavili `parallelCopies` čtyři, Data Factory souběžně vygeneruje a spustí čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat z databáze Netezza.

Navrhnete, abyste umožnili paralelní kopírování s vytvářením oddílů dat, zejména pokud načítáte velké množství dat z databáze Netezza. Následují Doporučené konfigurace pro různé scénáře. Při kopírování dat do úložiště dat založeného na souborech je znovu zaškrtnuto, aby bylo možné zapisovat do složky jako více souborů (zadejte pouze název složky). v takovém případě je výkon lepší než zápis do jednoho souboru.

| Scenario                                                     | Navrhovaná nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Úplné načtení z velké tabulky                                   | **Možnost oddílu**: datový řez. <br><br/>Během provádění Data Factory automaticky rozdělí data na základě [vestavěných datových řezů Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)a kopíruje data podle oddílů. |
| Načtení velkého množství dat pomocí vlastního dotazu.                 | **Možnost oddílu**: datový řez.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>` .<br>Během provádění Data Factory nahradí `?AdfPartitionCount` (pomocí paralelního kopírování nastaveného v aktivitě kopírování) a `?AdfDataSliceCondition` pomocí logiky oddílu datového řezu a pošle Netezza. |
| Načtěte velké množství dat pomocí vlastního dotazu, který má sloupec s celými čísly s rovnoměrně distribuovanou hodnotou pro dělení rozsahu. | **Možnosti oddílu**: dynamický oddíl rozsahu.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Partition – sloupec**: Určete sloupec, který se používá k dělení dat. Můžete rozdělit na sloupec s datovým typem Integer.<br>**Horní hranice oddílu** a **dolní mez oddílu**: Určete, jestli chcete filtrovat podle sloupce oddílu, aby se načetla data jenom mezi dolním a horním rozsahem.<br><br>Během provádění Data Factory nahradí `?AdfRangePartitionColumnName` , `?AdfRangePartitionUpbound` a `?AdfRangePartitionLowbound` skutečným názvem sloupce a rozsahy hodnoty pro každý oddíl a pošle do Netezza. <br>Pokud například sloupec oddílu "ID" nastaví s dolní hranicí jako 1 a horní mez jako 80, s paralelní kopií nastavenou na 4, Data Factory načte data po 4 oddíly. Jejich ID jsou mezi [1, 20], [21, 40], [41, 60] a [61, 80] v uvedeném pořadí. |

**Příklad: dotazování pomocí oddílu datového řezu**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Příklad: dotaz s dynamickým oddílem rozsahu**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky v Azure Data Factory, najdete v tématu [podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
