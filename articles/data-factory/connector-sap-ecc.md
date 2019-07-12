---
title: Kopírování dat z SAP ECC pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data ze SAP ECC úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 7ccd2e7a804c6495f6caf5e264b1f7c2a36cb02e
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827770"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Kopírování dat z SAP ECC pomocí služby Azure Data Factory

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat z SAP Enterprise centrální součásti (ECC) ve službě Azure Data Factory. Další informace najdete v tématu [přehled aktivit kopírování](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z SAP ECC do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SAP ECC podporuje:

- Kopírování dat ze SAP ECC na systému SAP NetWeaver verze 7.0 nebo novější.
- Kopírování dat ze všech objektů, vystavené služby SAP ECC OData, jako například:

  - SAP tabulek nebo zobrazení.
  - Objekty Business Application Programming Interface [BAPI].
  - – Extraktory data.
  - Data nebo dokumenty zprostředkující (Idoc) odeslané k SAP procesu integrace (číslo PÍ), který prostřednictvím relativní adaptéry může být přijata jako OData.

- Kopírování dat pomocí základního ověřování.

>[!TIP]
>Chcete-li kopírovat data z SAP ECC prostřednictvím SAP tabulky nebo zobrazení, použijte [tabulku SAP](connector-sap-table.md) konektor, který je rychlejší a lépe škálovatelný.

## <a name="prerequisites"></a>Požadavky

Obecně platí SAP ECC zpřístupňuje entity pomocí služby OData pomocí brány SAP. Pokud chcete použít tento konektor SAP ECC, budete muset:

- **Nastavení brány SAP**. U serverů s vyšší než 7.4 verze SAP NetWeaver je už nainstalovaná brána SAP. U starších verzí je nutné nainstalovat vložený brány SAP nebo rozbočovač systému SAP brány před zveřejnění dat SAP ECC prostřednictvím služby OData. Nastavení brány SAP, najdete v článku [Průvodce instalací](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktivaci a konfiguraci služby SAP OData**. Služby OData pomocí TCODE SICF můžete aktivovat v řádu sekund. Můžete také nakonfigurovat, které objekty vyžadují zpřístupní. Další informace najdete v tématu [podrobných pokynů](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektoru SAP ECC.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SAP ECC propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požadováno |
|:--- |:--- |:--- |
| `type` | `type` Musí být vlastnost nastavena na `SapEcc`. | Ano |
| `url` | Adresa URL služby SAP ECC OData. | Ano |
| `username` | Uživatelské jméno pro připojení k SAP ECC. | Ne |
| `password` | Heslo jako prostý text pro připojení k SAP ECC. | Ne |
| `connectVia` | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul runtime integrace v místním prostředí nebo prostředí Azure integration runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud nezadáte modulu runtime, `connectVia` používá výchozí prostředí Azure integration runtime. | Ne |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datových sad](concepts-datasets-linked-services.md). Následující část obsahuje seznam vlastností nepodporuje datové sady SAP ECC.

Chcete-li kopírovat data z SAP ECC, nastavte `type` vlastnosti datové sady na `SapEccResource`.

Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požadováno |
|:--- |:--- |:--- |
| `path` | Cesta SAP ECC OData entity. | Ano |

### <a name="example"></a>Příklad

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). Následující část obsahuje seznam vlastností podporována zdroji SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC jako zdroj

Ke zkopírování dat z SAP ECC, nastavte `type` vlastnost `source` část aktivitu kopírování, která `SapEccSource`.

Následující vlastnosti jsou podporovány v aktivitě kopírování `source` části:

| Vlastnost | Popis | Požadováno |
|:--- |:--- |:--- |
| `type` | `type` Vlastnost aktivity kopírování `source` oddílu musí být nastaveno na `SapEccSource`. | Ano |
| `query` | Možnosti dotazu OData k filtrování dat. Příklad:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Konektor SAP ECC kopíruje data z kombinované adresy URL:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Další informace najdete v tématu [OData pro adresy URL komponenty](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Ne |

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

## <a name="data-type-mappings-for-sap-ecc"></a>Mapování datového typu pro SAP ECC

Po zkopírování dat z SAP ECC následující mapování se používají z datových typů OData pro SAP ECC data do služby Azure Data Factory dočasné datových typů. Informace o tom, jak aktivity kopírování mapuje typ zdroje schéma a data jímky, najdete v článku [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

| Typ dat OData | Data Factory dočasné datový typ |
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

## <a name="next-steps"></a>Další postup

Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
