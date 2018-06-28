---
title: Kopírování dat z DB2 pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak zkopírovat data z DB2 do úložiště dat podporovaných podřízený pomocí aktivity kopírování v kanál služby Azure Data Factory.
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
ms.date: 03/28/2018
ms.author: jingwang
ms.openlocfilehash: 11647c231db3ff7beb2fed641dc72ff339b2b45a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046237"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopírování dat z DB2 pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-onprem-db2-connector.md)
> * [Aktuální verze](connector-db2.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z databáze DB2. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Do úložiště dat žádné podporované podřízený může kopírovat data z databáze DB2. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor DB2 podporuje tyto platformy IBM DB2 a verzí s distribuované relační databáze architektura (DRDA) SQL přístup správce (SQLAM) verze 9, 10 a 11:

* IBM DB2 pro z 11.1 operačního systému
* IBM DB2 pro z 10.1 operačního systému
* IBM DB2 pro i 7.2
* IBM DB2 pro i 7.1
* IBM DB2 pro LUW 11
* IBM DB2 pro LUW 10.5
* IBM DB2 pro LUW 10.1

> [!TIP]
> Pokud se zobrazí chybová zpráva s oznámením "nebyl nalezen balíček odpovídající žádost o provedení příkazu SQL. SQLSTATE = 51002 SQLCODE =-805 ", z důvodu je potřebný balíček není vytvořena pro běžné uživatele na tyto operačního systému. Postupujte podle těchto pokynů podle typu DB2 serveru:
> - DB2 pro i (AS400): umožní vytvoření kolekce pro přihlášení uživatele před použitím aktivity kopírování power users. Příkaz: `create collection <username>`
> - DB2 pro z/OS nebo LUW: Po spuštění aktivity kopírování pomocí účtu s vysokou úrovní oprávnění - power users nebo správce s balíček autority a vazby, BINDADD, UDĚLTE provést na veřejná oprávnění - a potom potřebné balíček je automaticky vytvořen během kopírování. Později můžete přepnout zpět na normální pro vaše další kopie spustí.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít kopírování dat z databáze DB2, která není veřejně přístupný, nastavení modulu Runtime Self-hosted integrace. Další informace o vlastním hostováním integrační moduly Runtime najdete v tématu [Self-hosted integrace Runtime](create-self-hosted-integration-runtime.md) článku. Modul Runtime integrace poskytuje integrované ovladače DB2, proto nemusíte ručně nainstalovat všechny ovladače při kopírování dat z databáze DB2.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru DB2.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro DB2 propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **Db2** | Ano |
| server |Název serveru DB2. |Ano |
| databáze |Název databáze DB2. |Ano |
| authenticationType. |Typ ověřování používaný pro připojení k databázi DB2.<br/>Povolená hodnota je: **základní**. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno pro připojení k databázi DB2. |Ano |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je veřejně přístupná data store), můžete použít modul Runtime integrace Self-hosted nebo Runtime integrace Azure. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

**Příklad:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername>",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje datovou sadu DB2.

Ke zkopírování dat z DB2, nastavte vlastnost typu datové sady, která **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **RelationalTable** | Ano |
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje zdroj DB2.

### <a name="db2-as-source"></a>DB2 jako zdroj

Ke zkopírování dat z DB2, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **RelationalSource** | Ano |
| query | Čtení dat pomocí vlastního dotazu SQL. Například: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Ne (když je určena "tableName" v datové sadě) |

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

Při kopírování dat z DB2, se používají následující mapování z DB2 datové typy k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| Typ databáze DB2 | Typ průběžných dat objektu pro vytváření dat |
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
| Integer |Int32 |
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
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
