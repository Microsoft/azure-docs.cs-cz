---
title: Kopírování dat z databáze DB2 pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z databáze DB2 úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: f9d1d2181649cf24784dc7ad11638946c9ee4406
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054636"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopírování dat z databáze DB2 pomocí služby Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-onprem-db2-connector.md)
> * [Aktuální verze](connector-db2.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z databáze DB2. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z databáze DB2 do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor DB2 podporuje tyto platformy IBM DB2 a verze pomocí distribuovaných relační databáze architektury (DRDA) SQL přístup správce (SQLAM) verze 9, 10 a 11:

* IBM DB2 pro 11.1 z/OS
* IBM DB2 pro 10.1 z/OS
* IBM DB2 pro i 7.2
* IBM DB2 pro i 7.1
* IBM DB2 LUW 11
* IBM DB2 pro LUW 10.5
* IBM DB2 pro LUW 10.1

> [!TIP]
> Pokud se zobrazí chybová zpráva s oznámením "balíček, který odpovídá na žádost o spuštění příkazu SQL nebyl nalezen. SQLSTATE = 51002 SQLCODE =-805 ", důvodem je potřebný balíček není vytvořena pro běžného uživatele na těchto operačních systémech. Postupujte podle těchto pokynů podle typu DB2 serveru:
> - Pro DB2 i (AS400): uživatel power vytvoření kolekce pro přihlášení uživatele před použitím aktivity kopírování. Příkaz: `create collection <username>`
> - DB2 z/OS nebo LUW: použijte účet vysoká oprávnění - power users nebo správcem orgány balíček a VAZBU, BINDADD, udělení provést na veřejné oprávnění – spuštění aktivity kopírování jednou a potom potřebný balíček se automaticky vytvoří během kopírování. Později můžete přepnout zpět na normální uživatele pro následné kopie spuštění.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít kopírování dat z databáze DB2, který není veřejně přístupná, musíte nastavit modul Integration Runtime. Další informace o prostředí v místním prostředí integration Runtime najdete v tématu [modul Integration Runtime](create-self-hosted-integration-runtime.md) článku. Prostředí Integration Runtime poskytuje integrované ovladač DB2, proto není nutné ručně nainstalovat všechny ovladače při kopírování dat z databáze DB2.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní do DB2. konektor.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro DB2 propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **Db2** | Ano |
| server |Název serveru DB2. Můžete zadat číslo portu za název serveru oddělené dvojtečkou třeba `server:port`. |Ano |
| databáze |Název databáze DB2. |Ano |
| authenticationType. |Typ ověřování používaný pro připojení k databázi DB2.<br/>Povolená hodnota je: **základní**. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno pro připojení k databázi DB2. |Ano |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje datové sady DB2.

Ke zkopírování dat z databáze DB2, nastavte vlastnost typ datové sady na **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ datové sady, musí být nastavena na: **RelationalTable** | Ano |
| tableName | Název tabulky v databázi DB2. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem DB2.

### <a name="db2-as-source"></a>DB2 jako zdroj

Ke zkopírování dat z databáze DB2, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu zdroje aktivity kopírování musí být nastavena na: **RelationalSource** | Ano |
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
                "type": "RelationalSource",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-db2"></a>Datový typ mapování pro DB2

Při kopírování dat z databáze DB2, se používají následující mapování z DB2 datových typů na Azure Data Factory dočasné datové typy. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Typ databáze DB2. | Data factory dočasné datový typ |
|:--- |:--- |
| BigInt |Int64 |
| Binární hodnota |Byte] |
| Objekt blob |Byte] |
| Char |Řetězec |
| Datový typ CLOB |Řetězec |
| Datum |Datum a čas |
| DB2DynArray |Řetězec |
| DbClob |Řetězec |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Obrázek |Řetězec |
| Integer |Datový typ Int32 |
| LongVarBinary |Byte] |
| LongVarChar |Řetězec |
| LongVarGraphic |Řetězec |
| Čísla |Decimal |
| Real |Jednoduchá |
| SmallInt |Int16 |
| Čas |Časový interval |
| Časové razítko |DateTime |
| VarBinary |Byte] |
| VarChar |Řetězec |
| VarGraphic |Řetězec |
| XML |Byte] |


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
