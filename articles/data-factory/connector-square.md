---
title: Kopírovat data z čtverce (Preview)
description: Naučte se, jak kopírovat data z čtverce do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.date: 08/01/2019
ms.openlocfilehash: ac968271685c66c8fab8d7723d994a446f49e85f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81410317"
---
# <a name="copy-data-from-square-using-azure-data-factory-preview"></a>Kopírování dat z čtverce pomocí Azure Data Factory (Náhled)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z čtverce. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

> [!IMPORTANT]
> Tento konektor je momentálně ve verzi Preview. Můžete si to vyzkoušet a sdělit nám svůj názor. Pokud do svého řešení chcete zavést závislost na konektorech ve verzi Preview, kontaktujte [podporu Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Podporované možnosti

Tato čtvercová spojnice je podporována pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)


Data z čtverce můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory poskytuje integrovaný ovladač pro povolení připojení, takže nemusíte ručně instalovat žádné ovladače pomocí tohoto konektoru.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factorych entit specifických pro čtvercovou spojnici.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro čtvercovou propojenou službu jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Vyžadováno |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **Square** . | Ano |
| host | Adresa URL čtvercové instance (tj. mystore.mysquare.com)  | Ano |
| clientId | ID klienta přidružené k vaší čtvercové aplikaci.  | Ano |
| clientSecret | Tajný kód klienta přidružený ke své čtvercové aplikaci. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| redirectUri | Adresa URL pro přesměrování přiřazená v čtverci řídicího panelu aplikace (tj. http: \/ /localhost: 2 500)  | Ano |
| useEncryptedEndpoints | Určuje, zda jsou koncové body zdroje dat šifrovány pomocí protokolu HTTPS. Výchozí hodnotou je hodnota true.  | Ne |
| useHostVerification | Určuje, jestli se má při připojování přes protokol TLS vyžadovat název hostitele v certifikátu serveru tak, aby odpovídal názvu hostitele serveru. Výchozí hodnotou je hodnota true.  | Ne |
| usePeerVerification | Určuje, jestli se má při připojování přes protokol TLS ověřit identita serveru. Výchozí hodnotou je hodnota true.  | Ne |

**Příklad:**

```json
{
    "name": "SquareLinkedService",
    "properties": {
        "type": "Square",
        "typeProperties": {
            "host" : "mystore.mysquare.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "redirectUri" : "http://localhost:2500"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . Tato část uvádí seznam vlastností, které jsou podporovány čtvercovou datovou sadou.

Chcete-li kopírovat data z čtverce, nastavte vlastnost Type datové sady na **SquareObject**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Vyžadováno |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **SquareObject** . | Ano |
| tableName | Název tabulky | Ne (Pokud je zadáno "dotaz" ve zdroji aktivity) |

**Příklad**

```json
{
    "name": "SquareDataset",
    "properties": {
        "type": "SquareObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Square linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných čtvercovým zdrojem.

### <a name="square-as-source"></a>Čtvercový as source

Chcete-li kopírovat data z čtverce, nastavte typ zdroje v aktivitě kopírování na **SquareSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Vyžadováno |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **SquareSource** . | Ano |
| query | Pro čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM Business"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSquare",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Square input dataset name>",
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
                "type": "SquareSource",
                "query": "SELECT * FROM Business"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
