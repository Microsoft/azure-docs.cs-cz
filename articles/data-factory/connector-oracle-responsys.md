---
title: Kopírování dat z oracle responsys (preview)
description: Zjistěte, jak kopírovat data z Oracle Responsys do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: cde0e2d118490801aeee3874e220d298fea24598
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416844"
---
# <a name="copy-data-from-oracle-responsys-using-azure-data-factory-preview"></a>Kopírování dat z oracle responsys pomocí Azure Data Factory (Preview)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z Oracle Responsys. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

> [!IMPORTANT]
> Tento konektor je aktuálně ve verzi Preview. Můžete to vyzkoušet a dát nám zpětnou vazbu. Pokud do svého řešení chcete zavést závislost na konektorech ve verzi Preview, kontaktujte [podporu Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Oracle Responsys je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z oracle responsys můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, proto není nutné ručně instalovat žádný ovladač pomocí tohoto konektoru.

## <a name="getting-started"></a>Začínáme

Můžete vytvořit kanál s aktivitou kopírování pomocí .NET SDK, Python SDK, Azure PowerShell, REST API nebo Azure Resource Manager šablony. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](quickstart-create-data-factory-dot-net.md) aktivity.

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit datové továrny specifických pro konektor Oracle Responsys.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Oracle Responsys jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena **na: Responsys** | Ano |
| endpoint | Koncový bod serveru Respopnsys  | Ano |
| clientId | ID klienta přidružené k aplikaci Responsys.  | Ano |
| clientSecret | Tajný klíč klienta přidružený k aplikaci Responsys. Toto pole můžete označit jako securestring pro bezpečné uložení v adf nebo ukládat heslo v trezoru klíčů Azure a nechat adf kopírovat aktivitu vytáhnout odtud při provádění kopírování dat - další informace z [přihlašovacích údajů úložiště v trezoru klíčů](store-credentials-in-key-vault.md). | Ano |
| použitíEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovány pomocí protokolu HTTPS. Výchozí hodnotou je hodnota true.  | Ne |
| useHostVerification | Určuje, zda má být při připojování přes TLS vyžadovánnázev hostitele v certifikátu serveru. Výchozí hodnotou je hodnota true.  | Ne |
| usePeerVerification | Určuje, zda se má ověřit identita serveru při připojování přes TLS. Výchozí hodnotou je hodnota true.  | Ne |

**Příklad:**

```json
{
    "name": "OracleResponsysLinkedService",
    "properties": {
        "type": "Responsys",
        "typeProperties": {
            "endpoint" : "<endpoint>",
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}

```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Oracle Responsys.

Chcete-li zkopírovat data z oracle responsys, nastavte vlastnost type datové sady na **ResponsysObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena **na: ResponsysObject.** | Ano |
| tableName | Název tabulky. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "OracleResponsysDataset",
    "properties": {
        "type": "ResponsysObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Oracle Responsys linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem Oracle Responsys.

### <a name="oracle-responsys-as-source"></a>Oracle Responsys jako zdroj

Chcete-li zkopírovat data z oracle responsys, nastavte typ zdroje v aktivitě kopírování na **ResponsysSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: ResponsysSource.** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromOracleResponsys",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle Responsys input dataset name>",
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
                "type": "ResponsysSource",
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
