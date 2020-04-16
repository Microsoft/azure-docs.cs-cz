---
title: Kopírování dat z MySQL pomocí Azure Data Factory
description: Další informace o konektoru MySQL v Azure Data Factory, který umožňuje kopírovat data z databáze MySQL do úložiště dat podporovaného jako jímka.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: jingwang
ms.openlocfilehash: 8467bbe1512e45342b86ff62d51a3f66b3096f03
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418129"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Kopírování dat z MySQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-mysql-connector.md)
> * [Aktuální verze](connector-mysql.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z databáze MySQL. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

>[!NOTE]
>Chcete-li zkopírovat data ze [služby Azure Database for MySQL](../mysql/overview.md) nebo do ní, použijte specializovaný [konektor Azure Database for MySQL](connector-azure-database-for-mysql.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor MySQL je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data z databáze MySQL do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento mysql konektor podporuje MySQL **verze 5.6 a 5.7**.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integrační modul Runtime poskytuje vestavěný ovladač MySQL od verze 3.7, proto nemusíte ručně instalovat žádný ovladač.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor MySQL.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu MySQL jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **MySql** | Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci Azure Database for MySQL.<br/> Můžete také umístit heslo do azure `password` key vault a vyžádat konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovacích údajích úložiště v](store-credentials-in-key-vault.md) článku Azure Key Vault. | Ano |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

Typický připojovací řetězec je `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Další vlastnosti, které můžete nastavit pro váš případ:

| Vlastnost | Popis | Možnosti | Požaduje se |
|:--- |:--- |:--- |:--- |
| Režim SSLMode | Tato možnost určuje, zda ovladač používá šifrování TLS a ověření při připojování k MySQL. Např.`SSLMode=<0/1/2/3/4>`| ZDRAVOTNĚ postižené (0) / PREFEROVANÉ (1) **(Výchozí)** / POVINNÉ (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Ne |
| UseSystemTrustStore | Tato možnost určuje, zda se má použít certifikát certifikační autority z úložiště důvěryhodnosti systému nebo ze zadaného souboru PEM. Například `UseSystemTrustStore=<0/1>;`| Povoleno (1) / Zakázáno (0) **(Výchozí)** | Ne |

**Příklad:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
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
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;",
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

Pokud jste používali službu propojenou s MySQL s následující datovou částí, je stále podporována tak, jak je, zatímco se doporučuje používat novou do budoucna.

**Předchozí datová část:**

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou MySQL.

Chcete-li kopírovat data z MySQL, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena **na: MySqlTable** | Ano |
| tableName | Název tabulky v databázi MySQL. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "MySqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Pokud jste `RelationalTable` používali zadaný datový soubor, je stále podporována tak, jak je, zatímco se doporučuje používat novou do budoucna.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem MySQL.

### <a name="mysql-as-source"></a>MySQL jako zdroj

Chcete-li kopírovat data z MySQL, jsou podporovány následující vlastnosti v části **zdroj aktivity** kopírování:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: MySqlSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

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
                "type": "MySqlSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Pokud jste `RelationalSource` používali zadaný zdroj, je stále podporován jako-je, zatímco jste navrhl použít nový do budoucna.

## <a name="data-type-mapping-for-mysql"></a>Mapování datových typů pro MySQL

Při kopírování dat z MySQL se používají následující mapování z datových typů MySQL do dočasných datových typů Azure Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování schématu a datových typů.](copy-activity-schema-and-type-mapping.md)

| Datový typ MySQL | Dočasný datový typ datové továrny |
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


## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
