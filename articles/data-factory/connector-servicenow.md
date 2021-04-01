---
title: Kopírovat data z ServiceNow
description: Naučte se, jak kopírovat data z ServiceNow do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 4e7ebc422a9fd8503c5a3b004e1d06cb5ebfb987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378447"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Kopírování dat z ServiceNow pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z ServiceNow. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor ServiceNow je podporován pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z ServiceNow můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, takže nemusíte ručně instalovat žádné ovladače pomocí tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor ServiceNow.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu ServiceNow jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **ServiceNow** . | Yes |
| endpoint | Koncový bod serveru ServiceNow ( `http://<instance>.service-now.com` ).  | Yes |
| authenticationType | Typ ověřování, který se má použít. <br/>Povolené hodnoty jsou: **Basic**, **OAuth2** | Yes |
| username | Uživatelské jméno použité pro připojení k serveru ServiceNow pro ověřování Basic a OAuth2.  | Yes |
| heslo | Heslo odpovídající uživatelskému jménu pro základní a OAuth2 ověřování. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| clientId | ID klienta pro ověřování OAuth2  | No |
| clientSecret | Tajný klíč klienta pro ověřování OAuth2 Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | No |
| useEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovány pomocí protokolu HTTPS. Výchozí hodnotou je hodnota true.  | No |
| useHostVerification | Určuje, jestli se má při připojování přes protokol TLS vyžadovat název hostitele v certifikátu serveru tak, aby odpovídal názvu hostitele serveru. Výchozí hodnotou je hodnota true.  | No |
| usePeerVerification | Určuje, jestli se má při připojování přes protokol TLS ověřit identita serveru. Výchozí hodnotou je hodnota true.  | No |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných ServiceNow DataSet.

Chcete-li kopírovat data z ServiceNow, nastavte vlastnost Type datové sady na **ServiceNowObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **ServiceNowObject** . | Yes |
| tableName | Název tabulky | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných ServiceNow zdrojem.

### <a name="servicenow-as-source"></a>ServiceNow as source

Chcete-li kopírovat data z ServiceNow, nastavte typ zdroje v aktivitě kopírování na **ServiceNowSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **ServiceNowSource** . | Yes |
| query | Pro čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM Actual.alm_asset"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

Při zadávání schématu a sloupce pro ServiceNow v dotazu Pamatujte na následující **informace a podívejte se na [tipy k výkonu](#performance-tips) při kopírování výkonu**.

- **Schéma:** zadejte schéma jako `Actual` nebo `Display` v dotazu ServiceNow, který můžete `sysparm_display_value` při volání [rozhraní API RESTful ServiceNow](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET)použít jako parametr jako true nebo false. 
- **Column (sloupec):** název sloupce pro skutečnou hodnotu ve `Actual` schématu je `[column name]_value` , zatímco pro zobrazovanou hodnotu v části `Display` schéma je `[column name]_display_value` . Všimněte si, že název sloupce musí být namapován na schéma používané v dotazu.

**Vzorový dotaz:** 
 `SELECT col_value FROM Actual.alm_asset` ANI`SELECT col_display_value FROM Display.alm_asset`

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

### <a name="schema-to-use"></a>Schéma, které se má použít

ServiceNow má 2 různá schémata, jedna je **"skutečnost"** , která vrací skutečná data, druhá je **"zobrazení"** , která vrací zobrazované hodnoty dat. 

Pokud máte v dotazu filtr, použijte "skutečné" schéma, které má vyšší výkon kopírování. Při dotazování na "skutečnost" schématu ServiceNow nativně podporuje filtr při načítání dat, aby vracela pouze filtrovaná sada výsledků dotazu, zatímco při dotazování schématu "zobrazení", ADF načte všechna data a aplikuje filtr interně.

### <a name="index"></a>Index

Index tabulky ServiceNow může přispět k lepšímu výkonu dotazů, a to v tématu [vytvoření indexu tabulky](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
