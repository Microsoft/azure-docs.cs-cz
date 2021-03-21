---
title: Kopírovat data z SAP BW
description: Naučte se, jak kopírovat data ze SAP Business Warehouse do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 4a5eb7c8d729fc0c3b95a61a4b8dbc8266d1eec3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386658"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopírování dat ze SAP Business Warehouse pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Aktuální verze](connector-sap-business-warehouse.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z SAP Business Warehouse (ČERNOBÍLe). Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

>[!TIP]
>Pokud chcete získat přehled o celkové podpoře pro integraci dat v programu, přečtěte si článek [integrace dat SAP pomocí Azure Data Factory dokumentu White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobným úvodem na jednotlivé konektory SAP, comparsion a doprovodné materiály.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor SAP Business Warehouse se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z SAP Business Warehouse můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor SAP Business Warehouse podporuje:

- SAP Business Warehouse **verze 7. x**.
- Kopírování dat z **InfoCubes a QueryCubes** (včetně dotazů BEx) pomocí dotazů MDX.
- Kopírování dat pomocí základního ověřování.

## <a name="prerequisites"></a>Předpoklady

K použití tohoto konektoru SAP Business Warehouse potřebujete:

- Nastavte Integration Runtime pro místní hostování. Podrobnosti najdete v článku [Integration runtime](create-self-hosted-integration-runtime.md) v místním prostředí.
- Na Integration Runtime počítač nainstalujte **knihovnu SAP NetWeaver** . Knihovnu SAP NetWeaver Library můžete získat od správce SAP nebo přímo z [webu SAP software Download Center](https://support.sap.com/swdc). Vyhledejte **#1025361 poznámky SAP** a získejte umístění pro stažení nejnovější verze. Ujistěte se, že jste vybrali **64** knihovnu SAP NetWeaver, která odpovídá vaší instalaci Integration runtime. Pak nainstalujte všechny soubory zahrnuté v sadě SAP NetWeaver RFC SDK podle poznámky SAP. Knihovna SAP NetWeaver Library je také součástí instalace klientských nástrojů SAP.

>[!TIP]
>Pokud chcete řešit potíže s připojením SAP BW, ujistěte se, že:
>- Všechny knihovny závislostí extrahované ze sady NetWeaver RFC SDK jsou umístěné ve složce%Windir%\System32. Obvykle má icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- Potřebné porty používané pro připojení k serveru SAP jsou povoleny v místním počítači IR, což jsou obvykle porty 3300 a 3201.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor SAP Business Warehouse.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu SAP Business Warehouse (ČERNOBÍLe) jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **SapBw** . | Yes |
| server | Název serveru, na kterém se nachází instance SAP BW. | Yes |
| systemNumber | Číslo systému SAP BW systému<br/>Povolená hodnota: dvoumístné desetinné číslo reprezentované jako řetězec. | Yes |
| clientId | ID klienta klienta v systému SAP W.<br/>Povolená hodnota: desítkové číslo se třemi číslicemi reprezentované jako řetězec. | Yes |
| userName | Jméno uživatele, který má přístup k serveru SAP. | Yes |
| heslo | Heslo pro tohoto uživatele. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Integration Runtime v místním prostředí se vyžaduje, jak je uvedeno v [požadavcích](#prerequisites). |Yes |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných sadou SAP BW DataSet.

Chcete-li kopírovat data z SAP BW, nastavte vlastnost Type datové sady na **SapBwCube**. Neexistují žádné vlastnosti specifické pro typ pro SAP BW datovou sadu relačních objektů typu.

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

Pokud jste používali `RelationalTable` typovou datovou sadu, je stále podporovaná tak, jak je, a až budete chtít začít používat novinku dál.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW jako zdroj

Chcete-li kopírovat data z SAP BW, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **SapBwSource** . | Yes |
| query | Určuje dotaz MDX pro čtení dat z instance SAP BW. | Yes |

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

Pokud jste používali `RelationalSource` typový zdroj, je stále podporován tak, jak je, a když jste navrhli začít používat nový.

## <a name="data-type-mapping-for-sap-bw"></a>Mapování datových typů pro SAP BW

Při kopírování dat z SAP BW se z SAP BW datových typů používají následující mapování pro Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování typů schématu a dat](copy-activity-schema-and-type-mapping.md) .

| SAP BW datový typ | Typ dat interim Data Factory |
|:--- |:--- |
| ACCP | Int |
| CHAR | Řetězec |
| CLNT | Řetězec |
| CURR | Decimal |
| CUKY | Řetězec |
| 18.12 | Decimal |
| FLTP | dvojité |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| JAZYK | Řetězec |
| LCHR | Řetězec |
| LRAW | Byte [] |
| PREC | Int16 |
| QUAN | Decimal |
| ZÍSKÁNÍ | Byte [] |
| RAWSTRING | Byte [] |
| ŘETEZCE | Řetězec |
| JEDNOTCE | Řetězec |
| DEFINIC | Řetězec |
| NUMC | Řetězec |
| TIMS | Řetězec |


## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
