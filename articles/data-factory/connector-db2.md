---
title: Kopírování dat z DB2 pomocí Azure Data Factory
description: Zjistěte, jak zkopírovat data z DB2 do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: jingwang
ms.openlocfilehash: 22ecac12e049e58e533cdde0078f4a25f6bb2aa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77423823"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopírování dat z DB2 pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-db2-connector.md)
> * [Aktuální verze](connector-db2.md)

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z databáze DB2. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor databáze DB2 je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z databáze DB2 můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor DB2 podporuje následující platformy a verze IBM DB2 s distribuovanou architekturou relační databáze (DRDA) SQL Access Manager (SQLAM) verze 9, 10 a 11:

* IBM DB2 pro z/OS 12.1
* IBM DB2 pro z/OS 11.1
* IBM DB2 pro z/OS 10.1
* IBM DB2 pro i 7.3
* IBM DB2 pro i 7.2
* IBM DB2 pro i 7.1
* IBM DB2 pro LUW 11
* IBM DB2 pro LUW 10.5
* IBM DB2 pro LUW 10.1

>[!TIP]
>Konektor DB2 je postaven na zprostředkovateli TECHNOLOGIE Microsoft OLE DB pro DB2. Chcete-li odstranit chyby konektoru DB2, přečtěte si [kódy chyb zprostředkovatele dat](https://docs.microsoft.com/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Prostředí Integration Runtime poskytuje vestavěný ovladač DB2, proto není nutné ručně instalovat žádný ovladač při kopírování dat z DB2.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor DB2.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu DB2 jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **Db2** | Ano |
| server |Název serveru DB2. Můžete zadat číslo portu za názvem serveru odděleným `server:port`dvojtečkou, například . |Ano |
| database |Název databáze DB2. |Ano |
| authenticationType |Typ ověřování používaný pro připojení k databázi DB2.<br/>Povolená hodnota je: **Základní**. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno pro připojení k databázi DB2. |Ano |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). |Ano |
| packageCollection | Určete v části, kde jsou potřebné balíčky automaticky vytvořeny adf při dotazování na databázi. | Ne |
| název certificateCommonName | Při použití šifrování SSL (Secure Sockets L) nebo Transport Layer Security (TLS) je nutné zadat hodnotu běžného názvu certifikátu. | Ne |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

> [!TIP]
> Pokud se zobrazí chybová `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`zpráva, která uvádí , důvodem je potřebný balíček není vytvořen pro uživatele. Ve výchozím nastavení se adf pokusí vytvořit balíček pod kolekcí pojmenovanou jako uživatel, který jste použili pro připojení k DB2. Zadejte vlastnost kolekce balíčků, která označuje, kde chcete, aby adf vytvořit potřebné balíčky při dotazování na databázi.

**Příklad:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou DB2.

Kopírování dat z DB2 jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **Db2Table.** | Ano |
| Schématu | Název schématu. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tabulka | Název tabulky. |Ne (pokud je zadán "dotaz" ve zdroji aktivity)  |
| tableName | Název tabulky se schématem. Tato vlastnost je podporována pro zpětnou kompatibilitu. Použití `schema` `table` a pro nové pracovní zatížení. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Pokud jste `RelationalTable` používali zadaný datový soubor, je stále podporována tak, jak je, zatímco se doporučuje používat novou do budoucna.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem DB2.

### <a name="db2-as-source"></a>DB2 jako zdroj

Chcete-li kopírovat data z DB2, jsou podporovány následující vlastnosti v části **zdroj aktivity** kopírování:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na: **Db2Source.** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Pokud jste `RelationalSource` používali zadaný zdroj, je stále podporován jako-je, zatímco jste navrhl použít nový do budoucna.

## <a name="data-type-mapping-for-db2"></a>Mapování datového typu pro DB2

Při kopírování dat z DB2 se používají následující mapování z datových typů DB2 do dočasných datových typů Azure Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování schématu a datových typů.](copy-activity-schema-and-type-mapping.md)

| Db2 typ databáze | Dočasný datový typ datové továrny |
|:--- |:--- |
| Bigint |Int64 |
| binární |Bajt[] |
| Objekt blob |Bajt[] |
| Char |Řetězec |
| Clob |Řetězec |
| Datum |Datum a čas |
| Db2DynArray |Řetězec |
| DbClob |Řetězec |
| Desetinné číslo |Desetinné číslo |
| DecimalFloat |Desetinné číslo |
| Double |Double |
| Plovoucí desetinná čárka |Double |
| Grafické |Řetězec |
| Integer |Int32 |
| Binární soubor LongVar |Bajt[] |
| LongVarChar (Vavč.) |Řetězec |
| Funkce LongVarGraphic |Řetězec |
| Numeric |Desetinné číslo |
| Skutečné |Single |
| Smallint |Int16 |
| Time |TimeSpan |
| Časové razítko |DateTime |
| Varbinary |Bajt[] |
| Varchar |Řetězec |
| VarGrafika |Řetězec |
| XML |Bajt[] |

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
