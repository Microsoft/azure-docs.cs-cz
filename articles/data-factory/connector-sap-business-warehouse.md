---
title: Kopírování dat z SAP BW pomocí Azure Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data ze SAP Business Warehouse do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 4ee9048202a5953ff27ea83cfb39cb1af0739e7a
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277509"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopírování dat ze SAP Business Warehouse pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Aktuální verze](connector-sap-business-warehouse.md)

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z SAP Business Warehouse (ČERNOBÍLe). Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

>[!TIP]
>Pokud chcete získat přehled o celkové podpoře pro integraci dat přes ADF, přečtěte si článek [integrace dat SAP pomocí Azure Data Factory dokumentu White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobnými pokyny k úvodu, comparsion a pokyny.

## <a name="supported-capabilities"></a>Podporované funkce

Data z SAP Business Warehouse můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SAP Business Warehouse podporuje:

- SAP Business Warehouse **verze 7. x**.
- Kopírování dat z **InfoCubes a QueryCubes** (včetně dotazů BEx) pomocí dotazů MDX.
- Kopírování dat pomocí základního ověřování.

## <a name="prerequisites"></a>Požadavky

K použití tohoto konektoru SAP Business Warehouse potřebujete:

- Nastavte Integration Runtime pro místní hostování. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.
- Na Integration Runtime počítač nainstalujte **knihovnu SAP NetWeaver** . Knihovnu SAP NetWeaver Library můžete získat od správce SAP nebo přímo z [webu SAP software Download Center](https://support.sap.com/swdc). Vyhledejte **#1025361 poznámky SAP** a získejte umístění pro stažení nejnovější verze. Ujistěte se, že jste vybrali **64** knihovnu SAP NetWeaver, která odpovídá vaší instalaci Integration runtime. Pak nainstalujte všechny soubory zahrnuté v sadě SAP NetWeaver RFC SDK podle poznámky SAP. Knihovna SAP NetWeaver Library je také součástí instalace klientských nástrojů SAP.

>[!TIP]
>Pokud chcete řešit potíže s připojením SAP BW, ujistěte se, že:
>- Všechny knihovny závislostí extrahované ze sady NetWeaver RFC SDK jsou umístěné ve složce%Windir%\System32. Obvykle má icudt34. dll, icuin34. dll, icuuc34. dll, libicudecnumber. dll, librfc32. dll, libsapucum. dll, sapcrypto. dll, sapcryto_old. dll, sapnwrfc. dll.
>- Potřebné porty používané pro připojení k serveru SAP jsou povoleny v místním počítači IR, což jsou obvykle porty 3300 a 3201.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor SAP Business Warehouse.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu SAP Business Warehouse (ČERNOBÍLe) jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **SapBw** | Ano |
| Server | Název serveru, na kterém se nachází instance SAP BW. | Ano |
| systemNumber | Číslo systému SAP BW systému<br/>Povolená hodnota: dvoumístné desetinné číslo reprezentované jako řetězec. | Ano |
| clientId | ID klienta klienta v systému SAP W.<br/>Povolená hodnota: desítkové číslo se třemi číslicemi reprezentované jako řetězec. | Ano |
| userName | Jméno uživatele, který má přístup k serveru SAP. | Ano |
| password | Heslo pro tohoto uživatele. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Integration Runtime v místním prostředí se vyžaduje, jak je uvedeno v [požadavcích](#prerequisites). |Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných sadou SAP BW DataSet.

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

Pokud jste `RelationalTable` používali typovou datovou sadu, je stále podporovaná tak, jak je, a až budete chtít začít používat novinku dál.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností podporovaných zdrojem SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW jako zdroj

Chcete-li kopírovat data z SAP BW, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **SapBwSource** | Ano |
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

Pokud jste používali `RelationalSource` typový zdroj, je stále podporován tak, jak je, a když jste navrhli začít používat nový.

## <a name="data-type-mapping-for-sap-bw"></a>Mapování datových typů pro SAP BW

Při kopírování dat z SAP BW se z SAP BW datových typů používají následující mapování pro Azure Data Factory dočasných datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| SAP BW datový typ | Data factory dočasné datový typ |
|:--- |:--- |
| ACCP | Int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| 18.12 | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| JAZYK | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| ZÍSKÁNÍ | Byte[] |
| RAWSTRING | Byte[] |
| ŘETEZCE | String |
| UNIT | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
