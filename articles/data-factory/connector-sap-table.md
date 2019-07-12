---
title: Kopírování dat z tabulky SAP pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z tabulky SAP na úložiště dat jímky podporované s využitím aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: jingwang
ms.openlocfilehash: 9216f5c00cbdac273b562736abdd1c812d172237
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827755"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Kopírování dat z tabulky SAP pomocí Azure Data Factory

Tento článek ukazuje, jak použije aktivitu kopírování ke kopírování dat z tabulky SAP v Azure Data Factory. Další informace najdete v tématu [přehled aktivit kopírování](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z tabulky aplikace SAP do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SAP tabulky podporuje:

- Kopírování dat z tabulky SAP v:

  - SAP ERP ústřední součást (SAP ECC) verze 7.01 nebo novějších verzích (v poslední SAP Support balíček zásobníku vydanou po 2015).
  - SAP Business Warehouse (SAP BW) verze 7.01 nebo novější.
  - SAP S/4HANA.
  - Ostatní produkty SAP Business Suite verze 7.01 nebo novější.

- Kopírování dat z obou tabulku transparentní SAP, tabulku ve fondu, Clusterované tabulky a zobrazení.
- Kopírování dat pomocí základní ověřování nebo Secure Network Communications (SNC), pokud je nakonfigurovaný SNC.
- Připojení k aplikační server SAP nebo server zpráv SAP.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor SAP tabulky, je potřeba:

- Nastavte místní prostředí integration runtime (verze 3.17 nebo novější). Další informace najdete v tématu [vytvořit a nakonfigurovat místní prostředí integration runtime](create-self-hosted-integration-runtime.md).

- Stáhněte si 64-bit [konektoru SAP pro rozhraní Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) z webu SAP a nainstalujte ho na počítači s modulem runtime integrace v místním prostředí. Během instalace, ujistěte se, že jste vybrali **nainstalovat sestavení do GAC** možnost **pokynů k instalaci** okna.

  ![Instalace konektoru SAP pro .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- SAP uživatele, který se používá v konektoru SAP objekt pro vytváření dat tabulky musí mít následující oprávnění:

  - Autorizace pomocí vzdáleného volání funkce (RFC) cíle.
  - Oprávnění pro spouštění aktivit objektu S_SDSAUTH autorizace.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní tabulky konektoru SAP.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SAP BW Open Centrum propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požadováno |
|:--- |:--- |:--- |
| `type` | `type` Musí být vlastnost nastavena na `SapTable`. | Ano |
| `server` | Název serveru, na kterém se nachází SAP instance.<br/>Použijte pro připojení k aplikační server SAP. | Ne |
| `systemNumber` | Číslo systému systému SAP.<br/>Použijte pro připojení k aplikační server SAP.<br/>Povolené hodnoty: Dvě číslice desetinné číslo reprezentované jako řetězec. | Ne |
| `messageServer` | Název hostitele serveru zpráv SAP.<br/>Použijte pro připojení k serveru zpráv SAP. | Ne |
| `messageServerService` | Název služby nebo port číslo zprávy serveru.<br/>Použijte pro připojení k serveru zpráv SAP. | Ne |
| `systemId` | ID systému SAP, kde se nachází v tabulce.<br/>Použijte pro připojení k serveru zpráv SAP. | Ne |
| `logonGroup` | Přihlašovací skupina systému SAP.<br/>Použijte pro připojení k serveru zpráv SAP. | Ne |
| `clientId` | ID klienta v systému SAP.<br/>Povolené hodnoty: Tři číslice desetinné číslo reprezentované jako řetězec. | Ano |
| `language` | Jazyk, který používá systém SAP.<br/>Výchozí hodnota je `EN`.| Ne |
| `userName` | Jméno uživatele, který má přístup k serveru SAP. | Ano |
| `password` | Heslo pro tohoto uživatele. Označte toto pole se `SecureString` typ bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| `sncMode` | Indikátor SNC aktivaci pro přístup k serveru SAP, kde se nachází v tabulce.<br/>Pokud chcete použít pro připojení k serveru SAP SNC použijte.<br/>Povolené hodnoty jsou `0` (vypnuté, výchozí nastavení) nebo `1` (na). | Ne |
| `sncMyName` | Jméno volajícího SNC pro přístup k serveru SAP, kde se nachází v tabulce.<br/>Platí tehdy, když `sncMode` zapnutý. | Ne |
| `sncPartnerName` | Komunikace partnera SNC název pro přístup k serveru SAP, kde se nachází v tabulce.<br/>Platí tehdy, když `sncMode` zapnutý. | Ne |
| `sncLibraryPath` | Externí zabezpečení produktu knihovnu pro přístup k serveru SAP ve kterém se nachází v tabulce.<br/>Platí tehdy, když `sncMode` zapnutý. | Ne |
| `sncQop` | Úroveň SNC kvality ochrany použít.<br/>Platí tehdy, když `sncMode` zapnutý. <br/>Povolené hodnoty jsou `1` (ověřování), `2` (integrita) `3` (Ochrana osobních údajů), `8` (výchozí), `9` (maximální). | Ne |
| `connectVia` | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Místní prostředí integration runtime je nutné, jak je uvedeno výše v [požadavky](#prerequisites). |Ano |

**Příklad 1: Připojení k aplikační server SAP**

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

### <a name="example-2-connect-to-an-sap-message-server"></a>Příklad 2: Připojte se k serveru zpráv SAP

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

### <a name="example-3-connect-by-using-snc"></a>Příklad 3: Připojení s použitím SNC

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

Úplný seznam oddílů a vlastnosti pro definování datové sady, naleznete v tématu [datových sad](concepts-datasets-linked-services.md). Následující část obsahuje seznam vlastností podporován tabulka dataset SAP.

Pro kopírování dat z a do SAP BW Open Centrum propojené služby, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požadováno |
|:--- |:--- |:--- |
| `type` | `type` Musí být vlastnost nastavena na `SapTableResource`. | Ano |
| `tableName` | Název tabulky SAP pro kopírování dat z. | Ano |

### <a name="example"></a>Příklad

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). Následující část obsahuje seznam vlastností podporován tabulka zdroji SAP.

### <a name="sap-table-as-a-source"></a>SAP tabulku jako zdroj

Pro kopírování dat z tabulky SAP, jsou podporovány následující vlastnosti:

| Vlastnost                         | Popis                                                  | Požadováno |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | `type` Musí být vlastnost nastavena na `SapTableSource`.         | Ano      |
| `rowCount`                         | Počet řádků, které se mají načíst.                              | Ne       |
| `rfcTableFields`                   | Polí (sloupců) ke kopírování z tabulky SAP. Například, `column0, column1`. | Ne       |
| `rfcTableOptions`                  | Možnost filtrovat řádky v tabulce SAP. Například, `COLUMN0 EQ 'SOMEVALUE'`. Další informace najdete v článku tabulky – operátor dotazu SAP dále v tomto článku. | Ne       |
| `customRfcReadTableFunctionModule` | Vlastní RFC funkce modulu, který je možné číst data z tabulky SAP.<br>Vlastní modul RFC funkce můžete použít k definování, jak se data načte z vašeho systému SAP a vrátí do služby Data Factory. Vlastního funkčního modulu musí mít rozhraní implementované (import, export tabulek.), který je podobný `/SAPDS/RFC_READ_TABLE2`, což je výchozí rozhraní používané datovou továrnou. | Ne       |
| `partitionOption`                  | Mechanismus oddílu číst z tabulky SAP. Podporované možnosti patří: <ul><li>`None`</li><li>`PartitionOnInt` (normální integer nebo celočíselné hodnoty s nulovou odsazení na levé straně, jako například `0000012345`)</li><li>`PartitionOnCalendarYear` (4 číslic ve formátu "YYYY")</li><li>`PartitionOnCalendarMonth` (6 číslic ve formátu "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 číslic ve formátu "RRRRMMDD")</li></ul> | Ne       |
| `partitionColumnName`              | Název sloupce použitá při vytváření oddílů data.                | Ne       |
| `partitionUpperBound`              | Maximální hodnota sloupce zadané v `partitionColumnName` , který se použije a pokračujte dělení. | Ne       |
| `partitionLowerBound`              | Minimální hodnota sloupce zadané v `partitionColumnName` , který se použije a pokračujte dělení. | Ne       |
| `maxPartitionsNumber`              | Maximální počet oddílů pro rozdělení dat do.     | Ne       |

>[!TIP]
>Pokud má vaše tabulka SAP velké objemy dat, jako je například několik miliard řádků, použijte `partitionOption` a `partitionSetting` pro rozdělení dat do menších oddílů. V takovém případě data načítají na oddíl, a každý oddíl dat je načten z vašeho serveru SAP prostřednictvím jednoho volání RFC.<br/>
<br/>
>Pořízení `partitionOption` jako `partitionOnInt` například počet řádků v jednotlivých oddílech se počítá pomocí tohoto vzorce: (celkový počet řádků, které spadají mezi `partitionUpperBound` a `partitionLowerBound`) /`maxPartitionsNumber`.<br/>
<br/>
>Ke spuštění oddíly paralelní ke zrychlení kopírování, důrazně doporučujeme provedení `maxPartitionsNumber` násobkem hodnoty `parallelCopies` vlastnost. Další informace najdete v tématu [paralelní kopírování](copy-activity-performance.md#parallel-copy).

V `rfcTableOptions`, můžete použít následující běžné operátory dotazu SAP filtrovat řádky:

| Operátor | Popis |
| :------- | :------- |
| `EQ` | Je rovno |
| `NE` | Není rovno |
| `LT` | Je menší než |
| `LE` | Je menší nebo rovno |
| `GT` | Je větší než |
| `GE` | Je větší nebo rovno |
| `LIKE` | Stejně jako v `LIKE 'Emma%'` |

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
            }
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mapování datového typu pro tabulku SAP

Po zkopírování dat z tabulky SAP se používají následující mapování z typy tabulkových dat SAP do služby Azure Data Factory dočasné datových typů. Informace o tom, jak aktivity kopírování mapuje typ zdroje schéma a data jímky, najdete v článku [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

| Typ ABAP SAP | Data Factory dočasné datový typ |
|:--- |:--- |
| `C` (String) | `String` |
| `I` (Integer) | `Int32` |
| `F` (Float) | `Double` |
| `D` (Date) | `String` |
| `T` (Čas) | `String` |
| `P` (BCD zabaleny, Měna, Decimal, množství) | `Decimal` |
| `N` (Číselné) | `String` |
| `X` (Binární a Raw) | `String` |

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
