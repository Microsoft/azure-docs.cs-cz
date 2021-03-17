---
title: Kopírování dat z SAP Business Warehouse přes Open hub
description: Přečtěte si, jak kopírovat data z SAP Business Warehouse (ČERNOBÍLe) prostřednictvím otevřeného centra pro podporovaná úložiště dat jímky pomocí aktivity kopírování v Azure Data Factoryovém kanálu.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/02/2020
ms.openlocfilehash: b766ce248a3543ef3323e026d760e550a0e3dd75
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386675"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopírování dat z SAP Business Warehouse přes Open hub pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z SAP Business Warehouse (ČERNOBÍLého) prostřednictvím otevřeného centra. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

>[!TIP]
>Pokud chcete získat přehled o celkové podpoře pro integraci dat v programu, přečtěte si článek [integrace dat SAP pomocí Azure Data Factory dokumentu White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobným úvodem na jednotlivé konektory SAP, comparsion a doprovodné materiály.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento SAP Business Warehouse prostřednictvím konektoru open hub se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z SAP Business Warehouse můžete kopírovat prostřednictvím otevřeného centra pro všechna podporovaná úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně konektor Open hub pro SAP Business Warehouse podporuje:

- SAP Business Warehouse **verze 7,01 nebo vyšší (v nedávných balíčcích balíčku podpory SAP vydané po roce 2015)**. Tento konektor nepodporuje SAP BW/4HANA.
- Kopírování dat prostřednictvím místní tabulky v otevřeném centru, která se může nacházet na DSO, InfoCube, s více dodávajících, zdroji dat atd.
- Kopírování dat pomocí základního ověřování.
- Připojování k aplikačnímu serveru SAP nebo serveru zpráv SAP.
- Načítání dat prostřednictvím RFC.

## <a name="sap-bw-open-hub-integration"></a>SAP BW otevřít integraci centra 

[Služba SAP BW Open hub](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) je efektivní způsob, jak extrahovat data z SAP BW. Následující diagram znázorňuje jedno z typických toků zákazníků v jejich systému SAP. v takovém případě data přechází ze SAP ECC-> PSA-> datové krychle DSO->.

Cíl OHD (otevřít rozbočovač) definuje cíl, do kterého se přepřenosují data SAP. SAP BW Všechny objekty podporované procesy SAP Přenos dat Process (DTP) se dají použít jako otevřené zdroje dat hub, například DSO, InfoCube, DataSource atd. Otevřít cílový typ centra – kde jsou uložená data, můžou být databázové tabulky (místní nebo vzdálené) a ploché soubory. Tento SAP BW otevřít konektor konektoru podpora kopírování dat z místní tabulky OHD v ČERNOBÍLém prostředí. V případě, že používáte jiné typy, můžete se přímo připojit k databázi nebo systému souborů pomocí jiných konektorů.

![SAP BW otevření centra](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Průběh extrakce Delta

Konektor ADF SAP BW Open hub nabízí dvě volitelné vlastnosti: `excludeLastRequest` a `baseRequestId` můžete ho použít ke zpracování rozdílového zatížení z otevřeného centra. 

- **excludeLastRequestId**: Určuje, zda mají být vyloučeny záznamy poslední žádosti. Výchozí hodnota je true (pravda). 
- **baseRequestId**: ID žádosti o rozdílové načtení. Po nastavení budou načtena pouze data s identifikátorem requestId větším, než je hodnota této vlastnosti. 

Extrakce z SAP InfoProviders to Azure Data Factory (ADF) se skládá ze dvou kroků: 

1. **Proces Přenos dat SAP BW (DTP)** Tento krok zkopíruje data z SAP BW InfoProvider do tabulky SAP BW otevřít hub. 

1. **Kopie dat ADF** V tomto kroku přečte otevřená tabulka hub konektor ADF. 

![Průběh extrakce Delta](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

V prvním kroku se spustí DTP. Každé spuštění vytvoří nové ID požadavku SAP. ID žádosti je uloženo v otevřené tabulce centra a pak ji použije konektor ADF k identifikaci rozdílu. Dva kroky se spouštějí asynchronně: DTP se aktivuje pomocí SAP a v rámci ADF se aktivuje kopie dat ADF. 

Ve výchozím nastavení ADF nečte poslední rozdíl z otevřené tabulky hub (možnost "vyloučit poslední žádost" je true). V případě, že data v ADF neobsahují 100%, data v otevřené tabulce hub (poslední Delta chybí). V rámci tohoto postupu zajišťuje tento postup, aby se žádné řádky neztratily kvůli asynchronní extrakci. Funguje správně i v případě, že ADF čte otevřenou tabulku hub, zatímco DTP stále zapisuje do stejné tabulky. 

V pracovním úložišti dat (jako je Azure Blob ve výše uvedeném diagramu) obvykle ukládáte maximální zkopírované ID žádosti v posledním spuštění pomocí ADF. Proto se stejný požadavek během následného spuštění v rámci ADF nepřečte podruhé. Mezitím si všimněte, že data se v otevřené tabulce hub automaticky neodstraní.

Pro správné zpracování rozdílů není povoleno mít ID žádosti z různých DTPs ve stejné otevřené tabulce hub. Proto nesmíte pro každé místo v otevřeném centru (OHD) vytvořit více než jednu DTP. Pokud budete potřebovat úplnou a rozdílovou extrakci ze stejného InfoProvider, měli byste vytvořit dvě OHDs pro stejné InfoProvider. 

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor pro SAP Business Warehouse, musíte:

- Nastavte Integration Runtime v místním prostředí s verzí 3,13 nebo vyšší. Podrobnosti najdete v článku [Integration runtime](create-self-hosted-integration-runtime.md) v místním prostředí.

- Stáhněte si **64-bit [SAP .net Connector 3,0](https://support.sap.com/en/product/connectors/msnet.html)** z webu SAP a nainstalujte ho na místně hostovaný počítač IR. Při instalaci nástroje v okně volitelné kroky instalace se ujistěte, že jste vybrali možnost **instalovat sestavení do GAC** , jak je znázorněno na následujícím obrázku. 

    ![Nainstalovat konektor SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Uživatel SAP používaný v konektoru Data Factoryá ČERNOBÍLé připojení musí mít následující oprávnění: 

    - Autorizace pro RFC a SAP BW. 
    - Oprávnění pro aktivitu Execute objektu autorizace S_SDSAUTH.

- Vytvoří cílový typ pro SAP otevřený rozbočovač jako **databázovou tabulku** se zaškrtnutou možností "technický klíč".  Doporučuje se také nechat odstranit data z tabulky jako nezaškrtnutá, i když se nevyžaduje. Použijte DTP (přímo spouštěný nebo integrovaný do stávajícího řetězce procesu) k vytvoření pozemku dat ze zdrojového objektu (například datové krychle), který jste zvolili v otevřené cílové tabulce centra.

## <a name="getting-started"></a>Začínáme

> [!TIP]
>
> Návod, jak pomocí SAP BW otevřít konektor centra, najdete v tématu [načtení dat z SAP Business Warehouse (černobíle) pomocí Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech najdete podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro SAP Business Warehouse Open hub Connector.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu SAP Business Warehouse se podporují následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **SapOpenHub** . | Yes |
| server | Název serveru, na kterém se nachází instance SAP BW. | Yes |
| systemNumber | Číslo systému SAP BW systému<br/>Povolená hodnota: dvoumístné desetinné číslo reprezentované jako řetězec. | Yes |
| messageServer | Název hostitele serveru zpráv SAP.<br/>Slouží k připojení k serveru zpráv SAP. | No |
| messageServerService | Název služby nebo číslo portu serveru zpráv.<br/>Slouží k připojení k serveru zpráv SAP. | No |
| systemId | ID systému SAP, ve kterém je tabulka umístěna.<br/>Slouží k připojení k serveru zpráv SAP. | No |
| přihlášená | Přihlašovací skupina pro systém SAP.<br/>Slouží k připojení k serveru zpráv SAP. | No |
| clientId | ID klienta klienta v systému SAP W.<br/>Povolená hodnota: desítkové číslo se třemi číslicemi reprezentované jako řetězec. | Yes |
| language | Jazyk používaný systémem SAP. | Ne (výchozí hodnota je **EN**)|
| userName | Jméno uživatele, který má přístup k serveru SAP. | Yes |
| heslo | Heslo pro tohoto uživatele. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Integration Runtime v místním prostředí se vyžaduje, jak je uvedeno v [požadavcích](#prerequisites). |Yes |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou SAP BW Open hub.

Chcete-li kopírovat data z a do SAP BW otevřít centrum, nastavte vlastnost Type objektu DataSet na **SapOpenHubTable**. Podporovány jsou následující vlastnosti.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na **SapOpenHubTable**.  | Yes |
| openHubDestinationName | Název otevřeného cíle centra, ze kterého se mají kopírovat data | Yes |

Pokud jste nacházeli `excludeLastRequest` a `baseRequestId` v datové sadě, je stále podporováno tak, jak jsou, a Vy jste navrženi použít nový model ve zdroji aktivity.

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností, které SAP BW Open hub source podporovat.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW otevřít centrum jako zdroj

Pokud chcete kopírovat data z SAP BW otevřete centrum, v části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **SapOpenHubSource**. | Yes |
| excludeLastRequest | Určuje, zda mají být vyloučeny záznamy poslední žádosti. | Ne (výchozí hodnota je **true**) |
| baseRequestId | ID požadavku pro rozdílové načtení. Po nastavení budou načtena pouze data s identifikátorem requestId **větším, než** je hodnota této vlastnosti.  | No |

>[!TIP]
>Pokud otevřená tabulka centra obsahuje jenom data generovaná IDENTIFIKÁTORem jediného požadavku, například vždy stačí úplné načtení a přepsat stávající data v tabulce, nebo když pro test spustíte jenom DTP, nezapomeňte zrušit kontrolu možnosti excludeLastRequest, aby se data zkopírovala.

Pro urychlení načítání dat můžete [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) u aktivity kopírování nastavit, aby se data načetla z SAP BW otevřeného centra paralelně. Pokud jste například nastavili `parallelCopies` na čtyři, Data Factory souběžně spouští čtyři volání RFC a každé volání RFC načte část dat z SAP BW otevřené tabulky hub rozdělené podle ID žádosti DTP a ID balíčku. To platí v případě, že počet jedinečných ID žádosti DTP + ID balíčku je větší než hodnota `parallelCopies` . Při kopírování dat do úložiště dat založeného na souborech je také znovu zaškrtnuto, aby bylo možné zapisovat do složky jako více souborů (pouze název složky). v takovém případě je výkon lepší než zápis do jednoho souboru.

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

Při kopírování dat z SAP BW otevřeném centru, se z SAP BW datových typů používají následující mapování pro Azure Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování typů schématu a dat](copy-activity-schema-and-type-mapping.md) .

| Typ SAP ABAP | Typ dat interim Data Factory |
|:--- |:--- |
| C (řetězec) | Řetězec |
| I (celé číslo) | Int32 |
| F (float) | dvojité |
| D (datum) | Řetězec |
| T (čas) | Řetězec |
| P (komprimovaná BCD, měna, desetinné číslo, množství) | Decimal |
| N (NUMC) | Řetězec |
| X (binární a hrubá) | Řetězec |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

**Příznaky:** Pokud používáte SAP BW v HANA a při použití aktivity kopírování ADF (1 000 000 řádků) se kopíruje jenom podmnožina dat, může to být tím, že v DTP povolíte možnost "spuštění SAP HANA". v takovém případě může ADF načíst jenom první dávku dat.

**Řešení:** Zakažte v DTP možnost spuštění SAP HANA, znovu zpracujte data a pak zkuste aktivitu kopírování znovu spustit.

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
