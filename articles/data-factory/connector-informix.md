---
title: Kopírování dat z a do IBM Informix pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z a do IBM Informix pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: jingwang
ms.openlocfilehash: 93f484bd30de1ba0ca0f7aa5db263243bebc5b09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85508805"
---
# <a name="copy-data-from-and-to-ibm-informix-using-azure-data-factory"></a>Kopírování dat z a do IBM Informix pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z úložiště dat IBM Informix. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Informix se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data ze zdroje Informix můžete kopírovat do libovolného podporovaného úložiště dat jímky nebo je zkopírovat z libovolného podporovaného zdrojového úložiště dat do jímky Informix. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

## <a name="prerequisites"></a>Požadavky

K použití tohoto konektoru Informix potřebujete:

- Nastavte Integration Runtime pro místní hostování. Podrobnosti najdete v článku [Integration runtime](create-self-hosted-integration-runtime.md) v místním prostředí.
- Nainstalujte na Integration Runtime počítač ovladač Informix ODBC pro úložiště dat. Můžete například použít ovladač IBM INFORMIX Informix DRIVER (64-bit).

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor Informix.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Informix jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **Informix** . | Yes |
| připojovací řetězec | Připojovací řetězec ODBC s výjimkou části s přihlašovacími údaji Můžete zadat připojovací řetězec nebo použít systémový DSN (název zdroje dat), který jste nastavili na Integration Runtimem počítači (v odpovídajícím způsobem je potřeba zadat část přihlašovacích údajů v propojené službě). <br> Můžete také vložit heslo do Azure Key Vault a získat  `password`   konfiguraci z připojovacího řetězce. Další podrobnosti najdete [v tématu uložení přihlašovacích údajů v Azure Key Vault](store-credentials-in-key-vault.md)   .| Yes |
| authenticationType | Typ ověřování, který se používá pro připojení k úložišti dat Informix.<br/>Povolené hodnoty jsou: **základní** a **anonymní**. | Yes |
| userName | Pokud používáte základní ověřování, zadejte uživatelské jméno. | No |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | No |
| pověření | Část přístupového pověření v připojovacím řetězci, kterou jste zadali ve formátu hodnoty vlastnosti specifické pro ovladač. Označte toto pole jako SecureString. | No |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Integration Runtime v místním prostředí se vyžaduje, jak je uvedeno v [požadavcích](#prerequisites). |Yes |

**Příklad:**

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

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **Informix** . | Yes |
| tableName | Název tabulky v Informix. | Ne pro zdroj (Pokud je zadáno "dotaz" ve zdroji aktivity);<br/>Ano pro jímku |

**Příklad**

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

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **InformixSource** . | Yes |
| query | Pomocí vlastního dotazu můžete číst data. Například: `"SELECT * FROM MyTable"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

**Příklad:**

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

### <a name="informix-as-sink"></a>Informix jako jímka

Chcete-li kopírovat data do Informix, v části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type jímky aktivity kopírování musí být nastavená na: **InformixSink** . | Yes |
| writeBatchTimeout |Počkejte, než se operace dávkového vložení dokončí předtím, než vyprší časový limit.<br/>Povolené hodnoty jsou: TimeSpan. Příklad: "00:30:00" (30 minut). |No |
| writeBatchSize |Když velikost vyrovnávací paměti dosáhne writeBatchSize, vloží data do tabulky SQL.<br/>Povolené hodnoty jsou: celé číslo (počet řádků). |Ne (výchozí hodnota je 0 – automaticky se zjistilo) |
| preCopyScript |Zadejte dotaz SQL pro aktivitu kopírování, která má být provedena před zápisem dat do úložiště dat v každém spuštění. Tuto vlastnost můžete použít k vyčištění předem načtených dat. |No |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Informix output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "InformixSink"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
