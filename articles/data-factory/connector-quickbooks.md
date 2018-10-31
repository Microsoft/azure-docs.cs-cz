---
title: Kopírování dat z QuickBooks Online pomocí Azure Data Factory (Preview) | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z QuickBooks Online úložiště dat jímky podporované s využitím aktivity kopírování v kanálu Azure Data Factory.
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
ms.date: 10/29/2018
ms.author: jingwang
ms.openlocfilehash: a3d079483ecf4ea8cf9a4c6bda050bfe8befcfd0
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241677"
---
# <a name="copy-data-from-quickbooks-online-using-azure-data-factory-preview"></a>Kopírování dat z QuickBooks Online pomocí Azure Data Factory (Preview)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z QuickBooks Online. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

> [!IMPORTANT]
> Tento konektor je aktuálně ve verzi preview. Můžete vyzkoušet a sdělte nám svůj názor. Pokud do svého řešení chcete zavést závislost na konektorech ve verzi Preview, kontaktujte [podporu Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Podporované funkce

Data můžete zkopírovat z QuickBooks Online pro všechny podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Poskytuje integrované ovladače chcete umožnit připojení k Azure Data Factory, proto není nutné ručně nainstalovat všechny ovladače používání tohoto konektoru.

Tento konektor aktuálně podporují pouze 1.0a, což znamená, že je potřeba mít účet pro vývojáře s aplikacemi, které jsou vytvořené před 17. července 2017.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní QuickBooks konektoru.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

QuickBooks propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **QuickBooks** | Ano |
| endpoint | Koncový bod serveru QuickBooks Online. (to znamená quickbooks.api.intuit.com)  | Ano |
| % companyId | ID společnosti QuickBooks společnosti k autorizaci. Informace o tom, jak najít ID společnosti, naleznete v tématu [jak najít ID společnosti?](https://quickbooks.intuit.com/community/Getting-Started/How-do-I-find-my-Company-ID/m-p/185551). | Ano |
| consumerKey | Uživatelský klíč pro ověřování OAuth 1.0. | Ano |
| consumerSecret | Uživatelský tajný klíč pro ověřování OAuth 1.0. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| accessToken | Přístupový token k ověření OAuth 1.0. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| accessTokenSecret | Tajný klíč přístupového tokenu pro ověřování OAuth 1.0. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| useEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovat pomocí protokolu HTTPS. Výchozí hodnota je true.  | Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností podporovaných datovou sadu QuickBooks.

Ke zkopírování dat z QuickBooks Online, nastavte vlastnost typ datové sady na **QuickBooksObject**. Neexistuje žádné další vlastnosti specifické pro typ. v tomto typu datové sady.

**Příklad**

```json
{
    "name": "QuickBooksDataset",
    "properties": {
        "type": "QuickBooksObject",
        "linkedServiceName": {
            "referenceName": "<QuickBooks linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem QuickBooks.

### <a name="quickbookssource-as-source"></a>QuickBooksSource jako zdroj

Ke zkopírování dat z QuickBooks Online, nastavte typ zdroje v aktivitě kopírování do **QuickBooksSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu zdroje aktivity kopírování musí být nastavena na: **QuickBooksSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM "Bill" WHERE Id = '123'"`. | Ano |

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
## <a name="copy-data-from-quickbooks-desktop"></a>Kopírování dat z Quickbooks plochy

Aktivita kopírování ve službě Azure Data Factory nelze kopírovat data přímo z plochy Quickbooks. Ke zkopírování dat z Desktopu Quickbooks, exportovat Quickbooks data do souboru čárkou hodnot oddělených čárkami (CSV) a pak nahrajte soubor do úložiště objektů Blob v Azure. Odtud můžete použít Data Factory ke zkopírování dat do jímky podle vašeho výběru.

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
