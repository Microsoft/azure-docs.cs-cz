---
title: Kopírování dat ze zdroje OData s použitím služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data ze zdroje OData do úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
ms.openlocfilehash: c8bee6902fb74cb77c34395fd05c1c861b4f630e
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166130"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Kopírování dat ze zdroje OData pomocí služby Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-odata-connector.md)
> * [Aktuální verze](connector-odata.md)

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat ze zdroje OData ve službě Azure Data Factory. Tento článek vychází [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md), který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírovat data ze zdroje OData k jakékoli podporovaného úložiště dat jímky. Seznam dat ukládá podporovanou aktivitou kopírování jako zdroje a jímky, najdete v části [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).

Konkrétně tento konektor OData podporuje:

- OData verze 3.0 a 4.0.
- Kopírování dat pomocí jedné z následujících ověření: **anonymní**, **základní**, nebo **Windows**.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které lze použít k definování entit služby Data Factory, které jsou specifické pro konektor služby OData.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro služby OData propojené se podporují následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **OData**. |Ano |
| url | Kořenovou adresu URL služby OData. |Ano |
| authenticationType. | Typ ověřování používaný pro připojení ke zdroji OData. Povolené hodnoty jsou **anonymní**, **základní**, a **Windows**. OAuth se nepodporuje. | Ano |
| uživatelské jméno | Zadejte **uživatelské jméno** Pokud používáte ověřování Basic nebo Windows. | Ne |
| heslo | Zadejte **heslo** uživatele účtu, který jste zadali pro **uživatelské jméno**. Označte toto pole jako **SecureString** typ bezpečně uložit ve službě Data Factory. Můžete také [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) používat pro připojení k úložišti. Můžete vybrat prostředí Azure Integration Runtime nebo v místním prostředí Integration Runtime (Pokud je vaše úložiště dat se nachází v privátní síti). Pokud není zadán, použije se výchozí prostředí Azure Integration Runtime. |Ne |

**Příklad 1: Použití anonymní ověřování**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
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

**Příklad 3: Použití ověřování Windows**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source>",
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

## <a name="dataset-properties"></a>Vlastnosti datové sady

Tato část obsahuje seznam vlastností, které podporuje datovou sadu OData.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z prostředí OData, nastavte **typ** vlastnosti datové sady na **ODataResource**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost datové sady **ODataResource**. | Ano |
| path | Cesta k prostředku OData. | Ano |

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

Tato část obsahuje seznam vlastností, které podporuje zdroj OData.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData jako zdroj

Chcete-li kopírovat data z prostředí OData, nastavte **zdroj** typ v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost zdroje aktivity kopírování **RelationalSource**. | Ano |
| query | Možnosti dotazu OData pro filtrování dat Příklad: `"?$select=Name,Description&$top=5"`.<br/><br/>**Poznámka:**: konektor The OData kopíruje data z adresy URL pro kombinované: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Další informace najdete v tématu [OData pro adresy URL komponenty](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Ne |

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
                "query": "?$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-odata"></a>Datový typ mapování pro protokol OData

Při kopírování dat z protokolu OData, se používají následující mapování mezi Azure Data Factory dočasné datové typy a typy dat OData. Informace o tom, jak aktivitu kopírování, která mapuje typ zdroje schéma a data jímky, najdete v článku [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

| Typ dat OData | Data Factory dočasné datový typ |
|:--- |:--- |
| Edm.Binary | Byte] |
| Edm.Boolean | BOOL |
| Edm.Byte | Byte] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Jednoduchá |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Datový typ Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Řetězec |
| Edm.Time | Časový interval |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> OData komplexních datových typů (například **objekt**) nejsou podporovány.


## <a name="next-steps"></a>Další postup

Seznam úložišť dat, která aktivitu kopírování, která podporuje jako zdroje a jímky ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md##supported-data-stores-and-formats).