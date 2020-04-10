---
title: Kopírování dat z Marketo pomocí Azure Data Factory (Preview)
description: Zjistěte, jak kopírovat data z Marketo do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 40a16d559a96c88a864ef809d40d798b99746230
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992092"
---
# <a name="copy-data-from-marketo-using-azure-data-factory-preview"></a>Kopírování dat z Marketo pomocí Azure Data Factory (Preview)

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z Marketo. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

> [!IMPORTANT]
> Tento konektor je aktuálně ve verzi Preview. Můžete to vyzkoušet a dát nám zpětnou vazbu. Pokud do svého řešení chcete zavést závislost na konektorech ve verzi Preview, kontaktujte [podporu Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Marketo je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data z Marketo do libovolného úložiště dat podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, proto není nutné ručně instalovat žádný ovladač pomocí tohoto konektoru.

>[!NOTE]
>Tento konektor Marketo je postaven na rozhraní Marketo REST API. Uvědomte si, že Marketo má [souběžný limit požadavků](https://developers.marketo.com/rest-api/) na straně služby. Pokud se zobrazí chyby s nápisem "Chyba při pokusu o použití rozhraní REST API: Maximální limit rychlosti 100" byl překročen v chybách 20 sekund (606)" nebo "Chyba při pokusu o použití rozhraní REST API: Souběžný limit přístupu "10" dosažený (615)", zvažte snížení souběžných spuštění aktivity kopírování, abyste snížili počet požadavků na službu.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro konektor Marketo.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Marketo jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena **na: Marketo** | Ano |
| endpoint | Koncový bod serveru Marketo. (tj. 123-ABC-321.mktorest.com)  | Ano |
| clientId | ID klienta vaší služby Marketo.  | Ano |
| clientSecret | Klient tajemství vaší služby Marketo. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| použitíEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovány pomocí protokolu HTTPS. Výchozí hodnotou je hodnota true.  | Ne |
| useHostVerification | Určuje, zda má být při připojování přes TLS vyžadovánnázev hostitele v certifikátu serveru. Výchozí hodnotou je hodnota true.  | Ne |
| usePeerVerification | Určuje, zda se má ověřit identita serveru při připojování přes TLS. Výchozí hodnotou je hodnota true.  | Ne |

**Příklad:**

```json
{
    "name": "MarketoLinkedService",
    "properties": {
        "type": "Marketo",
        "typeProperties": {
            "endpoint" : "123-ABC-321.mktorest.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Marketo.

Chcete-li kopírovat data z Marketo, nastavte vlastnost type datové sady na **MarketoObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena **na: MarketoObject.** | Ano |
| tableName | Název tabulky. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "MarketoDataset",
    "properties": {
        "type": "MarketoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Marketo linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem Marketo.

### <a name="marketo-as-source"></a>Marketo jako zdroj

Chcete-li kopírovat data z Marketo, nastavte typ zdroje v aktivitě kopírování **na MarketoSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: MarketoSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM Activitiy_Types"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromMarketo",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Marketo input dataset name>",
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
                "type": "MarketoSource",
                "query": "SELECT top 1000 * FROM Activitiy_Types"
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
