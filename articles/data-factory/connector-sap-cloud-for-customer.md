---
title: Kopírování dat z/do cloudu SAP pro zákazníka pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z cloudu SAP pro zákazníka do úložišť dat jímky podporované (nebo) z podporované zdrojové úložiště dat do cloudu SAP pro zákazníka pomocí služby Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: jingwang
ms.openlocfilehash: 360cf92686682e65cf5348ee717bbb80cdc85cb4
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016167"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Kopírování dat z cloudu SAP pro zákazníka (C4C) pomocí služby Azure Data Factory

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat z/do cloudu SAP pro zákazníka (C4C) ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Můžete kopírovat data z SAP cloudem pro zákazníka jakékoli podporovaného úložiště dat jímky nebo kopírování dat ze všech podporovaných zdrojů úložišť dat SAP cloudem pro zákazníka. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor umožňuje Azure Data Factory pro kopírování dat z/do cloudu SAP pro zákazníka, včetně SAP cloudem pro prodej, Cloud SAP pro službu a SAP cloudem pro sociální zapojení řešení.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní SAP cloud pro konektor zákazníka.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SAP cloudem pro zákaznické propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **SapCloudForCustomer**. | Ano |
| url | Adresa URL služby SAP C4C OData. | Ano |
| uživatelské jméno | Zadejte uživatelské jméno pro připojení k SAP C4C. | Ano |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne pro zdroj, Ano pro jímku |

>[!IMPORTANT]
>Pro kopírování dat do cloudu SAP pro zákazníka, explicitně [vytvořit prostředí Azure IR](create-azure-integration-runtime.md#create-azure-ir) umístění blízko vaší SAP cloudem pro zákazníky a jeho přidružení v propojené službě jako v následujícím příkladu:

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností podporována SAP cloudem pro datovou sadu zákazníků.

Ke zkopírování dat z cloudu SAP pro zákazníka, nastavte vlastnost typ datové sady na **SapCloudForCustomerResource**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **SapCloudForCustomerResource** |Ano |
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností pro zákazníka zdroj podporována SAP cloudem.

### <a name="sap-c4c-as-source"></a>SAP C4C jako zdroj

Ke zkopírování dat z cloudu SAP pro zákazníka, nastavte typ zdroje v aktivitě kopírování do **SapCloudForCustomerSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **SapCloudForCustomerSource**  | Ano |
| query | Zadejte vlastní dotaz OData číst data. | Ne |

Ukázkový dotaz pro získání dat pro určitý den: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

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

### <a name="sap-c4c-as-sink"></a>SAP C4C jako jímka

Ke zkopírování dat do cloudu SAP pro zákazníka, nastavte typ jímky v aktivitě kopírování do **SapCloudForCustomerSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **SapCloudForCustomerSink**  | Ano |
| WriteBehavior | Chování zápisu operace. Může být "Vložit", "Úpravy". | Ne. Výchozí "Vložit". |
| WriteBatchSize | Velikost dávky zápisu operace. Velikost dávky získat nejlepší výkon se může lišit pro jiné tabulky nebo serveru. | Ne. Výchozí hodnota 10. |

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
            "dataIntegrationUnits": 4,
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

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapování datového typu pro SAP Cloud pro odběratele

Při kopírování dat z cloudu SAP pro zákazníka, následující mapování umožňují ze SAP Cloud pro typy dat zákazníků Azure Data Factory dočasné datové typy. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| SAP C4C OData datový typ | Data factory dočasné datový typ |
|:--- |:--- |
| Edm.Binary | Byte] |
| Edm.Boolean | BOOL |
| Edm.Byte | Byte] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Desítkově |
| Edm.Double | Double |
| Edm.Single | Jednoduchá |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Datový typ Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Řetězec |
| Edm.Time | Časový interval |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
