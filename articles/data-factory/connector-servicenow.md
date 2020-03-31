---
title: Kopírování dat ze služby ServiceNow
description: Zjistěte, jak zkopírovat data z ServiceNow do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: dabcc5afe4a092e4919c854071a698c6e6ebf0b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926168"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Kopírování dat ze služby ServiceNow pomocí Azure Data Factory

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z ServiceNow. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor ServiceNow je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data z ServiceNow do libovolného úložiště dat podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, proto není nutné ručně instalovat žádný ovladač pomocí tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro konektor ServiceNow.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu ServiceNow jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **ServiceNow** | Ano |
| endpoint | Koncový bod serveru ServiceNow`http://<instance>.service-now.com`( ).  | Ano |
| authenticationType | Typ ověřování, který chcete použít. <br/>Povolené hodnoty jsou: **Základní**, **OAuth2** | Ano |
| uživatelské jméno | Uživatelské jméno používané pro připojení k serveru ServiceNow pro základní ověřování a ověřování OAuth2.  | Ano |
| heslo | Heslo odpovídající uživatelskému jménu pro základní a oauth2 ověřování. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| clientId | ID klienta pro ověřování OAuth2.  | Ne |
| clientSecret | Tajný klíč klienta pro ověřování OAuth2. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |
| použitíEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovány pomocí protokolu HTTPS. Výchozí hodnotou je hodnota true.  | Ne |
| useHostVerification | Určuje, zda má být při připojování přes SSL vyžadován název hostitele v certifikátu serveru. Výchozí hodnotou je hodnota true.  | Ne |
| usePeerVerification | Určuje, zda se má ověřit identita serveru při připojování přes SSL. Výchozí hodnotou je hodnota true.  | Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou ServiceNow.

Chcete-li kopírovat data z ServiceNow, nastavte vlastnost type datové sady na **ServiceNowObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **ServiceNowObject.** | Ano |
| tableName | Název tabulky. | Ne (pokud je zadán "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem ServiceNow.

### <a name="servicenow-as-source"></a>ServiceNow jako zdroj

Chcete-li kopírovat data z ServiceNow, nastavte typ zdroje v aktivitě kopírování na **ServiceNowSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: ServiceNowSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM Actual.alm_asset"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

Všimněte si následující při zadávání schématu a sloupce servicenow v dotazu a **naleznete v tipy pro výkon [výkonu](#performance-tips) kopírování důsledky**.

- **Schéma:** zadejte `Actual` schéma jako `Display` nebo v servicenow dotazu, který můžete podívat na `sysparm_display_value` to jako parametr jako true nebo false při volání [ServiceNow restful ROZHRANÍ API](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Sloupec:** název sloupce pro `Actual` skutečnou hodnotu `[column name]_value`ve schématu `Display` je , zatímco `[column name]_display_value`pro zobrazovanou hodnotu pod schématem je . Všimněte si, že název sloupce musí být mapován na schéma používané v dotazu.

**Ukázkový dotaz:** 
 `SELECT col_value FROM Actual.alm_asset` NEBO 
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

### <a name="schema-to-use"></a>Schéma, které má být

ServiceNow má 2 různé schémata, jeden je **"Skutečné",** který vrací skutečná data, druhý je **"Display",** který vrací zobrazované hodnoty dat. 

Pokud máte filtr v dotazu, použijte "Skutečné" schéma, které má lepší výkon kopírování. Při dotazování proti "Skutečné" schéma ServiceNow nativně podporují filtr při načítání dat pouze vrátit filtrované resultset, zatímco při dotazování "Zobrazit" schéma, ADF načíst všechna data a použít filtr interně.

### <a name="index"></a>Index

ServiceNow tabulka index může pomoci zlepšit výkon dotazu, viz [Vytvoření indexu tabulky](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
