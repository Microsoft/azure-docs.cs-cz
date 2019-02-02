---
title: Kopírování dat z databáze MySQL pomocí Azure Data Factory | Dokumentace Microsoftu
description: Další informace o konektoru MySQL ve službě Azure Data Factory, který umožňuje kopírovat data z databáze MySQL do úložiště dat podporován jako jímka.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 1bc2277b4100fe7571dc27758de12f1ca00020a1
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656504"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Kopírování dat z databáze MySQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-onprem-mysql-connector.md)
> * [Aktuální verze](connector-mysql.md)

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat z databáze MySQL ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z databáze MySQL do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor MySQL podporuje MySQL **verze 5.6 a 5.7**.

## <a name="prerequisites"></a>Požadavky

Pokud vaše databáze MySQL není veřejně přístupná, budete muset nastavit modul Integration Runtime. Další informace o prostředí v místním prostředí integration Runtime najdete v tématu [modul Integration Runtime](create-self-hosted-integration-runtime.md) článku. Prostředí Integration Runtime poskytuje integrované ovladače MySQL od verze 3.7, proto není nutné ručně nainstalovat všechny ovladače.

Pro místní prostředí IR verze nižší než 3.7, je potřeba nainstalovat [MySQL Connector/Net pro Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) verze mezi 6.6.5 a 6.10.7 na počítači prostředí Integration Runtime. Tento ovladač 32 bitů je kompatibilní s 64-bit IR.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory, které jsou specifické pro konektor MySQL.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro MySQL propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **MySql** | Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k Azure Database for MySQL – instance.<br/>Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory. Heslo můžete také vložit do služby Azure Key Vault a o přijetí změn `password` konfigurace z připojovacího řetězce. Podívejte se na následující ukázky a [Store přihlašovacích údajů ve službě Azure Key Vault](store-credentials-in-key-vault.md) článku s dalšími podrobnostmi. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

Připojovací řetězec je `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Další vlastnosti, které můžete nastavit na váš případ:

| Vlastnost | Popis | Možnosti | Požaduje se |
|:--- |:--- |:--- |:--- |:--- |
| SSLMode | Tato možnost určuje, jestli ovladač používá šifrování pomocí protokolu SSL a ověřování při připojení k MySQL. Například `SSLMode=<0/1/2/3/4>`| ZAKÁZÁNO (0) / upřednostňované (1) **(výchozí)** / požadované (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Ne |
| UseSystemTrustStore | Tato možnost určuje, jestli se má používat certifikát certifikační Autority z úložiště důvěryhodných systému, nebo ze zadaného souboru PEM. Například `UseSystemTrustStore=<0/1>;`| (1) povolený / zakázaný (0) **(výchozí)** | Ne |

**Příklad:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: ukládání hesel ve službě Azure Key Vault**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;"
            },
            "password": { 
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

Pokud jste používali službu MySQL propojené s následující datová část, je stále podporovány jako-se, když byly navrženy používat novou do budoucna.

**Předchozí datové části:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje MySQL datové sady.

Pro kopírování dat z databáze MySQL, nastavte vlastnost typ datové sady na **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **RelationalTable** | Ano |
| tableName | Název tabulky v databázi MySQL. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem MySQL.

### <a name="mysql-as-source"></a>MySQL jako zdroj

Pro kopírování dat z databáze MySQL, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **RelationalSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM MyTable"`. | Ne (když je "tableName" v datové sadě zadán) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-mysql"></a>Datový typ mapování pro MySQL

Při kopírování dat z databáze MySQL, se používají následující mapování z datových typů MySQL do služby Azure Data Factory dočasné datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Typ dat MySQL | Data factory dočasné datový typ |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
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
| `year` |`Int` |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
