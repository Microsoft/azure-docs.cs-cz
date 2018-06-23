---
title: Kopírování dat z databáze MySQL pomocí Azure Data Factory | Microsoft Docs
description: Další informace o konektoru MySQL v Azure Data Factory, která umožňuje kopírování dat z databáze MySQL do úložiště dat jako jímka podporované.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/23/2018
ms.author: jingwang
ms.openlocfilehash: 43a27b98d8b53523bee8694ed3071e65a03355a6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335869"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Kopírování dat z databáze MySQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-onprem-mysql-connector.md)
> * [Verze 2 – Preview](connector-mysql.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z databáze MySQL. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [MySQL konektoru V1](v1/data-factory-onprem-mysql-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Můžete zkopírovat data z databáze MySQL do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor MySQL podporuje MySQL **verze 5.1 a vyšší**.

## <a name="prerequisites"></a>Požadavky

Pokud databáze MySQL není veřejně přístupná, budete muset nastavit Self-hosted integrace Runtime. Další informace o vlastním hostováním integrační moduly Runtime najdete v tématu [Self-hosted integrace Runtime](create-self-hosted-integration-runtime.md) článku. Modul Runtime integrace poskytuje integrované ovladače MySQL od verze 3.7, proto nemusíte ručně nainstalovat všechny ovladače.

Self-hosted IR verze nižší než 3.7, je potřeba nainstalovat [MySQL Connector/Net pro Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) verze mezi 6.6.5 a 6.10.7 na počítači integrace modulu Runtime. Tento ovladač 32bitové je kompatibilní s 64bitové infračerveného signálu.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru MySQL.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro službu MySQL propojené jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **MySql** | Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k databázi Azure pro instanci databáze MySQL. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. Můžete použít Self-hosted integrace Runtime (Pokud je vaše úložiště dat se nachází v privátní síti) nebo modul Runtime integrace Azure. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

Typické připojovací řetězec je `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Další vlastnosti, které můžete nastavit na váš případ:

| Vlastnost | Popis | Možnosti | Požaduje se |
|:--- |:--- |:--- |:--- |:--- |
| SSLMode | Tato možnost určuje, jestli ovladač používá šifrování SSL a ověření při připojování k MySQL. Například `SSLMode=<0/1/2/3/4>`| ZAKÁZÁNO (0) / upřednostňované (1) **(výchozí)** / požadované (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Ne |
| useSystemTrustStore | Tato možnost určuje, jestli se má použít certifikát Certifikační autority z úložiště důvěryhodnosti systému, nebo z určeného souboru PEM. Například `UseSystemTrustStore=<0/1>;`| (1) povoleno nebo zakázáno (0) **(výchozí)** | Ne |

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

Pokud používáte službu MySQL propojené s následující datové části, je stále podporovány jako-se, když jsou navrhované používat novým do budoucna.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje MySQL datovou sadu.

Ke zkopírování dat z databáze MySQL, nastavte vlastnost typu datové sady, která **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **RelationalTable** | Ano |
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje MySQL zdroje.

### <a name="mysql-as-source"></a>MySQL jako zdroj

Ke zkopírování dat z databáze MySQL, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **RelationalSource** | Ano |
| query | Čtení dat pomocí vlastního dotazu SQL. Například: `"SELECT * FROM MyTable"`. | Ne (když je určena "tableName" v datové sadě) |

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

## <a name="data-type-mapping-for-mysql"></a>Datový typ mapování pro databázi MySQL

Při kopírování dat z databáze MySQL, se používají následující mapování z databáze MySQL datové typy k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| MySQL datový typ | Typ průběžných dat objektu pro vytváření dat |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Decimal` |
| `blob` |`Byte[]` |
| `bool` |`Boolean` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal` |
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
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
