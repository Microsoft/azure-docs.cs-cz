---
title: Kopírování dat z marketingového cloudu Salesforce
description: Zjistěte, jak kopírovat data z Salesforce Marketing Cloud do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: a76150756d87b5f2c1b73b8755500e63cdfe05c9
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991735"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Kopírování dat z marketingového cloudu Salesforce pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z Salesforce Marketing Cloud. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Salesforce Marketing Cloud je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z salesforce marketingcloudu můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konektor Salesforce Marketing Cloud podporuje ověřování OAuth 2. Je postaven na rozhraní [Salesforce Marketing Cloud REST API](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm).

>[!NOTE]
>Tento konektor nepodporuje načítání vlastních objektů nebo vlastních datových rozšíření.

## <a name="getting-started"></a>Začínáme

Můžete vytvořit kanál s aktivitou kopírování pomocí .NET SDK, Python SDK, Azure PowerShell, REST API nebo Azure Resource Manager šablony. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](quickstart-create-data-factory-dot-net.md) aktivity.

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Datové továrny specifických pro konektor Salesforce Marketing Cloud.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Salesforce Marketing Cloud jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena **na: SalesforceMarketingCloud.** | Ano |
| clientId | ID klienta přidružené k aplikaci Salesforce Marketing Cloud.  | Ano |
| clientSecret | Tajný klíč klienta přidružený k aplikaci Salesforce Marketing Cloud. Toto pole můžete označit jako securestring pro bezpečné uložení v adf nebo ukládat heslo v trezoru klíčů Azure a nechat adf kopírovat aktivitu vytáhnout odtud při provádění kopírování dat - další informace z [přihlašovacích údajů úložiště v trezoru klíčů](store-credentials-in-key-vault.md). | Ano |
| použitíEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovány pomocí protokolu HTTPS. Výchozí hodnotou je hodnota true.  | Ne |
| useHostVerification | Určuje, zda má být při připojování přes TLS vyžadovánnázev hostitele v certifikátu serveru. Výchozí hodnotou je hodnota true.  | Ne |
| usePeerVerification | Určuje, zda se má ověřit identita serveru při připojování přes TLS. Výchozí hodnotou je hodnota true.  | Ne |

**Příklad:**

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Salesforce Marketing Cloud.

Chcete-li kopírovat data z cloudu Salesforce Marketing Cloud, nastavte vlastnost type datové sady na **SalesforceMarketingCloudObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena **na: SalesforceMarketingCloudCloudObject.** | Ano |
| tableName | Název tabulky. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem služby Salesforce Marketing Cloud.

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce Marketing Cloud jako zdroj

Chcete-li kopírovat data z cloudu Salesforce Marketing Cloud, nastavte typ zdroje v aktivitě kopírování na **SalesforceMarketingCloudSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivit kopírování musí být nastavena **na: SalesforceMarketingCloudSource.** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
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
