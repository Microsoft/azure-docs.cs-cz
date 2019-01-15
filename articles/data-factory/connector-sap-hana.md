---
title: Kopírování dat ze SAP HANA pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data ze SAP HANA na úložiště dat jímky podporované s využitím aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: cdd83c3ff9d34a5e8b7f2c164136ab82f498ffb5
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022967"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopírování dat ze SAP HANA pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-sap-hana-connector.md)
> * [Aktuální verze](connector-sap-hana.md)

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat z databáze SAP HANA ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z databáze SAP HANA do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SAP HANA podporuje:

- Kopírování dat z libovolné verze databáze SAP HANA.
- Kopírování dat z **informační modely HANA** (například zobrazením analýzy a výpočtů) a **řádku/sloupce tabulky** pomocí příkazů jazyka SQL.
- Kopírování dat pomocí **základní** nebo **Windows** ověřování.

> [!NOTE]
> Pro kopírování dat **do** ukládat data systému SAP HANA, použít obecný konektor ODBC. Zobrazit [SAP HANA jímky](connector-odbc.md#sap-hana-sink) s podrobnostmi. Všimněte si, které jsou propojené služby pro konektor SAP HANA a konektor ODBC s jiným typem proto nelze znovu použít.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor SAP HANA, budete muset:

- Nastavte modul Integration Runtime. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.
- Nainstalujte ovladač SAP HANA ODBC na počítači prostředí Integration Runtime. Ovladače SAP HANA ODBC si můžete stáhnout [SAP Software Download Center](https://support.sap.com/swdc). Hledání pomocí klíčového slova **SAP HANA klienta pro Windows**.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor SAP HANA.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SAP HANA propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **SapHana** | Ano |
| server | Název serveru, na kterém se nachází instance SAP HANA. Pokud váš server používá vlastní port, zadejte `server:port`. | Ano |
| authenticationType. | Typ ověřování používaný pro připojení k databázi SAP HANA.<br/>Povolené hodnoty jsou: **Základní**, a **Windows** | Ano |
| uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP. | Ano |
| heslo | Heslo pro tohoto uživatele. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Modul Integration Runtime je povinný, jak je uvedeno v [požadavky](#prerequisites). |Ano |

**Příklad:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje datové sady SAP HANA.

Pro kopírování dat ze SAP HANA, nastavte vlastnost typ datové sady na **RelationalTable**. Přestože nejsou žádné vlastnosti specifické pro typ. podporované pro SAP HANA datové sady typu RelationalTable.

**Příklad:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA jako zdroj

Pro kopírování dat ze SAP HANA, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **RelationalSource** | Ano |
| query | Určuje dotaz SQL na čtení dat z instance SAP HANA. | Ano |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>Mapování datového typu pro SAP HANA

Kopírování dat ze SAP HANA, se používají následující mapování z typů dat SAP HANA na Azure Data Factory dočasné datové typy. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Typ dat SAP HANA | Data factory dočasné datový typ |
|:--- |:--- |
| ALPHANUM | Řetězec |
| BIGINT | Int64 |
| OBJEKT BLOB | Byte] |
| DATOVÝ TYP BOOLEAN | Bajt |
| DATOVÝ TYP CLOB | Byte] |
| DATE (Datum) | DateTime |
| DECIMAL | Desítkově |
| DOUBLE | Jednoduchá |
| INT | Datový typ Int32 |
| NVARCHAR | Řetězec |
| REAL | Jednoduchá |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| ČAS | Časový interval |
| ČASOVÉ RAZÍTKO | DateTime |
| TINYINT | Bajt |
| VARCHAR | Řetězec |

## <a name="known-limitations"></a>Známá omezení

Při kopírování dat ze SAP HANA platí několik známá omezení:

- U řetězce NVARCHAR je zkrácen na maximální délce 4000 znaků Unicode
- Se smalldecimal
- Nepodporuje se VARBINARY
- Platná data jsou mezi 1899, 12/30 a 9999/12/31


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
