---
title: Kopírování dat z cloudu z/do SAP pro zákazníky
description: Naučte se, jak kopírovat data z SAP cloudu pro zákazníky na podporovaná úložiště dat jímky (nebo) z podporovaných zdrojových úložišť dat do cloudu SAP pro zákazníky pomocí Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: b86559422b6efeed666a3ae35022563a66d9c7e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597313"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Kopírování dat z SAP cloudu pro zákazníky (C4C) pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z/do SAP cloudu pro zákazníka (C4C). Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

>[!TIP]
>Pokud chcete získat přehled o celkové podpoře pro integraci dat v programu, přečtěte si článek [integrace dat SAP pomocí Azure Data Factory dokumentu White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobným úvodem na jednotlivé konektory SAP, comparsion a doprovodné materiály.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor SAP pro zákazníky se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z SAP cloudu můžete kopírovat pro zákazníka do libovolného podporovaného úložiště dat jímky nebo zkopírovat data z libovolného podporovaného zdrojového úložiště dat do cloudu SAP pro zákazníky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor umožňuje Azure Data Factory kopírovat data z/do cloudu SAP pro zákazníky, včetně SAP cloudu pro prodej, SAP Cloud for Service a SAP cloudu pro řešení pro sociální zapojení.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech najdete podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor SAP Cloud for Customer.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu SAP Cloud pro zákazníka jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **SapCloudForCustomer**. | Yes |
| url | Adresa URL služby SAP C4C OData | Yes |
| username | Zadejte uživatelské jméno pro připojení k SAP C4C. | Yes |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Pokud není zadaný, použije se výchozí Azure Integration Runtime. | No |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností, které služba SAP Cloud pro zákaznickou datovou sadu podporuje.

Pokud chcete kopírovat data ze SAP cloudu pro zákazníka, nastavte vlastnost Type datové sady na **SapCloudForCustomerResource**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **SapCloudForCustomerResource** . |Yes |
| program | Zadejte cestu k entitě SAP C4C OData. |Yes |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných službou SAP Cloud pro zdroj zákazníků.

### <a name="sap-c4c-as-source"></a>SAP C4C as source

Pokud chcete kopírovat data ze SAP cloudu pro zákazníka, nastavte typ zdroje v aktivitě kopírování na **SapCloudForCustomerSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **SapCloudForCustomerSource** .  | Yes |
| query | Zadejte vlastní dotaz OData pro čtení dat. | No |
| httpRequestTimeout | Časový limit (hodnota **TimeSpan** ) požadavku HTTP získat odpověď. Tato hodnota představuje časový limit pro získání odpovědi, nikoli časový limit pro čtení dat odpovědi. Pokud není zadaný, výchozí hodnota je **00:30:00** (30 minut). | No |

Vzorový dotaz pro získání dat pro určitý den: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

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

Pokud chcete zkopírovat data do SAP cloudu pro zákazníka, nastavte typ jímky v aktivitě kopírování na **SapCloudForCustomerSink**. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **SapCloudForCustomerSink** .  | Yes |
| writeBehavior | Chování operace zápisu. Může být "vložení", "Update". | No. Výchozí hodnota "Insert". |
| writeBatchSize | Velikost dávky operace zápisu. Velikost dávky, která má dosáhnout nejlepšího výkonu, se může lišit pro různé tabulky nebo servery. | No. Výchozí hodnota 10. |
| maxConcurrentConnections |Horní limit souběžných připojení navázaných na úložiště dat během spuštění aktivity. Zadejte hodnotu pouze v případě, že chcete omezit souběžná připojení.| No |

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

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapování datového typu pro SAP v cloudu pro zákazníky

Při kopírování dat z cloudu SAP pro zákazníka se z cloudu SAP pro typy zákaznických dat, které Azure Data Factory dočasné datové typy, používají následující mapování. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování typů schématu a dat](copy-activity-schema-and-type-mapping.md) .

| Datový typ OData C4C SAP | Typ dat interim Data Factory |
|:--- |:--- |
| EDM. Binary | Byte [] |
| Edm.Boolean | Logická hodnota |
| EDM. Byte | Byte [] |
| EDM. DateTime | DateTime |
| EDM. Decimal | Decimal |
| Edm.Double | dvojité |
| EDM. Single | Jednoduché |
| EDM. GUID | Identifikátor GUID |
| EDM. Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| EDM. SByte | Int16 |
| Edm.String | Řetězec |
| EDM. time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
