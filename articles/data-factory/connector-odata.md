---
title: Kopírování dat ze zdrojů OData pomocí Azure Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data ze zdrojů OData do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 30bad3dd519d622d7e224da7bd53e7c6625014f6
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966476"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Kopírování dat ze zdroje OData pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-odata-connector.md)
> * [Aktuální verze](connector-odata.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data ze zdroje OData. Tento článek vychází [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md), který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Data ze zdroje OData můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam dat ukládá podporovanou aktivitou kopírování jako zdroje a jímky, najdete v části [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).

Konkrétně tento konektor OData podporuje:

- OData verze 3,0 a 4,0.
- Kopírování dat pomocí jednoho z následujících ověřování: **Anonymní**, **základní**, **Windows**, **instanční objekt služby AAD**a **spravované identity pro prostředky Azure**.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které můžete použít k definování Data Factory entit, které jsou specifické pro konektor OData.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu OData jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **type** musí být vlastnost nastavena na **OData**. |Ano |
| url | Kořenová adresa URL služby OData |Ano |
| authenticationType | Typ ověřování, který se používá pro připojení ke zdroji OData. Povolené hodnoty jsou **anonymní**, **základní**, **Windows**, **AadServicePrincipal**a **ManagedServiceIdentity**. OAuth založené na uživateli se nepodporuje. | Ano |
| userName | Zadejte **userName** Pokud používáte ověřování Basic nebo Windows. | Ne |
| password | Zadejte **password** uživatele účtu, který jste zadali pro **uživatelské jméno**. Označte toto pole jako **SecureString** typ bezpečně uložit ve službě Data Factory. Můžete také [vytvořit odkaz na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| servicePrincipalId | Zadejte ID klienta Azure Active Directory aplikace. | Ne |
| aadServicePrincipalCredentialType | Zadejte typ přihlašovacích údajů, který se použije pro ověřování instančního objektu. Povolené hodnoty jsou: `ServicePrincipalKey` nebo `ServicePrincipalCert`. | Ne |
| servicePrincipalKey | Zadejte klíč Azure Active Directory aplikace. Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| servicePrincipalEmbeddedCert | Zadejte certifikát kódovaný v kódování Base64 vaší aplikace zaregistrovaný v Azure Active Directory. Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| servicePrincipalEmbeddedCertPassword | Zadejte heslo certifikátu, pokud je certifikát zabezpečený heslem. Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md).  | Ne|
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Načtení podržením ukazatele myši v pravém horním rohu webu Azure portal. | Ne |
| aadResourceId | Zadejte prostředek AAD, který požadujete pro autorizaci.| Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) používat pro připojení k úložišti. Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadán, použije se výchozí prostředí Azure Integration Runtime. |Ne |

**Příklad 1: Použití anonymního ověřování**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 2: Použití základního ověřování**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 3: Použití ověřování systému Windows**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 4: Použití ověřování pomocí klíče instančního objektu**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Příklad 5: Ověřování pomocí certifikátu instančního objektu**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

V této části najdete seznam vlastností, které datová sada OData podporuje.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z OData, nastavte vlastnost **Type** datové sady na **ODataResource**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** datové sady musí být nastavená na **ODataResource**. | Ano |
| path | Cesta k prostředku OData | Ano |

**Příklad**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

V této části najdete seznam vlastností, které podporuje zdroj OData.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData as source

Chcete-li kopírovat data z OData, nastavte typ **zdroje** v aktivitě kopírování na **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **RelationalSource**. | Ano |
| query | Možnosti dotazu OData pro filtrování dat Příklad: `"$select=Name,Description&$top=5"`.<br/><br/>**Poznámka:** Konektor OData kopíruje data z kombinované adresy URL: `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`. Další informace najdete v tématu [komponenty adresy URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Ne |

**Příklad**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "RelationalSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-odata"></a>Mapování datového typu pro OData

Při kopírování dat z OData se používají následující mapování mezi datovými typy OData a Azure Data Factory pomocnými datovými typy. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ OData | Data Factory dočasné datový typ |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| EDM. Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| EDM. time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Komplexní datové typy OData (například **Object**) se nepodporují.


## <a name="next-steps"></a>Další postup

Seznam úložišť dat, která aktivitu kopírování, která podporuje jako zdroje a jímky ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md##supported-data-stores-and-formats).