---
title: Kopírování dat z DB2 pomocí Azure Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data z DB2 do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: c774725d4a4db4f624cd3980041b2974dfc8ed28
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275553"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopírování dat z DB2 pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-db2-connector.md)
> * [Aktuální verze](connector-db2.md)

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z databáze DB2. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Data z databáze DB2 můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor DB2 podporuje následující platformy a verze IBM DB2 s architekturou Distributed relačních databází (DRDA) SQL Access Manager (SQLAM) verze 9, 10 a 11:

* IBM DB2 pro z/OS 12,1
* IBM DB2 pro z/OS 11,1
* IBM DB2 pro z/OS 10,1
* IBM DB2 pro i 7,3
* IBM DB2 pro i 7,2
* IBM DB2 pro i 7,1
* IBM DB2 pro LUW 11
* IBM DB2 pro LUW 10,5
* IBM DB2 pro LUW 10,1

> [!TIP]
> Pokud se zobrazí chybová zpráva oznamující, že balíček odpovídající požadavku na spuštění příkazu SQL nebyl nalezen. SQLSTATE = 51002 SQLCODE =-805 ", důvod je potřebný balíček není vytvořen pro normálního uživatele v takovém operačním systému. Postupujte podle těchto pokynů podle vašeho typu serveru DB2:
> - DB2 pro i (AS400): před použitím aktivity kopírování umožněte Power User vytvořit kolekci pro přihlašovacího uživatele. Systému`create collection <username>`
> - DB2 pro z/OS nebo LUW: použití účtu s vysokou úrovní oprávnění – uživatel nebo správce s oprávněními balíčku a BIND, BINDADD, udělení oprávnění k VEŘEJNÉmu spuštění aktivity kopírování – potřebný balíček se automaticky vytvoří během kopírování. Následně můžete přejít zpět na normálního uživatele pro následné spuštění kopírování.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime poskytuje integrovaný ovladač DB2, takže při kopírování dat z DB2 nemusíte ručně instalovat žádné ovladače.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor DB2.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu DB2 jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **Db2** | Ano |
| Server |Název serveru DB2. Můžete zadat číslo portu za názvem serveru oddělené dvojtečkou, např. `server:port`. |Ano |
| database |Název databáze DB2 |Ano |
| authenticationType |Typ ověřování, který se používá pro připojení k databázi DB2.<br/>Povolená hodnota je: **Základní**. |Ano |
| username |Zadejte uživatelské jméno pro připojení k databázi DB2. |Ano |
| password |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných datovou sadou DB2.

Chcete-li kopírovat data z DB2, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **Db2Table** | Ano |
| schema | Název schématu. |Ne (když je zadán zdroj aktivity "query")  |
| table | Název tabulky. |Ne (když je zadán zdroj aktivity "query")  |
| tableName | Název tabulky se schématem Tato vlastnost je podporována z důvodu zpětné kompatibility. Pro `schema` nové `table` zatížení použijte a. | Ne (když je zadán zdroj aktivity "query") |

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

Pokud jste `RelationalTable` používali typovou datovou sadu, je stále podporovaná tak, jak je, a až budete chtít začít používat novinku dál.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností podporovaných zdrojem DB2.

### <a name="db2-as-source"></a>DB2 jako zdroj

Chcete-li kopírovat data z DB2, v části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **Db2Source** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Ne (když je "tableName" v datové sadě zadán) |

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

Pokud jste používali `RelationalSource` typový zdroj, je stále podporován tak, jak je, a když jste navrhli začít používat nový.

## <a name="data-type-mapping-for-db2"></a>Mapování datových typů pro DB2

Při kopírování dat z DB2 se z datových typů DB2 používají následující mapování pro Azure Data Factory dočasných datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Typ databáze DB2 | Data factory dočasné datový typ |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| Blob |Byte[] |
| Char |Řetězec |
| Datový typ CLOB |Řetězec |
| Date |Datetime |
| DB2DynArray |Řetězec |
| DbClob |Řetězec |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |Řetězec |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Řetězec |
| LongVarGraphic |Řetězec |
| Numeric |Decimal |
| Real |Single |
| SmallInt |Int16 |
| Time |TimeSpan |
| Timestamp |Datetime |
| VarBinary |Byte[] |
| VarChar |Řetězec |
| VarGraphic |Řetězec |
| Xml |Byte[] |


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
