---
title: Kopírování dat ze zdroje ODBC pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data ze zdroje OData do úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
ms.openlocfilehash: c51804748e4313d79cc3a369b659974d2d32e2e2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014263"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Kopírování dat z a do úložiště dat rozhraní ODBC pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-odbc-connector.md)
> * [Aktuální verze](connector-odbc.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory pro kopírování dat z a do úložiště dat rozhraní ODBC. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Můžete kopírování dat ze zdroje ODBC do jakékoli podporovaného úložiště dat jímky nebo zkopírovat ze všech podporovaných zdrojů úložišť dat do rozhraní ODBC jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor ODBC podporuje kopírování dat z/do **úložiště dat žádné kompatibilní se standardem ODBC** pomocí **základní** nebo **anonymní** ověřování.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor ODBC, budete muset:

- Nastavte modul Integration Runtime. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.
- Nainstalujte ovladač ODBC pro úložiště dat na počítači prostředí Integration Runtime.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor ODBC.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro rozhraní ODBC propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **Rozhraní ODBC** | Ano |
| připojovací řetězec | Připojovací řetězec, s výjimkou části přihlašovací údaje. Můžete zadat připojovací řetězec se vzorem jako `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, nebo použít systém DSN (název zdroje dat) nastavíte na počítači modul Runtime integrace s `"DSN=<name of the DSN on IR machine>;"` (třeba stále zadáte části přihlašovací údaje v propojené službě odpovídajícím způsobem).<br>Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md).| Ano |
| authenticationType. | Typ ověřování používaný pro připojení k úložišti dat rozhraní ODBC.<br/>Povolené hodnoty jsou: **Základní** a **anonymní**. | Ano |
| uživatelské jméno | Pokud používáte základní ověřování, zadejte uživatelské jméno. | Ne |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |
| pověření | Část přístup přihlašovacích údajů z připojovacího řetězce zadaného ve vlastnosti specifické pro ovladač formátu. Příklad: `"RefreshToken=<secret refresh token>;"`. Toto pole můžete označte jako SecureString. | Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Modul Integration Runtime je povinný, jak je uvedeno v [požadavky](#prerequisites). |Ano |

**Příklad 1: použití základního ověřování**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
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

**Příklad 2: použití anonymní ověřování**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností podporovaných datovou sadu rozhraní ODBC.

Pro kopírování dat z/do úložiště dat rozhraní ODBC kompatibilní, nastavte vlastnost typ datové sady na **RelationalTable**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **RelationalTable** | Ano |
| tableName | Název tabulky v úložišti dat rozhraní ODBC. | Ne pro zdroj (Pokud je zadán "dotaz" v zdroj aktivity);<br/>Ano pro jímku |

**Příklad**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "RelationalTable",
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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporována zdroji ODBC.

### <a name="odbc-as-source"></a>Rozhraní ODBC jako zdroj

Ke zkopírování dat z úložiště dat rozhraní ODBC kompatibilní, nastavte typ zdroje v aktivitě kopírování do **RelationalSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **RelationalSource** | Ano |
| query | Použijte vlastní dotaz SQL číst data. Například: `"SELECT * FROM MyTable"`. | Ne (když je "tableName" v datové sadě zadán) |

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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="odbc-as-sink"></a>Rozhraní ODBC jako jímka

Ke zkopírování dat do úložiště dat rozhraní ODBC kompatibilní, nastavte typ jímky v aktivitě kopírování do **OdbcSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ jímky aktivity kopírování musí být nastavena: **OdbcSink** | Ano |
| writeBatchTimeout |Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu.<br/>Povolené hodnoty jsou: časový interval. Příklad: "00: 30:00" (30 minut). |Ne |
| WriteBatchSize |Vloží data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti.<br/>Povolené hodnoty jsou: celé číslo (počet řádků). |Ne (výchozí hodnota je 0 - zjistil automaticky) |
| preCopyScript |Zadejte dotaz SQL pro aktivitu kopírování ke spuštění před zápis dat do úložiště dat při každém spuštění. Tato vlastnost slouží k vyčištění předem načtená data. |Ne |

> [!NOTE]
> Pro "writeBatchSize" Pokud není nastavená, (automatické rozpoznání), aktivita kopírování nejprve zjistí, jestli ovladač podporuje dávkové operace a nastavte ho na 10000-li tomu nebo nastaven na hodnotu 1, pokud tomu tak není. Pokud explicitně nastavena hodnota než 0, aktivita kopírování respektuje hodnotu a za běhu selže, pokud ovladač nepodporuje dávkových operací.

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

## <a name="ibm-informix-source"></a>Zdroj IBM Informix

Kopírování dat z databáze IBM Informix pomocí konektoru rozhraní obecná rozhraní ODBC.

Nastavte modul Integration Runtime na počítači s přístupem s vaším úložištěm dat. Prostředí Integration Runtime používá pro připojení k úložišti dat ovladače ODBC pro Informix. Proto se ovladač nainstalujte, pokud ještě není nainstalovaná na stejném počítači. Například můžete použít ovladač "Ovladač ODBC IBM INFORMIX (64-bit)". Zobrazit [požadavky](#prerequisites) podrobné informace.

Než použijete zdroji Informix v řešení Data Factory, ověřte, zda prostředí Integration Runtime může připojit k úložišti dat podle pokynů v [řešit problémy s připojením](#troubleshoot-connectivity-issues) oddílu.

Vytvoření služby propojené ODBC můžete propojit úložiště dat IBM Informix do služby Azure data factory, jak je znázorněno v následujícím příkladu:

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
            },
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

Přečtěte si článek od začátku podrobný přehled použití dat rozhraní ODBC jsou uloženy jako úložiště dat zdroj/jímka v operaci kopírování.

## <a name="microsoft-access-source"></a>Aplikace Microsoft Access zdroje

Kopírování dat z databáze Microsoft Access pomocí konektoru rozhraní obecná rozhraní ODBC.

Nastavte modul Integration Runtime na počítači s přístupem s vaším úložištěm dat. Prostředí Integration Runtime používá pro připojení k úložišti dat ovladač ODBC Microsoft Access. Proto se ovladač nainstalujte, pokud ještě není nainstalovaná na stejném počítači. Zobrazit [požadavky](#prerequisites) podrobné informace.

Před použitím aplikace Microsoft Access zdroj v řešení Data Factory, ověřte, zda prostředí Integration Runtime může připojit k úložišti dat podle pokynů v [řešit problémy s připojením](#troubleshoot-connectivity-issues) oddílu.

Vytvoření služby ODBC propojený odkaz databáze Microsoft Access na služby Azure data factory, jak je znázorněno v následujícím příkladu:

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
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

Přečtěte si článek od začátku podrobný přehled použití dat rozhraní ODBC jsou uloženy jako úložiště dat zdroj/jímka v operaci kopírování.

## <a name="sap-hana-sink"></a>SAP HANA jímky

>[!NOTE]
>Kopírování dat z úložiště dat SAP HANA, najdete v tématu nativní [konektor SAP HANA](connector-sap-hana.md). Pro kopírování dat do systému SAP HANA, postupujte prosím podle tento pokyn k používání konektoru ODBC. Všimněte si, které jsou propojené služby pro konektor SAP HANA a konektor ODBC s jiným typem proto nelze znovu použít.
>

Kopírování dat do databáze SAP HANA pomocí konektoru rozhraní obecná rozhraní ODBC.

Nastavte modul Integration Runtime na počítači s přístupem s vaším úložištěm dat. Prostředí Integration Runtime používá pro připojení k úložišti dat ovladače ODBC pro SAP HANA. Proto se ovladač nainstalujte, pokud ještě není nainstalovaná na stejném počítači. Zobrazit [požadavky](#prerequisites) podrobné informace.

Než použijete jímky SAP HANA v řešení Data Factory, ověřte, zda prostředí Integration Runtime může připojit k úložišti dat podle pokynů v [řešit problémy s připojením](#troubleshoot-connectivity-issues) oddílu.

Vytvoření služby ODBC propojený odkaz úložiště dat SAP HANA na služby Azure data factory, jak je znázorněno v následujícím příkladu:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
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

Přečtěte si článek od začátku podrobný přehled použití dat rozhraní ODBC jsou uloženy jako úložiště dat zdroj/jímka v operaci kopírování.

## <a name="troubleshoot-connectivity-issues"></a>Řešení problémů s připojením

Chcete-li řešit potíže s připojením, použijte **diagnostiky** kartě **Správce konfigurace modulu Integration Runtime**.

1. Spuštění **Správce konfigurace modulu Integration Runtime**.
2. Přepněte **diagnostiky** kartu.
3. V části "Test připojení", vyberte **typ** dat uložit (propojené služby).
4. Zadejte **připojovací řetězec** , který slouží k připojení k úložišti dat, zvolte **ověřování** a zadejte **uživatelské jméno**, **heslo**, a/nebo **pověření**.
5. Klikněte na tlačítko **Test připojení** chcete otestovat připojení k úložišti.

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
