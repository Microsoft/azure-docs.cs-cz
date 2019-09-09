---
title: Kopírování dat z Netezza pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z Netezza úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 20e5e23e2000095a95913964673ce90a72b87e59
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813541"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Kopírování dat z Netezza pomocí služby Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z Netezza. Tento článek vychází [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md), který nabízí obecný přehled o aktivitě kopírování.

>[!TIP]
>V případě scénáře migrace dat z Netezza do Azure se dozvíte víc o tom, jak [pomocí Azure Data Factory migrovat data z místního serveru Netezza do Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z Netezza do jakékoli podporovaného úložiště dat jímky. Seznam dat ukládá podporovanou aktivitou kopírování jako zdroje a jímky, najdete v části [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).

Konektor Netezza podporuje paralelní kopírování ze zdroje. Podrobnosti najdete v části [paralelní kopírování z Netezza](#parallel-copy-from-netezza) .

Azure Data Factory poskytuje integrovaný ovladač umožňující připojení. Není nutné ručně nainstalovat všechny ovladače, které chcete použít tento konektor.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

Můžete vytvořit kanál, který používá aktivitu kopírování pomocí sady .NET SDK, Python SDK, Azure Powershellu, rozhraní REST API nebo šablony Azure Resource Manageru. Najdete v článku [kurz aktivity kopírování](quickstart-create-data-factory-dot-net.md) pro podrobné pokyny k vytvoření kanálu obsahujícího aktivitu kopírování.

Následující části obsahují podrobnosti o vlastnostech, které lze použít k definování entit služby Data Factory, které jsou specifické pro konektor Netezza.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Netezza propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **Netezza**. | Ano |
| connectionString | ODBC připojovací řetězec služby pro připojení k Netezza. <br/>Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. Můžete také do Azure Key Vault umístit heslo a načíst `pwd` konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) používat pro připojení k úložišti. Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadán, použije se výchozí prostředí Azure Integration Runtime. |Ne |

Připojovací řetězec je `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. Následující tabulka popisuje další vlastnosti, které můžete nastavit:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| SecurityLevel | Úroveň zabezpečení (SSL/TLS), který používá ovladač pro připojení k úložišti. Příklad: `SecurityLevel=preferredSecured`. Podporované hodnoty jsou:<br/>- **Jenom nezabezpečené** (**onlyUnSecured**): Ovladač nepoužívá protokol SSL.<br/>- **Upřednostňované nezabezpečené (preferredUnSecured) (výchozí)** : Pokud server nabízí možnost výběru, ovladač nepoužívá protokol SSL. <br/>- **Upřednostňované zabezpečené (preferredSecured)** : Pokud server nabízí možnost výběru, použije ovladač protokol SSL. <br/>- **Pouze zabezpečené (onlySecured)** : Ovladač se nepřipojí, pokud není k dispozici připojení SSL. | Ne |
| CaCertFile | Úplná cesta k certifikátu SSL, který se používá serverem. Příklad: `CaCertFile=<cert path>;`| Ano, pokud je povolen protokol SSL |

**Příklad**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
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
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
            },
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Tato část obsahuje seznam vlastností, které podporuje Netezza datové sady.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datových sad](concepts-datasets-linked-services.md).

Chcete-li kopírovat data z Netezza, nastavte **typ** vlastnosti datové sady na **NetezzaTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **NetezzaTable** | Ano |
| schema | Název schématu. |Ne (když je zadán zdroj aktivity "query")  |
| table | Název tabulky. |Ne (když je zadán zdroj aktivity "query")  |
| tableName | Název tabulky se schématem Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro `schema` nové `table` zatížení použijte a. | Ne (když je zadán zdroj aktivity "query") |

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

Tato část obsahuje seznam vlastností, které podporuje Netezza zdroje.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza jako zdroj

>[!TIP]
>Pokud chcete data z Netezza efektivně načíst pomocí dělení dat, další informace najdete v části [paralelní kopírování z Netezza](#parallel-copy-from-netezza) .

Ke zkopírování dat z Netezza, nastavte **zdroj** typ v aktivitě kopírování do **NetezzaSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost zdroje aktivity kopírování **NetezzaSource**. | Ano |
| query | Použijte vlastní dotaz SQL číst data. Příklad: `"SELECT * FROM MyTable"` | Ne (když je "tableName" v datové sadě zadán) |
| partitionOptions | Určuje možnosti dělení dat, které se používají k načtení dat z Netezza. <br>Povolené hodnoty jsou: **Žádné** (výchozí), **dataslice** a **DynamicRange**.<br>Pokud je povolená možnost oddílu (to znamená, ne `None`), stupeň paralelismu na souběžně načtená data z databáze Netezza se [`parallelCopies`](copy-activity-performance.md#parallel-copy) řídí nastavením aktivity kopírování. | Ne |
| partitionSettings | Určete skupinu nastavení pro dělení dat. <br>Použijte, pokud není `None`možnost oddílu. | Ne |
| partitionColumnName | Zadejte název zdrojového sloupce **v typu Integer** , který bude použit pro vytváření oddílů rozsahu pro paralelní kopírování. Pokud není zadaný, primární klíč tabulky se automaticky zjistí a použije se jako sloupec partition. <br>Použijte, pokud je `DynamicRange`parametr partition. Pokud použijete dotaz k načtení zdrojových dat, `?AdfRangePartitionColumnName` zapojte v klauzuli WHERE. Viz příklad v části [paralelní kopírování z Netezza](#parallel-copy-from-netezza) . | Ne |
| partitionUpperBound | Maximální hodnota sloupce oddílu pro kopírování dat. <br>Použijte, pokud je `DynamicRange`možnost oddílu. Použijete-li dotaz k načtení zdrojových dat, `?AdfRangePartitionUpbound` zapojte v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Netezza](#parallel-copy-from-netezza) . | Ne |
| partitionLowerBound | Minimální hodnota sloupce oddílu pro kopírování dat. <br>Použijte, pokud je `DynamicRange`parametr partition. Použijete-li dotaz k načtení zdrojových dat, `?AdfRangePartitionLowbound` zapojte v klauzuli WHERE. Příklad najdete v části [paralelní kopírování z Netezza](#parallel-copy-from-netezza) . | Ne |

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

Když povolíte dělenou kopii, Data Factory spustí paralelní dotazy na váš zdroj Netezza a načte data podle oddílů. Paralelní míra je řízena [`parallelCopies`](copy-activity-performance.md#parallel-copy) nastavením aktivity kopírování. Pokud jste například nastavili `parallelCopies` čtyři, Data Factory souběžně vygeneruje a spustí čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat z databáze Netezza.

Navrhnete, abyste umožnili paralelní kopírování s vytvářením oddílů dat, zejména pokud načítáte velké množství dat z databáze Netezza. Následují Doporučené konfigurace pro různé scénáře. Při kopírování dat do úložiště dat založeného na souborech je znovu zaškrtnuto, aby bylo možné zapisovat do složky jako více souborů (zadejte pouze název složky). v takovém případě je výkon lepší než zápis do jednoho souboru.

| Scénář                                                     | Navrhovaná nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Úplné načtení z velké tabulky                                   | **Možnost oddílu**: Datový řez. <br><br/>Během provádění Data Factory automaticky rozdělí data na základě [vestavěných datových řezů Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)a kopíruje data podle oddílů. |
| Načtení velkého množství dat pomocí vlastního dotazu.                 | **Možnost oddílu**: Datový řez.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Během provádění Data Factory nahradí `?AdfPartitionCount` (pomocí paralelního kopírování nastaveného v aktivitě kopírování) a `?AdfDataSliceCondition` pomocí logiky oddílu datového řezu a pošle Netezza. |
| Načtěte velké množství dat pomocí vlastního dotazu, který má sloupec s celými čísly s rovnoměrně distribuovanou hodnotou pro dělení rozsahu. | **Možnosti oddílu**: Dynamický oddíl rozsahu.<br>**Dotaz**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Sloupec oddílu**: Zadejte sloupec, který se používá k dělení dat. Můžete rozdělit na sloupec s datovým typem Integer.<br>**Horní mez oddílu** a **dolní mez oddílu**: Určete, jestli chcete filtrovat podle sloupce oddílu, aby se načetla data jenom mezi dolním a horním rozsahem.<br><br>Během provádění Data Factory nahradí `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`a `?AdfRangePartitionLowbound` skutečným názvem sloupce a rozsahy hodnoty pro každý oddíl a pošle do Netezza. <br>Pokud například sloupec oddílu "ID" nastaví s dolní hranicí jako 1 a horní mez jako 80, s paralelní kopií nastavenou na 4, Data Factory načte data po 4 oddíly. Jejich ID jsou mezi [1, 20], [21, 40], [41, 60] a [61, 80] v uvedeném pořadí. |

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

## <a name="next-steps"></a>Další postup

Seznam úložišť dat, která aktivitu kopírování, která podporuje jako zdroje a jímky ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
