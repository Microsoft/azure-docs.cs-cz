---
title: Kopírování dat z Dynamics AX
description: Naučte se, jak kopírovat data z Dynamics AX do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/12/2020
ms.openlocfilehash: 38ff77ad56f16fbd33b77021b18be77f6a153b3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380980"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Kopírování dat z Dynamics AX pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat ze zdroje Dynamics AX. Článek se vytvoří na [aktivitě kopírování v Azure Data Factory](copy-activity-overview.md), která představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Dynamics AX se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z Dynamics AX můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky, najdete v tématu [podporované úložiště a formáty dat](copy-activity-overview.md#supported-data-stores-and-formats).

Konkrétně tento konektor Dynamics AX podporuje kopírování dat z Dynamics AX pomocí **protokolu OData** s **ověřováním instančního objektu**.

>[!TIP]
>Pomocí tohoto konektoru můžete také kopírovat data z **Dynamics 365 finance a operace**. Podívejte se na podporu a [metodu ověřování](/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication) [OData](/dynamics365/unified-operations/dev-itpro/data-entities/odata) Dynamics 365.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které můžete použít k definování Data Factory entit, které jsou specifické pro konektor Dynamics AX.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít ověřování instančního objektu, postupujte takto:

1. Pomocí [Registrace aplikace v Tenantovi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)Zaregistrujte entitu aplikace v Azure Active Directory (Azure AD). Poznamenejte si následující hodnoty, které použijete k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Přejděte na Dynamics AX a udělte tomuto instančnímu objektu správná oprávnění pro přístup k aplikaci Dynamics AX.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Dynamics AX jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** musí být nastavená na **DynamicsAx**. |Yes |
| url | Koncový bod OData instance Dynamics AX (nebo finance a operace Dynamics 365). |Yes |
| servicePrincipalId | Zadejte ID klienta aplikace. | Yes |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** , abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Načtěte ho tak, že najedete myší v pravém horním rohu Azure Portal. | Yes |
| aadResourceId | Zadejte prostředek AAD, který požadujete pro autorizaci. Například pokud je adresa URL vašeho Dynamics `https://sampledynamics.sandbox.operations.dynamics.com/data/` , odpovídající prostředek AAD je obvykle `https://sampledynamics.sandbox.operations.dynamics.com` . | Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , který se má použít pro připojení k úložišti dat. Můžete zvolit Azure Integration Runtime nebo v místním prostředí Integration Runtime (Pokud se vaše úložiště dat nachází v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. |No |

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

V této části najdete seznam vlastností, které podporuje datová sada Dynamics AX.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, najdete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z Dynamics AX, nastavte vlastnost **Type** datové sady na **DynamicsAXResource**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** datové sady musí být nastavená na **DynamicsAXResource**. | Yes |
| program | Cesta k entitě Dynamics AX OData | Yes |

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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

V této části najdete seznam vlastností, které podporuje zdroj Dynamics AX.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX jako zdroj

Chcete-li kopírovat data z Dynamics AX, nastavte typ **zdroje** v aktivitě kopírování na **DynamicsAXSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **DynamicsAXSource**. | Yes |
| query | Možnosti dotazu OData pro filtrování dat Příklad: `"?$select=Name,Description&$top=5"`.<br/><br/>**Poznámka**: konektor kopíruje data z kombinované adresy URL: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]` . Další informace najdete v tématu [komponenty adresy URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | No |
| httpRequestTimeout | Časový limit (hodnota **TimeSpan** ) požadavku HTTP získat odpověď. Tato hodnota představuje časový limit pro získání odpovědi, nikoli časový limit pro čtení dat odpovědi. Pokud není zadaný, výchozí hodnota je **00:30:00** (30 minut). | No |

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


## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky v Azure Data Factory, najdete v tématu [podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).