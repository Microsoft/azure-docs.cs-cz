---
title: Kopírování dat z a do datových úložišť rozhraní ODBC pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z a do úložišť dat ODBC pomocí aktivity kopírování v kanálu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: jingwang
ms.openlocfilehash: 9b73e10b0ed539879e9a32d3961b6375828cc153
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389616"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Kopírování dat z a do datových úložišť rozhraní ODBC pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-odbc-connector.md)
> * [Aktuální verze](connector-odbc.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do úložiště dat ODBC. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor ODBC se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data ze zdroje ODBC můžete kopírovat do libovolného podporovaného úložiště dat jímky nebo je zkopírovat z libovolného podporovaného zdrojového úložiště dat do jímky rozhraní ODBC. Seznam úložišť dat, která jsou v rámci aktivity kopírování podporovaná jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Konkrétně tento konektor ODBC podporuje kopírování dat z/do **libovolného úložiště dat kompatibilního s rozhraním ODBC** pomocí **základního** nebo **anonymního** ověřování. Vyžaduje se **64 ovladač ODBC** . V případě jímky ODBC podporuje ADF Standard ODBC verze 2,0.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete použít tento konektor ODBC, musíte:

- Nastavte Integration Runtime pro místní hostování. Podrobnosti najdete v článku [Integration runtime](create-self-hosted-integration-runtime.md) v místním prostředí.
- Nainstalujte na Integration Runtime počítač pro úložiště dat ovladač ODBC 64.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro konektor ODBC.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu ODBC jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **ODBC** . | Yes |
| připojovací řetězec | Připojovací řetězec s výjimkou části s přihlašovacími údaji Můžete zadat připojovací řetězec se vzorem, jako `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` je, nebo použít systémový DSN (název zdroje dat), který jste nastavili na Integration runtimem počítači, a `"DSN=<name of the DSN on IR machine>;"` (je třeba přesto zadat část přihlašovacích údajů v propojené službě odpovídajícím způsobem).<br>Můžete také vložit heslo do Azure Key Vault a získat `password` konfiguraci z připojovacího řetězce. Další podrobnosti najdete [v tématu uložení přihlašovacích údajů v Azure Key Vault](store-credentials-in-key-vault.md) .| Yes |
| authenticationType | Typ ověřování, který se používá pro připojení k úložišti dat ODBC.<br/>Povolené hodnoty jsou: **základní** a **anonymní**. | Yes |
| userName | Pokud používáte základní ověřování, zadejte uživatelské jméno. | No |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | No |
| pověření | Část přístupového pověření v připojovacím řetězci, kterou jste zadali ve formátu hodnoty vlastnosti specifické pro ovladač. Příklad: `"RefreshToken=<secret refresh token>;"`. Označte toto pole jako SecureString. | No |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Integration Runtime v místním prostředí se vyžaduje, jak je uvedeno v [požadavcích](#prerequisites). |Yes |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou ODBC.

Pro kopírování dat z úložiště dat kompatibilních s rozhraním ODBC jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **ODBC** . | Yes |
| tableName | Název tabulky v úložišti dat rozhraní ODBC. | Ne pro zdroj (Pokud je zadáno "dotaz" ve zdroji aktivity);<br/>Ano pro jímku |

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

Pokud jste používali `RelationalTable` typovou datovou sadu, je stále podporovaná tak, jak je, a až budete chtít začít používat novinku dál.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem rozhraní ODBC.

### <a name="odbc-as-source"></a>ODBC jako zdroj

Pokud chcete kopírovat data z úložiště dat kompatibilního s rozhraním ODBC, v části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **OdbcSource** . | Yes |
| query | Pro čtení dat použijte vlastní dotaz SQL. Příklad: `"SELECT * FROM MyTable"`. | Ne (Pokud je zadáno "tableName" v datové sadě |

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

Pokud jste používali `RelationalSource` typový zdroj, je stále podporován tak, jak je, a když jste navrhli začít používat nový.

### <a name="odbc-as-sink"></a>ODBC jako jímka

Chcete-li kopírovat data do úložiště dat kompatibilního s rozhraním ODBC, nastavte typ jímky v aktivitě kopírování na **OdbcSink**. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type jímky aktivity kopírování musí být nastavená na: **OdbcSink** . | Yes |
| writeBatchTimeout |Počkejte, než se operace dávkového vložení dokončí předtím, než vyprší časový limit.<br/>Povolené hodnoty jsou: TimeSpan. Příklad: "00:30:00" (30 minut). |No |
| writeBatchSize |Když velikost vyrovnávací paměti dosáhne writeBatchSize, vloží data do tabulky SQL.<br/>Povolené hodnoty jsou: celé číslo (počet řádků). |Ne (výchozí hodnota je 0 – automaticky se zjistilo) |
| preCopyScript |Zadejte dotaz SQL pro aktivitu kopírování, která má být provedena před zápisem dat do úložiště dat v každém spuštění. Tuto vlastnost můžete použít k vyčištění předem načtených dat. |No |

> [!NOTE]
> Pokud je "writeBatchSize", pokud není nastavené (automaticky se zjistilo), aktivita kopírování nejprve zjistí, jestli ovladač podporuje operace dávkového zpracování, a nastavte ho 10000 na 1, pokud tomu tak je, nebo ho nastavte na 1, pokud ne. Pokud jste explicitně nastavili jinou hodnotu než 0, aktivita kopírování tuto hodnotu respektuje a za běhu se nezdařila, pokud ovladač nepodporuje operace dávkového zpracování.

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

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="troubleshoot-connectivity-issues"></a>Řešení potíží s připojením

Pokud chcete řešit problémy s připojením,  použijte kartu diagnostika **Integration runtime Configuration Manager**.

1. Spusťte **Integration Runtime Configuration Manager**.
2. Přepněte na kartu **Diagnostika** .
3. V části test připojení vyberte **typ** úložiště dat (propojená služba).
4. Zadejte **připojovací řetězec** , který se používá pro připojení k úložišti dat, vyberte **ověřování** a zadejte **uživatelské jméno**, **heslo** a/nebo **přihlašovací údaje**.
5. Klikněte na **Test připojení** a otestujte připojení k úložišti dat.

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
