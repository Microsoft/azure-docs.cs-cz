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
ms.date: 06/10/2018
ms.author: jingwang
ms.openlocfilehash: 49f07b4aaadfd45e9743bde58dc715230e5bc983
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074065"
---
# <a name="copy-data-from-sap-table-using-azure-data-factory"></a>Kopírování dat z tabulky SAP pomocí Azure Data Factory

Tento článek ukazuje, jak použije aktivitu kopírování ke kopírování dat z tabulky SAP v Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat z tabulky SAP do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor SAP tabulky podporuje:

- Kopírování dat z tabulky SAP v:

    - **SAP ECC** s verzí 7.01 nebo vyšší (v posledních SAP podporu balíčku zásobníku vydanou po roce 2015)
    - **SAP BW** 7.01 nebo novější verze
    - **SAP S/4HANA**
    - **Ostatní produkty SAP Business Suite** 7.01 nebo novější verze 

- Kopírování dat z obou **transparentní tabulku SAP** a **zobrazení**.
- Kopírování dat pomocí **základní ověřování** nebo **SNC** (zabezpečení síťové komunikace) Pokud je nakonfigurovaný SNC.
- Připojení k **aplikační Server** nebo **Server zpráv**.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor SAP tabulky, je potřeba:

- Nastavte modul Integration Runtime s verzí 3.17 nebo vyšší. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.

- Stáhněte si **64-bit [3.0 konektoru SAP .NET](https://support.sap.com/en/product/connectors/msnet.html)**  z webu SAP a nainstalujte ho na místní prostředí IR počítače. Při instalaci, v okně kroky volitelné nastavení, ujistěte se, že jste vybrali **nainstalovat sestavení do GAC** možnost. 

    ![Instalace konektoru SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Uživatel systému SAP používá v konektoru SAP tabulku datové továrny musí mít následující oprávnění: 

    - Autorizace RFC. 
    - Oprávnění k autorizace objektu "S_SDSAUTH" aktivity "Spustit".

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor SAP tabulky.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro SAP Business Warehouse otevřete Centrum propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **SapTable** | Ano |
| server | Název serveru, na kterém se nachází SAP instance.<br/>Použít, pokud se chcete připojit k **aplikační Server SAP**. | Ne |
| systemNumber | Číslo systému systému SAP.<br/>Použít, pokud se chcete připojit k **aplikační Server SAP**.<br/>Povolené hodnoty: dvě číslice desetinné číslo reprezentované jako řetězec. | Ne |
| messageServer | Název hostitele serveru zpráv SAP.<br/>Použít, pokud se chcete připojit k **Server zpráv SAP**. | Ne |
| messageServerService | Název služby nebo port číslo zprávy serveru.<br/>Použít, pokud se chcete připojit k **Server zpráv SAP**. | Ne |
| systemId | ID systému SAP systému, kde se nachází v tabulce.<br/>Použít, pokud se chcete připojit k **Server zpráv SAP**. | Ne |
| logonGroup | Přihlašovací skupina systému SAP.<br/>Použít, pokud se chcete připojit k **Server zpráv SAP**. | Ne |
| clientId | ID klienta klienta v systému SAP.<br/>Povolené hodnoty: tři číslice desetinné číslo reprezentované jako řetězec. | Ano |
| language | Jazyk, který používá systém SAP. | Ne (výchozí hodnota je **EN**)|
| userName | Jméno uživatele, který má přístup k serveru SAP. | Ano |
| password | Heslo pro tohoto uživatele. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| sncMode | Indikátor SNC aktivaci pro přístup k serveru SAP, kde se nachází v tabulce.<br/>Použít, pokud chcete použít pro připojení k serveru SAP SNC.<br/>Povolené hodnoty jsou: **0** (vypnuté, výchozí) nebo **1** (na). | Ne |
| sncMyName | Jméno volajícího SNC pro přístup k serveru SAP, kde se nachází v tabulce.<br/>Použít v případě `sncMode` zapnutý. | Ne |
| sncPartnerName | Název komunikace partnera SNC pro přístup k serveru SAP, kde se nachází v tabulce.<br/>Použít v případě `sncMode` zapnutý. | Ne |
| sncLibraryPath | Externí zabezpečení produktu knihovnu pro přístup k serveru SAP ve kterém se nachází v tabulce.<br/>Použít v případě `sncMode` zapnutý. | Ne |
| sncQop | Kvalita SNC ochrany.<br/>Použít v případě `sncMode` zapnutý. <br/>Povolené hodnoty jsou: **1** (ověřování), **2** (integrita) **3** (Ochrana osobních údajů), **8** (výchozí), **9** (maximální). | Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Modul Integration Runtime je povinný, jak je uvedeno v [požadavky](#prerequisites). |Ano |

**Příklad 1: připojení k aplikační Server SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
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

**Příklad 2: připojení k serveru zpráv SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system id>",
            "logonGroup": "<logon group>",
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

**Příklad 3: připojení pomocí SNC**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "snc myname",
            "sncPartnerName": "snc partner name",
            "sncLibraryPath": "snc library path",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností podporován tabulka SAP datové sady.

Pro kopírování dat z a do SAP BW Open centra, jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **SapTableResource**. | Ano |
| tableName | Název tabulky SAP pro kopírování dat z. | Ano |

**Příklad:**

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP Table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporován tabulka SAP zdroje.

### <a name="sap-table-as-source"></a>SAP tabulku jako zdroj

Ke zkopírování dat z tabulky SAP, jsou podporovány následující vlastnosti.

| Vlastnost                         | Popis                                                  | Požaduje se |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| type                             | Vlastnost type musí být nastavená na **SapTableSource**.       | Ano      |
| Počet řádků                         | Počet řádků, které se mají načíst.                              | Ne       |
| rfcTableFields                   | Pole pro kopírování z tabulky SAP. Například, `column0, column1`. | Ne       |
| rfcTableOptions                  | Možnost filtrovat řádky v tabulce SAP. Například, `COLUMN0 EQ 'SOMEVALUE'`. Zobrazit další popis dál v této tabulce. | Ne       |
| customRfcReadTableFunctionModule | Vlastní funkce modul RFC, který je možné číst data z tabulky SAP. | Ne       |
| partitionOption                  | Mechanismus oddílu číst z tabulky SAP. Podporované možnosti patří: <br/>- **None**<br/>- **PartitionOnInt** (normální integer nebo celočíselné hodnoty s nulovou odsazení na levé straně, jako je například 0000012345)<br/>- **PartitionOnCalendarYear** (4 číslic ve formátu "YYYY")<br/>- **PartitionOnCalendarMonth** (6 číslic ve formátu "YYYYMM")<br/>- **PartitionOnCalendarDate** (8 číslic ve formátu "RRRRMMDD") | Ne       |
| partitionColumnName              | Název sloupce pro rozdělení dat. | Ne       |
| partitionUpperBound              | Maximální hodnota sloupce zadané v `partitionColumnName` , který se použije pro řízení dělení. | Ne       |
| partitionLowerBound              | Minimální hodnota sloupce zadané v `partitionColumnName` , který se použije pro řízení dělení. | Ne       |
| maxPartitionsNumber              | Maximální počet oddílů pro rozdělení dat do. | Ne       |

>[!TIP]
>- Pokud má vaše tabulka SAP velkého objemu dat, jako je například několik až miliardy řádků, použijte `partitionOption` a `partitionSetting` pro rozdělení dat do malých oddílů, v takovém případě se data načítají oddílů a každý oddíl dat je načten z vašeho serveru SAP prostřednictvím jednom Volání RFC.<br/>
>- Pořízení `partitionOption` jako `partitionOnInt` jako například počet řádků v jednotlivých oddílech se vypočte tak (celkový počet řádků, které spadají mezi *partitionUpperBound* a *partitionLowerBound*) /*maxPartitionsNumber*.<br/>
>- Pokud chcete dál běžet paralelně ke zrychlení kopírování oddíly, důrazně doporučujeme provést `maxPartitionsNumber` jako násobek čísla hodnotu `parallelCopies` (Další informace z [paralelní kopírování](copy-activity-performance.md#parallel-copy)).

V `rfcTableOptions`, například následující běžné SAP operátory dotazu můžete filtrovat řádky: 

| Operátor | Popis |
| :------- | :------- |
| EQ | Je rovno |
| NE | Není rovno |
| LT | Je menší než |
| LE | Je menší nebo rovno |
| GT | Větší než |
| GE | Je větší nebo rovno |
| STEJNĚ JAKO | Například jako "% Emmy. |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP Table input dataset name>",
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

## <a name="data-type-mapping-for-sap-table"></a>Mapování datového typu pro tabulku SAP

Při kopírování dat z tabulky SAP, se používají následující mapování z tabulky SAP datových typů na Azure Data Factory dočasné datové typy. Zobrazit [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md) Další informace o způsobu, jakým aktivitu kopírování, která mapuje typ zdroje schéma a data na jímce.

| Typ ABAP SAP | Data factory dočasné datový typ |
|:--- |:--- |
| C (String) | String |
| I (Integer) | Int32 |
| F (Float) | Double |
| D (Date) | String |
| T (čas) | String |
| P (konfiguračních dat spouštění, balení, Měna, Decimal, množství) | Decimal |
| N (číselné) | String |
| X (binární a Raw) | String |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
