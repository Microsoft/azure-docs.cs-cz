---
title: Kopírování dat ze služby Azure Database for MySQL pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data ze služby Azure Database for MySQL do úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/28/2018
ms.author: jingwang
ms.openlocfilehash: cbf8a70dae566dcc22b5c5caa84d0781dc2467f9
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022168"
---
# <a name="copy-data-from-azure-database-for-mysql-using-azure-data-factory"></a>Kopírování dat ze služby Azure Database for MySQL pomocí Azure Data Factory

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat ze služby Azure Database for MySQL ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Můžete kopírování dat ze služby Azure Database for MySQL k jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Poskytuje integrované ovladače chcete umožnit připojení k Azure Data Factory, proto není nutné ručně nainstalovat všechny ovladače používání tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní ke službě Azure Database pro konektor MySQL.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro službu Azure Database pro MySQL propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **AzureMySql** | Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k Azure Database for MySQL – instance. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

Připojovací řetězec je `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Další vlastnosti, které můžete nastavit na váš případ:

| Vlastnost | Popis | Možnosti | Požaduje se |
|:--- |:--- |:--- |:--- |:--- |
| SSLMode | Tato možnost určuje, jestli ovladač používá šifrování pomocí protokolu SSL a ověřování při připojení k MySQL. Například `SSLMode=<0/1/2/3/4>`| ZAKÁZÁNO (0) / upřednostňované (1) **(výchozí)** / požadované (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Ne |
| useSystemTrustStore | Tato možnost určuje, jestli se má používat certifikát certifikační Autority z úložiště důvěryhodných systému, nebo ze zadaného souboru PEM. Například `UseSystemTrustStore=<0/1>;`| (1) povolený / zakázaný (0) **(výchozí)** | Ne |

**Příklad:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastnosti podporované službou Azure Database pro sadu dat MySQL.

Ke kopírování dat ze služby Azure Database for MySQL, nastavte vlastnost typ datové sady na **AzureMySqlTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **AzureMySqlTable** | Ano |
| tableName | Název tabulky v databázi MySQL. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných – Azure Database for MySQL zdroje.

### <a name="azure-database-for-mysql-as-source"></a>Azure Database for MySQL jako zdroj

Ke kopírování dat ze služby Azure Database for MySQL, nastavte typ zdroje v aktivitě kopírování do **AzureMySqlSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **AzureMySqlSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM MyTable"`. | Ne (když je "tableName" v datové sadě zadán) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Mapování datového typu pro službu Azure Database for MySQL

Při kopírování dat ze služby Azure Database for MySQL, se používají následující mapování z datových typů MySQL do služby Azure Data Factory dočasné datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Azure Database for MySQL – datový typ | Data factory dočasné datový typ |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
