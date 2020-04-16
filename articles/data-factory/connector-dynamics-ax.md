---
title: Kopírování dat z aplikace Dynamics AX
description: Zjistěte, jak kopírovat data z Dynamics AX do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: 4dd82eea0a80ef81a0f972d1964a62e6c17a80c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417372"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Kopírování dat z Dynamics AX pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí kopírování aktivity v Azure Data Factory ke kopírování dat ze zdroje Dynamics AX. Článek vychází [z aktivity kopírování v Azure Data Factory](copy-activity-overview.md), která představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Dynamics AX je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z aplikace Dynamics AX můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která podporují aktivita kopírování jako zdroje a propady, naleznete v [tématu Podporovaná úložiště a formáty dat](copy-activity-overview.md#supported-data-stores-and-formats).

Konkrétně tento konektor Dynamics AX podporuje kopírování dat z aplikace Dynamics AX pomocí **protokolu OData** s **ověřováním hlavního povinného použití**.

>[!TIP]
>Tento konektor můžete také použít ke kopírování dat z **aplikace Dynamics 365 Finance and Operations**. Podívejte se na [metodu](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication)podpory a ověřování [OData](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) aplikace Dynamics 365 .

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které můžete použít k definování entit Factory dat, které jsou specifické pro konektor Dynamics AX.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít ověřování instančního objektu, postupujte takto:

1. Zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) podle [registrace aplikace u klienta Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Poznamenejte si následující hodnoty, které používáte k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Přejděte na dynamics AX a udělte tomuto objektu zabezpečení služby vlastní oprávnění pro přístup k dynamice AX.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Dynamics AX jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** musí být nastavena na **DynamicsAX**. |Ano |
| url | Koncový bod OData instance Dynamics AX (nebo Dynamics 365 Finance and Operations). |Ano |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| Nájemce | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Načtěte ji najetím myší v pravém horním rohu portálu Azure. | Ano |
| aadResourceId | Zadejte prostředek AAD, který požadujete pro autorizaci. Pokud je například adresa `https://sampledynamics.sandbox.operations.dynamics.com/data/`URL aplikace Dynamics , `https://sampledynamics.sandbox.operations.dynamics.com`je obvykle odpovídající prostředek ad . | Ano |
| connectVia | [Prostředí Integrace Runtime](concepts-integration-runtime.md) pro připojení k úložišti dat. Můžete zvolit Azure Integration Runtime nebo prostředí Integration Runtime s vlastním hostitelem (pokud je vaše úložiště dat umístěné v privátní síti). Pokud není zadán, použije se výchozí prostředí Azure Integration Runtime. |Ne |

**Příklad**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Tato část obsahuje seznam vlastností, které podporuje datová sada Dynamics AX.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu Datové sady a propojené služby](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z aplikace Dynamics AX, nastavte vlastnost **type** datové sady na **DynamicsAXResource**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** datové sady musí být nastavena na **DynamicsAXResource**. | Ano |
| cesta | Cesta k entitě Dynamics AX OData. | Ano |

**Příklad**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopírovat vlastnosti aktivity

Tato část obsahuje seznam vlastností, které podporuje zdroj Dynamics AX.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v [tématu Potrubí](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX jako zdroj

Chcete-li kopírovat data z aplikace Dynamics AX, nastavte typ **zdroje** v části Aktivita kopírování na **DynamicsAXSource**. V části Zdroj **kopírování aktivity** jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** zdroje Aktivity kopírování musí být nastavena na **DynamicsAXSource**. | Ano |
| query | Možnosti dotazu OData pro filtrování dat. Příklad: `"?$select=Name,Description&$top=5"`.<br/><br/>**Poznámka:** Konektor zkopíruje data z `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`kombinované adresy URL: . Další informace naleznete v tématu [Součásti adresy URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Ne |

**Příklad**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
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

Seznam úložišť dat, která podporují aktivity kopírování jako zdroje a propady v Azure Data Factory, najdete v [tématu Podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
