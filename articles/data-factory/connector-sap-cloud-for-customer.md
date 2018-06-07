---
title: Kopírování dat z/do cloudu SAP pro zákazníka pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak zkopírovat data z cloudu SAP pro zákazníka k úložištím dat. podporované podřízený (nebo) z podporované zdrojové úložiště dat do cloudu SAP pro zákazníka pomocí služby Data Factory.
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
ms.date: 04/17/2018
ms.author: jingwang
ms.openlocfilehash: 7003e68fcbceb4e45477f4f13dfa75b920405ace
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617650"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Kopírování dat z cloudu SAP pro zákazníka (C4C) pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z/do cloudu SAP pro zákazníka (C4C). Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [aktivitu kopírování v V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Můžete zkopírovat data z cloudu SAP pro zákazníka do úložiště dat žádné podporované podřízený nebo zkopírování dat z jakékoli úložiště podporované zdroje dat do cloudu SAP pro zákazníka. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor umožňuje Azure Data Factory ke zkopírování dat z/do cloudu SAP pro zákazníka, včetně SAP cloudu pro prodej, SAP cloudové služby a SAP Cloud pro sociální zapojení řešení.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení entit služby Data Factory konkrétní SAP cloudu pro konektor zákazníka.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SAP Cloud pro zákazníka propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **SapCloudForCustomer**. | Ano |
| url | Adresa URL služby SAP C4C OData. | Ano |
| uživatelské jméno | Zadejte uživatelské jméno pro připojení k SAP C4C. | Ano |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. Pokud není zadaný, použije výchozí Runtime integrace Azure. | Ne Ano pro sink zdroje |

>[!IMPORTANT]
>Ke zkopírování dat do cloudu SAP pro zákazníka, explicitně [vytvoření služby Azure IR](create-azure-integration-runtime.md#create-azure-ir) s umístěním téměř SAP cloudu pro zákazníka a přidružení v propojené službě jako v následujícím příkladu:

**Příklad:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datové sady](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností nepodporuje SAP cloudu pro datovou sadu zákazníka.

Ke zkopírování dat z cloudu SAP pro zákazníka, nastavte vlastnost typu datové sady, která **SapCloudForCustomerResource**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **SapCloudForCustomerResource** |Ano |
| path | Zadejte cestu SAP C4C OData entity. |Ano |

**Příklad:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typePoperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje SAP cloudové zdroje zákazníka.

### <a name="sap-c4c-as-source"></a>SAP C4C jako zdroj

Ke zkopírování dat z cloudu SAP pro zákazníka, nastavte typ zdroje v aktivitě kopírování do **SapCloudForCustomerSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **SapCloudForCustomerSource**  | Ano |
| query | Zadejte vlastního dotazu OData číst data. | Ne |

Ukázkový dotaz k získání dat pro určitý den: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C jako jímku

Ke zkopírování dat do cloudu SAP pro zákazníka, nastavte typ jímky v aktivitě kopírování do **SapCloudForCustomerSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **podřízený** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **SapCloudForCustomerSink**  | Ano |
| WriteBehavior | Chování zápisu operace. Může být "Vložit", "Update". | Ne. Výchozí "Insert". |
| writeBatchSize | Velikost dávky zápisu operace. Velikost dávky získat nejlepší výkon se může lišit pro jinou tabulku nebo server. | Ne. Výchozí hodnota je 10. |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "cloudDataMovementUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapování datového typu pro SAP Cloud zákazníka.

Při kopírování dat z cloudu SAP pro zákazníka, následující mapování se používají z cloudu SAP pro typy dat zákazníka k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| SAP C4C OData datový typ | Typ průběžných dat objektu pro vytváření dat |
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


## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
