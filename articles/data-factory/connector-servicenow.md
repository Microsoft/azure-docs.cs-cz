---
title: Kopírování dat z ServiceNow pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak zkopírovat data z ServiceNow k úložištím dat. podporované podřízený pomocí aktivity kopírování v kanál služby Azure Data Factory.
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
ms.date: 05/28/2018
ms.author: jingwang
ms.openlocfilehash: c67f6c14dc396367e0179fe5bdb4663fcb7725da
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045962"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Kopírování dat z ServiceNow pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z ServiceNow. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Data můžete zkopírovat z ServiceNow do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Azure Data Factory poskytuje integrované ovladače pro umožnění připojení, proto nemusíte ručně nainstalovat všechny ovladače, používání tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru ServiceNow.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro ServiceNow propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **ServiceNow** | Ano |
| endpoint | Koncový bod serveru ServiceNow (`http://<instance>.service-now.com`).  | Ano |
| authenticationType. | Typ ověřování používat. <br/>Povolené hodnoty jsou: **základní**, **OAuth2** | Ano |
| uživatelské jméno | Uživatelské jméno používané pro připojení k serveru ServiceNow pro ověřování Basic a OAuth2.  | Ano |
| heslo | Heslo odpovídající uživatelské jméno pro ověřování Basic a OAuth2. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| clientId | ID klienta pro ověřování OAuth2.  | Ne |
| clientSecret | Tajný klíč klienta pro ověřování OAuth2. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| useEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat jsou šifrované pomocí protokolu HTTPS. Výchozí hodnota je true.  | Ne |
| useHostVerification | Určuje, jestli chcete vyžadovat názvu hostitele v certifikátu serveru, aby odpovídal názvu hostitele serveru při připojení přes protokol SSL. Výchozí hodnota je true.  | Ne |
| usePeerVerification | Určuje, jestli pro ověření totožnosti serveru při připojení přes protokol SSL. Výchozí hodnota je true.  | Ne |

**Příklad:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datové sady](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností nepodporuje ServiceNow datovou sadu.

Ke zkopírování dat z ServiceNow, nastavte vlastnost typu datové sady, která **ServiceNowObject**. Není k dispozici žádné další vlastnosti specifické pro typ v tomto typu datové sady.

**Příklad**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje ServiceNow zdroje.

### <a name="servicenow-as-source"></a>ServiceNow jako zdroj

Ke zkopírování dat z ServiceNow, nastavte typ zdroje v aktivitě kopírování do **ServiceNowSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **ServiceNowSource** | Ano |
| query | Čtení dat pomocí vlastního dotazu SQL. Například: `"SELECT * FROM Actual.alm_asset"`. | Ano |

Při zadání schéma a sloupec pro ServiceNow v dotazu, pamatujte na tyhle:

- **Schéma:** zadejte schéma jako `Actual` nebo `Display` v ServiceNow dotazu, které můžete se podívat na ji jako parametr `sysparm_display_value` jako true nebo false při volání metody [rozhraní restful API ServiceNow](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Sloupec:** název sloupce, pro skutečné hodnoty v části `Actual` schéma je `[columne name]_value`, zatímco pro zobrazované hodnoty v části `Display` schéma je `[columne name]_display_value`. Poznámka: název sloupce potřebovat mapy schématu použitá v dotazu.

**Ukázkový dotaz:** 
 `SELECT col_value FROM Actual.alm_asset` nebo `SELECT col_display_value FROM Display.alm_asset`

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
