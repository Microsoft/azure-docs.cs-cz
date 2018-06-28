---
title: Kopírování dat z SAP BW pomocí Azure Data Factory | Microsoft Docs
description: Postup kopírování dat z SAP Business Warehouse do úložiště dat podporovaných podřízený pomocí aktivity kopírování v kanál služby Azure Data Factory.
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 9934e9757b5def444afb39d110e490aa6516521f
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045071"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopírování dat z SAP Business Warehouse pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Aktuální verze](connector-sap-business-warehouse.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z SAP Business Warehouse (BW). Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Ze SAP Business Warehouse může kopírovat data do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SAP Business Warehouse podporuje:

- SAP Business Warehouse **verze 7.x**.
- Kopírování dat z **InfoCubes a QueryCubes** (včetně BEx dotazy) s dotazy MDX.
- Kopírování dat pomocí základního ověřování.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor SAP Business Warehouse, budete muset:

- Nastavte Self-hosted integrace Runtime. V tématu [Self-hosted integrace Runtime](create-self-hosted-integration-runtime.md) článku.
- Nainstalujte **SAP NetWeaver knihovny** na počítači integrace modulu Runtime. Knihovna SAP Netweaver získáte od správce SAP, nebo přímo z [SAP služby Stažení softwaru](https://support.sap.com/swdc). Vyhledejte **1025361 # Poznámka SAP** získat umístění stahování na nejnovější verzi. Ujistěte se, že vyberete **64-bit** SAP NetWeaver knihovny, která odpovídá vaší instalace integrace modulu CLR. Nainstalujte všechny soubory, které jsou součástí sady SDK SAP NetWeaver RFC podle Poznámka SAP. Knihovna SAP NetWeaver jsou také obsaženy v nástrojích klienta SAP instalace.

> [!TIP]
> Uveďte knihovny DLL do složky system32 extrahována ze sady SDK NetWeaver RFC.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru SAP Business Warehouse.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SAP Business Warehouse (BW) propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **SapBw** | Ano |
| server | Název serveru, na kterém se nachází instance SAP BW. | Ano |
| systemNumber | Číslo systému SAP BW systému.<br/>Povolené hodnoty: letopočty řetězec desetinné číslo. | Ano |
| clientId | ID klienta v systému SAP W klienta.<br/>Povolené hodnoty: třímístné řetězec desetinné číslo. | Ano |
| uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP. | Ano |
| heslo | Heslo pro uživatele. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. Modul Runtime Self-hosted integrace se vyžaduje, jak je uvedeno v [požadavky](#prerequisites). |Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje SAP BW datovou sadu.

Ke zkopírování dat z SAP BW, nastavte vlastnost typu datové sady, která **RelationalTable**. Když nejsou k dispozici žádné vlastnosti specifické pro typ podporované pro SAP BW datové sady zadejte RelationalTable.

**Příklad:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje SAP BW zdroje.

### <a name="sap-bw-as-source"></a>SAP BW jako zdroj

Ke zkopírování dat z SAP BW, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **RelationalSource** | Ano |
| query | Určuje dotaz MDX číst data z instance SAP BW. | Ano |

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
                "type": "RelationalSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw"></a>Mapování datového typu pro SAP BW

Při kopírování dat z SAP BW, se používají následující mapování z typů dat SAP BW k Azure Data Factory dočasné datové typy. V tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) a zjistěte, jak aktivity kopírování mapuje zdroje schéma a data typ jímky.

| Typ dat SAP BW | Typ průběžných dat objektu pro vytváření dat |
|:--- |:--- |
| ACCP | Int |
| CHAR – | Řetězec |
| CLNT | Řetězec |
| AKTUÁLNÍ | Decimal |
| CUKY | Řetězec |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Bajt |
| INT2 | Int16 |
| INT4 | Int |
| JAZYK | Řetězec |
| LCHR | Řetězec |
| LRAW | Byte] |
| PREC | Int16 |
| QUAN | Decimal |
| NEZPRACOVANÁ | Byte] |
| RAWSTRING | Byte] |
| ŘETĚZEC | Řetězec |
| JEDNOTKA | Řetězec |
| SOUBORY DAT | Řetězec |
| NUMC | Řetězec |
| TIMS | Řetězec |


## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
