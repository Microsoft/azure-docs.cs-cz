---
title: Kopírování dat ze zdrojů aplikace Microsoft Access
description: Zjistěte, jak kopírovat data ze zdrojů aplikace Microsoft Access do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: fc2179efcda4ee11dda3b424b16a072a2bb2c26e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418180"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Kopírování dat z úložišť dat microsoft access a do nich pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí kopírovat aktivitu v Azure Data Factory ke kopírování dat z úložiště dat Microsoft Access. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor aplikace Microsoft Access je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data ze zdroje aplikace Microsoft Access můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="prerequisites"></a>Požadavky

Chcete-li používat tento konektor aplikace Microsoft Access, je třeba:

- Nastavte runtime integrace s vlastním hostitelem. Podrobnosti najdete v článku [runtime integrace s vlastním hostitelem.](create-self-hosted-integration-runtime.md)
- Nainstalujte ovladač ODBC aplikace Microsoft Access pro úložiště dat v integračním automatu Runtime.

>[!NOTE]
>Microsoft Access 2016 verze ovladače ODBC nefunguje s tímto konektorem. Místo toho použijte ovladač verze 2013 nebo 2010.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit datové továrny specifických pro konektor aplikace Microsoft Access.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu aplikace Microsoft Access jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **MicrosoftAccess.** | Ano |
| připojovací řetězec | Připojovací řetězec ODBC s výjimkou části pověření. Můžete zadat připojovací řetězec nebo použít systém DSN (Název zdroje dat), který jste nastavili v integračním stroji Runtime (musíte podle toho zadat část pověření v propojené službě).<br> Můžete také umístit heslo v Azure Key `password` Vault a vyžádat konfiguraci z připojovacího řetězce.Další podrobnosti najdete [v přihlašovacích údajích úložiště v azure trezoru](store-credentials-in-key-vault.md) klíčů.| Ano |
| authenticationType | Typ ověřování používaný k připojení k úložišti dat aplikace Microsoft Access<br/>Povolené hodnoty jsou: **Základní** a **Anonymní**. | Ano |
| userName | Pokud používáte základní ověřování, zadejte uživatelské jméno. | Ne |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |
| pověření | Část připojovacího řetězce přístupová pověření zadaná ve formátu hodnoty vlastnosti specifické pro ovladač. Označte toto pole jako securestring. | Ne |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Runtime integrace hostované samostatně je vyžadován, jak je uvedeno v [požadavky](#prerequisites). |Ano |

**Příklad:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
        "typeProperties": {
            "connectionString": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou aplikace Microsoft Access.

Chcete-li zkopírovat data z aplikace Microsoft Access, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **MicrosoftAccessTable.** | Ano |
| tableName | Název tabulky v aplikaci Microsoft Access. | Ne pro zdroj (pokud je zadán "dotaz" ve zdroji aktivity);<br/>Ano pro umyvadlo |

**Příklad**

```json
{
    "name": "MicrosoftAccessDataset",
    "properties": {
        "type": "MicrosoftAccessTable",
        "linkedServiceName": {
            "referenceName": "<Microsoft Access linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem aplikace Microsoft Access.

### <a name="microsoft-access-as-source"></a>Aplikace Microsoft Access jako zdroj

Chcete-li kopírovat data z úložiště dat kompatibilních s aplikací Microsoft Access, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na: **MicrosoftAccessSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz. Například: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromMicrosoftAccess",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Microsoft Access input dataset name>",
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
                "type": "MicrosoftAccessSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
