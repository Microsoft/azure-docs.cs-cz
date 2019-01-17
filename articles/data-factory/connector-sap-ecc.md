---
title: Kopírování dat z SAP ECC pomocí Azure Data Factory | Dokumentace Microsoftu
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
ms.date: 04/26/2018
ms.author: jingwang
ms.openlocfilehash: d6a6d9b352db61d98e85c840a3ebc5cb6a832a3f
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352457"
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Kopírování dat z SAP ECC pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z SAP ECC (centrální součást Enterprise SAP). Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z SAP ECC do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SAP ECC podporuje:

- Kopírování dat z SAP ECC na systému SAP NetWeaver verze 7.0 a vyšším. 
- Kopírování dat ze všech objektů, které jsou vystavené služby SAP ECC OData (například SAP tabulky a zobrazení, BAPI, – extraktory dat atd.) nebo data/Idoc odesílat SAP PI, který může být přijata jako OData, který prostřednictvím relativní adaptéry.
- Kopírování dat pomocí základního ověřování.

## <a name="prerequisites"></a>Požadavky

Obecně platí SAP ECC zpřístupňuje entity pomocí služby OData pomocí brány SAP. Pokud chcete použít tento konektor SAP ECC, budete muset:

- **Nastavení brány SAP**. U serverů s vyšší než 7.4 verze SAP NetWeaver je už nainstalovaná brána SAP. V opačném případě je potřeba nainstalovat vložené brána nebo brána centra před zveřejnění dat SAP ECC prostřednictvím služby OData. Zjistěte, jak nastavit službu Brána SAP [Průvodce instalací](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktivaci a konfiguraci služby SAP OData**. Služby OData pomocí TCODE SICF můžete aktivovat v řádu sekund. Můžete také nakonfigurovat, které objekty musí být vystavené. Tady je ukázka [podrobných pokynů](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="getting-started"></a>Začínáme

Vytvoření kanálu s aktivitou kopírování pomocí sady .NET SDK, Python SDK, Azure Powershellu, rozhraní REST API nebo šablony Azure Resource Manageru. Zobrazit [kurz aktivity kopírování](quickstart-create-data-factory-dot-net.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor SAP ECC.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SAP ECC propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **SapEcc** | Ano |
| url | Adresa url služby SAP ECC OData. | Ano |
| uživatelské jméno | Uživatelské jméno pro připojení k SAP ECC. | Ne |
| heslo | Heslo jako prostý text pro připojení k SAP ECC. | Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

**Příklad:**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje datové sady SAP ECC.

Ke zkopírování dat z SAP ECC, nastavte vlastnost typ datové sady na **SapEccResource**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| path | Cesta SAP ECC OData entity. | Ano |

**Příklad**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem SAP ECC.

### <a name="sap-ecc-as-source"></a>SAP ECC jako zdroj

Ke zkopírování dat z SAP ECC, nastavte typ zdroje v aktivitě kopírování do **SapEccSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **SapEccSource** | Ano |
| query | Možnosti dotazu OData k filtrování dat Příklad: "$select = název, popis a $top = 10".<br/><br/>Konektor SAP ECC kopíruje data z adresy URL pro kombinované: (adresa url zadaná v propojené službě) / (Cesta zadaná v datové sadě)? (dotaz zadaný v zdroj aktivita kopírování). Odkazovat na [OData pro adresy URL komponenty](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Ne |

**Příklad:**

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

## <a name="data-type-mapping-for-sap-ecc"></a>Mapování datového typu pro SAP ECC

Kopírování dat ze SAP ECC, následující mapování se používají z datových typů OData pro SAP ECC data do služby Azure Data Factory dočasné datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Typ dat OData | Data factory dočasné datový typ |
|:--- |:--- |:--- |
| Edm.Binary | Řetězec |
| Edm.Boolean | BOOL |
| Edm.Byte | Řetězec |
| Edm.DateTime | DateTime |
| Edm.Decimal | Desítkově |
| Edm.Double | Double |
| Edm.Single | Jednoduchá |
| Edm.Guid | Řetězec |
| Edm.Int16 | Int16 |
| Edm.Int32 | Datový typ Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Řetězec |
| Edm.Time | Časový interval |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Komplexní datové typy nejsou nyní podporovány.

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
