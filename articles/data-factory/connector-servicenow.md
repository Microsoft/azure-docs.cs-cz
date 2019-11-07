---
title: Kopírování dat z ServiceNow pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z ServiceNow do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 933b12f852fcbcc20e50f3c89d597bbe6b84bd8e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680214"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Kopírování dat z ServiceNow pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z ServiceNow. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor ServiceNow je podporován pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data z ServiceNow můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, takže nemusíte ručně instalovat žádné ovladače pomocí tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor ServiceNow.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu ServiceNow jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **ServiceNow** . | Ano |
| endpoint | Koncový bod serveru ServiceNow (`http://<instance>.service-now.com`).  | Ano |
| authenticationType | Typ ověřování, který se má použít. <br/>Povolené hodnoty jsou: **Basic**, **OAuth2** | Ano |
| uživatelské jméno | Uživatelské jméno použité pro připojení k serveru ServiceNow pro ověřování Basic a OAuth2.  | Ano |
| heslo | Heslo odpovídající uživatelskému jménu pro základní a OAuth2 ověřování. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| clientId | ID klienta pro ověřování OAuth2  | Ne |
| clientSecret | Tajný klíč klienta pro ověřování OAuth2 Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| useEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovány pomocí protokolu HTTPS. Výchozí hodnota je true (pravda).  | Ne |
| useHostVerification | Určuje, jestli se má při připojování přes SSL vyžadovat, aby název hostitele v certifikátu serveru odpovídal názvu hostitele serveru. Výchozí hodnota je true (pravda).  | Ne |
| usePeerVerification | Určuje, jestli se má při připojování přes SSL ověřit identita serveru. Výchozí hodnota je true (pravda).  | Ne |

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

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **ServiceNowObject** . | Ano |
| tableName | Název tabulky | Ne (když je zadán zdroj aktivity "query") |

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

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **ServiceNowSource** . | Ano |
| query | Pro čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM Actual.alm_asset"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

Při zadávání schématu a sloupce pro ServiceNow v dotazu Pamatujte na následující **informace a podívejte se na [tipy k výkonu](#performance-tips) při kopírování výkonu**.

- **Schéma:** zadejte schéma jako `Actual` nebo `Display` v dotazu ServiceNow, který můžete při volání [rozhraní API RESTful ServiceNow](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET)použít jako parametr `sysparm_display_value` nebo false. 
- **Column (sloupec):** název sloupce pro skutečnou hodnotu pod hodnotou `Actual` schema je `[column name]_value`, zatímco hodnota display je `[column name]_display_value``Display` Schema. Všimněte si, že název sloupce musí být namapován na schéma používané v dotazu.

**Vzorový dotaz:** 
`SELECT col_value FROM Actual.alm_asset` nebo 
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

### <a name="schema-to-use"></a>Schéma, které se má použít

ServiceNow má 2 různá schémata, jedna je **"skutečnost"** , která vrací skutečná data, druhá je **"zobrazení"** , která vrací zobrazované hodnoty dat. 

Pokud máte v dotazu filtr, použijte "skutečné" schéma, které má vyšší výkon kopírování. Při dotazování na "skutečnost" schématu ServiceNow nativně podporuje filtr při načítání dat, aby vracela pouze filtrovaná sada výsledků dotazu, zatímco při dotazování schématu "zobrazení", ADF načte všechna data a aplikuje filtr interně.

### <a name="index"></a>Index

Index tabulky ServiceNow může přispět k lepšímu výkonu dotazů, a to v tématu [vytvoření indexu tabulky](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
