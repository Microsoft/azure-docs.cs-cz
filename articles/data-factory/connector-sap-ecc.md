---
title: Kopírování dat z SAP ECC
description: Naučte se, jak kopírovat data z SAP ECC na podporovaná úložiště dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/28/2020
ms.openlocfilehash: a3e701f3d433b5b52d8992035ac4ad75b78cb795
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386692"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Kopírování dat z SAP ECC pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z SAP Enterprise Central Component (ECC). Další informace najdete v tématu [Přehled aktivit kopírování](copy-activity-overview.md).

>[!TIP]
>Pokud chcete získat přehled o celkové podpoře pro integraci dat v programu, přečtěte si článek [integrace dat SAP pomocí Azure Data Factory dokumentu White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobným úvodem na jednotlivé konektory SAP, comparsion a doprovodné materiály.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor SAP ECC se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z SAP ECC můžete kopírovat do jakéhokoli podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje nebo jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor SAP ECC podporuje:

- Kopírování dat z SAP ECC v SAP NetWeaver verze 7,0 a novější.
- Kopírování dat ze všech objektů vystavených pomocí SAP ECC služby OData, například:

  - Tabulky nebo zobrazení SAP.
  - Objekty rozhraní API pro obchodní aplikace [BAPI].
  - Extraktory dat.
  - Data nebo zprostředkující dokumenty (IDOCs) odeslané do integrace procesu SAP (PI), které lze přijmout jako OData prostřednictvím relativních adaptérů.

- Kopírování dat pomocí základního ověřování.

Verze 7,0 nebo novější odkazuje na verzi SAP NetWeaver namísto verze SAP ECC. Například SAP ECC 6,0 EHP 7 obecně má NetWeaver verzi >= 7,4. V případě, že si nejste jisti vaším prostředím, tady je postup, jak ověřit verzi ze systému SAP:

1. Pomocí grafického uživatelského rozhraní SAP se připojte k systému SAP. 
2. Přejít na   ->  **stav** systému. 
3. Zkontrolujte vydání SAP_BASIS, ujistěte se, že je větší nebo rovno 701.  
      ![Kontrolovat SAP_BASIS](./media/connector-sap-table/sap-basis.png)

>[!TIP]
>Pokud chcete kopírovat data z SAP ECC přes tabulku nebo zobrazení SAP, použijte konektor [SAP TABLE](connector-sap-table.md) Connector, který je rychlejší a škálovatelný.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor SAP ECC, je nutné vystavit entity SAP ECC prostřednictvím služby OData prostřednictvím brány SAP. A konkrétně:

- **Nastavte bránu SAP**. Pro servery s verzemi SAP NetWeaver novějšími než 7,4 je už nainstalovaná brána SAP. V případě starších verzí musíte nainstalovat vloženou bránu SAP nebo centrum brány SAP a teprve potom vystavit data SAP ECC prostřednictvím služeb OData. Informace o nastavení brány SAP najdete v [instalační příručce](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktivujte a nakonfigurujte službu SAP OData**. Službu OData můžete aktivovat prostřednictvím TCODE SICF během několika sekund. Můžete taky nakonfigurovat, které objekty je potřeba zveřejnit. Další informace najdete v [podrobných pokynech](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factorych entit specifických pro konektor SAP ECC.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu SAP ECC jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| `type` | `type`Vlastnost musí být nastavena na hodnotu `SapEcc` . | Yes |
| `url` | Adresa URL služby SAP ECC OData. | Yes |
| `username` | Uživatelské jméno použité pro připojení k SAP ECC. | No |
| `password` | Heslo ve formátu prostého textu používané pro připojení k SAP ECC. | No |
| `connectVia` | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Další informace najdete v části [požadavky](#prerequisites) . Pokud nezadáte modul runtime, použije se výchozí prostředí Azure Integration runtime. | No |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v tématu [datové sady](concepts-datasets-linked-services.md). V následující části najdete seznam vlastností podporovaných datovou sadou SAP ECC.

Chcete-li kopírovat data z SAP ECC, nastavte `type` vlastnost datové sady na `SapEccResource` .

Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| `path` | Cesta k entitě SAP ECC OData | Yes |

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

Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). V následující části najdete seznam vlastností podporovaných zdrojem SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC jako zdroj

Chcete-li kopírovat data z SAP ECC, nastavte `type` vlastnost v `source` části aktivity kopírování na `SapEccSource` .

V části aktivity kopírování jsou podporovány následující vlastnosti `source` :

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| `type` | `type`Vlastnost oddílu aktivity kopírování `source` musí být nastavena na hodnotu `SapEccSource` . | Yes |
| `query` | Možnosti dotazu OData pro filtrování dat Příklad:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Konektor SAP ECC kopíruje data z kombinované adresy URL:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Další informace najdete v tématu [komponenty adresy URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | No |
| `sapDataColumnDelimiter` | Jeden znak, který se používá jako oddělovač předaný do SAP RFC pro rozdělení výstupních dat. | No |
| `httpRequestTimeout` | Časový limit (hodnota **TimeSpan** ) požadavku HTTP získat odpověď. Tato hodnota představuje časový limit pro získání odpovědi, nikoli časový limit pro čtení dat odpovědi. Pokud není zadaný, výchozí hodnota je **00:30:00** (30 minut). | No |

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

Při kopírování dat z SAP ECC se z datových typů OData pro data SAP ECC používají následující mapování pro Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| Datový typ OData | Data Factory pomocný datový typ |
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
> Komplexní datové typy se momentálně nepodporují.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
