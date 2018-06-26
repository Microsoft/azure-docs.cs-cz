---
title: Kopírování dat z Azure databáze pro databázi MySQL pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak zkopírovat data z databáze Azure pro databázi MySQL do úložiště dat podporovaných podřízený pomocí aktivity kopírování v kanál služby Azure Data Factory.
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
ms.date: 04/28/2018
ms.author: jingwang
ms.openlocfilehash: c93ef69b024bd3331abf9537f30a30876066249c
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750569"
---
# <a name="copy-data-from-azure-database-for-mysql-using-azure-data-factory"></a>Kopírování dat z Azure databáze pro databázi MySQL pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z Azure databáze pro databázi MySQL. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [MySQL konektoru V1](v1/data-factory-onprem-mysql-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Můžete zkopírovat data z databáze Azure pro databázi MySQL do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Azure Data Factory poskytuje integrované ovladače pro umožnění připojení, proto nemusíte ručně nainstalovat všechny ovladače, používání tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory k databázi Azure pro konektor MySQL.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro databázi Azure pro službu MySQL propojené jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **AzureMySql** | Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k databázi Azure pro instanci databáze MySQL. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

Typické připojovací řetězec je `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Další vlastnosti, které můžete nastavit na váš případ:

| Vlastnost | Popis | Možnosti | Požaduje se |
|:--- |:--- |:--- |:--- |:--- |
| SSLMode | Tato možnost určuje, jestli ovladač používá šifrování SSL a ověření při připojování k MySQL. Například `SSLMode=<0/1/2/3/4>`| ZAKÁZÁNO (0) / upřednostňované (1) **(výchozí)** / požadované (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Ne |
| useSystemTrustStore | Tato možnost určuje, jestli se má použít certifikát Certifikační autority z úložiště důvěryhodnosti systému, nebo z určeného souboru PEM. Například `UseSystemTrustStore=<0/1>;`| (1) povoleno nebo zakázáno (0) **(výchozí)** | Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datové sady](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje Azure databáze MySQL datové sady.

Ke zkopírování dat z Azure databáze pro databázi MySQL, nastavte vlastnost typu datové sady, která **AzureMySqlTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **AzureMySqlTable** | Ano |
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Azure databáze pro zdroj MySQL.

### <a name="azure-database-for-mysql-as-source"></a>Azure databáze pro databázi MySQL jako zdroj

Ke zkopírování dat z Azure databáze pro databázi MySQL, nastavte typ zdroje v aktivitě kopírování do **AzureMySqlSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **AzureMySqlSource** | Ano |
| query | Čtení dat pomocí vlastního dotazu SQL. Například: `"SELECT * FROM MyTable"`. | Ne (když je určena "tableName" v datové sadě) |

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

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Mapování datového typu pro databázi Azure pro databázi MySQL

Při kopírování dat z Azure databáze pro databázi MySQL, se používají následující mapování z databáze MySQL datové typy k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| Azure databáze MySQL datový typ | Typ průběžných dat objektu pro vytváření dat |
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
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
