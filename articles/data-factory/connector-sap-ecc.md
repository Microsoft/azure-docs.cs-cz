---
title: Kopírování dat z SAP ECC
description: Zjistěte, jak zkopírovat data z SAP ECC do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: ad26fca94527864af10bb0051336c372ea65b3e0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413804"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Kopírování dat z SAP ECC pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z komponenty SAP Enterprise Central Component (ECC). Další informace naleznete v [tématu Kopírování přehledu aktivit](copy-activity-overview.md).

>[!TIP]
>Informace o celkové podpoře ADF ve scénáři integrace dat SAP najdete v článku [integrace dat SAP pomocí whitepaper Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobným úvodem, porovnáním a pokyny.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor SAP ECC je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data z SAP ECC do libovolného úložiště dat podporovaných jímek. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivity kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor SAP ECC podporuje:

- Kopírování dat z SAP ECC na SAP NetWeaver verze 7.0 a novější.
- Kopírování dat z libovolných objektů vystavených službami SAP ECC OData, například:

  - Tabulky nebo zobrazení SAP.
  - Objekty Business Application Programming Interface [BAPI].
  - Odsávače dat.
  - Data nebo zprostředkující dokumenty (IDOC) odeslané do integrace procesů SAP (PI), které mohou být přijaty jako OData prostřednictvím relativních adaptérů.

- Kopírování dat pomocí základního ověřování.

>[!TIP]
>Chcete-li kopírovat data z SAP ECC prostřednictvím tabulky SAP nebo zobrazení, použijte konektor [tabulky SAP,](connector-sap-table.md) který je rychlejší a škálovatelnější.

## <a name="prerequisites"></a>Požadavky

Obecně SAP ECC zveřejňuje entity prostřednictvím služeb OData prostřednictvím služby SAP Gateway. Chcete-li použít tento konektor SAP ECC, musíte:

- **Nastavte bránu SAP**. Pro servery s verzemi SAP NetWeaver novějšími než 7.4 je již nainstalována brána SAP. Pro starší verze je nutné nainstalovat vestavěnou bránu SAP nebo systém centra BRÁNY SAP, než vystavíte data SAP ECC prostřednictvím služeb OData. Chcete-li nastavit službu SAP Gateway, přečtěte si [instalační příručku](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktivace a konfigurace služby SAP OData**. Službu OData můžete aktivovat prostřednictvím TCODE SICF během několika sekund. Můžete také nakonfigurovat, které objekty musí být vystaveny. Další informace naleznete v [podrobných pokynech](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro konektor SAP ECC.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu SAP ECC jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| `type` | Vlastnost `type` musí být `SapEcc`nastavena na . | Ano |
| `url` | Adresa URL služby SAP ECC OData. | Ano |
| `username` | Uživatelské jméno používané pro připojení k SAP ECC. | Ne |
| `password` | Heslo ve formátu prostého textu používané pro připojení k SAP ECC. | Ne |
| `connectVia` | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud nezadáte runtime, použije se výchozí prostředí integrace Azure. | Ne |

### <a name="example"></a>Příklad

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností dostupných pro definování datových sad naleznete v [tématu Datové sady](concepts-datasets-linked-services.md). V následující části naleznete seznam vlastností podporovaných datovou sadou SAP ECC.

Chcete-li kopírovat data z `type` SAP ECC, `SapEccResource`nastavte vlastnost datové sady na .

Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| `path` | Cesta entity SAP ECC OData. | Ano |

### <a name="example"></a>Příklad

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v [tématu Potrubí](concepts-pipelines-activities.md). V následující části je uveden seznam vlastností podporovaných zdrojem SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC jako zdroj

Chcete-li zkopírovat data z `type` SAP `source` ECC, nastavte `SapEccSource`vlastnost v části aktivity kopírování na .

V části aktivity kopírování jsou `source` podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| `type` | Vlastnost `type` `source` části aktivity kopírování musí být `SapEccSource`nastavena na . | Ano |
| `query` | Možnosti dotazu OData pro filtrování dat. Příklad:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Konektor SAP ECC zkopíruje data z kombinované adresy URL:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Další informace naleznete v tématu [Součásti adresy URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Ne |

### <a name="example"></a>Příklad

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Mapování datových typů pro SAP ECC

Při kopírování dat z SAP ECC se z datových typů OData pro data SAP ECC do dočasných datových typů Azure Data Factory používají následující mapování. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, naleznete v [tématu Schémata a mapování datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ OData | Dočasný datový typ datové továrny |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Komplexní datové typy nejsou aktuálně podporovány.

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
