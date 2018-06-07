---
title: Kopírování dat z Teradata pomocí Azure Data Factory | Microsoft Docs
description: Další informace o Teradata konektor služby Data Factory, která umožňuje kopírování dat z databáze Teradata k úložištím dat podporovaných službou Data Factory jako jímky.
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 4360ff12a435afc4347fa97bba4506ccd81618aa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618976"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Kopírování dat z Teradata pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-onprem-teradata-connector.md)
> * [Verze 2 – Preview](connector-teradata.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z databáze Teradata. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [Teradata konektoru V1](v1/data-factory-onprem-teradata-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Do úložiště dat žádné podporované podřízený může kopírovat data z databáze Teradata. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Teradata podporuje:

- Teradata **verze 12 a vyšší**.
- Kopírování dat pomocí **základní** nebo **Windows** ověřování.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor Teradata, budete muset:

- Nastavte Self-hosted integrace Runtime. V tématu [Self-hosted integrace Runtime](create-self-hosted-integration-runtime.md) článku.
- Nainstalujte [zprostředkovatel dat .NET pro Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) verzi 14 nebo vyšší na počítači integrace modulu Runtime.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru Teradata.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Teradata propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **Teradata** | Ano |
| server | Název serveru Teradata. | Ano |
| authenticationType. | Typ ověřování používaný pro připojení k databázi Teradata.<br/>Povolené hodnoty jsou: **základní**, a **Windows**. | Ano |
| uživatelské jméno | Zadejte uživatelské jméno pro připojení k databázi Teradata. | Ano |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. Modul Runtime Self-hosted integrace se vyžaduje, jak je uvedeno v [požadavky](#prerequisites). |Ano |

**Příklad:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje datovou sadu Teradata.

Ke zkopírování dat z Teradata, nastavte vlastnost typu datové sady, která **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **RelationalTable** | Ano |
| tableName | Název tabulky v databázi Teradata. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které jsou podporovány zdrojem Teradata.

### <a name="teradata-as-source"></a>Teradata jako zdroj

Ke zkopírování dat z Teradata, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **RelationalSource** | Ano |
| query | Čtení dat pomocí vlastního dotazu SQL. Například: `"SELECT * FROM MyTable"`. | Ne (když je určena "tableName" v datové sadě) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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

## <a name="data-type-mapping-for-teradata"></a>Datový typ mapování pro Teradata

Při kopírování dat z Teradata, se používají následující mapování Teradata datových typů k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| Datový typ Teradata | Typ průběžných dat objektu pro vytváření dat |
|:--- |:--- |
| BigInt |Int64 |
| Objekt blob |Byte] |
| Bajtů |Byte] |
| ByteInt |Int16 |
| Char |Řetězec |
| Datový typ CLOB |Řetězec |
| Datum |DateTime |
| Decimal |Decimal |
| Double |Double |
| Obrázek |Řetězec |
| Integer |Int32 |
| Interval den |Časový interval |
| Interval den a hodina |Časový interval |
| Denní interval minuty. |Časový interval |
| Denní interval sekundy. |Časový interval |
| Interval hodinu |Časový interval |
| Interval hodiny, minuty. |Časový interval |
| Interval hodinu sekundu |Časový interval |
| Interval minutu |Časový interval |
| Interval minuty, sekundy. |Časový interval |
| Interval měsíc |Řetězec |
| Interval druhý |Časový interval |
| Interval roku |Řetězec |
| Interval rok, měsíc |Řetězec |
| Číslo |Double |
| Period(Date) |Řetězec |
| Period(Time) |Řetězec |
| Období (čas s časovým pásmem) |Řetězec |
| Period(Timestamp) |Řetězec |
| Období (časové razítko s časovým pásmem) |Řetězec |
| SmallInt |Int16 |
| Čas |Časový interval |
| Čas s časovým pásmem |Řetězec |
| Časové razítko |DateTime |
| Časové razítko s časovým pásmem |Datový typ DateTimeOffset |
| VarByte |Byte] |
| VarChar |Řetězec |
| VarGraphic |Řetězec |
| XML |Řetězec |


## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
