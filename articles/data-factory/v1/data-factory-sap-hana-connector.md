---
title: Přesouvání dat ze SAP HANA pomocí Azure Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z SAP HANA pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 96d16552cfadca9b345d0f0cd0a344249897f571
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020944"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Přesouvání dat ze SAP HANA pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-sap-hana-connector.md)
> * [Verze 2 (aktuální verze)](../connector-sap-hana.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor SAP HANA ve V2](../connector-sap-business-warehouse.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunu dat z v místním SAP HANA. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

Kopírování dat z do místního úložiště dat SAP HANA do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky, najdete v článku [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Data factory aktuálně podporuje pouze přesouvá data z SAP HANA do jiných úložišť dat, ale ne pro přesun dat z jiných datových úložišť do systému SAP HANA.

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Tento konektor podporuje všechny verze databáze SAP HANA. Podporuje kopírování dat z informační modely HANA (například zobrazením analýzy a výpočtů) a řádku/sloupce tabulky za použití příkazů jazyka SQL.

Pokud chcete povolit připojení k instanci SAP HANA, nainstalujte následující komponenty:
- **Brána správy dat**: Služba data Factory podporuje připojení k místní úložiště dat (včetně SAP HANA) pomocí komponenty s názvem Data Management Gateway. Další informace o bráně pro správu dat a podrobné pokyny pro nastavení brány najdete v tématu [přesouvání dat mezi místními daty uložení do cloudového úložiště dat](data-factory-move-data-between-onprem-and-cloud.md) článku. I v případě, že SAP HANA je hostovaný na virtuálním počítači Azure IaaS (VM) je potřeba brána. Bránu můžete nainstalovat na stejný virtuální počítač jako úložiště dat nebo jinému virtuálnímu počítači, tak dlouho, dokud brána lze připojit k databázi.
- **Ovladač SAP HANA ODBC** na počítači brány. Ovladače SAP HANA ODBC si můžete stáhnout [SAP Software Download Center](https://support.sap.com/swdc). Hledání pomocí klíčového slova **SAP HANA klienta pro Windows**. 

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště dat v místním SAP HANA pomocí různých nástrojů a rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data. 
- Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory.
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. 

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Tady je příklad s definice JSON entit služby Data Factory, které se používají ke kopírování dat z v místním SAP HANA, naleznete v tématu [příklad JSON: Kopírování dat ze SAP HANA do Azure Blob](#json-example-copy-data-from-sap-hana-to-azure-blob) části tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory konkrétní nebo úložiště dat SAP HANA:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro SAP HANA propojené služby.

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém se nachází instance SAP HANA. Pokud váš server používá vlastní port, zadejte `server:port`. | řetězec | Ano
authenticationType. | Typ ověřování. | řetězec. "Základní" nebo "Windows" | Ano 
uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP | řetězec | Ano
heslo | Heslo pro tohoto uživatele. | řetězec | Ano
Název brány | Název brány, který služba Data Factory měla použít pro připojení k místní instanci SAP HANA. | řetězec | Ano
encryptedCredential | Řetězec, který šifrované přihlašovací údaje. | řetězec | Ne

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny datové sady typy (Azure SQL, Azure blob, tabulky Azure, atd.).

**TypeProperties** oddílu se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. Nejsou žádné vlastnosti specifické pro typ. podporované pro SAP HANA datové sady typu **RelationalTable**. 


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky, jsou zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, k dispozici ve vlastnosti **typeProperties** části aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

Když zdroj v aktivitě kopírování je typu **RelationalSource** (která zahrnuje SAP HANA), v části typeProperties jsou k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query | Určuje dotaz SQL na čtení dat z instance SAP HANA. | Dotaz SQL. | Ano |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>Příklad JSON: Kopírování dat ze SAP HANA do objektů Blob v Azure
Následující příklad obsahuje ukázky JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tento příklad ukazuje, jak kopírovat data ze v místním SAP HANA do Azure Blob Storage. Nicméně je možné zkopírovat data **přímo** k libovolnému jímky uvedené [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.  

> [!IMPORTANT]
> Tato ukázka poskytuje fragmenty kódu JSON. Neobsahuje podrobné pokyny pro vytvoření datové továrny. Zobrazit [přesun dat mezi místními umístěními a cloudu](data-factory-move-data-between-onprem-and-cloud.md) najdete podrobné pokyny.

Ukázka obsahuje následující entit datové továrny:

1. Propojené služby typu [SapHana](#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázce kopíruje data z instance SAP HANA do objektu blob Azure každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

Jako první krok instalační program brány správy dat. Pokyny jsou v [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

### <a name="sap-hana-linked-service"></a>SAP HANA propojené služby
Tato propojená služba propojuje SAP HANA instanci objektu pro vytváření dat. Vlastnost type je nastavená na **SapHana**. V části typeProperties najdete informace o připojení pro instance SAP HANA.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Tato propojená služba propojuje účet úložiště Azure pro vytváření dat. Vlastnost type je nastavená na **AzureStorage**. V části typeProperties najdete informace o připojení pro účet služby Azure Storage.

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="sap-hana-input-dataset"></a>Vstupní datová sada SAP HANA

Tato datová sada definuje datovou sadu SAP HANA. Nastavit typ datovou sadu služby Data Factory, která **RelationalTable**. V současné době nezadáte žádné vlastnosti specifické pro typ. pro datovou sadu SAP HANA. Dotaz v definici aktivity kopírování Určuje, jaká data ke čtení z instance SAP HANA. 

Nastavení vlastnost external na hodnotu true informuje služby Data Factory, že v tabulce je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

Definuje vlastnosti frekvencí a intervalem plán. V takovém případě data načítají z instance SAP HANA po hodinách. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Výstupní datová sada Azure Blob
Tato datová sada definuje výstupní datovou sadu objektů Blob v Azure. Vlastnost type je nastaven do Azureblobu. V části typeProperties najdete, ukládat data zkopírovaná z instance SAP HANA. Data je zapsána do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje na základě doby spuštění řez, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="pipeline-with-copy-activity"></a>Kanál s aktivitou kopírování

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **RelationalSource** (pro SAP HANA zdroj) a **jímky** je typ nastaven na **BlobSink**. Zadaná pro dotaz SQL **dotazu** vlastnost vybere data za poslední hodinu pro kopírování.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>Mapování typu pro SAP HANA
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku, aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typy s následující dvoukrokový přístup:

1. Převést na typ formátu .NET typy nativních zdrojů
2. Převést z typu .NET native jímky typu

Při přesouvání dat ze SAP HANA, se používají následující mapování z typů SAP HANA na typy .NET.

Typ SAP HANA | Základní typ .NET
------------- | ---------------
TINYINT | Bajt
SMALLINT | Int16
INT | Datový typ Int32
BIGINT | Int64
REAL | Jednoduchá
DOUBLE | Jednoduchá
DECIMAL | Desítkově
DATOVÝ TYP BOOLEAN | Bajt
VARCHAR | Řetězec
NVARCHAR | Řetězec
DATOVÝ TYP CLOB | Byte]
ALPHANUM | Řetězec
OBJEKT BLOB | Byte]
DATE (Datum) | DateTime
ČAS | Časový interval
ČASOVÉ RAZÍTKO | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Známá omezení
Při kopírování dat ze SAP HANA platí několik známá omezení:

- U řetězce NVARCHAR je zkrácen na maximální délce 4000 znaků Unicode
- Se smalldecimal
- Nepodporuje se VARBINARY
- Platná data jsou mezi 1899, 12/30 a 9999/12/31

## <a name="map-source-to-sink-columns"></a>Mapování zdroje do jímky sloupce
Další informace o mapování sloupců v datové sadě zdroje do sloupců v datové sadě jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení z relačních zdrojů
Při kopírování dat z relačních dat ukládá, mějte opakovatelnosti aby se zabránilo neúmyslnému výsledků. Ve službě Azure Data Factory můžete znovu spustit řezu ručně. Zásady opakování pro datovou sadu můžete také nakonfigurovat tak, aby určitý řez se znovu spustí, když dojde k chybě. V obou případech se znovu spustí určitý řez, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát spustit určitý řez. Zobrazit [Repeatable z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.
