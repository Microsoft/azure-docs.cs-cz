---
title: Kopírování dat z tabulky SAP
description: Zjistěte, jak zkopírovat data z tabulky SAP do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: 44efd0d402d6cb9f1ee44c583c88140121ca3001
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011612"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Kopírování dat z tabulky SAP pomocí Azure Data Factory

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat z tabulky SAP. Další informace naleznete v [tématu Kopírování přehledu aktivit](copy-activity-overview.md).

>[!TIP]
>Informace o celkové podpoře ADF ve scénáři integrace dat SAP najdete v článku [integrace dat SAP pomocí whitepaper Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobným úvodem, porovnáním a pokyny.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor tabulky SAP je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z tabulky SAP můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivity kopírování, naleznete v tabulce [Podporovaná data úložiště.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor tabulky SAP podporuje:

- Kopírování dat z tabulky SAP v:

  - SAP ERP Central Component (SAP ECC) verze 7.01 nebo novější (v nedávném zásobníku balíčku podpory SAP vydaném po roce 2015).
  - SAP Business Warehouse (SAP BW) verze 7.01 nebo novější (v nedávném zásobníku balíčku podpory SAP vydaném po 2015).
  - SAP S/4HANA.
  - Další produkty v SAP Business Suite verze 7.01 nebo novější (v nedávném balíčku podpory SAP, který byl vydán po roce 2015).

- Kopírování dat z transparentní tabulky SAP, sdružené tabulky, clusterované tabulky a zobrazení.
- Kopírování dat pomocí základního ověřování nebo zabezpečené síťové komunikace (SNC), pokud je nakonfigurováno SNC.
- Připojení k aplikačnímu serveru SAP nebo serveru zpráv SAP.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor tabulky SAP, musíte:

- Nastavte runtime integrace s vlastním hostitelem (verze 3.17 nebo novější). Další informace naleznete v [tématu Vytvoření a konfigurace prostředí runtime integrace](create-self-hosted-integration-runtime.md)s vlastním hostitelem .

- Stáhněte si 64bitový [konektor SAP pro Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) z webu SAP a nainstalujte jej do automatu runtime integrace s vlastním hostitelem. Během instalace nezapomeňte vybrat možnost **Instalovat sestavení do gac** v okně **Volitelné kroky nastavení.**

  ![Instalace konektoru SAP pro rozhraní .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Uživatel SAP, který je používán v konektoru tabulky DATA Factory SAP, musí mít následující oprávnění:

  - Autorizace pro použití cílů vzdáleného volání funkcí (RFC).
  - Oprávnění k aktivitě spuštění objektu autorizace S_SDSAUTH.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro konektor tabulky SAP.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu SAP BW Open Hub jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| `type` | Vlastnost `type` musí být `SapTable`nastavena na . | Ano |
| `server` | Název serveru, na kterém je umístěna instance SAP.<br/>Slouží k připojení k aplikačnímu serveru SAP. | Ne |
| `systemNumber` | Systémové číslo systému SAP.<br/>Slouží k připojení k aplikačnímu serveru SAP.<br/>Povolená hodnota: Dvoumístné desetinné číslo reprezentované jako řetězec. | Ne |
| `messageServer` | Název hostitele serveru zpráv SAP.<br/>Slouží k připojení k serveru zpráv SAP. | Ne |
| `messageServerService` | Název služby nebo číslo portu serveru zpráv.<br/>Slouží k připojení k serveru zpráv SAP. | Ne |
| `systemId` | ID systému SAP, kde je tabulka umístěna.<br/>Slouží k připojení k serveru zpráv SAP. | Ne |
| `logonGroup` | Přihlašovací skupina pro systém SAP.<br/>Slouží k připojení k serveru zpráv SAP. | Ne |
| `clientId` | ID klienta v systému SAP.<br/>Povolená hodnota: Třímístné desetinné číslo reprezentované jako řetězec. | Ano |
| `language` | Jazyk, který používá systém SAP.<br/>Výchozí hodnota `EN`je .| Ne |
| `userName` | Jméno uživatele, který má přístup k serveru SAP. | Ano |
| `password` | Heslo pro uživatele. Toto pole `SecureString` označte typem pro bezpečné uložení v datové továrně nebo [odkazujte na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| `sncMode` | Indikátor aktivace SNC pro přístup k serveru SAP, kde je tabulka umístěna.<br/>Použijte, pokud chcete použít SNC pro připojení k serveru SAP.<br/>Povolené hodnoty `0` jsou (vypnuto, `1` výchozí) nebo (zapnuto). | Ne |
| `sncMyName` | Název SNC iniciátora pro přístup k serveru SAP, kde je tabulka umístěna.<br/>Platí, `sncMode` když je zapnutý. | Ne |
| `sncPartnerName` | Název SNC komunikačního partnera pro přístup k serveru SAP, kde je tabulka umístěna.<br/>Platí, `sncMode` když je zapnutý. | Ne |
| `sncLibraryPath` | Knihovna externího bezpečnostního produktu pro přístup k serveru SAP, kde je tabulka umístěna.<br/>Platí, `sncMode` když je zapnutý. | Ne |
| `sncQop` | SNC kvalita ochrany úroveň použít.<br/>Platí, `sncMode` když je zapnuto. <br/>Povolené hodnoty `1` jsou `2` (ověřování), `3` (Integrita), (Ochrana osobních údajů), `8` (Výchozí), `9` (Maximum). | Ne |
| `connectVia` | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Je vyžadován osazený integrační runtime, jak je uvedeno dříve v [požadavky](#prerequisites). |Ano |

**Příklad 1: Připojení k aplikačnímu serveru SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
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

### <a name="example-2-connect-to-an-sap-message-server"></a>Příklad 2: Připojení k serveru zpráv SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Příklad 3: Připojení pomocí SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností pro definování datových sad naleznete v [tématu Datové sady](concepts-datasets-linked-services.md). V následující části naleznete seznam vlastností podporovaných datovou sadou tabulek SAP.

Chcete-li kopírovat data z a do propojené služby SAP BW Open Hub, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| `type` | Vlastnost `type` musí být `SapTableResource`nastavena na . | Ano |
| `tableName` | Název tabulky SAP pro kopírování dat. | Ano |

### <a name="example"></a>Příklad

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností pro definování aktivit naleznete v [tématu Pipelines](concepts-pipelines-activities.md). V následující části naleznete seznam vlastností podporovaných zdrojem tabulky SAP.

### <a name="sap-table-as-source"></a>Tabulka SAP jako zdroj

Chcete-li kopírovat data z tabulky SAP, jsou podporovány následující vlastnosti:

| Vlastnost                         | Popis                                                  | Požaduje se |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | Vlastnost `type` musí být `SapTableSource`nastavena na .         | Ano      |
| `rowCount`                         | Počet řádků, které mají být načteny.                              | Ne       |
| `rfcTableFields`                   | Pole (sloupce), která chcete zkopírovat z tabulky SAP. Například, `column0, column1`. | Ne       |
| `rfcTableOptions`                  | Možnosti filtrování řádků v tabulce SAP. Například, `COLUMN0 EQ 'SOMEVALUE'`. Viz také tabulka operátoru dotazu SAP dále v tomto článku. | Ne       |
| `customRfcReadTableFunctionModule` | Vlastní modul funkce RFC, který lze použít ke čtení dat z tabulky SAP.<br>Pomocí vlastního modulu funkce RFC můžete definovat, jak jsou data načtena ze systému SAP a vrácena do data factory. Modul vlastní funkce musí mít implementované rozhraní (import, export, `/SAPDS/RFC_READ_TABLE2`tabulky), které je podobné , což je výchozí rozhraní používané modulem Data Factory. | Ne       |
| `partitionOption`                  | Mechanismus oddílu číst z tabulky SAP. Mezi podporované možnosti patří: <ul><li>`None`</li><li>`PartitionOnInt`(normální celé číslo nebo celé číslo hodnoty s nulovým `0000012345`odsazením vlevo, například )</li><li>`PartitionOnCalendarYear`(4 číslice ve formátu "YYYYY")</li><li>`PartitionOnCalendarMonth`(6 číslic ve formátu "YYYYMM")</li><li>`PartitionOnCalendarDate`(8 číslic ve formátu "YYYYMMDD")</li></ul> | Ne       |
| `partitionColumnName`              | Název sloupce použitého k rozdělení dat.                | Ne       |
| `partitionUpperBound`              | Maximální hodnota sloupce zadané `partitionColumnName` v, který bude použit k pokračování dělení. | Ne       |
| `partitionLowerBound`              | Minimální hodnota sloupce zadané `partitionColumnName` v, který bude použit k pokračování dělení. (Poznámka: `partitionLowerBound` nemůže být "0", `PartitionOnInt`pokud je možnost oddílu ) | Ne       |
| `maxPartitionsNumber`              | Maximální počet oddílů rozdělit data do.     | Ne       |

>[!TIP]
>Pokud vaše tabulka SAP obsahuje velký objem dat, například několik miliard řádků, použijte `partitionOption` a `partitionSetting` rozdělte data na menší oddíly. V tomto případě jsou data čtena na oddíl a každý datový oddíl je načten ze serveru SAP prostřednictvím jednoho volání RFC.<br/>
<br/>
>`partitionOnInt` Jako `partitionOption` příklad se počet řádků v každém oddílu vypočítá pomocí tohoto vzorce: `partitionLowerBound`(celkové řádky mezi`maxPartitionsNumber` `partitionUpperBound` a )/ .<br/>
<br/>
>Chcete-li načíst datové oddíly paralelně s urychlením [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopírování, paralelní stupeň je řízen nastavením aktivity kopírování. Pokud například nastavíte `parallelCopies` na čtyři, Data Factory současně generuje a spouští čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat z tabulky SAP. Důrazně doporučujeme `maxPartitionsNumber` vytvořit násobek hodnoty `parallelCopies` nemovitosti. Při kopírování dat do úložiště dat založeného na souborech je také přikázáno zapisovat do složky jako více souborů (pouze zadejte název složky), v takovém případě je výkon lepší než zápis do jednoho souboru.

V `rfcTableOptions`aplikaci můžete k filtrování řádků použít následující běžné operátory dotazů SAP:

| Operátor | Popis |
| :------- | :------- |
| `EQ` | Je rovno |
| `NE` | Není rovno |
| `LT` | Menší než |
| `LE` | Menší nebo rovno |
| `GT` | Větší než |
| `GE` | Větší nebo rovno |
| `IN` | Stejně jako v`TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | Stejně jako v`LIKE 'Emma%'` |

### <a name="example"></a>Příklad

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mapování datových typů pro tabulku SAP

Při kopírování dat z tabulky SAP se používají následující mapování z datových typů tabulky SAP do dočasných datových typů Azure Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, naleznete v [tématu Schémata a mapování datových typů](copy-activity-schema-and-type-mapping.md).

| Typ SAP ABAP | Dočasný datový typ datové továrny |
|:--- |:--- |
| `C`(Řetězec) | `String` |
| `I`(Celé číslo) | `Int32` |
| `F`(Plovák) | `Double` |
| `D`(Datum) | `String` |
| `T`(Čas) | `String` |
| `P`(BCD Baleno, Měna, Desetinné číslo, Množství) | `Decimal` |
| `N`(Číselné) | `String` |
| `X`(Binární a nezpracovaný) | `String` |

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
