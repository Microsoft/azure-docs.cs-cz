---
title: Kopírování dat z SAP BW pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z řešení SAP Business Warehouse úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: jingwang
ms.openlocfilehash: 9a0abcd70b4aeb2369604bafa924136122206e0a
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022276"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopírování dat z řešení SAP Business Warehouse pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Aktuální verze](connector-sap-business-warehouse.md)

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat z SAP Business Warehouse (BW) ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z řešení SAP Business Warehouse do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SAP Business Warehouse podporuje:

- SAP Business Warehouse **verze 7.x**.
- Kopírování dat z **InfoCubes a QueryCubes** (včetně BEx dotazy) s dotazy MDX.
- Kopírování dat pomocí základního ověřování.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor SAP Business Warehouse, je potřeba:

- Nastavte modul Integration Runtime. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.
- Nainstalujte **knihovna SAP NetWeaver** na počítači prostředí Integration Runtime. SAP Netweaver knihovny můžete získat od správce SAPU nebo přímo z [SAP Software Download Center](https://support.sap.com/swdc). Hledat **SAP Poznámka #1025361** na umístění ke stažení nejnovější verze. Ujistěte se, že vyberete **64-bit** SAP NetWeaver knihovny, která odpovídá vaší instalaci modulu Integration Runtime. Nainstalujte všechny soubory zahrnuté v SAP NetWeaver RFC SDK podle SAP Note. SAP NetWeaver knihovny je také součástí instalace SAP Client Tools.

>[!TIP]
>Chcete-li potíže s připojením k SAP BW, ujistěte se, že:
>- Všechny závislosti knihoven extrahují z NetWeaver RFC SDK se ve složce %windir%\system32. Má obvykle icudt34.dll icuin34.dll, icuuc34.dll, libicudecnumber.dll, souboru librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- Potřebné porty používané pro připojení k serveru SAP jsou povolené na místní prostředí IR počítače, které jsou obvykle port 3300 a 3201.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor SAP Business Warehouse.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SAP Business Warehouse (BW) propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **SapBw** | Ano |
| server | Název serveru, na kterém se nachází instance SAP BW. | Ano |
| systemNumber | Číslo systému systému SAP BW.<br/>Povolené hodnoty: dvě číslice desetinné číslo reprezentované jako řetězec. | Ano |
| ID klienta | ID klienta v systému SAP W klienta.<br/>Povolené hodnoty: tři číslice desetinné číslo reprezentované jako řetězec. | Ano |
| uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP. | Ano |
| heslo | Heslo pro tohoto uživatele. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Modul Integration Runtime je povinný, jak je uvedeno v [požadavky](#prerequisites). |Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje datové sady SAP BW.

Ke zkopírování dat z SAP BW, nastavte vlastnost typ datové sady na **RelationalTable**. Přestože nejsou žádné vlastnosti specifické pro typ. podporované pro SAP BW datové sady typu RelationalTable.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporována zdroji SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW jako zdroj

Ke zkopírování dat z SAP BW, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **RelationalSource** | Ano |
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

Kopírování dat ze SAP BW, se používají následující mapování z datových typů SAP BW na Azure Data Factory dočasné datové typy. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Typ dat SAP BW | Data factory dočasné datový typ |
|:--- |:--- |
| ACCP | Int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| NEZPRACOVANÉ | Byte[] |
| RAWSTRING | Byte[] |
| ŘETĚZEC | String |
| JEDNOTKA | String |
| DAT | String |
| NUMC | String |
| TIMS | String |


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
