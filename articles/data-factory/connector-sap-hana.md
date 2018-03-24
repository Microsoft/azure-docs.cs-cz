---
title: Kopírování dat z SAP HANA pomocí Azure Data Factory | Microsoft Docs
description: Postup kopírování dat z SAP HANA do úložiště dat podporovaných podřízený pomocí aktivity kopírování v kanál služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 96d9da90114ae392c3a9e1c22d8e993fd787ada8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopírování dat z SAP HANA pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-sap-hana-connector.md)
> * [Verze 2 – Preview](connector-sap-hana.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z databáze SAP HANA. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [SAP HANA konektoru V1](v1/data-factory-sap-hana-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Do úložiště dat žádné podporované podřízený může kopírovat data z databáze SAP HANA. Seznam úložišť dat jako zdroje nebo jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SAP HANA podporuje:

- Kopírování dat z libovolné verze databáze SAP HANA.
- Kopírování dat z **HANA informace modely** (například analýzy a výpočet zobrazení) a **řádek nebo sloupce tabulky** pomocí dotazů SQL.
- Kopírování dat pomocí **základní** nebo **Windows** ověřování.

> [!NOTE]
> Ke zkopírování dat **do** SAP HANA data uložit, použijte obecné konektor ODBC. V tématu [SAP HANA podřízený](connector-odbc.md#sap-hana-sink) s podrobnostmi. Všimněte si, které jsou propojené služby pro SAP HANA connector a konektor ODBC s jiným typem proto nelze znovu použít.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor SAP HANA, budete muset:

- Nastavte Self-hosted integrace Runtime. V tématu [Self-hosted integrace Runtime](create-self-hosted-integration-runtime.md) článku.
- Nainstalujte ovladač SAP HANA ODBC na počítači integrace modulu Runtime. Ovladač SAP HANA ODBC z si můžete stáhnout [SAP služby Stažení softwaru](https://support.sap.com/swdc). Vyhledávání pomocí klíčového slova **SAP HANA klienta pro systém Windows**.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru SAP HANA.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SAP HANA propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **SapHana** | Ano |
| server | Název serveru, na kterém se nachází instance SAP HANA. Pokud váš server používá vlastní port, zadejte `server:port`. | Ano |
| authenticationType. | Typ ověřování používaný pro připojení k databázi SAP HANA.<br/>Povolené hodnoty jsou: **základní**, a **Windows** | Ano |
| userName | Jméno uživatele, který má přístup k serveru SAP. | Ano |
| heslo | Heslo pro uživatele. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. Modul Runtime Self-hosted integrace se vyžaduje, jak je uvedeno v [požadavky](#prerequisites). |Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje datovou sadu SAP HANA.

Ke zkopírování dat z SAP HANA, nastavte vlastnost typu datové sady, která **RelationalTable**. Když nejsou k dispozici žádné vlastnosti specifické pro typ podporované pro SAP HANA datové sady zadejte RelationalTable.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které jsou podporovány zdrojem SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA jako zdroj

Ke zkopírování dat z SAP HANA, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **RelationalSource** | Ano |
| query | Určuje příkaz jazyka SQL pro čtení dat z instance SAP HANA. | Ano |

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

Při kopírování dat z SAP HANA, se používají následující mapování SAP HANA datových typů k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| Datový typ SAP HANA | Typ průběžných dat objektu pro vytváření dat |
|:--- |:--- |
| ALPHANUM | Řetězec |
| BIGINT | Int64 |
| OBJEKT BLOB | Byte[] |
| LOGICKÁ HODNOTA | Bajtů |
| DATOVÝ TYP CLOB | Byte[] |
| DATE (Datum) | DateTime |
| DECIMAL | Decimal |
| DOUBLE | Svobodný/svobodná |
| INT | Int32 |
| NVARCHAR | Řetězec |
| SKUTEČNÉ | Svobodný/svobodná |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| ČAS | TimeSpan |
| ČASOVÉ RAZÍTKO | DateTime |
| TINYINT | Bajtů |
| VARCHAR | Řetězec |

## <a name="known-limitations"></a>Známá omezení

Při kopírování dat z SAP HANA existuje několik známá omezení:

- NVARCHAR řetězce byl zkrácen na maximální délce 4000 znaků Unicode
- SMALLDECIMAL není podporován.
- VARBINARY není podporován.
- Platná data jsou mezi 1899/12/30 a 9999/12/31


## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).