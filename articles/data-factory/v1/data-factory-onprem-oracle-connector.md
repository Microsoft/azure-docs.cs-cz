---
title: Kopírování dat do nebo z oracle pomocí data factory
description: Zjistěte, jak kopírovat data do nebo z místní databáze Oracle pomocí Azure Data Factory.
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
ms.openlocfilehash: 066e32d5ab21f88b170498173606043c54fec586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265854"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Kopírování dat do nebo z místního řešení Oracle pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-onprem-oracle-connector.md)
> * [Verze 2 (aktuální verze)](../connector-oracle.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Azure Data Factory, přečtěte si téma [Oracle connector ve verzi 2](../connector-oracle.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesunout data do nebo z místní databáze Oracle. Článek vychází z [aktivit přesunu dat](data-factory-data-movement-activities.md), který představuje obecný přehled přesunu dat pomocí aktivity kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře

Data z *databáze Oracle* můžete zkopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete zkopírovat *do databáze Oracle*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Požadavky

Data Factory podporuje připojení k místním zdrojům Oracle pomocí brány pro správu dat. Další informace o bráně pro správu dat najdete v tématu [Brána pro správu](data-factory-data-management-gateway.md) dat. Podrobné pokyny k nastavení brány v datovém kanálu pro přesun dat najdete [v tématu Přesun dat z místního do cloudu](data-factory-move-data-between-onprem-and-cloud.md).

Brána je vyžadována i v případě, že Oracle je hostovaný v infrastruktuře Azure jako virtuální počítač (IaaS). Bránu můžete nainstalovat na stejný virtuální počítač IaaS jako úložiště dat nebo na jiný virtuální počítač, pokud se brána může připojit k databázi.

> [!NOTE]
> Tipy k řešení problémů souvisejících s připojením a bránou najdete v [tématu Poradce při potížích s bránou](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace

Tento konektor Oracle podporuje dvě verze ovladačů:

- **Ovladač Microsoft pro Oracle (doporučeno):** Počínaje bránou pro správu dat verze 2.7 je automaticky nainstalován ovladač společnosti Microsoft pro oracle s bránou. Není nutné instalovat nebo aktualizovat ovladač k navázání připojení k oracle. Můžete také zaznamenat lepší výkon kopírování pomocí tohoto ovladače. Tyto verze databází Oracle jsou podporovány:
  - Oracle 12c R1 (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Proxy server Oracle není podporován.

    > [!IMPORTANT]
    > V současné době ovladač společnosti Microsoft pro oracle podporuje pouze kopírování dat z oracle. Ovladač nepodporuje psaní do Oracle. Možnost testovacího připojení na kartě **Diagnostika** brány pro správu dat tento ovladač nepodporuje. Případně můžete k ověření připojení použít Průvodce kopírováním.
    >

- **Oracle Data Provider for .NET**: Pomocí poskytovatele dat Oracle můžete kopírovat data z nebo do společnosti Oracle. Tato součást je součástí [součásti Oracle Data Access Components for Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Nainstalujte příslušnou verzi (32bitovou nebo 64bitovou) do počítače, ve kterém je brána nainstalována. [Oracle Data Provider .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) má přístup k oracle database 10g verze 2 a novější mj.

    Pokud vyberete **možnost Instalace XCopy**, proveďte kroky popsané v souboru readme.htm. Doporučujeme vybrat instalační program, který má uživatelské tlačítko (nikoli instalační program XCopy).

    Po instalaci zprostředkovatele restartujte hostitelskou službu Brány pro správu dat v počítači pomocí nástroje aplet služby nebo správce konfigurace brány pro správu dat.

Pokud použijete průvodce kopírováním k vytvoření kanálu kopírování, typ ovladače se automaticky určí. Ovladač společnosti Microsoft se používá ve výchozím nastavení, pokud vaše verze brány není starší než verze 2.7 nebo pokud nevyberete oracle jako jímku.

## <a name="get-started"></a>Začínáme

Můžete vytvořit kanál, který má aktivitu kopírování. Kanál přesouvá data do nebo z místní databáze Oracle pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál, je použít Průvodce kopírováním. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.

K vytvoření kanálu můžete taky použít jeden z následujících nástrojů: **Visual Studio**, **Azure PowerShell**, **šablonu Azure Resource Manager**, rozhraní **.NET API**nebo ROZHRANÍ REST **API**. Podrobné pokyny k vytvoření kanálu, který má aktivitu kopírování, najdete v [kurzu Kopírovat aktivitu.](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Bez ohledu na to, zda používáte nástroje nebo api, proveďte následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Továrna dat může obsahovat jeden nebo více kanálů.
2. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat. Pokud například kopírujete data z databáze Oracle do úložiště objektů Blob Azure, vytvořte dvě propojené služby, které propojí databázi Oracle a účet úložiště Azure s vaší továrně dat. Vlastnosti propojených služeb, které jsou specifické pro řešení Oracle, naleznete [v tématu Vlastnosti propojených služeb](#linked-service-properties).
3. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování. V příkladu v předchozím kroku vytvoříte datovou sadu, která určí tabulku v databázi Oracle, která obsahuje vstupní data. Vytvoříte další datovou sadu k určení kontejneru objektů blob a složky, která obsahuje data zkopírovaná z databáze Oracle. Vlastnosti datové sady, které jsou specifické pro oracle, naleznete [v tématu Vlastnosti datové sady](#dataset-properties).
4. Vytvořte **kanál,** který má aktivitu kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V předchozím příkladu použijete **OracleSource** jako zdroj a **BlobSink** jako jímku pro aktivitu kopírování. Podobně pokud kopírujete z úložiště objektů blob Azure do databáze Oracle, použijete v aktivitě kopírování **objekt blobSource** a **OracleSink.** Vlastnosti Kopírovat aktivitu, které jsou specifické pro databázi Oracle, naleznete v [tématu Kopírování vlastností aktivity](#copy-activity-properties). Podrobnosti o tom, jak použít úložiště dat jako zdroj nebo jímku, vyberte odkaz pro úložiště dat v předchozí části.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory: propojené služby, datové sady a kanál. Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON. Ukázky, které mají definice JSON pro entity Data Factory, které slouží ke kopírování dat do nebo z místní databáze Oracle, najdete v příkladech JSON.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které slouží k definování entit Data Factory.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Následující tabulka popisuje prvky JSON, které jsou specifické pro propojenou službu Oracle:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost **type** musí být nastavena **na OnPremisesOracle**. |Ano |
| type ovladače | Určete, který ovladač se má použít ke kopírování dat z databáze Oracle nebo do ní. Povolené hodnoty jsou **Microsoft** a **ODP** (výchozí). Podrobnosti o ovladači naleznete [v části Podporovaná verze a instalace.](#supported-versions-and-installation) | Ne |
| připojovací řetězec | Zadejte informace potřebné pro připojení k instanci databáze Oracle pro vlastnost **connectionString.** | Ano |
| název brány | Název brány, která se používá pro připojení k místnímu serveru Oracle. |Ano |

**Příklad: Použití ovladače společnosti Microsoft**

> [!TIP]
> Pokud se zobrazí chyba "ORA-01025: UPI parametr mimo rozsah" a vaše `WireProtocolMode=1` Oracle je verze 8i, přidejte do připojovacího řetězce a zkuste to znovu:

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

Další informace o povolených formátech naleznete v [tématu Oracle data provider for .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu Vytváření datových sad](data-factory-create-datasets.md).

Části souboru JSON datové sady, jako je struktura, dostupnost a zásady, jsou podobné pro všechny typy datových sad (například pro Oracle, Azure Blob storage a Azure Table storage).

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Část **typeProperties** pro datovou sadu typu **OracleTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v databázi Oracle, na kterou propojená služba odkazuje. |Ne (pokud je zadán **oracleReaderQuery** nebo **OracleSource)** |

## <a name="copy-activity-properties"></a>Kopírovat vlastnosti aktivity

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete [v tématu Vytváření kanálů](data-factory-create-pipelines.md).

Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování přebírá pouze jeden vstup a vytváří pouze jeden výstup.

Vlastnosti, které jsou k dispozici v části **typeProperties** aktivity se liší podle každého typu aktivity. Kopírovat Aktivita vlastnosti se liší v závislosti na typu zdroje a jímky.

### <a name="oraclesource"></a>Zdroj OracleSource

V části Aktivita kopírování **OracleSource** jsou v části **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| oracleReaderQuery |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Například "select \* from **MyTable**". <br/><br/>Pokud není zadán, je tento příkaz \* SQL proveden: "select from **MyTable**" |Ne<br />(pokud je zadán **název tabulky** **datové sady)** |

### <a name="oraclesink"></a>Řešení OracleSink

**Řešení OracleSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Čekací doba pro operaci dávkové vložení k dokončení před časovým oběhem. |**Timespan**<br/><br/> Příklad: 00:30:00 (30 minut) |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne hodnoty **writeBatchSize**. |Celé číslo (počet řádků) |Ne (výchozí: 100) |
| sqlWriterCleanupScript |Určuje dotaz pro aktivitu kopírování, který má být spuštěn tak, aby byla vyčištěna data určitého řezu. |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Určuje název sloupce pro aktivitu kopírování, který má být vyplnín automaticky generovaným identifikátorem řezu. Hodnota pro **sliceIdentifierColumnName** se používá k vyčištění dat určitého řezu při opětovném spuštění. |Název sloupce, který má datový typ **binární(32)**. |Ne |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Příklady JSON pro kopírování dat do a z databáze Oracle

Následující příklady poskytují ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Příklady ukazují, jak zkopírovat data z nebo do databáze Oracle a do úložiště objektů blob Azure nebo z úložiště objektů Blob Azure. Data však můžete zkopírovat do libovolného jímky uvedené v [podporovaných úložišť dat a formátů](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

**Příklad: Kopírování dat z Oracle do úložiště objektů blob Azure**

Ukázka má následující entity Data Factory:

* Propojená služba typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako zdroj a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) jako jímku.

Ukázka zkopíruje data z tabulky v místní databázi Oracle do objektu blob každou hodinu. Další informace o různých vlastnostech, které se používají v ukázce, naleznete v částech, které následují za ukázkami.

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

**Propojená služba úložiště objektů blob Azure**

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

Ukázka předpokládá, že jste vytvořili tabulku s názvem **MyTable** v Oracle. Obsahuje sloupec s názvem **časový sloupec** pro data časových řad.

Nastavení **externí**: **true** informuje službu Data Factory, že datová sada je externí pro datové továrny a že datová sada není vytvořena aktivitou v datové továrně.

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

Data se zapisují do nového objektu blob každou hodinu (**frekvence**: **hodina**, **interval**: **1**). Cesta ke složce a název souboru pro objekt blob jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část počátečního času rok, měsíc, den a hodinu.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a naplánovaná na hodinové spuštění. V definici kanálu JSON je typ **zdroje** nastaven na **OracleSource** a typ **jímky** je nastaven na **objekt BlobSink**. Dotaz SQL, který zadáte pomocí **vlastnosti oracleReaderQuery,** vybere data za poslední hodinu ke kopírování.

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

**Příklad: Kopírování dat z úložiště objektů blob Azure do oracle**

Tato ukázka ukazuje, jak zkopírovat data z účtu úložiště objektů blob Azure do místní databáze Oracle. Data však můžete kopírovat *přímo* z libovolného zdroje uvedeného v [podporovaných úložištích a formátech dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí kopírování aktivity v Azure Data Factory.

Ukázka má následující entity Data Factory:

* Propojená služba typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
* [Kanál,](data-factory-create-pipelines.md) který má aktivitu kopírování, která používá [objekt BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) jako zdroj [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako jímky.

Ukázka zkopíruje data z objektu blob do tabulky v místní databázi Oracle každou hodinu. Další informace o různých vlastnostech, které se používají v ukázce, naleznete v částech, které následují za ukázkami.

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

**Propojená služba úložiště objektů blob Azure**

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

Data se zosbírají z nového objektu blob každou hodinu (**frekvence**: **hodina**, **interval**: **1**). Cesta ke složce a název souboru pro objekt blob jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část počátečního času rok, měsíc a den. Název souboru používá hodinovou část počátečního času. Nastavení **externí**: **true** informuje službu Data Factory, že tato tabulka je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

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

Ukázka předpokládá, že jste vytvořili tabulku s názvem **MyTable** v Oracle. Vytvořte tabulku v oracle se stejným počtem sloupců, které očekáváte, že soubor CSV objektu blob obsahovat. Nové řádky jsou přidávány do tabulky každou hodinu.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a naplánovaná na spuštění každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **objekt BlobSource** a typ **jímky** je nastaven na **OracleSink**.

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

### <a name="problem-1-net-framework-data-provider"></a>Problém 1: Zprostředkovatel dat rozhraní .NET Framework

**Chybová zpráva**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**Možné příčiny**

* Zprostředkovatel dat rozhraní .NET Framework pro oracle nebyl nainstalován.
* Zprostředkovatel dat rozhraní .NET Framework pro oracle byl nainstalován do rozhraní .NET Framework 2.0 a nebyl nalezen ve složkách rozhraní .NET Framework 4.0.

**Rozlišení**

* Pokud jste nenainstalovali zprostředkovatele .NET pro oracle, [nainstalujte jej](https://www.oracle.com/technetwork/topics/dotnet/downloads/)a opakujte scénář.
* Pokud se chybová zpráva zobrazí i po instalaci zprostředkovatele, proveďte následující kroky:
    1. Otevřete konfigurační soubor počítače pro rozhraní .NET\>2.0 ze složky <systémovém disku :\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Vyhledejte **zprostředkovatele dat Oracle pro rozhraní .NET**. Měli byste být schopni najít položku, jak je znázorněno v následující ukázce pod **system.data** > **DbProviderFactories**:`<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Zkopírujte tuto položku do souboru Machine.config v následující složce .NET 4.0: <systémový disk\>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Potom změňte verzi na 4.xxx.x.x.
* Nainstalujte <\>ODP.NET nainstalovanou cestu \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll v globální mezipaměti sestavení (GAC) spuštěním **gacutil /i [cesta poskytovatele]**.

### <a name="problem-2-datetime-formatting"></a>Problém 2: Formátování data a času

**Chybová zpráva**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Rozlišení**

Řetězec dotazu v aktivitě kopírování může být nutné upravit na základě konfigurace kalendářních dat v databázi Oracle. Zde je příklad (pomocí **funkce to_date):**

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mapování typů pro oracle

Jak je uvedeno v [aktivitách přesunu dat](data-factory-data-movement-activities.md), aktivita kopírování provádí automatické převody typů z typů zdrojů na typy jímek pomocí následujícího dvoustupňového přístupu:

1. Převést z nativních typů zdrojů na typ .NET.
2. Převést z typu .NET na nativní typ jímky.

Při přesunu dat z oracle se používají následující mapování z datového typu Oracle na typ .NET a naopak:

| Datový typ Oracle | Datový typ rozhraní .NET Framework |
| --- | --- |
| BFILE |Bajt[] |
| Blob |Bajt[]<br/>(podporováno pouze v oracle 10g a novějších verzích při použití ovladače společnosti Microsoft) |
| Char |Řetězec |
| Clob |Řetězec |
| DATE (Datum) |DateTime |
| Float |Desetinné číslo, řetězec (pokud je přesnost > 28) |
| CELÉ ČÍSLO |Desetinné číslo, řetězec (pokud je přesnost > 28) |
| INTERVAL ROK OD MĚSÍCE |Int32 |
| INTERVAL ZE DNE NA DRUHÝ |TimeSpan |
| Dlouhé |Řetězec |
| DLOUHÉ RAW |Bajt[] |
| Nchar |Řetězec |
| Nclob |Řetězec |
| Číslo |Desetinné číslo, řetězec (pokud je přesnost > 28) |
| NVARCHAR2 |Řetězec |
| Syrové |Bajt[] |
| ROWID |Řetězec |
| Časové razítko |DateTime |
| ČASOVÉ RAZÍTKO S MÍSTNÍM ČASOVÝM PÁSMEM |DateTime |
| ČASOVÉ RAZÍTKO S ČASOVÝM PÁSMEM |DateTime |
| NEPODEPSANÉ CELÉ ČÍSLO |Číslo |
| VARCHAR2 |Řetězec |
| XML |Řetězec |

> [!NOTE]
> Datové typy **INTERVAL INTERVAL ROK OD MĚSÍC** A INTERVAL OD DNE DO **SEKUND** NEJSOU podporovány při použití ovladače společnosti Microsoft.

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj pro jímací sloupce

Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete [v tématu Mapování sloupců datové sady v Datové továrně](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení ze relačních zdrojů

Při kopírování dat z úložišť relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete ručně znovu spustit řez. Můžete také nakonfigurovat zásady opakování pro datovou sadu tak, aby řez je znovu spustit, když dojde k selhání. Při opětovném spuštění řezu, ručně nebo pomocí zásady opakování, ujistěte se, že stejná data jsou čtena bez ohledu na to, kolikrát je řez spuštěn. Další informace naleznete v tématu [Opakovatelné čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění

V průvodci [výkonem a laděním aktivity kopírování](data-factory-copy-activity-performance.md) najdete informace o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) v Azure Data Factory. Můžete se také dozvědět o různých způsobech optimalizace.
