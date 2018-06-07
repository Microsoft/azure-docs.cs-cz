---
title: Kopírování dat ze zdroje OData pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak ke zkopírování dat z OData zdroje k úložištím dat. podporované podřízený pomocí aktivity kopírování v kanál služby Azure Data Factory.
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
ms.openlocfilehash: f648bec7530260a6445f74b6d141140f728674ee
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617157"
---
# <a name="copy-data-from-odata-source-using-azure-data-factory"></a>Kopírování dat z zdroj OData pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-odata-connector.md)
> * [Verze 2 – Preview](connector-odata.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z zdroje OData. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [konektoru OData V1](v1/data-factory-odata-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Data můžete zkopírovat z zdroj OData do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor OData podporuje:

- OData **verze 3.0 a 4.0**.
- Kopírování dat pomocí následující ověření: **anonymní**, **základní**, a **Windows**.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru OData.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro OData propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **OData** |Ano |
| url | Adresy URL kořenového adresáře služby OData. |Ano |
| authenticationType. | Typ ověřování používaný pro připojení ke zdroji OData.<br/>Povolené hodnoty jsou: **anonymní**, **základní**, a **Windows**. Poznámka: OAuth není podporována. | Ano |
| Uživatelské jméno | Pokud používáte ověřování Basic nebo Windows, zadejte uživatelské jméno. | Ne |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

**Příklad 1: použití anonymní ověřování**

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

**Příklad 2: použití základního ověřování**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

**Příklad 3: pomocí ověřování systému Windows**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje datovou sadu OData.

Ke zkopírování dat z OData, nastavte vlastnost typu datové sady, která **ODataResource**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **ODataResource** | Ano |
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje zdroj OData.

### <a name="odata-as-source"></a>Jako zdroj OData

Ke zkopírování dat z OData, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **RelationalSource** | Ano |
| query | Možnosti dotazu OData k filtrování dat Příklad: "? $select = název, popis a $top = 5".<br/><br/>Poznámka: v poslední, konektor OData zkopíruje data z adresy URL pro kombinované: `[url specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Odkazovat na [OData pro adresy URL součásti](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Ne |

**Příklad:**

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

## <a name="data-type-mapping-for-odata"></a>Datový typ mapování pro OData

Při kopírování dat z OData, se používají následující mapování datových typů OData k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| Datový typ OData | Typ průběžných dat objektu pro vytváření dat |
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
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Řetězec |
| Edm.Time | Časový interval |
| Edm.DateTimeOffset | Datový typ DateTimeOffset |

> [!Note]
> OData komplexními datovými typy (například objekt) nejsou podporovány.


## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
