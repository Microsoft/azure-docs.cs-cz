---
title: Kopírování dat ze SAP Business Warehouse přes Open hub pomocí Azure Data Factory | Microsoft Docs
description: Přečtěte si, jak kopírovat data z SAP Business Warehouse (ČERNOBÍLe) prostřednictvím otevřeného centra pro podporovaná úložiště dat jímky pomocí aktivity kopírování v Azure Data Factoryovém kanálu.
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
ms.openlocfilehash: d82f843cb5cdd7b910c734f26a93144374061b74
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274501"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopírování dat z SAP Business Warehouse přes Open hub pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z SAP Business Warehouse (ČERNOBÍLého) prostřednictvím otevřeného centra. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

>[!TIP]
>Pokud chcete získat přehled o celkové podpoře pro integraci dat přes ADF, přečtěte si článek [integrace dat SAP pomocí Azure Data Factory dokumentu White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobnými pokyny k úvodu, comparsion a pokyny.

## <a name="supported-capabilities"></a>Podporované funkce

Data z SAP Business Warehouse můžete kopírovat prostřednictvím otevřeného centra pro všechna podporovaná úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně konektor Open hub pro SAP Business Warehouse podporuje:

- SAP Business Warehouse **verze 7,01 nebo vyšší (v nedávných balíčcích balíčku podpory SAP vydané po roce 2015)** .
- Kopírování dat prostřednictvím místní tabulky Open hub, která je v této části může být DSO, InfoCube, s více dodanými, zdroji dat atd.
- Kopírování dat pomocí základního ověřování.
- Připojování k aplikačnímu serveru.

## <a name="sap-bw-open-hub-integration"></a>SAP BW otevřít integraci centra 

[Služba SAP BW Open hub](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) je efektivní způsob, jak extrahovat data z SAP BW. Následující diagram znázorňuje jedno z typických toků zákazníků v jejich systému SAP. v takovém případě data přechází ze SAP ECC-> PSA-> datové krychle DSO->.

Cíl OHD (otevřít rozbočovač) definuje cíl, do kterého se přepřenosují data SAP. SAP BW Všechny objekty podporované procesy SAP Přenos dat Process (DTP) se dají použít jako otevřené zdroje dat hub, například DSO, InfoCube, DataSource atd. Otevřít cílový typ centra – kde jsou uložená data, můžou být databázové tabulky (místní nebo vzdálené) a ploché soubory. Tento SAP BW otevřít konektor konektoru podpora kopírování dat z místní tabulky OHD v ČERNOBÍLém prostředí. V případě, že používáte jiné typy, můžete se přímo připojit k databázi nebo systému souborů pomocí jiných konektorů.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Průběh extrakce Delta

Konektor ADF SAP BW Open hub nabízí dvě volitelné vlastnosti: `excludeLastRequest` a `baseRequestId` můžete ho použít ke zpracování rozdílového zatížení z otevřeného centra. 

- **excludeLastRequestId**: Určuje, zda mají být vyloučeny záznamy poslední žádosti. Výchozí hodnota je true. 
- **baseRequestId**: ID požadavku pro rozdílové načtení. Po nastavení budou načtena pouze data s identifikátorem requestId větším, než je hodnota této vlastnosti. 

Extrakce z SAP InfoProviders to Azure Data Factory (ADF) se skládá ze dvou kroků: 

1. **Proces Přenos dat SAP BW (DTP)** Tento krok zkopíruje data z SAP BW InfoProvider do tabulky SAP BW otevřít hub. 

1. **Kopie dat ADF** V tomto kroku přečte otevřená tabulka hub konektor ADF. 

![Průběh extrakce Delta](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

V prvním kroku se spustí DTP. Každé spuštění vytvoří nové ID požadavku SAP. ID žádosti je uloženo v otevřené tabulce centra a pak ji použije konektor ADF k identifikaci rozdílu. Dva kroky se spouštějí asynchronně: DTP se aktivuje pomocí SAP a v rámci ADF se aktivuje kopie dat ADF. 

Ve výchozím nastavení ADF nečte poslední rozdíl z otevřené tabulky hub (možnost "vyloučit poslední žádost" je true). V tomto případě nejsou data v ADF 100% v aktuálním stavu s daty v otevřené tabulce hub (poslední Delta chybí). V rámci tohoto postupu zajišťuje tento postup, aby se žádné řádky neztratily kvůli asynchronní extrakci. Funguje správně i v případě, že ADF čte otevřenou tabulku hub, zatímco DTP stále zapisuje do stejné tabulky. 

V pracovním úložišti dat (jako je Azure Blob ve výše uvedeném diagramu) obvykle ukládáte maximální zkopírované ID žádosti v posledním spuštění pomocí ADF. Proto se stejný požadavek během následného spuštění v rámci ADF nepřečte podruhé. Mezitím si všimněte, že data se v otevřené tabulce hub automaticky neodstraní.

Pro správné zpracování rozdílů není povoleno mít ID žádosti z různých DTPs ve stejné otevřené tabulce hub. Proto nesmíte pro každé místo v otevřeném centru (OHD) vytvořit více než jednu DTP. Pokud budete potřebovat úplnou a rozdílovou extrakci ze stejného InfoProvider, měli byste vytvořit dvě OHDs pro stejné InfoProvider. 

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor pro SAP Business Warehouse, musíte:

- Nastavte Integration Runtime v místním prostředí s verzí 3,13 nebo vyšší. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.

- Stáhněte si **64-bit [SAP .net Connector 3,0](https://support.sap.com/en/product/connectors/msnet.html)**  z webu SAP a nainstalujte ho na místně hostovaný počítač IR. Při instalaci nástroje v okně volitelné kroky instalace se ujistěte, že jste vybrali možnost **instalovat sestavení do GAC** , jak je znázorněno na následujícím obrázku. 

    ![Nainstalovat konektor SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Uživatel SAP používaný v konektoru Data Factoryá ČERNOBÍLé připojení musí mít následující oprávnění: 

    - Autorizace pro RFC a SAP BW. 
    - Oprávnění "Execute" aktivity objektu autorizace "S_SDSAUTH".

- Vytvoří cílový typ pro SAP otevřený rozbočovač jako **databázovou tabulku** se zaškrtnutou možností "technický klíč".  Doporučuje se také nechat odstranit data z tabulky jako nezaškrtnutá, i když se nevyžaduje. Využijte DTP (přímo spouštěný nebo integrovaný do stávajícího řetězce procesu) a využívejte data ze zdrojového objektu (například datové krychle), který jste zvolili v otevřené cílové tabulce centra.

## <a name="getting-started"></a>Začínáme

> [!TIP]
>
> Návod, jak pomocí SAP BW otevřít konektor centra, najdete v tématu [načtení dat z SAP Business Warehouse (černobíle) pomocí Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech najdete podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro SAP Business Warehouse Open hub Connector.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu SAP Business Warehouse se podporují následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **SapOpenHub** | Ano |
| Server | Název serveru, na kterém se nachází instance SAP BW. | Ano |
| systemNumber | Číslo systému SAP BW systému<br/>Povolená hodnota: dvoumístné desetinné číslo reprezentované jako řetězec. | Ano |
| clientId | ID klienta klienta v systému SAP W.<br/>Povolená hodnota: desítkové číslo se třemi číslicemi reprezentované jako řetězec. | Ano |
| jazyk | Jazyk používaný systémem SAP. | Ne (výchozí hodnota je **EN**)|
| userName | Jméno uživatele, který má přístup k serveru SAP. | Ano |
| password | Heslo pro tohoto uživatele. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Integration Runtime v místním prostředí se vyžaduje, jak je uvedeno v [požadavcích](#prerequisites). |Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných datovou sadou SAP BW Open hub.

Chcete-li kopírovat data z a do SAP BW otevřít centrum, nastavte vlastnost Type objektu DataSet na **SapOpenHubTable**. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **SapOpenHubTable**.  | Ano |
| openHubDestinationName | Název otevřeného cíle centra, ze kterého se mají kopírovat data | Ano |

Pokud jste `excludeLastRequest` nacházeli `baseRequestId` a v datové sadě, je stále podporováno tak, jak jsou, a Vy jste navrženi použít nový model ve zdroji aktivity.

**Příklad:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností, které SAP BW Open hub source podporovat.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW otevřít centrum jako zdroj

Pokud chcete kopírovat data z SAP BW otevřete centrum, v části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **SapOpenHubSource**. | Ano |
| excludeLastRequest | Určuje, zda mají být vyloučeny záznamy poslední žádosti. | Ne (výchozí hodnota je **true**) |
| baseRequestId | ID požadavku pro rozdílové načtení. Po nastavení budou načtena pouze data s identifikátorem requestId **větším, než** je hodnota této vlastnosti.  | Ne |

>[!TIP]
>Pokud vaše otevřená tabulka centra obsahuje jenom data generovaná IDENTIFIKÁTORem jediného požadavku, například vždy stačí úplné načtení a přepsat stávající data v tabulce, nebo pokud pro test spustíte jenom DTP jenom jednou, nezapomeňte zrušit kontrolu možnosti "excludeLastRequest", aby bylo možné zkopírovat d. ATA.

Pro urychlení načítání dat můžete u aktivity kopírování nastavit [`parallelCopies`](copy-activity-performance.md#parallel-copy) , aby se data načetla z SAP BW otevřeného centra paralelně. Pokud jste například nastavili `parallelCopies` na čtyři, Data Factory souběžně spouští čtyři volání RFC a každé volání RFC načte část dat z SAP BW otevřené tabulky hub rozdělené podle ID žádosti DTP a ID balíčku. To platí v případě, že počet jedinečných ID žádosti DTP + ID balíčku je větší než hodnota `parallelCopies`. Při kopírování dat do úložiště dat založeného na souborech je také znovu zaškrtnuto, aby bylo možné zapisovat do složky jako více souborů (pouze název složky). v takovém případě je výkon lepší než zápis do jednoho souboru.

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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapování datových typů pro SAP BW otevření centra

Při kopírování dat z SAP BW otevřeném centru, se z SAP BW datových typů používají následující mapování pro Azure Data Factory dočasných datových typů. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Typ SAP ABAP | Data factory dočasné datový typ |
|:--- |:--- |
| C (řetězec) | Řetězec |
| I (integer) | Int32 |
| F (float) | Double |
| D (datum) | Řetězec |
| T (Time) | Řetězec |
| P (BCD Packed, Currency, Decimal, Qty) | Decimal |
| N (NUMC) | Řetězec |
| X (Binary and Raw) | Řetězec |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
