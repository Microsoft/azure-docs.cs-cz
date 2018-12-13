---
title: Kopírování dat z Shopify pomocí Azure Data Factory (Preview) | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z Shopify úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: e9271081b36681c4011d96b329de5058aeaf8472
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090614"
---
# <a name="copy-data-from-shopify-using-azure-data-factory-preview"></a>Kopírování dat z Shopify pomocí Azure Data Factory (Preview)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z Shopify. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

> [!IMPORTANT]
> Tento konektor je aktuálně ve verzi preview. Můžete vyzkoušet a sdělte nám svůj názor. Pokud do svého řešení chcete zavést závislost na konektorech ve verzi Preview, kontaktujte [podporu Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z Shopify do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Poskytuje integrované ovladače chcete umožnit připojení k Azure Data Factory, proto není nutné ručně nainstalovat všechny ovladače používání tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní Shopify konektoru.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Shopify propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **Shopify** | Ano |
| hostitel | Koncový bod serveru Shopify. (to znamená mystore.myshopify.com)  | Ano |
| accessToken | Přístupový token rozhraní API, který slouží pro přístup k datům společnosti Shopify. Pokud je v režimu offline režimu, nemá prošlou platnost tokenu. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| useEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovat pomocí protokolu HTTPS. Výchozí hodnota je true.  | Ne |
| useHostVerification | Určuje, jestli se vyžaduje název hostitele v certifikátu serveru tak, aby odpovídaly názvu hostitele serveru při připojení přes protokol SSL. Výchozí hodnota je true.  | Ne |
| usePeerVerification | Určuje, jestli se má ověřit identitu serveru při připojení přes protokol SSL. Výchozí hodnota je true.  | Ne |

**Příklad:**

```json
{
    "name": "ShopifyLinkedService",
    "properties": {
        "type": "Shopify",
        "typeProperties": {
            "host" : "mystore.myshopify.com",
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje Shopify datové sady.

Ke zkopírování dat z Shopify, nastavte vlastnost typ datové sady na **ShopifyObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ datové sady, musí být nastavena na: **ShopifyObject** | Ano |
| tableName | Název tabulky. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad**

```json
{
    "name": "ShopifyDataset",
    "properties": {
        "type": "ShopifyObject",
        "linkedServiceName": {
            "referenceName": "<Shopify linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Shopify zdroje.

### <a name="shopify-as-source"></a>Shopify jako zdroj

Ke zkopírování dat z Shopify, nastavte typ zdroje v aktivitě kopírování do **ShopifySource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu zdroje aktivity kopírování musí být nastavena na: **ShopifySource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM "Products" WHERE Product_Id = '123'"`. | Ne (když je "tableName" v datové sadě zadán) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromShopify",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Shopify input dataset name>",
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
                "type": "ShopifySource",
                "query": "SELECT * FROM \"Products\" WHERE Product_Id = '123'"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
