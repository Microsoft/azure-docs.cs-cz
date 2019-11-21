---
title: Copy data from Dynamics AX
description: Learn how to copy data from Dynamics AX to supported sink data stores by using a copy activity in an Azure Data Factory pipeline.
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
ms.openlocfilehash: 555293492a37b9c635cb11087c08d52aa4b4d8c4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218629"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Copy data from Dynamics AX by using Azure Data Factory

This article outlines how to use Copy Activity in Azure Data Factory to copy data from Dynamics AX source. The article builds on [Copy Activity in Azure Data Factory](copy-activity-overview.md), which presents a general overview of Copy Activity.

## <a name="supported-capabilities"></a>Supported capabilities

This Dynamics AX connector is supported for the following activities:

- [Copy activity](copy-activity-overview.md) with [supported source/sink matrix](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

You can copy data from Dynamics AX to any supported sink data store. For a list of data stores that Copy Activity supports as sources and sinks, see [Supported data stores and formats](copy-activity-overview.md#supported-data-stores-and-formats).

Specifically, this Dynamics AX connector supports copying data from Dynamics AX using **OData protocol** with **Service Principal authentication**.

>[!TIP]
>You can also use this connector to copy data from **Dynamics 365 Finance and Operations**. Refer to Dynamics 365's [OData support](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) and [Authentication method](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication).

## <a name="get-started"></a>Začít

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

The following sections provide details about properties you can use to define Data Factory entities that are specific to Dynamics AX connector.

## <a name="prerequisites"></a>Předpoklady

To use service principal authentication, follow these steps:

1. Register an application entity in Azure Active Directory (Azure AD) by following [Register your application with an Azure AD tenant](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Make note of the following values, which you use to define the linked service:

    - ID aplikace
    - Application key
    - ID tenanta

2. Go to Dynamics AX, and grant this service principal proper permission to access your Dynamics AX.

## <a name="linked-service-properties"></a>Linked service properties

The following properties are supported for Dynamics AX linked service:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | The **type** property must be set to **DynamicsAX**. |Ano |
| url | The Dynamics AX (or Dynamics 365 Finance and Operations) instance OData endpoint. |Ano |
| servicePrincipalId | Specify the application's client ID. | Ano |
| servicePrincipalKey | Specify the application's key. Mark this field as a **SecureString** to store it securely in Data Factory, or [reference a secret stored in Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| tenant | Specify the tenant information (domain name or tenant ID) under which your application resides. Retrieve it by hovering the mouse in the top-right corner of the Azure portal. | Ano |
| aadResourceId | Specify the AAD resource you are requesting for authorization. For example, if your Dynamics URL is `https://sampledynamics.sandbox.operations.dynamics.com/data/`, the corresponding AAD resource is usually `https://sampledynamics.sandbox.operations.dynamics.com`. | Ano |
| connectVia | The [Integration Runtime](concepts-integration-runtime.md) to use to connect to the data store. You can choose Azure Integration Runtime or a self-hosted Integration Runtime (if your data store is located in a private network). If not specified, the default Azure Integration Runtime is used. |Ne |

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

## <a name="dataset-properties"></a>Dataset properties

This section provides a list of properties that the Dynamics AX dataset supports.

For a full list of sections and properties that are available for defining datasets, see [Datasets and linked services](concepts-datasets-linked-services.md). 

To copy data from Dynamics AX, set the **type** property of the dataset to **DynamicsAXResource**. The following properties are supported:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | The **type** property of the dataset must be set to **DynamicsAXResource**. | Ano |
| path | The path to the Dynamics AX OData entity. | Ano |

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

## <a name="copy-activity-properties"></a>Copy Activity properties

This section provides a list of properties that the Dynamics AX source supports.

For a full list of sections and properties that are available for defining activities, see [Pipelines](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX as source

To copy data from Dynamics AX, set the **source** type in Copy Activity to **DynamicsAXSource**. The following properties are supported in the Copy Activity **source** section:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | The **type** property of the Copy Activity source must be set to **DynamicsAXSource**. | Ano |
| query | OData query options for filtering data. Example: `"?$select=Name,Description&$top=5"`.<br/><br/>**Note**: The connector copies data from the combined URL: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. For more information, see [OData URL components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Ne |

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


## <a name="lookup-activity-properties"></a>Lookup activity properties

To learn details about the properties, check [Lookup activity](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky

For a list of data stores that Copy Activity supports as sources and sinks in Azure Data Factory, see [Supported data stores and formats](copy-activity-overview.md##supported-data-stores-and-formats).
