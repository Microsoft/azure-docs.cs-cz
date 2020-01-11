---
title: Kopírování dat z Dynamics AX
description: Naučte se, jak kopírovat data z Dynamics AX do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.openlocfilehash: 4acad5e2de55211b6c4492513f331c36286ed852
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892783"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Kopírování dat z Dynamics AX pomocí Azure Data Factory

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat ze zdroje Dynamics AX. Tento článek vychází [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md), který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Dynamics AX se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data z Dynamics AX můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam dat ukládá podporovanou aktivitou kopírování jako zdroje a jímky, najdete v části [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).

Konkrétně tento konektor Dynamics AX podporuje kopírování dat z Dynamics AX pomocí **protokolu OData** s **ověřováním instančního objektu**.

>[!TIP]
>Pomocí tohoto konektoru můžete také kopírovat data z **Dynamics 365 finance a operace**. Podívejte se na podporu a [metodu ověřování](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication) [OData](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) Dynamics 365.

## <a name="get-started"></a>Začít

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které můžete použít k definování Data Factory entit, které jsou specifické pro konektor Dynamics AX.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat ověřování instančních objektů, postupujte takto:

1. Zaregistrovat aplikaci entity ve službě Azure Active Directory (Azure AD) pomocí následujících [registrace aplikace pomocí tenanta služby Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Přejděte na Dynamics AX a udělte tomuto instančnímu objektu správná oprávnění pro přístup k aplikaci Dynamics AX.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Dynamics AX jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** musí být nastavená na **DynamicsAx**. |Ano |
| url | Koncový bod OData instance Dynamics AX (nebo finance a operace Dynamics 365). |Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Načtení podržením ukazatele myši v pravém horním rohu webu Azure portal. | Ano |
| aadResourceId | Zadejte prostředek AAD, který požadujete pro autorizaci. Pokud je například adresa URL vašeho Dynamics `https://sampledynamics.sandbox.operations.dynamics.com/data/`, je obvykle `https://sampledynamics.sandbox.operations.dynamics.com`odpovídající prostředek AAD. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) používat pro připojení k úložišti. Můžete zvolit Azure Integration Runtime nebo v místním prostředí Integration Runtime (Pokud se vaše úložiště dat nachází v privátní síti). Pokud není zadán, použije se výchozí prostředí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z Dynamics AX, nastavte vlastnost **Type** datové sady na **DynamicsAXResource**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** datové sady musí být nastavená na **DynamicsAXResource**. | Ano |
| Cesta | Cesta k entitě Dynamics AX OData | Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX jako zdroj

Chcete-li kopírovat data z Dynamics AX, nastavte typ **zdroje** v aktivitě kopírování na **DynamicsAXSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **DynamicsAXSource**. | Ano |
| query | Možnosti dotazu OData pro filtrování dat Příklad: `"?$select=Name,Description&$top=5"`.<br/><br/>**Poznámka**: konektor kopíruje data z kombinované adresy URL: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Další informace najdete v tématu [komponenty adresy URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Ne |

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

Seznam úložišť dat, která aktivitu kopírování, která podporuje jako zdroje a jímky ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
