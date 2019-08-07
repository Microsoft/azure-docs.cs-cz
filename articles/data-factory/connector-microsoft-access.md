---
title: Kopírování dat z zdrojů Microsoft Accessu pomocí Azure Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data ze zdrojů Microsoft Access do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 2010799dd590808a2c5be7d9e2330bd60e2c7913
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842488"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Kopírování dat z a do úložišť dat z aplikace Microsoft Access pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z úložiště dat Microsoft Access. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Data ze zdroje Microsoft Access můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor Microsoft Accessu, musíte:

- Nastavte Integration Runtime pro místní hostování. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.
- Na Integration Runtime počítač nainstalujte ovladač ODBC pro Microsoft Access pro úložiště dat.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor Microsoft Access.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Microsoft Access jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **MicrosoftAccess** | Ano |
| connectionString | Připojovací řetězec ODBC s výjimkou části s přihlašovacími údaji Můžete zadat připojovací řetězec nebo použít systémový DSN (název zdroje dat), který jste nastavili na Integration Runtimem počítači (v odpovídajícím způsobem je potřeba zadat část přihlašovacích údajů v propojené službě).<br>Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md).| Ano |
| authenticationType | Typ ověřování, který se používá pro připojení k úložišti dat Microsoft Access.<br/>Povolené hodnoty jsou: **Základní** a **anonymní**. | Ano |
| userName | Pokud používáte základní ověřování, zadejte uživatelské jméno. | Ne |
| password | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| credential | Část přístupového pověření v připojovacím řetězci, kterou jste zadali ve formátu hodnoty vlastnosti specifické pro ovladač. Označte toto pole jako SecureString. | Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Integration Runtime v místním prostředí se vyžaduje, jak je uvedeno v [požadavcích](#prerequisites). |Ano |

**Příklad:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. V této části najdete seznam vlastností podporovaných datovou sadou Microsoft Access.

Chcete-li kopírovat data z aplikace Microsoft Access, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **MicrosoftAccessTable** | Ano |
| tableName | Název tabulky v Microsoft Accessu | Ne pro zdroj (Pokud je zadáno "dotaz" ve zdroji aktivity);<br/>Ano pro jímku |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností podporovaných zdrojem přístupu společnosti Microsoft.

### <a name="microsoft-access-as-source"></a>Microsoft Access as source

Chcete-li kopírovat data z úložiště dat kompatibilního s Microsoft Access, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **MicrosoftAccessSource** | Ano |
| query | Pomocí vlastního dotazu můžete číst data. Například: `"SELECT * FROM MyTable"`. | Ne (když je "tableName" v datové sadě zadán) |

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

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
