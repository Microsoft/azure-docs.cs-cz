---
title: Kopírování dat ze zdrojů OData pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data ze zdrojů OData do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: c2fe6b6cc7b52dda9f2beffa444f1965723ea92a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416930"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Kopírování dat ze zdroje OData pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-odata-connector.md)
> * [Aktuální verze](connector-odata.md)

Tento článek popisuje, jak pomocí kopírovat aktivity v Azure Data Factory ke kopírování dat ze zdroje OData. Článek vychází [z aktivity kopírování v Azure Data Factory](copy-activity-overview.md), která představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor OData je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data ze zdroje OData můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která podporují aktivita kopírování jako zdroje a propady, naleznete v [tématu Podporovaná úložiště a formáty dat](copy-activity-overview.md#supported-data-stores-and-formats).

Konkrétně tento konektor OData podporuje:

- OData verze 3.0 a 4.0.
- Kopírování dat pomocí jednoho z následujících ověřování: **Anonymní**, **Základní**, **Windows**a **AAD instanční objekt**.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které můžete použít k definování entit Data Factory, které jsou specifické pro konektor OData.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu OData jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** musí být nastavena na **OData**. |Ano |
| url | Kořenová adresa URL služby OData. |Ano |
| authenticationType | Typ ověřování používaný pro připojení ke zdroji OData. Povolené hodnoty jsou **Anonymous**, **Basic**, **Windows**a **AadServicePrincipal**. OAuth založený na uživateli není podporován. | Ano |
| userName | Pokud používáte základní ověřování nebo ověřování systému Windows, zadejte uživatelské **jméno.** | Ne |
| heslo | Zadejte **heslo** pro uživatelský účet, který jste zadali pro **userName**. Označte toto pole jako typ **SecureString** pro bezpečné uložení v datové továrně. Můžete také [odkazovat na tajný klíč uložený v úložišti klíčů Azure](store-credentials-in-key-vault.md). | Ne |
| servicePrincipalId | Zadejte ID klienta aplikace Azure Active Directory. | Ne |
| aadServicePrincipalCredentialType | Zadejte typ pověření, který se má použít pro ověřování instančního objektu. Povolené hodnoty `ServicePrincipalKey` jsou: nebo `ServicePrincipalCert`. | Ne |
| servicePrincipalKey | Zadejte klíč aplikace Azure Active Directory. Označte toto pole jako **SecureString** bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |
| servicePrincipalEmbeddedCert | Zadejte kódovaný certifikát base64 vaší aplikace registrované ve službě Azure Active Directory. Označte toto pole jako **SecureString** bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |
| servicePrincipalEmbeddedCertPassword | Zadejte heslo certifikátu, pokud je certifikát zabezpečen heslem. Označte toto pole jako **SecureString** bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md).  | Ne|
| Nájemce | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Načtěte ji najetím myší v pravém horním rohu portálu Azure. | Ne |
| aadResourceId | Zadejte prostředek AAD, který požadujete pro autorizaci.| Ne |
| connectVia | [Prostředí Integrace Runtime](concepts-integration-runtime.md) pro připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, použije se výchozí prostředí Azure Integration Runtime. |Ne |

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

**Příklad 4: Použití ověřování primárního klíče služby**

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

**Příklad 5: Použití ověřování certifikátu instančního objektu**

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

Tato část obsahuje seznam vlastností, které podporuje datová sada OData.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu Datové sady a propojené služby](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z OData, nastavte vlastnost **type** datové sady na **ODataResource**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** datové sady musí být nastavena na **ODataResource**. | Ano |
| cesta | Cesta k prostředku OData. | Ano |

**Příklad**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
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

## <a name="copy-activity-properties"></a>Kopírovat vlastnosti aktivity

Tato část obsahuje seznam vlastností, které podporuje zdroj OData.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v [tématu Potrubí](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData jako zdroj

Chcete-li kopírovat data z OData, jsou podporovány následující vlastnosti v části **Zdroj aktivity** kopírování:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** zdroje Aktivity kopírování musí být nastavena na **ODataSource**. | Ano |
| query | Možnosti dotazu OData pro filtrování dat. Příklad: `"$select=Name,Description&$top=5"`.<br/><br/>**Poznámka:** Konektor OData kopíruje data `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`z kombinované adresy URL: . Další informace naleznete v tématu [Součásti adresy URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Ne |

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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Pokud jste `RelationalSource` používali zadaný zdroj, je stále podporován jako-je, zatímco jste navrhl použít nový do budoucna.

## <a name="data-type-mapping-for-odata"></a>Mapování datového typu pro OData

Při kopírování dat z OData se používají následující mapování mezi datovými typy OData a dočasnými datovými typy Azure Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, naleznete v [tématu Schémata a mapování datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ OData | Dočasný datový typ datové továrny |
|:--- |:--- |
| Edm.Binary | Bajt[] |
| Edm.Boolean | Logická hodnota |
| Edm.Byte | Bajt[] |
| Edm.DateČas | DateTime |
| Edm.Desetinné místo | Desetinné číslo |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Identifikátor GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Řetězec |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Datové typy OData (například **Object)** nejsou podporovány.


## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která podporují aktivity kopírování jako zdroje a propady v Azure Data Factory, najdete v [tématu Podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).