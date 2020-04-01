---
title: Kopírování dat z Netezzy pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data z Netezza do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: c7e17f7c4493560bd6118b8d4837fd795a6ab0c8
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422865"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Kopírování dat z Netezzy pomocí Azure Data Factory

Tento článek popisuje, jak pomocí kopírovat aktivity v Azure Data Factory ke kopírování dat z Netezza. Článek vychází [z aktivity kopírování v Azure Data Factory](copy-activity-overview.md), která představuje obecný přehled aktivity kopírování.

>[!TIP]
>Pokud se dozvíte, scénář migrace dat z Netezzy do Azure, přečtěte si další informace z [migrace dat z místního serveru Netezza do Azure pomocí Azure Data Factory.](data-migration-guidance-netezza-azure-sqldw.md)

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Netezza je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)


Můžete zkopírovat data z Netezza do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která podporují aktivita kopírování jako zdroje a propady, naleznete v [tématu Podporovaná úložiště a formáty dat](copy-activity-overview.md#supported-data-stores-and-formats).

Konektor Netezza podporuje paralelní kopírování ze zdroje. Podrobnosti najdete v části [Paralelní kopie z netezzy.](#parallel-copy-from-netezza)

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení. K použití tohoto konektoru není nutné ručně instalovat žádný ovladač.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

Můžete vytvořit kanál, který používá aktivitu kopírování pomocí .NET SDK, Python SDK, Azure PowerShell, ROZHRANÍ REST API nebo šablony Azure Resource Manager. Podrobné pokyny k vytvoření kanálu, který má aktivitu kopírování, najdete v [kurzu Kopírovat aktivitu.](quickstart-create-data-factory-dot-net.md)

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které můžete použít k definování entit Data Factory, které jsou specifické pro konektor Netezza.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Netezza jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** musí být nastavena na **Netezza**. | Ano |
| připojovací řetězec | Připojovací řetězec ODBC pro připojení k Netezze. <br/>Můžete také umístit heslo do azure `pwd` key vault a vyžádat konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovacích údajích úložiště v](store-credentials-in-key-vault.md) článku Azure Key Vault. | Ano |
| connectVia | [Prostředí Integrace Runtime](concepts-integration-runtime.md) pro připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, použije se výchozí prostředí Azure Integration Runtime. |Ne |

Typický připojovací řetězec je `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. Následující tabulka popisuje další vlastnosti, které můžete nastavit:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| Úroveň zabezpečení | Úroveň zabezpečení (SSL/TLS), kterou ovladač používá pro připojení k úložišti dat. Příklad: `SecurityLevel=preferredSecured`. Podporované hodnoty jsou:<br/>- **Pouze nezabezpečené** **(pouzeNezabezpečené):** Ovladač nepoužívá SSL.<br/>- **Upřednostňované nezabezpečené (upřednostňovanéNezabezpečené) (výchozí):** Pokud server poskytuje možnost volby, ovladač nepoužívá SSL. <br/>- **Upřednostňované zabezpečené (preferredSecured)**: Pokud server poskytuje možnost volby, ovladač používá SSL. <br/>- **Pouze zabezpečené (pouzeZabezpečené)**: Ovladač se nepřipojí, pokud není k dispozici připojení SSL. | Ne |
| Soubor CaCert | Úplná cesta k certifikátu SSL, který používá server. Příklad: `CaCertFile=<cert path>;`| Ano, pokud je povolen protokol SSL |

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

**Příklad: ukládání hesla v trezoru klíčů Azure**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
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

Tato část obsahuje seznam vlastností, které podporuje datová sada Netezza.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu Datové sady](concepts-datasets-linked-services.md).

Chcete-li kopírovat data z Netezza, nastavte vlastnost **type** datové sady na **NetezzaTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena **na: NetezzaTable.** | Ano |
| Schématu | Název schématu. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Použití `schema` `table` a pro nové pracovní zatížení. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

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

## <a name="copy-activity-properties"></a>Kopírovat vlastnosti aktivity

Tato část obsahuje seznam vlastností, které zdroj Netezza podporuje.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v [tématu Potrubí](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza jako zdroj

>[!TIP]
>Chcete-li efektivně načítat data z Netezzy pomocí dělení dat, přečtěte si další informace z paralelní kopie z sekce [Netezza.](#parallel-copy-from-netezza)

Chcete-li kopírovat data z Netezzy, nastavte **typ zdroje** v okně Kopírovat aktivitu na **NetezzaSource**. V části Zdroj **kopírování aktivity** jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** zdroje Aktivity kopírování musí být nastavena na **NetezzaSource**. | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"` | Ne (pokud je v datové sadě zadán "název_tabulky") |
| partitionOptions | Určuje možnosti rozdělení dat použité k načtení dat z aplikace Netezza. <br>Povolit hodnoty jsou: **None** (výchozí), **DataSlice**a **DynamicRange**.<br>Pokud je povolena možnost oddílu `None`(to znamená, že ne ), stupeň paralelismu souběžně [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) načíst data z databáze Netezza je řízen nastavením na aktivitu kopírování. | Ne |
| partitionSettings | Zadejte skupinu nastavení pro dělení dat. <br>Použít, pokud možnost `None`oddílu není . | Ne |
| partitionColumnName | Zadejte název zdrojového sloupce **v typu celé číslo,** který bude použit dělením rozsahu pro paralelní kopírování. Pokud není zadán, primární klíč tabulky je automaticky rozpoznán a použit jako sloupec oddílu. <br>Použít, pokud je `DynamicRange`možnost oddílu . Pokud použijete dotaz k načtení `?AdfRangePartitionColumnName` zdrojových dat, zavěste do klauzule WHERE. Viz příklad paralelní [kopie z oddílu Netezza.](#parallel-copy-from-netezza) | Ne |
| partitionUpperBound | Maximální hodnota sloupce oddílu pro kopírování dat. <br>Použít, pokud `DynamicRange`je možnost oddílu . Pokud použijete dotaz k načtení zdrojových dat, zavěste `?AdfRangePartitionUpbound` do klauzule WHERE. Příklad naleznete v části [Paralelní kopie z netezzy.](#parallel-copy-from-netezza) | Ne |
| oddíl LowerBound | Minimální hodnota sloupce oddílu zkopírovat data. <br>Použít, pokud je `DynamicRange`možnost oddílu . Pokud použijete dotaz k načtení `?AdfRangePartitionLowbound` zdrojových dat, zavěste do klauzule WHERE. Příklad naleznete v části [Paralelní kopie z netezzy.](#parallel-copy-from-netezza) | Ne |

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

## <a name="parallel-copy-from-netezza"></a>Paralelní kopie z Netezzy

Konektor Netezza data Factory poskytuje vestavěné dělení dat pro paralelní kopírování dat z Netezzy. Možnosti dělení dat najdete v **zdrojové** tabulce aktivity kopírování.

![Snímek obrazovky s možnostmi oddílu](./media/connector-netezza/connector-netezza-partition-options.png)

Když povolíte rozdělenou kopii, Data Factory spustí paralelní dotazy proti zdroji Netezza pro načtení dat pomocí oddílů. Paralelní stupeň je [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) řízen nastavením aktivity kopírování. Pokud například nastavíte `parallelCopies` na čtyři, Data Factory současně generuje a spouští čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat z databáze Netezza.

Doporučujeme povolit paralelní kopírování s dělením dat, zejména při načítání velkého množství dat z databáze Netezza. Níže jsou navrženy konfigurace pro různé scénáře. Při kopírování dat do úložiště dat založeného na souborech je připonuto zapisovat do složky jako více souborů (pouze zadejte název složky), v takovém případě je výkon lepší než zápis do jednoho souboru.

| Scénář                                                     | Navrhovaná nastavení                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Plné zatížení z velkého stolu.                                   | **Možnost oddílu**: Řez dat. <br><br/>Během provádění Data Factory automaticky rozdělí data na základě [integrovaných řezů dat společnosti Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)a zkopíruje data podle oddílů. |
| Načtěte velké množství dat pomocí vlastního dotazu.                 | **Možnost oddílu**: Řez dat.<br>**Dotaz** `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`: .<br>Během provádění Data Factory `?AdfPartitionCount` nahradí (s paralelní číslo kopie `?AdfDataSliceCondition` nastavena na kopírování aktivity) a s logikou oddílu řezu dat a odešle netezza. |
| Načtěte velké množství dat pomocí vlastního dotazu, který má celý sloupec s rovnoměrně rozloženou hodnotou pro dělení rozsahu. | **Možnosti oddílu**: Oddíl dynamického rozsahu.<br>**Dotaz** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Sloupec oddílu**: Zadejte sloupec použitý k rozdělení dat. Můžete oddíl proti sloupci s celýdatový typ.<br>**Horní mez oddílu** a **dolní mez oddílu**: Určete, zda chcete filtrovat proti sloupci oddílu, chcete-li načíst data pouze mezi dolní a horní oblastí.<br><br>Během provádění data factory `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`nahradí `?AdfRangePartitionLowbound` , a skutečné názvy sloupců a rozsahy hodnot pro každý oddíl a odešle netezza. <br>Například pokud sloupec oddílu "ID" nastavit s dolní mez jako 1 a horní mez jako 80, s paralelní kopírování nastavit jako 4, Data Factory načte data o 4 oddíly. Jejich ID jsou mezi [1,20], [21, 40], [41, 60], a [61, 80]. |

**Příklad: dotaz s oddílem řezu dat**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Příklad: dotaz s oddílem dynamického rozsahu**

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

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která podporují aktivity kopírování jako zdroje a propady v Azure Data Factory, najdete v [tématu Podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
