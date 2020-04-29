---
title: Kopírování dat ze zdrojů IBM Informix pomocí Azure Data Factory
description: Naučte se, jak kopírovat data ze zdrojů IBM Informix do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: b4fb6662491443db5d10825635cad8496e56e7f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414964"
---
# <a name="copy-data-from-and-to-ibm-informix-data-stores-using-azure-data-factory"></a>Kopírování dat z a do úložišť dat IBM Informix pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z úložiště dat IBM Informix. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Informix se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data ze zdroje Informix můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

## <a name="prerequisites"></a>Požadavky

K použití tohoto konektoru Informix potřebujete:

- Nastavte Integration Runtime pro místní hostování. Podrobnosti najdete v článku [Integration runtime](create-self-hosted-integration-runtime.md) v místním prostředí.
- Nainstalujte na Integration Runtime počítač ovladač Informix ODBC pro úložiště dat. Můžete například použít ovladač IBM INFORMIX Informix DRIVER (64-bit).

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor Informix.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Informix jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **Informix** . | Ano |
| připojovací řetězec | Připojovací řetězec ODBC s výjimkou části s přihlašovacími údaji Můžete zadat připojovací řetězec nebo použít systémový DSN (název zdroje dat), který jste nastavili na Integration Runtimem počítači (v odpovídajícím způsobem je potřeba zadat část přihlašovacích údajů v propojené službě). <br> Můžete také vložit heslo do Azure Key Vault a `password` získat konfiguraci z připojovacího řetězce. Další podrobnosti najdete [v tématu uložení přihlašovacích údajů v Azure Key Vault](store-credentials-in-key-vault.md) .| Ano |
| authenticationType | Typ ověřování, který se používá pro připojení k úložišti dat Informix.<br/>Povolené hodnoty jsou: **základní** a **anonymní**. | Ano |
| userName | Pokud používáte základní ověřování, zadejte uživatelské jméno. | Ne |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| pověření | Část přístupového pověření v připojovacím řetězci, kterou jste zadali ve formátu hodnoty vlastnosti specifické pro ovladač. Označte toto pole jako SecureString. | Ne |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Integration Runtime v místním prostředí se vyžaduje, jak je uvedeno v [požadavcích](#prerequisites). |Ano |

**Případě**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou Informix.

Chcete-li kopírovat data z Informix, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **Informix** . | Ano |
| tableName | Název tabulky v Informix. | Ne pro zdroj (Pokud je zadáno "dotaz" ve zdroji aktivity);<br/>Ano pro jímku |

**Případě**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem Informix.

### <a name="informix-as-source"></a>Informix jako zdroj

Chcete-li kopírovat data z Informix, v části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **InformixSource** . | Ano |
| query | Pomocí vlastního dotazu můžete číst data. Například: `"SELECT * FROM MyTable"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

**Případě**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
