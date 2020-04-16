---
title: Kopírování dat z QuickBooks Online pomocí Azure Data Factory (Preview)
description: Zjistěte, jak kopírovat data z QuickBooks Online do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: e2c9da9c1a37b087a31d1910094f51a39288c192
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416712"
---
# <a name="copy-data-from-quickbooks-online-using-azure-data-factory-preview"></a>Kopírování dat z QuickBooks Online pomocí Azure Data Factory (Preview)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí kopírovat aktivitu v Azure Data Factory ke kopírování dat z QuickBooks Online. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

> [!IMPORTANT]
> Tento konektor je aktuálně ve verzi Preview. Můžete to vyzkoušet a dát nám zpětnou vazbu. Pokud do svého řešení chcete zavést závislost na konektorech ve verzi Preview, kontaktujte [podporu Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor QuickBooks je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z aplikace QuickBooks Online můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, proto není nutné ručně instalovat žádný ovladač pomocí tohoto konektoru.

V současné době tento konektor podporuje pouze 1.0a, což znamená, že musíte mít vývojářský účet s aplikacemi vytvořenými před červencem 17, 2017.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor QuickBooks.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu QuickBooks jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **QuickBooks** | Ano |
| endpoint | Koncový bod serveru QuickBooks Online. (tedy quickbooks.api.intuit.com)  | Ano |
| společnostId | ID společnosti QuickBooks k autorizaci. Informace o tom, jak id společnosti najít, najdete v tématu [Jak najdu id společnosti?](https://quickbooks.intuit.com/community/Getting-Started/How-do-I-find-my-Company-ID/m-p/185551). | Ano |
| consumerKey | Spotřebitelský klíč pro ověřování OAuth 1.0. | Ano |
| consumerSecret | Tajný klíč příjemce pro ověřování OAuth 1.0. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| accessToken | Přístupový token pro ověřování OAuth 1.0. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| accessTokenSecret | Tajný klíč přístupového tokenu pro ověřování OAuth 1.0. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| použitíEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovány pomocí protokolu HTTPS. Výchozí hodnotou je hodnota true.  | Ne |

**Příklad:**

```json
{
    "name": "QuickBooksLinkedService",
    "properties": {
        "type": "QuickBooks",
        "typeProperties": {
            "endpoint" : "quickbooks.api.intuit.com",
            "companyId" : "<companyId>",
            "consumerKey": "<consumerKey>",
            "consumerSecret": {
                "type": "SecureString",
                "value": "<consumerSecret>"
            },
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            },
            "accessTokenSecret": {
                 "type": "SecureString",
                 "value": "<accessTokenSecret>"
            },
            "useEncryptedEndpoints" : true
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou QuickBooks.

Chcete-li kopírovat data z aplikace QuickBooks Online, nastavte vlastnost type datové sady na **QuickBooksObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **QuickBooksObject.** | Ano |
| tableName | Název tabulky. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "QuickBooksDataset",
    "properties": {
        "type": "QuickBooksObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<QuickBooks linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem QuickBooks.

### <a name="quickbooks-as-source"></a>QuickBooks jako zdroj

Chcete-li kopírovat data z aplikace QuickBooks Online, nastavte typ zdroje v aktivitě kopírování na **QuickBooksSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: QuickBooksSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM "Bill" WHERE Id = '123'"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromQuickBooks",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<QuickBooks input dataset name>",
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
                "type": "QuickBooksSource",
                "query": "SELECT * FROM \"Bill\" WHERE Id = '123' "
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="copy-data-from-quickbooks-desktop"></a>Kopírování dat z aplikace Quickbooks Desktop

Aktivita kopírování v Azure Data Factory nemůže kopírovat data přímo z quickbooks desktopu. Chcete-li zkopírovat data z aplikace Quickbooks Desktop, exportujte data Quickbooks do souboru csv (s hodnotami oddělenými čárkami) a pak soubor nahrajte do úložiště objektů Blob Azure. Odtud můžete použít Data Factory zkopírovat data do jímky podle vašeho výběru.

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
