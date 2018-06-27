---
title: Kopírování dat do nebo z databáze Oracle pomocí služby Data Factory | Microsoft Docs
description: Zjistěte, jak ke zkopírování dat z Oracle databázi, která je v místním prostředí pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0e9ed70de6d72026b8e3469417c53d6923a8a85e
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021470"
---
# <a name="copy-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Kopírování dat z místní Oracle pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-onprem-oracle-connector.md)
> * [Verze 2 – Preview](../connector-oracle.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [Oracle konektoru V2](../connector-oracle.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z databáze Oracle na místě. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Může kopírovat data **z databáze Oracle** ukládá do následující data:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových úložišť **k databázi Oracle**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Požadavky
Objekt pro vytváření dat podporuje připojení k místním zdrojům Oracle pomocí Brána pro správu dat. V tématu [Brána pro správu dat](data-factory-data-management-gateway.md) článku se dozvíte o Brána pro správu dat a [přesun dat z lokálního prostředí do cloudu](data-factory-move-data-between-onprem-and-cloud.md) článku podrobné pokyny k nastavení brány datovém kanálu pro Přesun dat.

Vyžaduje se brána, i když Oracle je hostovaná ve virtuálním počítači Azure IaaS. Bránu můžete nainstalovat na stejný virtuální počítač IaaS jako úložiště dat nebo na jiný virtuální počítač, dokud brána se může připojit k databázi.

> [!NOTE]
> V tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení nebo brány související s problémy.

## <a name="supported-versions-and-installation"></a>Podporované verze a instalaci
Tento konektor Oracle podporují dvě verze ovladače:

- **Ovladač Microsoft pro Oracle (doporučeno)**: od Brána pro správu dat. verze 2.7, ovladače pro Oracle se nainstaluje automaticky společně brány, takže nemusíte dále zpracovávat ovladač za účelem od společnosti Microsoft navázat připojení do databáze Oracle a můžete také dochází k lepší výkon kopírování pomocí tohoto ovladače. Následující verze systému Oracle jsou podporovány databází:
    - R1 Oracle 12c (12.1)
    - R1 Oracle 11g nebo R2 (11.1, 11.2)
    - R1 Oracle 10g, R2 (10,1, 10.2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

> [!NOTE]
> Oracle proxy serveru není podporována.

> [!IMPORTANT]
> Ovladač Microsoft pro Oracle aktuálně podporuje jenom kopírování dat z Oracle, ale není zápis do databáze Oracle. A Všimněte si, že testovací připojení možnosti na kartě diagnostiku brány správy dat nepodporuje tento ovladač. Alternativně můžete použít Průvodce kopírováním k ověření připojení.
>

- **Zprostředkovatel dat Oracle pro .NET:** můžete také použít poskytovatele dat Oracle ke zkopírování dat z/do databáze Oracle. Tato součást je součástí [Oracle Data přístup součásti pro Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Nainstalujte příslušnou verzi (32 nebo 64bitový) na počítači, kde se brána nainstaluje. [Zprostředkovatel dat Oracle .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) přístup k databázi Oracle Database 10 g verze 2 nebo novější.

    Pokud si zvolíte možnost "XCopy instalace", postupujte podle kroků v readme.htm. Doporučujeme že vybrat instalační program pomocí uživatelského rozhraní (bez XCopy jeden).

    Po instalaci poskytovatele, **restartujte** hostitelskou službu Brána pro správu dat v počítači pomocí služby aplet (nebo) Správce konfigurace brány pro správu dat.  

Pokud použijete Průvodce kopírováním vytvářet kanál kopírování, typ ovladače bude automaticky určit. Ovladač Microsoft použije ve výchozím nastavení, pokud vaše verze brány je nižší než 2.7 nebo zvolte Oracle jako jímku.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z databáze Oracle místně pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru** , **.NET API**, a **rozhraní REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **objekt pro vytváření dat**. Objekt pro vytváření dat může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory. Pokud jsou kopírování dat z databáze Oralce do Azure blob storage, například vytvoříte dvě propojené služby pro vytváření dat. propojte databáze Oracle a účet úložiště Azure. Vlastnosti propojené služby, které jsou specifické pro Oracle, najdete v části [propojené vlastnosti služby](#linked-service-properties) části.
3. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. V příkladu uvedených v posledním kroku vytvoříte datové sady a určete tabulky v databázi Oracle, který obsahuje vstupní data. A vytvořte jinou datovou sadu, která zadejte kontejner objektů blob a složky, která obsahuje data zkopírovat z databáze Oracle. Vlastnosti datové sady, které jsou specifické pro Oracle, najdete v části [vlastnosti datové sady](#dataset-properties) části.
4. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve použijete OracleSource jako zdroj a BlobSink jako jímku pro aktivitu kopírování. Podobně pokud kopírujete z Azure Blob Storage do databáze Oracle, můžete použít BlobSource a OracleSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro databáze Oracle, najdete v části [zkopírovat vlastnosti aktivity](#copy-activity-properties) části. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímka klikněte na odkaz v předchozí části pro data store. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat do nebo z databáze Oracle na místě, najdete v části [JSON příklady](#json-examples-for-copying-data-to-and-from-oracle-database) tohoto článku.

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení entit služby Data Factory:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro Oracle propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **OnPremisesOracle** |Ano |
| driverType | Určete, který ovladač použít ke zkopírování dat z/do databáze Oracle. Povolené hodnoty jsou **Microsoft** nebo **ODP** (výchozí). V tématu [podporované verze a instalace](#supported-versions-and-installation) části na podrobnosti o ovladači. | Ne |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci databáze Oracle pro vlastnost connectionString. | Ano |
| gatewayName | Název brány, který se používá pro připojení k místnímu serveru Oracle |Ano |

**Příklad: pomocí ovladače Microsoft:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Příklad: pomocí ODP ovladače**

Odkazovat na [tento web](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/) pro povolených formátech.

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Oracle, objektů blob v Azure, Azure table atd.).

V rámci typeProperties části se liší pro jednotlivé typy datovou sadu a poskytuje informace o umístění dat v úložišti. V rámci typeProperties části datové sady typu OracleTable má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v databázi Oracle, který propojená služba odkazuje na. |Ne (Pokud **oracleReaderQuery** z **OracleSource** je zadána) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování přijímá pouze jeden vstup a vytváří jenom jeden výstup.

Vzhledem k tomu, vlastnosti dostupné v rámci typeProperties části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

### <a name="oraclesource"></a>OracleSource
Při aktivitě kopírování, pokud je zdroj typu **OracleSource** následující vlastnosti jsou k dispozici v **rámci typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| oracleReaderQuery |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Příklad: vybrat * z MyTable <br/><br/>Pokud není zadaný příkaz jazyka SQL, která se provedla: vybrat * z MyTable |Ne (Pokud **tableName** z **datovou sadu** je zadána) |

### <a name="oraclesink"></a>OracleSink
**OracleSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte, než čas na dokončení předtím, než vyprší časový limit operace dávkové vložení. |časový interval<br/><br/> Příklad: 00:30:00 (30 minut). |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize. |Celé číslo (počet řádků) |Ne (výchozí: 100) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování provést tak, aby se vyčistit data určitý řez. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování vyplníte identifikátor automaticky generovány řez, který se používá k vyčištění dat určitý řez při spusťte znovu. |Název sloupce sloupce s datovým typem binary(32). |Ne |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>Příklady JSON pro kopírování dat do a z databáze Oracle
Následující příklad uvádí ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se ukazují, jak ke zkopírování dat z/do databáze Oracle do/z Azure Blob Storage. Však lze zkopírovat data do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Příklad: Kopírování dat z databáze Oracle do objektu Blob Azure

Ukázka má následující entity objektu pro vytváření dat:

1. Propojené služby typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako zdroj a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) jako jímku.

Ukázka zkopíruje data z tabulky v databázi Oracle místně do objektu blob každou hodinu. Další informace o různých vlastnosti používané ve vzorku naleznete v dokumentaci k v částech následující ukázky.

**Oracle propojené služby:**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Propojená služba Azure Blob storage:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Oracle vstupní datové sady:**

Příkladu se předpokládá, jste vytvořili tabulku "MyTable" v Oracle a obsahuje sloupec s názvem "timestampcolumn" pro data časové řady.

Nastavení "externí": "PRAVDA" informuje služba Data Factory, datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {     
            "externalData": {        
                "retryInterval": "00:01:00",    
                "retryTimeout": "00:10:00",       
                "maximumRetry": 3       
            }     
        }
    }
}
```

**Azure Blob výstupní datovou sadu:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Název složky a cesta k souboru pro tento objekt blob se vyhodnocují dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kanál s aktivitou kopírování:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **OracleSource** a **podřízený** je typ nastaven na **BlobSink**.  Příkaz jazyka SQL zadaným **oracleReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Příklad: Kopírování dat z objektu Blob Azure do Oracle
Tento příklad ukazuje, jak ke zkopírování dat z úložiště objektů Blob Azure do místní databáze Oracle. Nicméně je možné zkopírovat data **přímo** ze všech zdrojů uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.  

Ukázka má následující entity objektu pro vytváření dat:

1. Propojené služby typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) jako zdroj [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako jímku.

Ukázka kopíruje data z objektu blob do tabulky v databázi Oracle místní každou hodinu. Další informace o různých vlastnosti používané ve vzorku naleznete v dokumentaci k v částech následující ukázky.

**Oracle propojené služby:**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Propojená služba Azure Blob storage:**
```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Azure vstupní datovou sadu objektu Blob**

Data je převzata z nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Název složky a cesta k souboru pro tento objekt blob se vyhodnocují dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc a den součástí čas spuštění a název souboru používá hodinu součástí čas spuštění. "externí": "PRAVDA" nastavení informuje služba Data Factory, že tato tabulka je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Oracle výstupní datovou sadu:**

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v Oracle. Vytvořte v tabulce v Oracle s stejný počet sloupců, podle očekávání souboru CSV objektů Blob tak, aby obsahovala. Nové záznamy se přidají do tabulky každou hodinu.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Kanál s aktivitou kopírování:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **podřízený** je typ nastaven na **OracleSink**.  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```


## <a name="troubleshooting-tips"></a>Rady pro řešení potíží
### <a name="problem-1-net-framework-data-provider"></a>Problém 1: Zprostředkovatel dat .NET Framework

Najdete zde **chybová zpráva**:

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**Možné příčiny:**

1. Zprostředkovatel dat .NET Framework pro Oracle nebyl nainstalován.
2. Zprostředkovatel dat .NET Framework pro Oracle byla nainstalována na rozhraní .NET Framework 2.0 a nebyl nalezen ve složkách rozhraní .NET Framework 4.0.

**Řešení nebo alternativní řešení:**

1. Pokud jste nenainstalovali poskytovatele .NET pro Oracle, [ji nainstalovat](http://www.oracle.com/technetwork/topics/dotnet/downloads/) a opakujte tento scénář.
2. Pokud se zobrazí chybová zpráva i po instalaci poskytovatele, proveďte následující kroky:
   1. Otevřete ve složce Konfigurace počítače rozhraní .NET 2.0: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Vyhledejte **poskytovatele dat Oracle pro .NET**, a mělo by být schopna nalézt položku, jak znázorňuje následující ukázka v části **system.data** -> **DbProviderFactories**: "<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Poskytovatele dat oracle pro .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Zkopírujte tento záznam do souboru machine.config v následující složce v4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config a změňte na verzi 4.xxx.x.x.
4. Instalace "< cesta instalace ODP.NET > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll" do globální mezipaměti sestavení (GAC) spuštěním `gacutil /i [provider path]`. ## Tipy pro odstraňování potíží

### <a name="problem-2-datetime-formatting"></a>Problém 2: formátování data a času

Najdete zde **chybová zpráva**:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Řešení nebo alternativní řešení:**

Budete muset upravit řetězce dotazu v aktivitě kopírování založené na tom, jak jsou nakonfigurované data do databáze Oracle, jak znázorňuje následující ukázka (pomocí funkce to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapování typu pro Oracle
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup krok 2:

1. Převést na typ .NET typy nativní zdrojů
2. Převést na typ jímky nativní typ formátu .NET

Při přesouvání dat z databáze Oracle, se používají následující mapování z typu Oracle dat na typ .NET a naopak.

| Oracle datový typ | Datový typ rozhraní .NET framework |
| --- | --- |
| BFILE |Byte] |
| OBJEKT BLOB |Byte]<br/>(podporovány pouze na Oracle 10g a vyšší, kdy pomocí ovladače Microsoft) |
| CHAR – |Řetězec |
| DATOVÝ TYP CLOB |Řetězec |
| DATE (Datum) |DateTime |
| PLOVOUCÍ DESETINNÁ ČÁRKA |Decimal, řetězec (Pokud přesnost > 28) |
| CELÉ ČÍSLO |Decimal, řetězec (Pokud přesnost > 28) |
| INTERVAL ROK, MĚSÍC |Int32 |
| DENNÍ INTERVAL SEKUNDY. |Časový interval |
| DLOUHÁ |Řetězec |
| DLOUHO NEZPRACOVANÁ |Byte] |
| NCHAR |Řetězec |
| NCLOB |Řetězec |
| ČÍSLO |Decimal, řetězec (Pokud přesnost > 28) |
| NVARCHAR2 |Řetězec |
| NEZPRACOVANÁ |Byte] |
| ID ŘÁDKU |Řetězec |
| ČASOVÉ RAZÍTKO |DateTime |
| ČASOVÉ RAZÍTKO S MÍSTNÍM ČASOVÉM PÁSMU |DateTime |
| ČASOVÉ RAZÍTKO S ČASOVÝM PÁSMEM |DateTime |
| CELÉ ČÍSLO BEZ ZNAMÉNKA |Číslo |
| VARCHAR2 |Řetězec |
| XML |Řetězec |

> [!NOTE]
> Datový typ **INTERVAL roku na měsíc** a **INTERVAL den na druhé** nejsou podporována při použití ovladače Microsoft.

## <a name="map-source-to-sink-columns"></a>Mapování zdroje jímky sloupců
Další informace o mapování sloupců v datové sadě zdrojového sloupce v datové sadě podřízený najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelných číst z relační zdrojů
Při kopírování dat z relačních dat ukládá, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Zásady opakovaných pokusů pro datovou sadu můžete také nakonfigurovat tak, aby řez se znovu spustí, když dojde k chybě. Řez se znovu spustí, buď způsobem, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát řez je spustit. V tématu [Repeatable číst z relačními zdroji](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
