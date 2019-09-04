---
title: Kopírování dat z PostgreSQL pomocí Azure Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data z PostgreSQL do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 84a82e5fae7c56a13aeb4603079e9378b38785cb
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277563"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Kopírování dat z PostgreSQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-postgresql-connector.md)
> * [Aktuální verze](connector-postgresql.md)

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z databáze PostgreSQL. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Data z databáze PostgreSQL můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor PostgreSQL podporuje PostgreSQL **verze 7,4 a vyšší**.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

V případě, že je místní verze IR nižší než 3,7, je nutné nainstalovat [poskytovatele dat Ngpsql pro PostgreSQL](https://go.microsoft.com/fwlink/?linkid=282716) s verzí mezi 2.0.12 a 3.1.9 na Integration runtime počítači.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor PostgreSQL.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu PostgreSQL jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **PostgreSql** | Ano |
| connectionString | ODBC připojovací řetězec služby pro připojení ke službě Azure Database for PostgreSQL. <br/>Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. Můžete také do Azure Key Vault umístit heslo a načíst `password` konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

Připojovací řetězec je `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Další vlastnosti, které můžete nastavit na váš případ:

| Vlastnost | Popis | Možnosti | Požaduje se |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoda ovladač používá k šifrování dat posílaných mezi ovladač a databázový server. Například `EncryptionMethod=<0/1/6>;`| 0 (žádné šifrování) **(výchozí)** / 1 (SSL) / 6 (RequestSSL) | Ne |
| ValidateServerCertificate (virtuální čipové karty) | Určuje, jestli ovladač ověřil certifikát, který je odesláno serverem databáze, když je povoleno šifrování protokolu SSL (metoda šifrování = 1). Například `ValidateServerCertificate=<0/1>;`| 0 (zakázáno) **(výchozí)** / 1 (povoleno) | Ne |

**Příklad:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
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
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;"
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

Pokud jste používali propojenou službu PostgreSQL s následující datovou částí, je stále podporovaná tak, jak je, a až budete chtít začít používat novinku dál.

**Předchozí datová část:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných PostgreSQL DataSet.

Chcete-li kopírovat data z PostgreSQL, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **PostgreSqlTable** | Ano |
| schema | Název schématu. |Ne (když je zadán zdroj aktivity "query")  |
| table | Název tabulky. |Ne (když je zadán zdroj aktivity "query")  |
| tableName | Název tabulky se schématem Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro `schema` nové `table` zatížení použijte a. | Ne (když je zadán zdroj aktivity "query") |

**Příklad**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "PostgreSqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Pokud jste `RelationalTable` používali typovou datovou sadu, je stále podporovaná tak, jak je, a až budete chtít začít používat novinku dál.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností podporovaných PostgreSQL zdrojem.

### <a name="postgresql-as-source"></a>PostgreSQL as source

Chcete-li kopírovat data z PostgreSQL, v části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **PostgreSqlSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Ne (když je "tableName" v datové sadě zadán) |

> [!NOTE]
> V názvech schémat a tabulek se rozlišují velká a malá písmena. Uzavřete je do `""` dotazu (dvojité uvozovky).

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PostgreSQL input dataset name>",
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
                "type": "PostgreSqlSource",
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Pokud jste používali `RelationalSource` typový zdroj, je stále podporován tak, jak je, a když jste navrhli začít používat nový.

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
