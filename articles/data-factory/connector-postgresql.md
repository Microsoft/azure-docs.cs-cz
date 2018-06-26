---
title: Kopírování dat z PostgreSQL pomocí Azure Data Factory | Microsoft Docs
description: Postup kopírování dat z PostgreSQL do úložiště dat podporovaných podřízený pomocí aktivity kopírování v kanál služby Azure Data Factory.
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
ms.openlocfilehash: 436725e54e197e021f088e0fd0cd75fc944983a3
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751373"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Kopírování dat z PostgreSQL pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-onprem-postgresql-connector.md)
> * [Verze 2 – Preview](connector-postgresql.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z databáze PostgreSQL. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.


> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [PostgreSQL konektoru V1](v1/data-factory-onprem-postgresql-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Do úložiště dat žádné podporované podřízený může kopírovat data z databáze PostgreSQL. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor PostgreSQL podporuje PostgreSQL **verze 7.4 a vyšší**.

## <a name="prerequisites"></a>Požadavky

Pokud databázi PostgreSQL není veřejně přístupná, budete muset nastavit Self-hosted integrace Runtime. Další informace o vlastním hostováním integrační moduly Runtime najdete v tématu [Self-hosted integrace Runtime](create-self-hosted-integration-runtime.md) článku. Modul Runtime integrace poskytuje ovladač předdefinované PostgreSQL od verze 3.7, proto nemusíte ručně nainstalovat všechny ovladače.

Self-hosted IR verze nižší než 3.7, je potřeba nainstalovat [Ngpsql zprostředkovatele dat pro PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) s verzí mezi 2.0.12 a 3.1.9 na počítači integrace modulu Runtime.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru PostgreSQL.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro PostgreSQL propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **PostgreSql** | Ano |
| připojovací řetězec | Řetězec připojení rozhraní ODBC pro připojení k databázi Azure pro PostgreSQL. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je veřejně přístupná data store), můžete použít modul Runtime integrace Self-hosted nebo Runtime integrace Azure. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

Typické připojovací řetězec je `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Další vlastnosti, které můžete nastavit na váš případ:

| Vlastnost | Popis | Možnosti | Požaduje se |
|:--- |:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoda ovladače se používá k šifrování dat odesílaných mezi ovladače a databázový server. Například `ValidateServerCertificate=<0/1/6>;`| 0 (žádné šifrování) **(výchozí)** nebo 1 (SSL) / 6 (RequestSSL) | Ne |
| ValidateServerCertificate (virtuální čipové karty) | Určuje, jestli ovladač ověří certifikátu, která je odeslána na databázovém serveru, pokud je povolené šifrování SSL (metoda šifrování = 1). Například `ValidateServerCertificate=<0/1>;`| 0 (zakázáno) **(výchozí)** nebo 1 (povoleno) | Ne |

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

Pokud jste používali PostgreSQL propojené služby s následující datové části, je stále podporovány jako-se, když jsou navrhované používat novým do budoucna.

**Předchozí datové části:**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje datovou sadu PostgreSQL.

Ke zkopírování dat z PostgreSQL, nastavte vlastnost typu datové sady, která **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **RelationalTable** | Ano |
| tableName | Název tabulky v databázi PostgreSQL. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které jsou podporovány zdrojem PostgreSQL.

### <a name="postgresql-as-source"></a>PostgreSQL jako zdroj

Ke zkopírování dat z PostgreSQL, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **RelationalSource** | Ano |
| query | Čtení dat pomocí vlastního dotazu SQL. Například: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Ne (když je určena "tableName" v datové sadě) |

> [!NOTE]
> Schéma a tabulku názvy rozlišují malá a velká písmena. Uzavřete je do `""` (dvojité uvozovky) v dotazu.

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
                "type": "RelationalSource",
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-postgresql"></a>Datový typ mapování pro PostgreSQL

Při kopírování dat z PostgreSQL, se používají následující mapování PostgreSQL datových typů k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| PostgreSQL datový typ | PostgresSQL aliasy | Typ průběžných dat objektu pro vytváření dat |
|:--- |:--- |:--- |
| `abstime` |&nbsp; |`String` |
| `bigint` | `int8` | `Int64` |
| `bigserial` | `serial8` | `Int64` |
| `bit [1]` |&nbsp; | `Boolean` |
| `bit [(n)], n>1` |&nbsp; | `Byte[]` |
| `bit varying [(n)]` | `varbit` |`Byte[]` |
| `boolean` | `bool` | `Boolean` |
| `box` |&nbsp; | `String` |
| `bytea` |&nbsp; | `Byte[], String` |
| `character [(n)]` | `char [(n)]` | `String` |
| `character varying [(n)]` | `varchar [(n)]` | `String` |
| `cid` |&nbsp; | `Int32` |
| `cidr` |&nbsp; | `String` |
| `circle` |&nbsp; |` String` |
| `date` |&nbsp; |`Datetime` |
| `daterange` |&nbsp; |`String` |
| `double precision` |`float8` |`Double` |
| `inet` |&nbsp; |`String` |
| `intarray` |&nbsp; |`String` |
| `int4range` |&nbsp; |`String` |
| `int8range` |&nbsp; |`String` |
| `integer` | `int, int4` |`Int32` |
| `interval [fields] [(p)]` | | `String` |
| `json` |&nbsp; | `String` |
| `jsonb` |&nbsp; | `Byte[]` |
| `line` |&nbsp; | `Byte[], String` |
| `lseg` |&nbsp; | `String` |
| `macaddr` |&nbsp; | `String` |
| `money` |&nbsp; | `String` |
| `numeric [(p, s)]`|`decimal [(p, s)]` |`String` |
| `numrange` |&nbsp; |`String` |
| `oid` |&nbsp; |`Int32` |
| `path` |&nbsp; |`String` |
| `pg_lsn` |&nbsp; |`Int64` |
| `point` |&nbsp; |`String` |
| `polygon` |&nbsp; |`String` |
| `real` |`float4` |`Single` |
| `smallint` |`int2` |`Int16` |
| `smallserial` |`serial2` |`Int16` |
| `serial` |`serial4` |`Int32` |
| `text` |&nbsp; |`String` |
| `timewithtimezone` |&nbsp; |`String` |
| `timewithouttimezone` |&nbsp; |`String` |
| `timestampwithtimezone` |&nbsp; |`String` |
| `xid` |&nbsp; |`Int32` |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
