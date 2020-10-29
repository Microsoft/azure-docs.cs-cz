---
title: Kopírování dat z tabulky SAP
description: Naučte se, jak kopírovat data z tabulky SAP do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 1970709dea67111bfd8b90f9fc315a3b044b2ab9
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900251"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Kopírování dat z tabulky SAP pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z tabulky SAP. Další informace najdete v tématu [Přehled aktivit kopírování](copy-activity-overview.md).

>[!TIP]
>Pokud chcete získat přehled o celkové podpoře pro integraci dat v programu, přečtěte si článek [integrace dat SAP pomocí Azure Data Factory dokumentu White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) s podrobným úvodem na jednotlivé konektory SAP, comparsion a doprovodné materiály.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor tabulka SAP se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z tabulky SAP můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje nebo jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tato konektorová tabulka SAP podporuje:

- Kopírování dat z tabulky SAP v:

  - Součást SAP ERP Central Component (SAP ECC) verze 7,01 nebo novější (v nedávném zásobníku balíčku podpory SAP vydané po 2015).
  - SAP Business Warehouse (SAP BW) verze 7,01 nebo novější (v nedávném zásobníku balíčku podpory SAP vydané po 2015).
  - SAP S/4HANA.
  - Další produkty v SAP Business Suite verze 7,01 nebo novější (v nedávném zásobníku balíčku podpory SAP vydané po 2015).

- Kopírování dat z transparentní tabulky SAP, tabulky ve fondu, seskupené tabulky a zobrazení.
- Kopírování dat pomocí základního ověřování nebo zabezpečené síťové komunikace (SNC), pokud je nakonfigurován SNC.
- Připojování k aplikačnímu serveru SAP nebo serveru zpráv SAP.
- Načítání dat prostřednictvím výchozího nebo vlastního RFC.

Verze 7,01 nebo novější odkazuje na verzi SAP NetWeaver namísto verze SAP ECC. Například SAP ECC 6,0 EHP 7 obecně má NetWeaver verzi >= 7,4. V případě, že si nejste jisti vaším prostředím, tady je postup, jak ověřit verzi ze systému SAP:

1. Pomocí grafického uživatelského rozhraní SAP se připojte k systému SAP. 
2. Přejít na **System**  ->  **stav** systému. 
3. Zkontrolujte vydání SAP_BASIS, ujistěte se, že je větší nebo rovno 701.  
      ![Kontrolovat SAP_BASIS](./media/connector-sap-table/sap-basis.png)

## <a name="prerequisites"></a>Předpoklady

Pokud chcete použít tento konektor tabulky SAP, musíte:

- Nastavte modul runtime integrace v místním prostředí (verze 3,17 nebo novější). Další informace najdete v tématu [Vytvoření a konfigurace prostředí Integration runtime](create-self-hosted-integration-runtime.md)v místním prostředí.

- Stáhněte si z webu SAP konektor 64-bit [SAP pro Microsoft .net 3,0](https://support.sap.com/en/product/connectors/msnet.html) a nainstalujte ho do počítače s místním prostředím Integration runtime. Během instalace se ujistěte, že jste v okně **volitelné kroky instalace** vybrali možnost **instalovat sestavení do GAC** .

  ![Nainstalovat konektor SAP pro .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Uživatel SAP, který se používá ve službě Data Factory konektor tabulek SAP, musí mít následující oprávnění:

  - Autorizace pro použití cíle vzdáleného volání funkcí (RFC).
  - Oprávnění k aktivitě Execute objektu autorizace S_SDSAUTH.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech najdete podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor tabulky SAP.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro propojenou službu SAP BW Open hub:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| `type` | `type`Vlastnost musí být nastavena na hodnotu `SapTable` . | Ano |
| `server` | Název serveru, na kterém je umístěna instance SAP.<br/>Slouží k připojení k aplikačnímu serveru SAP. | Ne |
| `systemNumber` | Číslo systému systému SAP.<br/>Slouží k připojení k aplikačnímu serveru SAP.<br/>Povolená hodnota: dvoumístné desetinné číslo reprezentované jako řetězec. | Ne |
| `messageServer` | Název hostitele serveru zpráv SAP.<br/>Slouží k připojení k serveru zpráv SAP. | Ne |
| `messageServerService` | Název služby nebo číslo portu serveru zpráv.<br/>Slouží k připojení k serveru zpráv SAP. | Ne |
| `systemId` | ID systému SAP, ve kterém je tabulka umístěna.<br/>Slouží k připojení k serveru zpráv SAP. | Ne |
| `logonGroup` | Přihlašovací skupina pro systém SAP.<br/>Slouží k připojení k serveru zpráv SAP. | Ne |
| `clientId` | ID klienta v systému SAP.<br/>Povolená hodnota: desítkové číslo se třemi číslicemi reprezentované jako řetězec. | Ano |
| `language` | Jazyk, který používá systém SAP.<br/>Výchozí hodnota je `EN`.| Ne |
| `userName` | Jméno uživatele, který má přístup k serveru SAP. | Ano |
| `password` | Heslo pro uživatele. Označte toto pole `SecureString` typem pro bezpečné uložení v Data Factory nebo [odkaz na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| `sncMode` | Indikátor aktivace SNC pro přístup k serveru SAP, na kterém je tabulka umístěna.<br/>Použijte, pokud chcete použít SNC pro připojení k serveru SAP.<br/>Povolené hodnoty jsou `0` (vypnuto, výchozí) nebo `1` (zapnuto). | Ne |
| `sncMyName` | Název SNC iniciátoru pro přístup k serveru SAP, na kterém je tabulka umístěna.<br/>Platí `sncMode` , pokud je zapnutý. | Ne |
| `sncPartnerName` | Název SNC komunikačního partnera pro přístup k serveru SAP, na kterém je tabulka umístěna.<br/>Platí `sncMode` , pokud je zapnutý. | Ne |
| `sncLibraryPath` | Knihovna externího produktu zabezpečení pro přístup k serveru SAP, na kterém je tabulka umístěna.<br/>Platí `sncMode` , pokud je zapnutý. | Ne |
| `sncQop` | Úroveň ochrany SNC kvality, kterou chcete použít.<br/>Platí `sncMode` , pokud je zapnutý. <br/>Povolené hodnoty jsou `1` (ověřování), `2` (integrita), ( `3` Ochrana osobních údajů), `8` (výchozí), `9` (maximum). | Ne |
| `connectVia` | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Vyžaduje se místní prostředí Integration runtime, jak bylo zmíněno dříve v [požadavcích](#prerequisites). |Ano |

**Příklad 1: připojení k aplikačnímu serveru SAP**

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

### <a name="example-2-connect-to-an-sap-message-server"></a>Příklad 2: připojení k serveru zpráv SAP

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

### <a name="example-3-connect-by-using-snc"></a>Příklad 3: připojení pomocí SNC

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

Úplný seznam oddílů a vlastností pro definování datových sad najdete v tématu [datové sady](concepts-datasets-linked-services.md). V následující části najdete seznam vlastností podporovaných datovou sadou tabulky SAP.

Chcete-li kopírovat data z a do propojené služby SAP BW Open hub, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| `type` | `type`Vlastnost musí být nastavena na hodnotu `SapTableResource` . | Ano |
| `tableName` | Název tabulky SAP, ze které se mají kopírovat data | Ano |

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

Úplný seznam oddílů a vlastností pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). V následující části najdete seznam vlastností podporovaných zdrojem tabulky SAP.

### <a name="sap-table-as-source"></a>Tabulka SAP jako zdroj

Chcete-li kopírovat data z tabulky SAP, jsou podporovány následující vlastnosti:

| Vlastnost                         | Popis                                                  | Povinné |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | `type`Vlastnost musí být nastavena na hodnotu `SapTableSource` .         | Ano      |
| `rowCount`                         | Počet řádků, které mají být načteny.                              | Ne       |
| `rfcTableFields`                 | Pole (sloupce), která se mají kopírovat z tabulky SAP Například, `column0, column1`. | Ne       |
| `rfcTableOptions`                | Možnosti pro filtrování řádků v tabulce SAP Například, `COLUMN0 EQ 'SOMEVALUE'`. Další informace najdete v tabulce operátor dotazů SAP dále v tomto článku. | Ne       |
| `customRfcReadTableFunctionModule` | Vlastní modul funkcí RFC, který lze použít ke čtení dat z tabulky SAP.<br>Pomocí vlastního modulu funkce RFC můžete definovat, jak se data načítají z vašeho systému SAP a vrátit se do Data Factory. Vlastní modul funkcí musí mít implementované rozhraní (import, export, tabulky), které `/SAPDS/RFC_READ_TABLE2` je podobné, což je výchozí rozhraní používané Data Factory.<br>Data Factory | Ne       |
| `partitionOption`                  | Mechanismus oddílu pro čtení z tabulky SAP. Mezi podporované možnosti patří: <ul><li>`None`</li><li>`PartitionOnInt` (normální celé číslo nebo celočíselné hodnoty s nulovým odsazením vlevo, například `0000012345` )</li><li>`PartitionOnCalendarYear` (4 číslice ve formátu "rrrr")</li><li>`PartitionOnCalendarMonth` (6 číslic ve formátu "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 číslic ve formátu "RRRRMMDD")</li></ul> | Ne       |
| `partitionColumnName`              | Název sloupce, který se používá k vytvoření oddílů dat                | Ne       |
| `partitionUpperBound`              | Maximální hodnota sloupce určeného v `partitionColumnName` , která bude použita pro pokračování v dělení. | Ne       |
| `partitionLowerBound`              | Minimální hodnota sloupce zadaného v `partitionColumnName` , který bude použit pro pokračování v dělení. (Poznámka: `partitionLowerBound` při volbě oddílu nemůže být "0" `PartitionOnInt` ) | Ne       |
| `maxPartitionsNumber`              | Maximální počet oddílů, do kterých se mají rozdělit data     | Ne       |
| `sapDataColumnDelimiter` | Jeden znak, který se používá jako oddělovač předaný do SAP RFC pro rozdělení výstupních dat. | Ne |

>[!TIP]
>Pokud má tabulka SAP velký objem dat, například několik miliard řádků, použijte `partitionOption` a `partitionSetting` k rozdělení dat do menších oddílů. V takovém případě se data čtou na oddíl a každý datový oddíl se načte ze serveru SAP prostřednictvím jednoho volání RFC.<br/>
<br/>
>`partitionOption`Jako `partitionOnInt` příklad se počet řádků v jednotlivých oddílech vypočte pomocí tohoto vzorce: (celkový počet řádků mezi `partitionUpperBound` a `partitionLowerBound` )/ `maxPartitionsNumber` .<br/>
<br/>
>Chcete-li načíst datové oddíly paralelně a urychlit kopírování, je paralelní úroveň řízena [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) nastavením aktivity kopírování. Pokud jste například nastavili `parallelCopies` čtyři, Data Factory souběžně vygeneruje a spustí čtyři dotazy na základě zadané možnosti oddílu a nastavení a každý dotaz načte část dat z tabulky SAP. Důrazně doporučujeme, abyste si `maxPartitionsNumber` vynásobení hodnoty `parallelCopies` Vlastnosti. Při kopírování dat do úložiště dat založeného na souborech je také znovu zaškrtnuto, aby bylo možné zapisovat do složky jako více souborů (pouze název složky). v takovém případě je výkon lepší než zápis do jednoho souboru.

V nástroji `rfcTableOptions` můžete použít následující běžné operátory dotazů SAP k filtrování řádků:

| Operátor | Popis |
| :------- | :------- |
| `EQ` | Rovno |
| `NE` | Není rovno |
| `LT` | Menší než |
| `LE` | Menší než nebo rovno |
| `GT` | Větší než |
| `GE` | Větší než nebo rovno |
| `IN` | Jako v `TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | Jako v `LIKE 'Emma%'` |

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

Když kopírujete data z tabulky SAP, v datových typech tabulky SAP se použijí následující mapování na Azure Data Factory dočasné datové typy. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

| Typ SAP ABAP | Data Factory pomocný datový typ |
|:--- |:--- |
| `C` Řetezce | `String` |
| `I` Čísla | `Int32` |
| `F` Plovák | `Double` |
| `D` Datum | `String` |
| `T` Interval | `String` |
| `P` (Komprimovaná BCD, měna, desetinné číslo, množství) | `Decimal` |
| `N` Číselné | `String` |
| `X` (Binární a nezpracované) | `String` |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
