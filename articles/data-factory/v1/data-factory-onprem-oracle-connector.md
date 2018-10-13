---
title: Kopírování dat do nebo z Oracle pomocí služby Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data do nebo z místní databáze Oracle pomocí služby Azure Data Factory.
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
ms.openlocfilehash: 848616bb69aa0eae384b9c4e7ea1c2ac3da3c04e
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167116"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Kopírování dat do nebo z Oracle místní pomocí služby Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-onprem-oracle-connector.md)
> * [Verze 2 (aktuální verze)](../connector-oracle.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Azure Data Factory, přečtěte si téma [konektor Oracle ve V2](../connector-oracle.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování ve službě Azure Data Factory k přesunu dat do nebo z místní databáze Oracle. Tento článek vychází [aktivity přesunu dat](data-factory-data-movement-activities.md), který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře

Data můžete kopírovat *z databáze Oracle* ukládá následující data:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových skladů *k databázi Oracle*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Požadavky

Služba data Factory podporuje připojení k místním zdrojům Oracle pomocí brány správy dat. V tématu [brána správy dat](data-factory-data-management-gateway.md) získat další informace o Data Management Gateway. Podrobné pokyny o tom, jak nastavit službu gateway v datovém kanálu pro přesun dat najdete v tématu [přesun dat z místních do cloudu](data-factory-move-data-between-onprem-and-cloud.md).

Brána je požadovaná i v případě, že Oracle je hostovaná v Azure infrastruktury jako služby (IaaS) virtuálního počítače. Bránu můžete nainstalovat na stejném virtuálním počítači IaaS jako úložiště dat nebo na jiný virtuální počítač, tak dlouho, dokud brána lze připojit k databázi.

> [!NOTE]
> Tipy pro odstraňování problémů, které se vztahují k připojení a brány najdete v tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace

Tento konektor Oracle podporuje dvě verze ovladače:

- **Ovladač Microsoft pro Oracle (doporučeno)**: od brány správy dat verze 2.7, ovladač Microsoft pro Oracle je automaticky nainstalován se sadou brány. Není nutné pro instalaci nebo aktualizaci ovladače k navázání připojení k systému Oracle. Může také docházet lepší výkon kopírování s použitím tohoto ovladače. Jsou podporovány tyto verze databáze Oracle:
    - R1 Oracle 12c (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10,1, 10.2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Nepodporuje proxy serveru Oracle.

    > [!IMPORTANT]
    > Ovladač Microsoft pro Oracle v současné době podporuje pouze kopírování dat od Oraclu. Ovladač nepodporuje zápis do databáze Oracle. Funkci test připojení pro bránu správy dat. **diagnostiky** kartu nepodporuje tento ovladač. Alternativně můžete použít Průvodce kopírováním pro ověření připojení.
    >

- **Poskytovatel dat Oracle pro .NET**: poskytovatel dat Oracle můžete použít ke zkopírování dat z nebo do databáze Oracle. Tato součást je součástí [Oracle Data Access součásti pro Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Nainstalujte příslušné verze (32bitová nebo 64bitová verze) na počítači, kde je nainstalovaná brána. [Poskytovatel dat Oracle, .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) mají přístup k databázi Oracle Database 10 g vydaná verze 2 a novější verze.

    Pokud vyberete **XCopy instalace**, proveďte následující kroky jsou popsané v souboru readme.htm. Doporučujeme vybrat instalační program, který má uživatelské rozhraní (ne XCopy instalační program).

    Po instalaci poskytovatele restartujte službu hostitel Brána pro správu dat na svém počítači pomocí apletu služby nebo Data Management Gateway Configuration Manageru.  

Pokud použijete Průvodce kopírováním pro vytvoření kanálu pro kopírování, typ ovladače je autodetermined. Ovladač Microsoft se používá ve výchozím nastavení, pokud vaše brána verze je starší než verze 2.7 nebo vyberte Oracle jako jímku.

## <a name="get-started"></a>Začínáme

Můžete vytvořit kanál, který obsahuje aktivitu kopírování. Kanál přesouvání dat do nebo z místní databáze Oracle pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použití Průvodce kopírováním. Zobrazit [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírování dat.

Jeden z následujících nástrojů můžete také použít k vytvoření kanálu: **webu Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **Azure Resource Manageru Šablona**, **rozhraní .NET API**, nebo **rozhraní REST API**. Najdete v článku [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pro podrobné pokyny k vytvoření kanálu obsahujícího aktivitu kopírování.

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **služby data factory**. Datová továrna může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory. Například pokud se kopírování dat z databáze Oracle do Azure Blob storage, vytvořte dvě propojené služby propojující Oracle database a účet úložiště Azure do služby data factory. Vlastnosti propojené služby, které jsou specifické pro Oracle, najdete v části [vlastnostem propojených služeb](#linked-service-properties).
3. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. V příkladu v předchozím kroku vytvoříte datovou sadu, která určit tabulku v databázi Oracle, který obsahuje vstupní data. Můžete vytvořit jinou datovou sadu, která zadejte kontejner objektů blob a složku obsahující data zkopírovaná z databáze Oracle. Vlastnosti datové sady, které jsou specifické pro Oracle, najdete v části [vlastnosti datové sady](#dataset-properties).
4. Vytvoření **kanálu** , který obsahuje aktivitu kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. V předchozím příkladu použijete **OracleSource** jako zdroj a **BlobSink** jako jímka pro aktivitu kopírování. Podobně pokud kopírujete z úložiště objektů Blob v Azure k databázi Oracle, pomocí **BlobSource** a **třídě OracleSink** v aktivitě kopírování. Aktivita kopírování vlastností, které jsou specifické pro databáze Oracle, najdete v části [vlastnosti aktivity kopírování](#copy-activity-properties). Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku vyberte odkaz pro vaše úložiště dat v předchozí části. 

Při použití průvodce jsou definice JSON pro tyto entity služby Data Factory automaticky vytvoří za vás: propojené služby, datových sad a kanálu. Při použití nástroje nebo rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Ukázky, které mají definice JSON entit služby Data Factory, které používáte pro kopírování dat do nebo z místní databáze Oracle najdete v tématu [JSON příklady](#json-examples-for-copying-data-to-and-from-oracle-database).

Následující části obsahují podrobnosti o vlastnostech JSON, které slouží k definování entit služby Data Factory.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující tabulka popisuje elementy JSON, které jsou specifické pro Oracle propojené služby:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |**Typ** musí být vlastnost nastavena na **OnPremisesOracle**. |Ano |
| driverType | Určete, který ovladač určený ke kopírování dat z nebo do databáze Oracle. Povolené hodnoty jsou **Microsoft** a **ODP** (výchozí). Zobrazit [podporované verze a instalace](#supported-versions-and-installation) pro podrobnosti o ovladači. | Ne |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci databáze Oracle **connectionString** vlastnost. | Ano |
| Název brány | Název brány, který se používá pro připojení k místnímu serveru Oracle. |Ano |

**Příklad: Použití ovladače Microsoft**

> [!TIP]
> Pokud se zobrazí chyba, která říká "ORA-01025: UPI parametr je mimo rozsah" a systém Oracle je verze 8i, přidejte `WireProtocolMode=1` připojovací řetězec a zkuste to znovu:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Příklad: Použití ovladače ODP**

Další informace o povolených formátech, naleznete v tématu [poskytovatel dat Oracle pro .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [vytváření datových sad](data-factory-create-datasets.md). 

Části souboru JSON datové sady, jako je například struktura, dostupnost a zásady, jsou podobné pro všechny typy datovou sadu (třeba Oracle, Azure Blob storage a Azure Table storage).

**TypeProperties** oddílu se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. **TypeProperties** části datové sady typu **OracleTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v databázi Oracle, který odkazuje propojenou službu. |Ne (Pokud **oracleReaderQuery** nebo **OracleSource** určena) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md). 

Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

> [!NOTE]
> Aktivitu kopírování, která přijímá pouze jeden vstup a vytvoří jenom jeden výstup.

Vlastnosti, které jsou k dispozici v **typeProperties** části aktivity se liší s jednotlivými typu aktivity. Vlastnosti aktivity kopírování se liší v závislosti na typu zdroje a jímky.

### <a name="oraclesource"></a>OracleSource

V aktivitě kopírování, pokud je zdroj **OracleSource** typu, jsou k dispozici v následujících vlastností **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| oracleReaderQuery |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Například "vyberte \* z **MyTable**". <br/><br/>Pokud není zadán, je proveden tento příkaz SQL: "vyberte \* z **MyTable**" |Ne<br />(Pokud **tableName** z **datovou sadu** určena) |

### <a name="oraclesink"></a>Třídě OracleSink

**Třídě OracleSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Doba čekání pro dávku vložte na dokončení před vypršením časového limitu operace. |**Časový interval**<br/><br/> Příklad: 00:30:00 (30 minut) |Ne |
| WriteBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne hodnoty **writeBatchSize**. |Celé číslo (počet řádků) |Ne (výchozí: 100) |
| sqlWriterCleanupScript |Určuje dotaz pro aktivitu kopírování ke spuštění tak, aby se vyčistit data určitý řez. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Určuje název sloupce pro aktivitu kopírování k vyplnění s identifikátorem automaticky generované řez.  Hodnota pro **sliceIdentifierColumnName** se používá k vyčištění dat určitý řez, kdy se znovu spustit. |Název sloupce, který má datový typ sloupce **binary(32)**. |Ne |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Příklady JSON pro kopírování dat do a z databáze Oracle

Následující příklady popisují ukázkový JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Příklady ukazují, jak kopírovat data z nebo k databázi Oracle a do a z úložiště objektů Blob v Azure. Nicméně data je možné zkopírovat do libovolné jímky uvedené v [podporovaných úložišť dat a formáty](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování ve službě Azure Data Factory.   

**Příklad: Kopírování dat z Oracle do Azure Blob storage**

Ukázka obsahuje následující entity služby Data Factory:

* Propojené služby typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako zdroj a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) jako jímku.

Ukázce kopíruje data z tabulky v databázi Oracle v místním do objektu blob po hodinách. Další informace o různých vlastností, které se používají v ukázce najdete v následující části Ukázky.

**Oracle propojené služby**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Objekt Blob propojená služba Azure storage**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Vstupní datová sada Oracle**

Příkladu se předpokládá, že vytvoříte tabulku s názvem **MyTable** v databázi Oracle. Obsahuje sloupec s názvem **timestampcolumn** dat časové řady.

Nastavení **externí**: **true** služby Data Factory informuje, že tato datová sada je externí vzhledem k objektu pro vytváření dat a datové sady není vytvářených aktivity ve službě data factory.

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

**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu blob každou hodinu (**frekvence**: **hodinu**, **interval**: **1**). Název složky a cesta k souboru pro tento objekt blob se dynamicky vyhodnocuje podle času spuštění řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a hodinová část času zahájení.

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

**Kanál s aktivitou kopírování**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a naplánované na každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **OracleSource** a **jímky** je typ nastaven na **BlobSink**.  Dotaz SQL, který určíte pomocí **oracleReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
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

**Příklad: Kopírování dat z Azure Blob storage do databáze Oracle**

Tato ukázka předvádí, jak kopírovat data z účtu úložiště objektů Blob v Azure do místní databáze Oracle. Však můžete zkopírovat data *přímo* z libovolného zdroje podle [podporovaných úložišť dat a formáty](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování ve službě Azure Data Factory.  

Ukázka obsahuje následující entity služby Data Factory:

* Propojené služby typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) jako zdroj [třídě OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako jímku.

Ukázce kopíruje data z objektu blob do tabulky v databázi Oracle místní každou hodinu. Další informace o různých vlastností, které se používají v ukázce najdete v následující části Ukázky.

**Oracle propojené služby**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Objekt Blob propojená služba Azure storage**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Vstupní datová sada Azure Blob**

Data je převzata z nového objektu blob každou hodinu (**frekvence**: **hodinu**, **interval**: **1**). Název složky a cesta k souboru pro tento objekt blob se dynamicky vyhodnocuje podle času spuštění řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc a den část času zahájení. Název souboru používá hodinová část času zahájení. Nastavení **externí**: **true** služby Data Factory informuje, že tato tabulka je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

**Oracle výstupní datovou sadu**

Příkladu se předpokládá vytvoříte tabulku s názvem **MyTable** v databázi Oracle. Vytvoření tabulky v databázi Oracle stejný počet sloupců, které očekáváte, že soubor CSV blob tak, aby obsahovala. Nové řádky do tabulky přidají každou hodinu.

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

**Kanál s aktivitou kopírování**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a naplánované spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **jímky** je typ nastaven na **třídě OracleSink**.  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
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

**Chybová zpráva**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed.  

**Možné příčiny**

* Nebyl nainstalován zprostředkovatel dat .NET Framework pro Oracle.
* Zprostředkovatel dat .NET Framework pro Oracle byl nainstalován na rozhraní .NET Framework 2.0 a nebyla nalezena ve složce rozhraní .NET Framework 4.0.

**Řešení**

* Pokud jste nenainstalovali zprostředkovatele .NET pro Oracle, [ji nainstalovat](http://www.oracle.com/technetwork/topics/dotnet/downloads/)a zkuste zopakovat scénář.
* Pokud se zobrazí chybová zpráva, ani po instalaci poskytovatele, proveďte následující kroky:
   1. Otevřete konfigurační soubor počítače pro rozhraní .NET 2.0 ze složky < systémový disk\>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Vyhledejte **poskytovatel dat Oracle pro .NET**. Byste měli najít položku, jak je znázorněno v následujícím příkladu v části **system.data** > **DbProviderFactories**: `<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Zkopírujte tuto položku do souboru machine.config v následující složce rozhraní .NET 4.0: < disk systému\>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Změňte verzi na 4.xxx.x.x.
* Nainstalovat < cesta nainstalované ODP.NET\>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll v globální mezipaměti sestavení (GAC) spuštěním **gacutil /i [cesta zprostředkovatele]**.

### <a name="problem-2-datetime-formatting"></a>Problém č. 2: Formátu data a času

**Chybová zpráva**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Řešení**

Může být potřeba upravit řetězec dotazu v aktivitě kopírování založené na konfiguraci kalendářních dat v databázi Oracle. Tady je příklad (použití **to_date** funkce):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapování typu pro Oracle

Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md), aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s použitím následujících dvoukrokový přístup:

1. Převeďte na typ formátu .NET typy nativní zdroje.
2. Převeďte na typ jímky nativní typ formátu .NET.

Při přesunu dat od Oraclu, se od Oracle datového typu na typ formátu .NET a naopak používají následující mapování:

| Oracle datového typu | Datový typ rozhraní .NET framework |
| --- | --- |
| BFILE |Byte] |
| OBJEKT BLOB |Byte]<br/>(podporováno pouze ve Oracle 10g a novějších verzích při použití ovladač Microsoft) |
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
> Datové typy **INTERVALU roku a měsíce na** a **INTERVALU dne do druhé** nejsou podporovány při použití ovladače Microsoft.

## <a name="map-source-to-sink-columns"></a>Mapování zdroje do jímky sloupce

Další informace o mapování sloupců v datové sadě zdroje do sloupců v datové sadě jímky, najdete v článku [mapování sloupců v datové sadě ve službě Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení z relačních zdrojů

Při kopírování dat z úložiště relačních dat mít na paměti, aby se zabránilo neúmyslnému výsledků opakovatelnost. Ve službě Azure Data Factory můžete ručně znovu spustit určitý řez. Zásady opakování pro datovou sadu můžete také nakonfigurovat tak, aby určitý řez se znovu spustí, když dojde k chybě. Když určitý řez znovu spuštěn, buď ručně, nebo podle zásady opakovaných pokusů, ujistěte se, že je stejná data bez ohledu na to, jak číst v mnoha případech určitý řez běží. Další informace najdete v tématu [Repeatable z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění

Zobrazit [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, které ovlivňují výkon přesun dat (aktivita kopírování) ve službě Azure Data Factory. Můžete také informace o různé způsoby, jak optimalizovat.
