---
title: Kopírování dat ze systému SAP BW
description: Zjistěte, jak zkopírovat data z SAP Business Warehouse do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.date: 09/04/2019
ms.openlocfilehash: 0c37d77ca73ddbe8b79351f90275a1d639757633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74923743"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopírování dat z obchodního skladu SAP pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Aktuální verze](connector-sap-business-warehouse.md)

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z obchodního skladu SAP (BW). Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

>[!TIP]
>Informace o celkové podpoře ADF ve scénáři integrace dat SAP najdete v článku [integrace dat SAP pomocí whitepaper Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobným úvodem, porovnáním a pokyny.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor SAP Business Warehouse je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z obchodního skladu SAP můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor SAP Business Warehouse podporuje:

- SAP Business Warehouse **verze 7.x**.
- Kopírování dat z **InfoCubes a QueryCubes** (včetně dotazů BEx) pomocí dotazů MDX.
- Kopírování dat pomocí základního ověřování.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor SAP Business Warehouse, musíte:

- Nastavte runtime integrace s vlastním hostitelem. Podrobnosti najdete v článku [runtime integrace s vlastním hostitelem.](create-self-hosted-integration-runtime.md)
- Nainstalujte **knihovnu SAP NetWeaver** do integračního automatu Runtime. Knihovnu SAP Netweaver můžete získat od správce SAP nebo přímo ze [služby SAP Software Download Center](https://support.sap.com/swdc). Vyhledejte **poznámku SAP #1025361** a získejte umístění pro stažení nejnovější verze. Ujistěte se, že jste vybrali **64bitovou** knihovnu SAP NetWeaver, která odpovídá instalaci integračního běhu. Poté nainstalujte všechny soubory obsažené v sadě SAP NetWeaver RFC SDK podle poznámky SAP. Knihovna SAP NetWeaver je také součástí instalace klientských nástrojů SAP.

>[!TIP]
>Chcete-li vyřešit problém s připojením k systému SAP BW, ujistěte se, že:
>- Všechny knihovny závislostí extrahované ze sady NetWeaver RFC SDK jsou umístěny ve složce %windir%\system32. Obvykle má icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- Potřebné porty používané pro připojení k serveru SAP jsou povoleny na počítači infračervených zařízení s vlastním hostitelem, které jsou obvykle porty 3300 a 3201.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor SAP Business Warehouse.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu SAP Business Warehouse (BW) jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **SapBw** | Ano |
| server | Název serveru, na kterém se nachází instance SAP BW. | Ano |
| systemNumber | Systémové číslo systému SAP BW.<br/>Povolená hodnota: dvoumístné desetinné číslo reprezentované jako řetězec. | Ano |
| clientId | ID klienta v systému SAP W.<br/>Povolená hodnota: třímístné desetinné číslo reprezentované jako řetězec. | Ano |
| userName | Jméno uživatele, který má přístup k serveru SAP. | Ano |
| heslo | Heslo pro tohoto uživatele. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Runtime integrace hostované samostatně je vyžadován, jak je uvedeno v [požadavky](#prerequisites). |Ano |

**Příklad:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou SAP BW.

Chcete-li kopírovat data ze systému SAP BW, nastavte vlastnost type datové sady na **SapBwCube**. Zatímco neexistují žádné vlastnosti specifické pro typ podporovány pro datové sady SAP BW typu RelationalTable.

**Příklad:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Pokud jste `RelationalTable` používali zadaný datový soubor, je stále podporována tak, jak je, zatímco se doporučuje používat novou do budoucna.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW jako zdroj

Chcete-li kopírovat data z SAP BW, jsou podporovány následující vlastnosti v části **zdroj aktivity** kopírování:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na: **SapBwSource.** | Ano |
| query | Určuje dotaz MDX pro čtení dat z instance SAP BW. | Ano |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Pokud jste `RelationalSource` používali zadaný zdroj, je stále podporován jako-je, zatímco jste navrhl použít nový do budoucna.

## <a name="data-type-mapping-for-sap-bw"></a>Mapování datových typů pro SAP BW

Při kopírování dat z SAP BW se používají následující mapování z datových typů SAP BW do dočasných datových typů Azure Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování schématu a datových typů.](copy-activity-schema-and-type-mapping.md)

| Datový typ SAP BW | Dočasný datový typ datové továrny |
|:--- |:--- |
| Accp | Int |
| Char | Řetězec |
| Clnt | Řetězec |
| CURR | Desetinné číslo |
| CUKY | Řetězec |
| Prosince | Desetinné číslo |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| Lang | Řetězec |
| LCHR | Řetězec |
| LRAW | Bajt[] |
| PŘEDŠkolní hod | Int16 |
| Quan | Desetinné číslo |
| Syrové | Bajt[] |
| RAWSTRING | Bajt[] |
| Řetězec | Řetězec |
| Jednotka | Řetězec |
| Dat | Řetězec |
| SPOLEČNOST NUMC | Řetězec |
| Tims | Řetězec |


## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
