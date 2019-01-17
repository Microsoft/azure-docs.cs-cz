---
title: Kopírování dat z ServiceNow pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z ServiceNow úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 234b78a97c2663121d0d585154695887a58b9522
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351739"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Kopírování dat z ServiceNow pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z ServiceNow. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z ServiceNow do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Poskytuje integrované ovladače chcete umožnit připojení k Azure Data Factory, proto není nutné ručně nainstalovat všechny ovladače používání tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor ServiceNow.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro ServiceNow propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **ServiceNow** | Ano |
| endpoint | Koncový bod serveru ServiceNow (`http://<instance>.service-now.com`).  | Ano |
| authenticationType. | Typ ověřování, který se má použít. <br/>Povolené hodnoty jsou: **Basic**, **OAuth2** | Ano |
| uživatelské jméno | Uživatelské jméno pro připojení k ServiceNow server pro ověřování Basic a OAuth2.  | Ano |
| heslo | Uživatelské jméno pro ověřování OAuth2 pomocí Basic a odpovídající heslo. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| ID klienta | ID klienta pro ověřování OAuth2.  | Ne |
| ClientSecret | Tajný kód klienta pro ověřování OAuth2. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| useEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovat pomocí protokolu HTTPS. Výchozí hodnota je true.  | Ne |
| useHostVerification | Určuje, jestli se vyžaduje název hostitele v certifikátu serveru tak, aby odpovídaly názvu hostitele serveru při připojení přes protokol SSL. Výchozí hodnota je true.  | Ne |
| usePeerVerification | Určuje, jestli se má ověřit identitu serveru při připojení přes protokol SSL. Výchozí hodnota je true.  | Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje ServiceNow datové sady.

Ke zkopírování dat z ServiceNow, nastavte vlastnost typ datové sady na **ServiceNowObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **ServiceNowObject** | Ano |
| tableName | Název tabulky. | Ne (když je zadán zdroj aktivity "dotaz") |

**Příklad**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem ServiceNow.

### <a name="servicenow-as-source"></a>ServiceNow jako zdroj

Ke zkopírování dat z ServiceNow, nastavte typ zdroje v aktivitě kopírování do **ServiceNowSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **ServiceNowSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM Actual.alm_asset"`. | Ne (když je "tableName" v datové sadě zadán) |

Při zadání schématu ve sloupci pro ServiceNow v dotazu, pamatujte na Tyhle a **najdete [tipy ke zvýšení výkonu](#performance-tips) na dopad na výkon kopírování**.

- **Schéma:** zadejte schéma jako `Actual` nebo `Display` v dotazu, ServiceNow, které můžete na něj podíváte jako parametr `sysparm_display_value` jako true nebo false, při volání metody [rozhraní restful API ServiceNow](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Sloupec:** název sloupce pro skutečné hodnoty v rámci `Actual` schéma je `[column name]_value`, zatímco pro zobrazení hodnoty v rámci `Display` schéma je `[column name]_display_value`. Poznámka: název sloupce musí mapování schématu je použitý v dotazu.

**Ukázkový dotaz:**
`SELECT col_value FROM Actual.alm_asset` OR 
`SELECT col_display_value FROM Display.alm_asset`

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
## <a name="performance-tips"></a>Tipy pro zvýšení výkonu

### <a name="schema-to-use"></a>Schématu se má použít

ServiceNow má 2 různých schémat, jeden je **"Skutečný"** vracející skutečná data, druhá **"Zobrazit"** vracející zobrazení hodnoty data. 

Pokud máte v dotazu filtr, pomocí schématu "Skutečný", která má lepší výkon kopírování. Při dotazování na "Skutečný" schéma, ServiceNow nativně podporují filtr při načítání dat do vrátit pouze filtrovaná sada výsledků, že při dotazování na schéma "Display", ADF načíst všechna data a použít filtr interně.

### <a name="index"></a>Index

ServiceNow tabulka indexu může pomoct zlepšit výkon dotazů, přečtěte si [vytvořit index tabulky](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
