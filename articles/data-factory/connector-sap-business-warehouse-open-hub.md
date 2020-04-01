---
title: Kopírování dat z obchodního skladu SAP přes Open Hub
description: Zjistěte, jak kopírovat data z SAP Business Warehouse (BW) přes Open Hub do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.date: 03/24/2020
ms.openlocfilehash: b905c75e920577e46017caeb456f8237421086b2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421207"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopírování dat z obchodního skladu SAP přes Open Hub pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z obchodního skladu SAP (BW) přes Open Hub. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

>[!TIP]
>Informace o celkové podpoře ADF ve scénáři integrace dat SAP najdete v článku [integrace dat SAP pomocí whitepaper Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobným úvodem, porovnáním a pokyny.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento SAP Business Warehouse přes open hub konektor je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z OBCHODNÍHO SKLADU SAP můžete kopírovat přes Open Hub do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor SAP Business Warehouse Open Hub podporuje:

- SAP Business Warehouse **verze 7.01 nebo vyšší (v nedávném zásobníku balíčku podpory SAP vydaném po roce 2015)**.
- Kopírování dat přes Open Hub Cílové místní tabulky, které pod může být DSO, InfoCube, MultiProvider, DataSource, atd.
- Kopírování dat pomocí základního ověřování.
- Připojení k aplikačnímu serveru.

## <a name="sap-bw-open-hub-integration"></a>Integrace otevřených rozbočovačů SAP BW 

[Služba SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) je efektivní způsob, jak extrahovat data z SAP BW. Následující diagram znázorňuje jeden z typických toků, které mají zákazníci ve svém systému SAP, v takovém případě toky dat z SAP ECC -> PSA -> DSO -> cube.

SAP BW Open Hub Destination (OHD) definuje cíl, ke kterému jsou přenášena data SAP. Všechny objekty podporované procesem přenosu dat SAP (DTP) lze použít jako otevřené zdroje dat rozbočovače, například DSO, InfoCube, DataSource atd. Typ Open Hub Destination – kde jsou přenášená data uložena – mohou být databázové tabulky (místní nebo vzdálené) a ploché soubory. Tento konektor SAP BW Open Hub podporuje kopírování dat z místní tabulky OHD v BW. V případě, že používáte jiné typy, můžete se přímo připojit k databázi nebo souborovésoustavě pomocí jiných konektorů.

![Otevřený rozbočovač SAP BW](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Delta extrakční tok

ADF SAP BW Open Hub Connector `excludeLastRequest` `baseRequestId` nabízí dvě volitelné vlastnosti: a které lze použít ke zpracování rozdílového zatížení z Open Hub. 

- **excludeLastRequestId**: Zda vyloučit záznamy posledního požadavku. Výchozí hodnota je true. 
- **baseRequestId**: ID požadavku na rozdílové zatížení. Jakmile je nastavena, budou načtena pouze data s requestId větší než hodnota této vlastnosti. 

Celkově extrakce z SAP InfoProviders do Azure Data Factory (ADF) se skládá ze dvou kroků: 

1. **Proces přenosu dat SAP BW (DTP)** Tento krok zkopíruje data z poskytovatele SAP BW InfoProvider do tabulky SAP BW Open Hub 

1. **Kopírování dat ADF** V tomto kroku je tabulka Otevřít rozbočovač přečtena konektorem ADF 

![Delta extrakční tok](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

V prvním kroku je spuštěn DTP. Každé spuštění vytvoří nové ID požadavku SAP. ID požadavku je uloženo v tabulce Otevřít rozbočovač a poté je použito konektorem ADF k identifikaci rozdílu. Dva kroky spustit asynchronně: DTP je spuštěn sap a kopie dat ADF se aktivuje prostřednictvím ADF. 

Ve výchozím nastavení adf nečte nejnovější delta z tabulky Otevřít hub (možnost "vyloučit poslední požadavek" je true). Tímto data v ADF není 100% aktuální s daty v tabulce Otevřít hub (poslední delta chybí). Na oplátku tento postup zajišťuje, že žádné řádky ztratí způsobené asynchronní extrakce. Funguje dobře, i když ADF čte tabulku Open Hub, zatímco DTP stále píše do stejné tabulky. 

Obvykle ukládáte maximální zkopírované ID požadavku v posledním spuštění adf v úložišti pracovních dat (například Objekt blob Azure ve výše uvedeném diagramu). Proto stejný požadavek není číst podruhé ADF v následném spuštění. Mezitím si všimněte, že data nejsou automaticky odstraněna z tabulky Otevřít centrum.

Pro správné rozdílové zpracování není povoleno mít ID žádosti z různých DTP ve stejné tabulce Open Hub. Proto nesmíte vytvořit více než jeden DTP pro každý cíl Open Hub (OHD). Při nutnosti úplné a delta extrakce ze stejného InfoProvider, měli byste vytvořit dva OHD pro stejné InfoProvider. 

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor SAP Business Warehouse Open Hub, musíte:

- Nastavte runtime integrace s vlastním hostitelem s verzí 3.13 nebo vyšší. Podrobnosti najdete v článku [runtime integrace s vlastním hostitelem.](create-self-hosted-integration-runtime.md)

- Stáhněte si **64bitový [konektor SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html) ** z webu SAP a nainstalujte jej do počítače ir hostovaného na vlastní síti. Při instalaci se v okně volitelné kroky nastavení ujistěte, že jste vybrali možnost **Instalovat sestavení do gac,** jak je znázorněno na následujícím obrázku. 

    ![Instalace konektoru SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Uživatel SAP používaný v konektoru BW datové továrny musí mít následující oprávnění: 

    - Autorizace pro RFC a SAP BW. 
    - Oprávnění k aktivitě "Spustit" autorizačního objektu "S_SDSAUTH".

- Vytvořte typ cíle otevřeného rozbočovače SAP jako **databázovou tabulku** se zaškrtnutou možností "Technický klíč".  Doporučujese také ponechat odstranění dat z tabulky jako nezaškrtnuté, i když to není nutné. Využijte DTP (přímo spustit nebo integrovat do existujícího řetězce procesů) k umístění dat ze zdrojového objektu (například datové krychle), který jste zvolili do otevřené cílové tabulky rozbočovače.

## <a name="getting-started"></a>Začínáme

> [!TIP]
>
> Návod k použití konektoru SAP BW Open Hub najdete v tématu [Načítání dat z SAP Business Warehouse (BW) pomocí Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor SAP Business Warehouse Open Hub.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu SAP Business Warehouse Open Hub jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **SapOpenHub** | Ano |
| server | Název serveru, na kterém se nachází instance SAP BW. | Ano |
| systemNumber | Systémové číslo systému SAP BW.<br/>Povolená hodnota: dvoumístné desetinné číslo reprezentované jako řetězec. | Ano |
| clientId | ID klienta v systému SAP W.<br/>Povolená hodnota: třímístné desetinné číslo reprezentované jako řetězec. | Ano |
| language | Jazyk, který používá systém SAP. | Ne (výchozí hodnota je **EN)**|
| userName | Jméno uživatele, který má přístup k serveru SAP. | Ano |
| heslo | Heslo pro tohoto uživatele. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Runtime integrace hostované samostatně je vyžadován, jak je uvedeno v [požadavky](#prerequisites). |Ano |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou SAP BW Open Hub.

Chcete-li kopírovat data z a do SAP BW Open Hub, nastavte vlastnost type datové sady na **SapOpenHubTable**. Podporovány jsou následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **SapOpenHubTable**.  | Ano |
| openHubDestinationName | Název cíle otevřeného centra, ze kterých chcete kopírovat data. | Ano |

Pokud jste `excludeLastRequest` nastavili a `baseRequestId` v datové sadě, je stále podporována jako-je, zatímco jste navrhl použít nový model ve zdroji aktivity do budoucna.

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem SAP BW Open Hub.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub jako zdroj

Chcete-li zkopírovat data z otevřeného rozbočovače SAP BW, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** zdroje aktivity kopírování musí být nastavena na **SapOpenHubSource**. | Ano |
| excludeLastRequest | Určuje, zda mají být vyloučeny záznamy posledního požadavku. | Ne (výchozí hodnota je **pravdivá)** |
| baseRequestId | ID požadavku na načtení delta. Jakmile je nastavena, budou načtena pouze data s requestId **větší než** hodnota této vlastnosti.  | Ne |

>[!TIP]
>Pokud vaše tabulka Open Hub obsahuje pouze data vygenerovaná jedním ID požadavku, například vždy provádíte úplné načtení a přepisování existujících dat v tabulce, nebo dtp spustíte pouze jednou pro test, nezapomeňte zrušit zaškrtnutí možnosti "excludeLastRequest", abyste data zkopírovali.

Chcete-li urychlit načítání dat, [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) můžete nastavit na aktivitu kopírování načíst data z SAP BW Open Hub paralelně. Pokud například nastavíte `parallelCopies` na čtyři, Data Factory současně provede čtyři volání RFC a každé volání RFC načte část dat z tabulky SAP BW Open Hub rozdělené na ID požadavku DTP a ID balíčku. To platí v případě, že počet jedinečných ID požadavku DTP `parallelCopies`+ ID balíčku je větší než hodnota . Při kopírování dat do úložiště dat založeného na souborech je také přikázáno zapisovat do složky jako více souborů (pouze zadejte název složky), v takovém případě je výkon lepší než zápis do jednoho souboru.

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

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapování datových typů pro otevřený rozbočovač SAP BW

Při kopírování dat z OTEVŘENÉHO ROZBOČOVAČE SAP BW se z datových typů SAP BW do dočasných datových typů Azure Data Factory používají následující mapování. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, najdete v tématu [mapování schématu a datových typů.](copy-activity-schema-and-type-mapping.md)

| Typ SAP ABAP | Dočasný datový typ datové továrny |
|:--- |:--- |
| C (řetězec) | Řetězec |
| I (celé číslo) | Int32 |
| F (Plovoucí) | Double |
| D (datum) | Řetězec |
| T (čas) | Řetězec |
| P (BCD baleny, měna, desetinné číslo, množství) | Desetinné číslo |
| N (N) | Řetězec |
| X (binární a nezpracované) | Řetězec |

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

**Příznaky:** Pokud používáte SAP BW na HANA a sledovat pouze podmnožinu dat je zkopírován přes pomocí aktivity kopírování ADF (1 milion řádků), možnou příčinou je, že povolíte možnost "SAP HANA Spuštění" v DTP, v takovém případě ADF můžete načíst pouze první dávku dat.

**Rozlišení:** Zakažte možnost "SAP HANA Execution" v DTP, znovu zpracujte data a zkuste znovu spustit aktivitu kopírování.

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
