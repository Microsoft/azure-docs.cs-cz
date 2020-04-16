---
title: Kopírování dat ze Sybase pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data ze Sybase do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 495d16efcc26fc336a87c0f2d88f5202ab0b4a3e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416617"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Kopírování dat ze Sybase pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-sybase-connector.md)
> * [Aktuální verze](connector-sybase.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z databáze Sybase. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Sybase je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z databáze Sybase můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor Sybase podporuje:

- SAP Sybase SQL Anywhere (ASA) **verze 16 a vyšší**; IQ a ASE nejsou podporovány.
- Kopírování dat pomocí **základního** ověřování nebo ověřování **systému Windows.**

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor Sybase, musíte:

- Nastavte runtime integrace s vlastním hostitelem. Podrobnosti najdete v článku [runtime integrace s vlastním hostitelem.](create-self-hosted-integration-runtime.md)
- Nainstalujte [zprostředkovatele dat pro Sybase iAnywhere.Data.SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 nebo vyšší na integračním automatu Runtime.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro konektor Sybase.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Sybase jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **Sybase** | Ano |
| server | Název serveru Sybase. |Ano |
| database | Název databáze Sybase. |Ano |
| authenticationType | Typ ověřování používaný pro připojení k databázi Sybase.<br/>Povolené hodnoty jsou: **Basic**a **Windows**. |Ano |
| uživatelské jméno | Zadejte uživatelské jméno pro připojení k databázi Sybase. |Ano |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Runtime integrace hostované samostatně je vyžadován, jak je uvedeno v [požadavky](#prerequisites). |Ano |

**Příklad:**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Sybase.

Chcete-li kopírovat data ze sybase, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **SybaseTable.** | Ano |
| tableName | Název tabulky v databázi Sybase. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "SybaseTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Pokud jste `RelationalTable` používali zadaný datový soubor, je stále podporována tak, jak je, zatímco se doporučuje používat novou do budoucna.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem Sybase.

### <a name="sybase-as-source"></a>Sybase jako zdroj

Chcete-li kopírovat data ze služby Sybase, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: SybaseSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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
                "type": "SybaseSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Pokud jste `RelationalSource` používali zadaný zdroj, je stále podporován jako-je, zatímco jste navrhl použít nový do budoucna.

## <a name="data-type-mapping-for-sybase"></a>Mapování datového typu pro Sybase

Při kopírování dat ze Sybase se používají následující mapování z datových typů Sybase do dočasných datových typů Azure Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování schématu a datových typů.](copy-activity-schema-and-type-mapping.md)

Sybase podporuje typy T-SQL. Tabulku mapování z typů SQL na dočasné datové typy Azure Data Factory najdete v článku Azure SQL Database Connector – část [mapování datových typů.](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database)

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).



## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
