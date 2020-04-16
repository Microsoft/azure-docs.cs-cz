---
title: Kopírování dat z PostgreSQL pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data z PostgreSQL do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: 6d10e7b9b24817eb738172bd0f2d2c3e7f8f2cbf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416753"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Kopírování dat z PostgreSQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-postgresql-connector.md)
> * [Aktuální verze](connector-postgresql.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z databáze PostgreSQL. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor PostgreSQL je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data z databáze PostgreSQL do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor PostgreSQL podporuje PostgreSQL **verze 7.4 a vyšší**.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integrační modul Runtime poskytuje vestavěný ovladač PostgreSQL od verze 3.7, proto nemusíte ručně instalovat žádný ovladač.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor PostgreSQL.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu PostgreSQL jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **PostgreSql** | Ano |
| připojovací řetězec | Připojovací řetězec ODBC pro připojení k Azure Database pro PostgreSQL. <br/>Můžete také umístit heslo do azure `password` key vault a vyžádat konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovacích údajích úložiště v](store-credentials-in-key-vault.md) článku Azure Key Vault. | Ano |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

Typický připojovací řetězec je `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Další vlastnosti, které můžete nastavit pro váš případ:

| Vlastnost | Popis | Možnosti | Požaduje se |
|:--- |:--- |:--- |:--- |
| Metoda šifrování (EM)| Metoda, kterou ovladač používá k šifrování dat odeslaných mezi ovladačem a databázovým serverem. Např.`EncryptionMethod=<0/1/6>;`| 0 (bez šifrování) **(výchozí)** / 1 (SSL) / 6 (Requestssl) | Ne |
| Ověřit serverový certifikát (VSC) | Určuje, zda ovladač ověří certifikát odeslaný databázovým serverem, pokud je povoleno šifrování SSL (Metoda šifrování =1). Např.`ValidateServerCertificate=<0/1>;`| 0 (zakázáno) **(výchozí)** / 1 (povoleno) | Ne |

**Příklad:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
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
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;",
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

Pokud jste používali postgreSQL propojenou službu s následující datovou částí, je stále podporována tak, jak je, zatímco se doporučuje používat novou do budoucna.

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou PostgreSQL.

Kopírování dat z PostgreSQL jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena **na: PostgreSqlTable** | Ano |
| Schématu | Název schématu. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Použití `schema` `table` a pro nové pracovní zatížení. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

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

Pokud jste `RelationalTable` používali zadaný datový soubor, je stále podporována tak, jak je, zatímco se doporučuje používat novou do budoucna.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem PostgreSQL.

### <a name="postgresql-as-source"></a>PostgreSQL jako zdroj

Chcete-li kopírovat data z PostgreSQL, jsou podporovány následující vlastnosti v části **zdroj aktivity** kopírování:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: PostgreSqlSource.** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

> [!NOTE]
> Názvy schémat a tabulek rozlišují malá a velká písmena. Uzavřete `""` je do (dvojité uvozovky) v dotazu.

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

Pokud jste `RelationalSource` používali zadaný zdroj, je stále podporován jako-je, zatímco jste navrhl použít nový do budoucna.

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
