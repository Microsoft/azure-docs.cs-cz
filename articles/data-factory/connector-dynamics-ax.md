---
title: Kopírování dat z aplikace Dynamics AX pomocí služby Azure Data Factory (Preview) | Dokumentace Microsoftu
description: Zjistěte, jak ke zkopírování dat z aplikace Dynamics AX do úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: jingwang
ms.openlocfilehash: 48c6fb178b61fd65e0fbf6122d541bef5e3e9830
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014382"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory-preview"></a>Kopírování dat z aplikace Dynamics AX pomocí služby Azure Data Factory (Preview)

Tento článek popisuje, jak používat aktivitu kopírování, která ve službě Azure Data Factory pro kopírování dat ze zdroje aplikace Dynamics AX. Tento článek vychází [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md), který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z aplikace Dynamics AX do jakékoli podporovaného úložiště dat jímky. Seznam dat ukládá podporovanou aktivitou kopírování jako zdroje a jímky, najdete v části [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).

Konkrétně tento konektor Dynamics AX podporuje kopírování dat z aplikace Dynamics AX pomocí **protokolu OData** s **ověřování instančního objektu**.

>[!TIP]
>Tento konektor můžete také použít ke zkopírování dat z **Dynamics 365 Finance and Operations**. Přečtěte si Dynamics 365 [podporu protokolu OData](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) a [metodu ověřování](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které lze použít k definování entit služby Data Factory, které jsou specifické pro konektor Dynamics AX.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat ověřování instančních objektů, postupujte takto:

1. Zaregistrovat aplikaci entity ve službě Azure Active Directory (Azure AD) pomocí následujících [registrace aplikace pomocí tenanta služby Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Přejděte do aplikace Dynamics AX a udělte této služby hlavní správná oprávnění pro přístup k Dynamics AX.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro Dynamics AX propojené služby:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **aplikace Dynamics AX**. |Ano |
| url | Dynamics AX (nebo Dynamics 365 Finance and Operations) instance koncový bod OData. |Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Načtení podržením ukazatele myši v pravém horním rohu webu Azure portal. | Ano |
| aadResourceId | Zadejte prostředku AAD, které jste požádali o registraci. Například, pokud je vaše adresa URL Dynamics `https://sampledynamics.sandbox.operations.dynamics.com/data/`, odpovídající prostředek AAD je obvykle `https://sampledynamics.sandbox.operations.dynamics.com`. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) používat pro připojení k úložišti. Můžete vybrat prostředí Azure Integration Runtime nebo v místním prostředí Integration Runtime (Pokud je vaše úložiště dat se nachází v privátní síti). Pokud není zadán, použije se výchozí prostředí Azure Integration Runtime. |Ne |

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

Tato část obsahuje seznam vlastností, které podporuje datové sady Dynamics AX.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z Dynamics AX, nastavte **typ** vlastnosti datové sady na **DynamicsAXResource**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost datové sady **DynamicsAXResource**. | Ano |
| path | Cesta k entitě Dynamics AX OData. | Ano |

**Příklad**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typePoperties": {     
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Tato část obsahuje seznam vlastností, které podporuje zdroji Dynamics AX.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX jako zdroj

Chcete-li kopírovat data z Dynamics AX, nastavte **zdroj** typ v aktivitě kopírování do **DynamicsAXSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost zdroje aktivity kopírování **DynamicsAXSource**. | Ano |
| query | Možnosti dotazu OData pro filtrování dat Příklad: `"?$select=Name,Description&$top=5"`.<br/><br/>**Poznámka:** Konektor kopíruje data z adresy URL pro kombinované: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Další informace najdete v tématu [OData pro adresy URL komponenty](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Ne |

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

## <a name="next-steps"></a>Další postup

Seznam úložišť dat, která aktivitu kopírování, která podporuje jako zdroje a jímky ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md##supported-data-stores-and-formats).