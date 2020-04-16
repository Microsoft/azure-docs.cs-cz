---
title: Kopírování dat z/do SAP Cloud pro zákazníka
description: Zjistěte, jak kopírovat data z SAP Cloud pro zákazníka do podporovaných úložišť dat jímky (nebo) z podporovaných zdrojových úložišť dat do SAP Cloud pro zákazníka pomocí Data Factory.
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
ms.date: 09/02/2019
ms.openlocfilehash: 1d3772a17d0429d9b3a5bf95d2060f2dfbbbafe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418044"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Kopírování dat z SAP Cloud pro zákazníka (C4C) pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z/do SAP Cloud pro zákazníka (C4C). Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

>[!TIP]
>Informace o celkové podpoře ADF ve scénáři integrace dat SAP najdete v článku [integrace dat SAP pomocí whitepaper Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobným úvodem, porovnáním a pokyny.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor SAP Cloud for Customer je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data z SAP Cloud pro zákazníka do libovolného podporovaného úložiště dat jímky nebo zkopírovat data z libovolného úložiště dat podporovaného zdroje do SAP Cloud for Customer. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor umožňuje Azure Data Factory kopírovat data z/do SAP Cloud pro zákazníka, včetně SAP Cloud pro prodej, SAP Cloud for Service a SAP Cloud for Social Engagement řešení.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor SAP Cloud for Customer.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro službu propojenou službou SAP Cloud for Customer jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **SapCloudForCustomer**. | Ano |
| url | Adresa URL služby SAP C4C OData. | Ano |
| uživatelské jméno | Zadejte uživatelské jméno pro připojení k SAP C4C. | Ano |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Pokud není zadán, používá výchozí Azure Integration Runtime. | Ne pro zdroj, Ano pro umyvadlo |

>[!IMPORTANT]
>Chcete-li zkopírovat data do SAP Cloud pro zákazníka, explicitně [vytvořte Azure IR](create-azure-integration-runtime.md#create-azure-ir) s umístěním v blízkosti vašeho CLOUDU SAP pro zákazníka a přidružte k propojené službě jako následující příklad:

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou SAP Cloud for Customer.

Chcete-li zkopírovat data z SAP Cloud pro zákazníka, nastavte vlastnost type datové sady na **SapCloudForCustomerResource**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **SapCloudForCustomerResource.** |Ano |
| cesta | Zadejte cestu entity SAP C4C OData. |Ano |

**Příklad:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných sap cloud pro zákazníka zdroj.

### <a name="sap-c4c-as-source"></a>SAP C4C jako zdroj

Chcete-li zkopírovat data z SAP Cloud pro zákazníka, nastavte typ zdroje v aktivitě kopírování na **SapCloudForCustomerSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **SapCloudForCustomerSource.**  | Ano |
| query | Zadejte vlastní dotaz OData pro čtení dat. | Ne |

Ukázkový dotaz pro získání dat pro konkrétní den:`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

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

### <a name="sap-c4c-as-sink"></a>SAP C4C jako umyvadlo

Chcete-li zkopírovat data do SAP Cloud for Customer, nastavte typ jímky v aktivitě kopírování na **SapCloudForCustomerSink**. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **SapCloudForCustomerSink.**  | Ano |
| writeBehavior | Chování zápisu operace. Může to být "Vložit", "Aktualizovat". | Ne. Výchozí "Vložit". |
| writeBatchSize | Velikost dávky operace zápisu. Velikost dávky pro dosažení nejlepšího výkonu se může lišit pro různé tabulky nebo servery. | Ne. Výchozí hodnota 10. |

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

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapování datových typů pro SAP Cloud pro zákazníka

Při kopírování dat z SAP Cloud pro zákazníka se z SAP Cloud pro datové typy pro zákazníky používají následující mapování do dočasných datových typů Azure Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování schématu a datových typů.](copy-activity-schema-and-type-mapping.md)

| Datový typ SAP C4C OData | Dočasný datový typ datové továrny |
|:--- |:--- |
| Edm.Binary | Bajt[] |
| Edm.Boolean | Logická hodnota |
| Edm.Byte | Bajt[] |
| Edm.DateČas | DateTime |
| Edm.Desetinné místo | Desetinné číslo |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Identifikátor GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Řetězec |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
