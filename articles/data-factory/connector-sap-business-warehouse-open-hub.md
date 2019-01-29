---
title: Kopírování dat z řešení SAP Business Warehouse prostřednictvím Open centra pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data ze SAP Business Warehouse (BW) prostřednictvím Open rozbočovač do úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: jingwang
ms.openlocfilehash: d20ba372a23d2d4865bd9d6c5c004f955c896201
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55199021"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopírování dat z řešení SAP Business Warehouse prostřednictvím Open centra pomocí služby Azure Data Factory

Tento článek ukazuje, jak použít aktivitu kopírování ke kopírování dat z SAP Business Warehouse (BW) prostřednictvím Open centra ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub Integration 

[SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) je účinný způsob, jak extrahovat data z SAP BW. Následující diagram znázorňuje jeden z typických toků, zákazníci mají v jejich systému SAP, v jaké velikosti písmen datové toky z SAP ECC -> PSA -> DSO -> datové krychle.

SAP BW Open Centrum cílový (OHD) definuje cíl, do které se předá SAP data. Všechny objekty podporovány službou SAP Data Transfer procesu (DTP) může sloužit jako otevřete Centrum zdrojů dat, například DSO InfoCube, MultiProvider, zdroj dat, atd. Otevřete Centrum cílový typ – přenášená data se mají ukládat – mohou být databázových tabulek (místní nebo vzdálené) a plochých souborů. Tato podpora konektoru SAP BW Open centra kopírování dat z tabulky OHD místní BW. V případě, že používáte jiné typy, můžou přímo připojit k databázi nebo souboru systému pomocí jiné konektory.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z řešení SAP Business Warehouse prostřednictvím Open rozbočovač do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SAP Business Warehouse otevřete Centrum podporuje:

- SAP Business Warehouse **verze 7.30 nebo novější s SAPK 73013INPIBASIS nainstalovaný balíček pro podporu**.
- Kopírování dat přes otevřít Centrum cílový místní tabulky, který pod může být DSO InfoCube, MultiProvider, zdroj dat, atd.
- Kopírování dat pomocí základního ověřování.
- Připojení k aplikační Server.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor SAP Business Warehouse, je potřeba:

- Nastavte modul Integration Runtime s verzí 3.13 nebo vyšší. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.

- Stáhněte si **64-bit [3.0 konektoru SAP .NET](https://support.sap.com/en/product/connectors/msnet.html)**  z webu SAP a nainstalujte ho na místní prostředí IR počítače. Při instalaci, v okně kroky volitelné nastavení, ujistěte se, že jste vybrali **nainstalovat sestavení do GAC** možnosti, jak je znázorněno na následujícím obrázku. 

    ![Instalace konektoru SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Uživatel systému SAP používá ve službě Data Factory BW connector musí mít následující oprávnění: 

    - Autorizace RFC a SAP BW. 
    - Oprávnění k autorizace objektu "S_SDSAUTH" aktivity "Spustit".

- Craete SAP otevřít Centrum cílový typ jako **databázové tabulky** se zaškrtnutým políčkem "Technické klíč".  Doporučujeme také ponechat Data odstranění z tabulky jako zaškrtnuté políčko, i když není potřeba. Spustit DTP pro příjem dat ze zdrojového objektu (například datové krychle) jste se rozhodli otevřete Centrum cílové tabulky.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor SAP Business Warehouse.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SAP Business Warehouse (BW) propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **SapOpenHub** | Ano |
| server | Název serveru, na kterém se nachází instance SAP BW. | Ano |
| systemNumber | Číslo systému systému SAP BW.<br/>Povolené hodnoty: dvě číslice desetinné číslo reprezentované jako řetězec. | Ano |
| ID klienta | ID klienta v systému SAP W klienta.<br/>Povolené hodnoty: tři číslice desetinné číslo reprezentované jako řetězec. | Ano |
| uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP. | Ano |
| heslo | Heslo pro tohoto uživatele. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Modul Integration Runtime je povinný, jak je uvedeno v [požadavky](#prerequisites). |Ano |

**Příklad:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností podporovaných datovou sadu Salesforce.

Pro kopírování dat z a do SAP BW Open centra, nastavte vlastnost typ datové sady na **SapOpenHubTable**. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **SapOpenHubTable**.  | Ano |
| openHubDestinationName | Název otevřít Centrum cílový ke kopírování dat z. | Ano |
| excludeLastRequest | Jestli se mají vyloučit záznamy poslední žádosti. | Ne (výchozí hodnota je **true**) |
| baseRequestId | ID požadavku pro rozdílové načtení. Jakmile se nastaví, jenom data s ID žádosti **větší než** načte se hodnota této vlastnosti.  | Ne |

>[!TIP]
>Pokud otevřete Centrum tabulka obsahuje pouze data generovaná ID jedné žádosti, například vždy úplný zatížení a přepsání existujících dat v tabulce, nebo pouze spustit DTP jednou pro test, nezapomeňte zrušit zaškrtnutí políčka "excludeLastRequest" aby bylo možné kopírovat d ATA navýšení kapacity.

**Příklad:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporována zdroji SAP BW.

### <a name="sap-bw-open-hub-as-source"></a>Otevřete Centrum SAP BW jako zdroj

Ke zkopírování dat z centra otevřít SAP BW, nastavte typ zdroje v aktivitě kopírování do **SapOpenHubSource**. Přestože nejsou žádné další vlastnosti specifické pro typ. potřebné v aktivitě kopírování **zdroj** oddílu.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapování datového typu pro SAP BW Open Centrum

Kopírování dat ze SAP BW, se používají následující mapování z datových typů SAP BW na Azure Data Factory dočasné datové typy. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Typ ABAP SAP | Data factory dočasné datový typ |
|:--- |:--- |
| C (String) | Řetězec |
| Můžu (celé číslo) | Datový typ Int32 |
| F (Float) | Double |
| D (Date) | Řetězec |
| T (čas) | Řetězec |
| P (konfiguračních dat spouštění, balení, Měna, Decimal, množství) | Desítkově |
| N (Numc) | Řetězec |
| X (binární a Raw) | Řetězec |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
