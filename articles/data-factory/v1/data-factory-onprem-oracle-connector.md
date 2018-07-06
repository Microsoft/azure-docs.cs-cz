---
title: Kopírování dat do nebo z Oracle pomocí služby Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z databáze, která je na místní klasifikaci pomocí služby Azure Data Factory Oracle.
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
ms.openlocfilehash: 10535e75a32a9f95e759340cf14d693f43639473
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856837"
---
# <a name="copy-data-to-or-from-on-premises-oracle-using-azure-data-factory"></a>Kopírování dat do nebo z místní Oracle pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-onprem-oracle-connector.md)
> * [Verze 2 (aktuální verze)](../connector-oracle.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Oracle ve V2](../connector-oracle.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování ve službě Azure Data Factory k přesunu dat do a z místní databáze Oracle. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Data můžete kopírovat **z databáze Oracle** ukládá následující data:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových skladů **k databázi Oracle**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Požadavky
Data Factory podporuje připojení k místním zdrojům Oracle pomocí brány správy dat. Naleznete v tématu [brána správy dat](data-factory-data-management-gateway.md) článku se dozvíte o brána správy dat a [přesun dat z místních do cloudu](data-factory-move-data-between-onprem-and-cloud.md) článku podrobné pokyny k nastavení brány datového kanálu přesuňte data.

I v případě, že je hostovaná Oracle ve Virtuálním počítači Azure IaaS je potřeba brána. Bránu můžete nainstalovat na stejném virtuálním počítači IaaS jako úložiště dat nebo na jiný virtuální počítač tak dlouho, dokud brána lze připojit k databázi.

> [!NOTE]
> Naleznete v tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení/bránou související problémy.

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Tento konektor Oracle podporuje dvě verze ovladače:

- **Ovladač Microsoft pro Oracle (doporučeno)**: spouští se ze brána správy dat verze 2.7, Microsoft ovladače pro Oracle se nainstaluje automaticky společně brány, takže není nutné dále zpracovávat ovladač za účelem navázání připojení po Oracle, a může také docházet lepší výkon kopírování používá tento ovladač. Následující verze systému Oracle jsou podporovány databází:
    - R1 Oracle 12c (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10,1, 10.2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

> [!NOTE]
> Nepodporuje proxy serveru Oracle.

> [!IMPORTANT]
> Ovladač Microsoft pro Oracle aktuálně podporuje jenom kopírování dat z Oracle, ale ne zápisu do databáze Oracle. A Všimněte si, že připojení schopnosti testů na kartě diagnostiku brány správy dat nepodporuje tento ovladač. Alternativně můžete použít Průvodce kopírováním pro ověření připojení.
>

- **Poskytovatel dat Oracle pro .NET:** můžete také pomocí poskytovatele dat Oracle pro kopírování dat z/do Oracle. Tato součást je součástí [Oracle Data Access součásti pro Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Nainstalujte odpovídající verzi (32 nebo 64 bitů) na počítači, kde je nainstalovaná brána. [Poskytovatel dat Oracle, .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) přístup k databázi Oracle Database 10 g, 2 nebo novější verzi.

    Pokud se rozhodnete "XCopy instalace", postupujte podle kroků v souboru readme.htm. Doporučujeme, abyste že si vybrat instalační program s uživatelským rozhraním (bez XCopy jeden).

    Po instalaci poskytovatele, **restartovat** hostitelská služba Brána pro správu dat na svém počítači pomocí služby aplet (nebo) Data Management Gateway Configuration Manager.  

Pokud autor kanál kopírování pomocí Průvodce kopírováním typ ovladače bude určena automaticky. Ovladač Microsoft použije ve výchozím nastavení, pokud vaše verze brány je nižší než 2.7 nebo zvolte Oracle jako jímku.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, která přesouvání dat do a z místní databáze Oracle pomocí různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **webu Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru** , **Rozhraní .NET API**, a **rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **služby data factory**. Datová továrna může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory. Například pokud se kopírování dat z databáze Oralce do služby Azure blob storage, vytvoříte dvě propojené služby propojující Oracle database a účet úložiště Azure do služby data factory. Vlastnosti propojené služby, které jsou specifické pro Oracle, najdete v části [vlastnostem propojených služeb](#linked-service-properties) oddílu.
3. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. V příkladu uvedených v posledním kroku vytvoříte datovou sadu, která určit tabulku v databázi Oracle, který obsahuje vstupní data. A vytvořte jinou datovou sadu, která zadejte kontejner objektů blob a složku obsahující data zkopírovaná z databáze Oracle. Vlastnosti datové sady, které jsou specifické pro Oracle, najdete v části [vlastnosti datové sady](#dataset-properties) oddílu.
4. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve pomocí OracleSource jako zdroj a BlobSink jako jímka pro aktivitu kopírování. Podobně pokud kopírujete z Azure Blob Storage do databáze Oracle, můžete použít BlobSource a třídě OracleSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro Oracle database, najdete v části [vlastnosti aktivity kopírování](#copy-activity-properties) oddílu. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku klikněte na odkaz v předchozí části datového úložiště. 

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke kopírování dat do a z místní databáze Oracle najdete v tématu [JSON příklady](#json-examples-for-copying-data-to-and-from-oracle-database) části tohoto článku.

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro Oracle propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavená na: **OnPremisesOracle** |Ano |
| driverType | Určete, který ovladač určený ke kopírování dat z/do databáze Oracle. Povolené hodnoty jsou **Microsoft** nebo **ODP** (výchozí). Zobrazit [podporované verze a instalace](#supported-versions-and-installation) části na podrobnosti o ovladači. | Ne |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci databáze Oracle pro vlastnost připojovací řetězec. | Ano |
| Název brány | Název brány, který se používá pro připojení k místnímu serveru Oracle |Ano |

**Příklad: použití ovladače Microsoft:**

>[!TIP]
>Pokud dosáhnete o tom, že chyba "ORA-01025: UPI parametr je mimo rozsah" a systém Oracle je verze 8i, přidejte `WireProtocolMode=1` připojovací řetězec a zkuste to znovu.

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

**Příklad: použití ODP ovladače**

Odkazovat na [tento web](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/) povolených formátů.

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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Oracle, objektů blob v Azure, tabulky Azure, atd.).

V části typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. V části typeProperties datové sady typu OracleTable má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v databázi Oracle, který odkazuje propojenou službu. |Ne (Pokud **oracleReaderQuery** z **OracleSource** určena) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování přijímá pouze jeden vstup a vytvoří jenom jeden výstup.

Vzhledem k tomu, vlastnosti v části typeProperties aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

### <a name="oraclesource"></a>OracleSource
V aktivitě kopírování, pokud je zdroj typu **OracleSource** následující vlastnosti jsou k dispozici v **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| oracleReaderQuery |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Příklad: vybrat * z MyTable <br/><br/>Pokud není zadán, příkaz SQL, která se spustí: vybrat * z MyTable |Ne (Pokud **tableName** z **datovou sadu** určena) |

### <a name="oraclesink"></a>Třídě OracleSink
**Třídě OracleSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu. |časový interval<br/><br/> Příklad: 00:30:00 (30 minut). |Ne |
| writeBatchSize |Vloží data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti. |Celé číslo (počet řádků) |Ne (výchozí: 100) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování ke spuštění tak, že po vyčištění dat určitý řez. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování k vyplnění s identifikátorem automaticky generovány řez, který se používá k vyčištění dat určitý řez, kdy se znovu spustit. |Název sloupce pro sloupec s datovým typem binary(32). |Ne |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>Příklady JSON pro kopírování dat do a z databáze Oracle
Následující příklad obsahuje ukázky JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Zobrazí se kopírování dat z/do databáze Oracle do a z úložiště objektů Blob v Azure. Ale data je možné zkopírovat do libovolné jímky uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Příklad: Kopírování dat z Oracle do objektů Blob v Azure

Ukázka obsahuje následující entit datové továrny:

1. Propojené služby typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako zdroj a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) jako jímku.

Ukázce kopíruje data z tabulky v databázi Oracle v místním do objektu blob po hodinách. Další informace o různých vlastnosti používané ve vzorku najdete v dokumentaci v částech podle ukázky.

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

Ukázka předpokládá vytvoříte tabulku "MyTable" v databázi Oracle a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad.

Nastavení "externí": "PRAVDA" informuje služby Data Factory, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Název složky a cesta k souboru pro tento objekt blob se dynamicky vyhodnocuje podle času spuštění řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

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

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno na každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **OracleSource** a **jímky** je typ nastaven na **BlobSink**.  Zadaný dotaz SQL **oracleReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

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

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Příklad: Kopírování dat z objektu Blob Azure do databáze Oracle
Tato ukázka předvádí, jak kopírovat data ze služby Azure Blob Storage do místní databáze Oracle. Nicméně je možné zkopírovat data **přímo** z libovolného zdroje uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.  

Ukázka obsahuje následující entit datové továrny:

1. Propojené služby typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) jako zdroj [třídě OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako jímku.

Ukázce kopíruje data z objektu blob do tabulky v databázi Oracle místní každou hodinu. Další informace o různých vlastnosti používané ve vzorku najdete v dokumentaci v částech podle ukázky.

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

**Azure vstupní datová sada objektu Blob**

Data je převzata z nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Název složky a cesta k souboru pro tento objekt blob se dynamicky vyhodnocuje podle času spuštění řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc a den čas spuštění a název souboru používá hodinová část času zahájení. "externí": "PRAVDA" nastavení informuje služby Data Factory, že tato tabulka je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v databázi Oracle. Vytvoření tabulky v databázi Oracle se stejný počet sloupců tak, jak očekáváte objektů Blob CSV soubor obsahovat. Nové řádky do tabulky přidají každou hodinu.

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

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **jímky** je typ nastaven na **třídě OracleSink**.  

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

Zobrazí se následující **chybová zpráva**:

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**Možné příčiny:**

1. Nebyl nainstalován zprostředkovatel dat .NET Framework pro Oracle.
2. Zprostředkovatel dat .NET Framework pro Oracle byl nainstalován na rozhraní .NET Framework 2.0 a nebyla nalezena ve složce rozhraní .NET Framework 4.0.

**Řešení nebo alternativní řešení:**

1. Pokud jste nenainstalovali zprostředkovatele .NET pro Oracle, [ji nainstalovat](http://www.oracle.com/technetwork/topics/dotnet/downloads/) a opakujte tento scénář.
2. Pokud se zobrazí chybová zpráva i po instalaci poskytovatele, proveďte následující kroky:
   1. Otevřete konfiguraci počítače rozhraní .NET 2.0 ze složky: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Vyhledejte **poskytovatel dat Oracle pro .NET**, a byste měli najít položku, jak je znázorněno v následujícím příkladu v části **system.data** -> **DbProviderFactories**: "<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Poskytovatel dat oracle pro .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Zkopírujte tuto položku do souboru machine.config v následující složce v4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config a změnit na verzi, aby 4.xxx.x.x.
4. "< Cesta instalace ODP.NET > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll" nainstalovat do globální mezipaměti sestavení (GAC) spuštěním `gacutil /i [provider path]`. ## Tipy pro řešení potíží

### <a name="problem-2-datetime-formatting"></a>Problém 2: formátování data a času

Zobrazí se následující **chybová zpráva**:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Řešení nebo alternativní řešení:**

Budete muset upravit řetězec dotazu v aktivitě kopírování založené na konfiguraci kalendářních dat v databázi Oracle, jak je znázorněno v následujícím příkladu (použít funkci to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapování typu pro Oracle
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typy s přístupem následující krok 2:

1. Převést na typ formátu .NET typy nativních zdrojů
2. Převést z typu .NET native jímky typu

Při přesouvání dat od Oraclu, se používají následující mapování z Oracle datového typu na typ .NET a naopak.

| Oracle datového typu | Datový typ rozhraní .NET framework |
| --- | --- |
| BFILE |Byte] |
| OBJEKT BLOB |Byte]<br/>(podporováno pouze na Oracle 10g a vyšší při použití ovladače Microsoft) |
| CHAR |Řetězec |
| DATOVÝ TYP CLOB |Řetězec |
| DATE (Datum) |DateTime |
| PLOVOUCÍ DESETINNOU ČÁRKOU |Desetinné číslo, řetězec (Pokud přesnost > 28) |
| CELÉ ČÍSLO |Desetinné číslo, řetězec (Pokud přesnost > 28) |
| INTERVAL ROK MĚSÍC |Datový typ Int32 |
| DEN INTERVALU SEKUNDY. |Časový interval |
| LONG |Řetězec |
| DLOUHO NEZPRACOVANÉ |Byte] |
| NCHAR |Řetězec |
| NCLOB |Řetězec |
| ČÍSLO |Desetinné číslo, řetězec (Pokud přesnost > 28) |
| NVARCHAR2 |Řetězec |
| NEZPRACOVANÉ |Byte] |
| ID ŘÁDKU |Řetězec |
| ČASOVÉ RAZÍTKO |DateTime |
| ČASOVÉ RAZÍTKO S MÍSTNÍM ČASOVÉM PÁSMU |DateTime |
| ČASOVÉ RAZÍTKO S ČASOVÝM PÁSMEM |DateTime |
| CELÉ ČÍSLO BEZ ZNAMÉNKA |Číslo |
| VARCHAR2 |Řetězec |
| XML |Řetězec |

> [!NOTE]
> Datový typ **INTERVALU roku a měsíce na** a **INTERVALU dne do druhé** nejsou podporována při použití ovladače Microsoft.

## <a name="map-source-to-sink-columns"></a>Mapování zdroje do jímky sloupce
Další informace o mapování sloupců v datové sadě zdroje do sloupců v datové sadě jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení z relačních zdrojů
Při kopírování dat z relačních dat ukládá, mějte opakovatelnosti aby se zabránilo neúmyslnému výsledků. Ve službě Azure Data Factory můžete znovu spustit řezu ručně. Zásady opakování pro datovou sadu můžete také nakonfigurovat tak, aby určitý řez se znovu spustí, když dojde k chybě. V obou případech se znovu spustí určitý řez, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát spustit určitý řez. Zobrazit [Repeatable z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.
