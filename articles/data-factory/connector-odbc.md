---
title: Kopírování dat ze zdrojů ROZHRANÍ ODBC pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data ze zdrojů OData do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jingwang
ms.openlocfilehash: da5c53f8953960c382070be658add2877fff3f8c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416898"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Kopírování dat z úložišť dat ODBC a do nich pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-odbc-connector.md)
> * [Aktuální verze](connector-odbc.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do úložiště dat ODBC. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor ROZHRANÍ ODBC je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Můžete zkopírovat data ze zdroje ODBC do libovolného podporovaného úložiště dat jímky nebo z libovolného úložiště dat podporovaného zdroje do jímky ODBC. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitou kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor ODBC podporuje kopírování dat z/do **všech úložišť dat kompatibilních s rozhraním ODBC** pomocí **základního** nebo **anonymního** ověřování. Je vyžadován **64bitový ovladač ODBC.**

## <a name="prerequisites"></a>Požadavky

Chcete-li použít tento konektor ROZHRANÍ ODBC, musíte:

- Nastavte runtime integrace s vlastním hostitelem. Podrobnosti najdete v článku [runtime integrace s vlastním hostitelem.](create-self-hosted-integration-runtime.md)
- Nainstalujte 64bitový ovladač ODBC pro úložiště dat do integračního automatu Runtime.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro konektor ODBC.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu ODBC jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **Odbc** | Ano |
| připojovací řetězec | Připojovací řetězec s výjimkou části pověření. Můžete určit připojovací `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`řetězec se vzorem jako , nebo použít systém DSN (Název `"DSN=<name of the DSN on IR machine>;"` zdroje dat), který jste nastavili v integračním automatu Runtime (musíte podle toho zadat část pověření v propojené službě).<br>Můžete také umístit heslo v Azure Key `password` Vault a vyžádat konfiguraci z připojovacího řetězce.Další podrobnosti najdete [v přihlašovacích údajích úložiště v azure trezoru](store-credentials-in-key-vault.md) klíčů.| Ano |
| authenticationType | Typ ověřování používaný pro připojení k úložišti dat ODBC.<br/>Povolené hodnoty jsou: **Základní** a **Anonymní**. | Ano |
| userName | Pokud používáte základní ověřování, zadejte uživatelské jméno. | Ne |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |
| pověření | Část připojovacího řetězce přístupová pověření zadaná ve formátu hodnoty vlastnosti specifické pro ovladač. Příklad: `"RefreshToken=<secret refresh token>;"`. Označte toto pole jako securestring. | Ne |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Runtime integrace hostované samostatně je vyžadován, jak je uvedeno v [požadavky](#prerequisites). |Ano |

**Příklad 1: použití základního ověřování**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 2: použití anonymního ověřování**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou ODBC.

Chcete-li kopírovat data z úložiště dat kompatibilních s rozhraním ODBC nebo do ní, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **OdbcTable.** | Ano |
| tableName | Název tabulky v úložišti dat ODBC. | Ne pro zdroj (pokud je zadán "dotaz" ve zdroji aktivity);<br/>Ano pro umyvadlo |

**Příklad**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

Pokud jste `RelationalTable` používali zadaný datový soubor, je stále podporována tak, jak je, zatímco se doporučuje používat novou do budoucna.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem ROZHRANÍ ODBC.

### <a name="odbc-as-source"></a>ODBC jako zdroj

Chcete-li kopírovat data z úložiště dat kompatibilního s rozhraním ODBC, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na: **OdbcSource** | Ano |
| query | Ke čtení dat použijte vlastní dotaz SQL. Například: `"SELECT * FROM MyTable"`. | Ne (pokud je v datové sadě zadán "název_tabulky") |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Pokud jste `RelationalSource` používali zadaný zdroj, je stále podporován jako-je, zatímco jste navrhl použít nový do budoucna.

### <a name="odbc-as-sink"></a>ODBC jako umyvadlo

Chcete-li zkopírovat data do úložiště dat kompatibilního s rozhraním ODBC, nastavte typ jímky v aktivitě kopírování do **aplikace OdbcSink**. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type jímky aktivity kopírování musí být nastavena na: **OdbcSink** | Ano |
| writeBatchTimeout |Počkejte čas pro operaci dávkové vložení k dokončení před časovým výpadkem.<br/>Povolené hodnoty jsou: časový rozsah. Příklad: "00:30:00" (30 minut). |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize.<br/>Povolené hodnoty jsou: celé číslo (počet řádků). |Ne (výchozí hodnota je 0 - automaticky rozpoznáno) |
| preCopyScript |Zadejte dotaz SQL pro aktivitu kopírování, která má být spuštěna před zápisem dat do úložiště dat v každém spuštění. Tuto vlastnost můžete použít k vyčištění předem načtených dat. |Ne |

> [!NOTE]
> Pro "writeBatchSize", pokud není nastavena (automaticky zjištěna), aktivita kopírování nejprve zjistí, zda ovladač podporuje dávkové operace a nastavte jej na 10000, pokud ano, nebo nastavte na 1, pokud tomu tak není. Pokud explicitně nastavíte jinou hodnotu než 0, aktivita kopírování respektuje hodnotu a selže za běhu, pokud ovladač nepodporuje dávkové operace.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="sap-hana-sink"></a>Umyvadlo SAP HANA

>[!NOTE]
>Chcete-li zkopírovat data z úložiště dat SAP HANA, podívejte se na nativní [konektor SAP HANA](connector-sap-hana.md). Chcete-li zkopírovat data do SAP HANA, postupujte podle tohoto pokynu a použijte konektor ODBC. Všimněte si, že propojené služby pro konektor SAP HANA a konektor ODBC jsou s jiným typem, proto nelze znovu použít.
>

Data můžete zkopírovat do databáze SAP HANA pomocí obecného konektoru ODBC.

Nastavte runtime integrace s vlastním hostitelem na počítači s přístupem k úložišti dat. Integrační běh ový čas používá ovladač ODBC pro SAP HANA pro připojení k úložišti dat. Proto nainstalujte ovladač, pokud již není nainstalován ve stejném počítači. Podrobnosti naleznete v části [Požadavky.](#prerequisites)

Před použitím jímky SAP HANA v řešení Data Factory ověřte, zda se prostředí Integration Runtime může připojit k úložišti dat pomocí pokynů v části [Poradce při potížích s připojením.](#troubleshoot-connectivity-issues)

Vytvořte propojenou službu ODBC pro propojení úložiště dat SAP HANA s toto datovou továrnou Azure, jak je znázorněno v následujícím příkladu:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Přečtěte si článek od začátku pro podrobný přehled použití úložišť dat ODBC jako úložiště zdrojových/jímek v operaci kopírování.

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="troubleshoot-connectivity-issues"></a>Řešení potíží s připojením

Chcete-li vyřešit problémy s připojením, použijte kartu **Diagnostika** **nástroje Integration Runtime Configuration Manager**.

1. **Spusťte správce konfigurace prostředí Integration Runtime**.
2. Přepněte na kartu **Diagnostika.**
3. V části Testovat připojení vyberte **typ** úložiště dat (propojená služba).
4. Zadejte **připojovací řetězec,** který se používá pro připojení k úložišti dat, zvolte **ověřování** a zadejte **uživatelské jméno**, **heslo**nebo **pověření**.
5. Chcete-li otestovat připojení k úložišti dat, klepněte na **tlačítko Testovat připojení.**

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
