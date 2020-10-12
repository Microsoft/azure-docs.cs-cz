---
title: Kopírování dat z a do Oracle pomocí Data Factory
description: Naučte se, jak kopírovat data do nebo z místní databáze Oracle pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1aa8708701af37834ae3b6cdc42de9c691ccacec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86084286"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Kopírování dat do a z Oracle místně pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-onprem-oracle-connector.md)
> * [Verze 2 (aktuální verze)](../connector-oracle.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Azure Data Factory, přečtěte si téma [konektor Oracle v v2](../connector-oracle.md).


Tento článek vysvětluje, jak používat aktivitu kopírování v Azure Data Factory k přesunu dat do nebo z místní databáze Oracle. Článek se sestaví na [aktivity přesunu dat](data-factory-data-movement-activities.md), které představují obecný přehled přesunu dat pomocí aktivity kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře

Data *z databáze Oracle* můžete kopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete kopírovat *do databáze Oracle*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Požadavky

Data Factory podporuje připojení k místním zdrojům Oracle pomocí brány Správa dat. Další informace o Správa dat bráně najdete v tématu [Správa dat Gateway](data-factory-data-management-gateway.md) . Podrobné pokyny, jak nastavit bránu v datovém kanálu pro přesun dat, najdete v tématu [přesun dat z místního prostředí do cloudu](data-factory-move-data-between-onprem-and-cloud.md).

Brána se vyžaduje i v případě, že je Oracle hostovaný na virtuálním počítači infrastruktury Azure jako služba (IaaS). Bránu můžete nainstalovat na stejný virtuální počítač s IaaS jako úložiště dat nebo na jiný virtuální počítač, pokud se brána může připojit k databázi.

> [!NOTE]
> Tipy pro řešení potíží souvisejících s připojením a bránou najdete v tématu [řešení potíží s bránou](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace

Tento konektor Oracle podporuje dvě verze ovladačů:

- **Ovladač Microsoftu pro Oracle (doporučeno)**: od verze Správa dat Gateway verze 2,7 se ovladač Microsoftu pro Oracle automaticky nainstaluje spolu s bránou. K navázání připojení k Oracle nemusíte instalovat ani aktualizovat ovladač. Pomocí tohoto ovladače se můžete setkat i s lepším výkonem kopírování. Podporují se tyto verze databází Oracle:
  - Oracle 12c R1 (12,1)
  - Oracle 11g R1, R2 (11,1, 11,2)
  - Oracle 10g R1, R2 (10,1, 10,2)
  - Oracle 9i R1, R2 (9.0.1, 9,2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Proxy server Oracle se nepodporuje.

    > [!IMPORTANT]
    > V současné době podporuje ovladač Microsoftu pro Oracle pouze kopírování dat z Oracle. Ovladač nepodporuje zápis do Oracle. Možnost Test připojení na kartě **diagnostika** Správa dat brány nepodporuje tento ovladač. Alternativně můžete použít Průvodce kopírováním k ověření připojení.
    >

- **Oracle zprostředkovatel dat pro .NET**: ke kopírování dat z nebo do Oracle můžete použít Oracle zprostředkovatel dat. Tato součást je obsažena v [součástech Oracle Data Access Components pro systém Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Na počítač, na kterém je brána nainstalovaná, nainstalujte příslušnou verzi (32 bitů nebo 64). [Oracle zprostředkovatel dat .net 12,1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) má přístup Oracle Database 10g Release 2 a novějších verzích.

    Pokud vyberete **instalaci příkazu xcopy**, proveďte kroky popsané v souboru readme.htm. Doporučujeme vybrat instalační program, který má uživatelské rozhraní (nikoli instalační program XCopy).

    Po instalaci poskytovatele restartujte službu Správa dat hostitele brány na svém počítači pomocí apletu služby nebo Configuration Manager brány Správa dat.

Pokud použijete Průvodce kopírováním k vytvoření kanálu kopírování, typ ovladače se určí znovu. Ovladač společnosti Microsoft se používá ve výchozím nastavení, pokud verze brány starší než verze 2,7 nebo jako jímku vyberete Oracle.

## <a name="get-started"></a>Začínáme

Můžete vytvořit kanál s aktivitou kopírování. Kanál přesouvá data do nebo z místní databáze Oracle pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít Průvodce kopírováním. Rychlý návod k vytvoření kanálu pomocí Průvodce Kopírování dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

K vytvoření kanálu můžete použít také jeden z následujících nástrojů: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager šablona**, rozhraní **.NET API**nebo **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, proveďte následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Datová továrna může obsahovat jeden nebo více kanálů.
2. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou. Pokud například kopírujete data z databáze Oracle do úložiště objektů BLOB v Azure, vytvořte dvě propojené služby, které propojí databázi Oracle a účet Azure Storage s datovou továrnou. Vlastnosti propojených služeb, které jsou specifické pro Oracle, najdete v tématu [vlastnosti propojených služeb](#linked-service-properties).
3. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování. V příkladu v předchozím kroku vytvoříte datovou sadu pro zadání tabulky v databázi Oracle, která obsahuje vstupní data. Vytvoříte další datovou sadu pro určení kontejneru objektů BLOB a složky, která obsahuje data zkopírovaná z databáze Oracle. Vlastnosti datové sady, které jsou specifické pro Oracle, najdete v tématu [Vlastnosti datové sady](#dataset-properties).
4. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V předchozím příkladu použijete jako jímku aktivity kopírování **OracleSource** jako zdroj a **BlobSink** . Podobně pokud kopírujete ze služby Azure Blob Storage do databáze Oracle, použijete v aktivitě kopírování **BlobSource** a **OracleSink** . Vlastnosti aktivity kopírování, které jsou specifické pro databázi Oracle, najdete v tématu [vlastnosti aktivity kopírování](#copy-activity-properties). Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku, získáte výběrem odkazu pro úložiště dat v předchozí části.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Data Factory entity: propojené služby, datové sady a kanál. Pokud používáte nástroje nebo rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON. Ukázky, které mají definice JSON pro Data Factory entit, které slouží ke kopírování dat do nebo z místní databáze Oracle, najdete v ukázkách JSON.

Následující části obsahují podrobné informace o vlastnostech JSON, které slouží k definování Data Factory entit.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující tabulka popisuje elementy JSON, které jsou specifické pro propojenou službu Oracle:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost **Type** musí být nastavená na **OnPremisesOracle**. |Yes |
| driverType | Určete, který ovladač má být použit ke zkopírování dat z databáze Oracle nebo do ní. Povolené hodnoty jsou **Microsoft** a **ODP** (výchozí). Podrobnosti o ovladači najdete v části [podporovaná verze a instalace](#supported-versions-and-installation) . | No |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci databáze Oracle pro vlastnost **ConnectionString** . | Yes |
| gatewayName | Název brány, který se používá pro připojení k místnímu serveru Oracle. |Yes |

**Příklad: použití ovladače společnosti Microsoft**

> [!TIP]
> Pokud se zobrazí chyba s názvem ORA-01025: UPI je mimo rozsah a Oracle je verze 8i, přidejte `WireProtocolMode=1` do svého připojovacího řetězce a zkuste to znovu:

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

**Příklad: použití ovladače ODP**

Další informace o povolených formátech najdete v tématu [poskytovatel dat Oracle pro .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam sekcí a vlastností, které jsou k dispozici pro definování datových sad, najdete v tématu [vytváření datových sad](data-factory-create-datasets.md).

Oddíly souboru JSON datové sady, jako je například struktura, dostupnost a zásada, jsou podobné pro všechny typy datových sad (například pro Oracle, úložiště objektů BLOB v Azure a úložiště tabulek Azure).

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl **typeProperties** pro datovou sadu typu **Oracle** má následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky v databázi Oracle, na kterou odkazuje propojená služba. |Ne (Pokud je zadáno **oracleReaderQuery** nebo **OracleSource** ) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md).

Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásada.

> [!NOTE]
> Aktivita kopírování používá pouze jeden vstup a vytváří pouze jeden výstup.

Vlastnosti, které jsou k dispozici v části **typeProperties** v aktivitě, se liší podle typu aktivity. Vlastnosti aktivity kopírování se liší v závislosti na typu zdroje a jímky.

### <a name="oraclesource"></a>OracleSource

V části aktivita kopírování, pokud je zdrojem **OracleSource** typ, jsou v oddílu **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| oracleReaderQuery |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu jazyka SQL. Například "vybrat \* z **myTable**". <br/><br/>Pokud není zadaný, spustí se tento příkaz SQL: "vybrat \* z **myTable**". |No<br />(Pokud je zadaný **TableName** **objektu DataSet** ) |

### <a name="oraclesink"></a>OracleSink

**OracleSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| writeBatchTimeout |Doba čekání na dokončení operace dávkového vložení před vypršením časového limitu. |**timespan**<br/><br/> Příklad: 00:30:00 (30 minut) |No |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne hodnoty **writeBatchSize**. |Integer (počet řádků) |Ne (výchozí: 100) |
| sqlWriterCleanupScript |Určuje dotaz pro aktivitu kopírování, která se má provést, aby se vyčistila data konkrétního řezu. |Příkaz dotazu. |No |
| sliceIdentifierColumnName |Určuje název sloupce pro aktivitu kopírování, která se má vyplnit automaticky generovaným identifikátorem řezu. Hodnota pro **sliceIdentifierColumnName** se používá k vyčištění dat určitého řezu při opakovaném spuštění. |Název sloupce sloupce, který má datový typ **Binary (32)**. |No |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Příklady JSON pro kopírování dat do a z databáze Oracle

V následujících příkladech jsou uvedeny ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). V příkladech se dozvíte, jak kopírovat data z nebo do databáze Oracle nebo do úložiště objektů BLOB v Azure. Data je ale možné zkopírovat do kterékoli z jímky uvedených v [podporovaných úložištích a formátech dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

**Příklad: kopírování dat z Oracle do úložiště objektů BLOB v Azure**

Ukázka má následující Data Factory entit:

* Propojená služba typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [Oracle](data-factory-onprem-oracle-connector.md#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako zdroj a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) jako jímku.

Ukázka kopíruje data z tabulky v místní databázi Oracle do objektu BLOB hourly. Další informace o různých vlastnostech, které se používají v ukázce, najdete v částech, které následují po ukázkách.

**Propojená služba Oracle**

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

**Propojená služba Azure Blob Storage**

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

Ukázka předpokládá, že jste v Oracle vytvořili tabulku s názvem **myTable** . Obsahuje sloupec s názvem **timestampcolumn** pro data časové řady.

Nastavení **External**: **true** informuje službu Data Factory, že datová sada je externí pro objekt pro vytváření dat a že datová sada není vytvořená aktivitou v datové továrně.

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

Data se zapisují do nového objektu BLOB každou hodinu (**frekvence**: **hodina**, **interval**: **1**). Cesta ke složce a název souboru pro objekt BLOB jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část roku, měsíce, dne a hodiny počátečního času.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a bylo naplánováno spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **OracleSource** a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL, který zadáte pomocí vlastnosti **oracleReaderQuery** , vybere data za uplynulou hodinu ke zkopírování.

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

**Příklad: kopírování dat ze služby Azure Blob Storage do Oracle**

V této ukázce se dozvíte, jak kopírovat data z účtu služby Azure Blob Storage do místní databáze Oracle. Data ale můžete kopírovat *přímo* z libovolného zdroje uvedeného v části [podporovaná úložiště a formáty dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

Ukázka má následující Data Factory entit:

* Propojená služba typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [Oracle](data-factory-onprem-oracle-connector.md#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) , který má aktivitu kopírování, která jako jímku používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) jako zdroj [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) .

Ukázka kopíruje data z objektu blob do tabulky v místní databázi Oracle každou hodinu. Další informace o různých vlastnostech, které se používají v ukázce, najdete v částech, které následují po ukázkách.

**Propojená služba Oracle**

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

**Propojená služba Azure Blob Storage**

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

Data se vybírají z nového objektu BLOB každou hodinu (**frekvence**: **hodina**, **interval**: **1**). Cesta ke složce a název souboru pro objekt BLOB jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část rok, měsíc a den v počátečním čase. Název souboru používá hodinovou část času spuštění. Nastavení **externí**: **true** informuje službu Data Factory, že tato tabulka je externí pro objekt pro vytváření dat a není vytvořená aktivitou v datové továrně.

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

**Výstupní datová sada Oracle**

Ukázka předpokládá, že jste v Oracle vytvořili tabulku s názvem **myTable** . Vytvořte tabulku v Oracle se stejným počtem sloupců, který očekáváte, že soubor CSV BLOB bude obsahovat. Nové řádky jsou do tabulky přidány každou hodinu.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a bylo naplánováno spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **BlobSource** a typ **jímky** je nastavený na **OracleSink**.

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

### <a name="problem-1-net-framework-data-provider"></a>Problém 1: .NET Framework Zprostředkovatel dat

**Chybová zpráva**

```text
Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.
```

**Možné příčiny**

* Zprostředkovatel dat .NET Framework pro Oracle nebyla nainstalována.
* Zprostředkovatel dat .NET Framework pro Oracle byl nainstalován do .NET Framework 2,0 a nenalezne se ve složce .NET Framework 4,0.

**Řešení**

* Pokud jste nenainstalovali poskytovatele .NET pro Oracle, [nainstalujte ho](https://www.oracle.com/technetwork/topics/dotnet/downloads/)a pak zkuste scénář zopakovat.
* Pokud se zobrazí chybová zpráva i po instalaci poskytovatele, proveďte následující kroky:
    1. Otevřete soubor konfigurace počítače pro .NET 2,0 ze složky <systémový disk \>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Vyhledejte **Oracle zprostředkovatel dat pro .NET**. Měli byste být schopni najít položku, jak je znázorněno v následujícím příkladu v části **System. data**  >  **DbProviderFactories**:`<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Zkopírujte tuto položku do souboru machine.config v následující složce .NET 4,0: <systémový disk \>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Pak změňte verzi na 4. xxx. x.x.
* Nainstalujte <cestu nainstalovanou \>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll ODP.NET do globální mezipaměti sestavení (GAC) spuštěním **Gacutil/i [cesta zprostředkovatele]**.

### <a name="problem-2-datetime-formatting"></a>Problém 2: formátování data a času

**Chybová zpráva**

```text
Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.
```

**Řešení**

Je možné, že budete muset upravit řetězec dotazu v aktivitě kopírování na základě toho, jak jsou data ve vaší databázi Oracle nakonfigurovaná. Tady je příklad (pomocí funkce **TO_DATE** ):

```console   
"oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"
```


## <a name="type-mapping-for-oracle"></a>Mapování typů pro Oracle

Jak je uvedeno v části [aktivity přesunu dat](data-factory-data-movement-activities.md), aktivita kopírování provádí automatické převody typů ze zdrojových typů do typů jímky pomocí následujících dvou kroků přístupu:

1. Převeďte z nativního zdrojového typu na typ .NET.
2. Převeďte z typu .NET na nativní typ jímky.

Když přesunete data z Oracle, použijí se z datového typu Oracle na typ .NET následující mapování a naopak:

| Datový typ Oracle | .NET Framework datový typ |
| --- | --- |
| BFILE |Byte [] |
| PŘÍZNAKY |Byte []<br/>(podporované jenom v Oracle 10g a novějších verzích při použití ovladače Microsoftu) |
| CHAR |Řetězec |
| DATOVÝ typ CLOB |Řetězec |
| DATE |DateTime |
| Plovák |Decimal, String (if Precision > 28) |
| CELÉ ČÍSLO |Decimal, String (if Precision > 28) |
| INTERVAL OD ROKU DO MĚSÍCE |Int32 |
| DRUHÝ DEN INTERVALU |TimeSpan |
| DLOUHOU |Řetězec |
| DLOUHO NEZPRACOVANÉ |Byte [] |
| NCHAR |Řetězec |
| NCLOB |Řetězec |
| Automatické |Decimal, String (if Precision > 28) |
| NVARCHAR2 |Řetězec |
| ZÍSKÁNÍ |Byte [] |
| ROWID |Řetězec |
| ČASOVÉ razítko |DateTime |
| ČASOVÉ RAZÍTKO S MÍSTNÍM ČASOVÝM PÁSMEM |DateTime |
| ČASOVÉ RAZÍTKO S ČASOVÝM PÁSMEM |DateTime |
| CELÉ ČÍSLO BEZ ZNAMÉNKA |Číslo |
| VARCHAR2 |Řetězec |
| XML |Řetězec |

> [!NOTE]
> Datové typy **intervalu roku do měsíce** a **druhý den v sekundách** nejsou podporovány při použití ovladače společnosti Microsoft.

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj na sloupce jímky

Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete v tématu [mapování sloupců datové sady v Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakované čtení z relačních zdrojů

Při kopírování dat z relačních úložišť dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez ručně znovu spustit. Pro datovou sadu můžete také nakonfigurovat zásady opakování, takže se řez znovu spustí, když dojde k selhání. Pokud je řez znovu spuštěn, buď ručně, nebo podle zásad opakování, ujistěte se, že stejná data jsou čtena bez ohledu na to, kolikrát je řez spuštěn. Další informace najdete v tématu [opakované čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění

Informace o klíčových faktorech ovlivňujících výkon přesunu dat (aktivita kopírování) v Azure Data Factory najdete v [Průvodci výkonem a optimalizací aktivity kopírování](data-factory-copy-activity-performance.md) . Můžete si také přečíst různé způsoby, jak je optimalizovat.
