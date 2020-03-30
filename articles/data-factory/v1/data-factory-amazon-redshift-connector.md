---
title: Přesunutí dat z Amazon U Redshift pomocí Azure Data Factory
description: Zjistěte, jak přesunout data z Amazon Redshift pomocí aktivity kopírování Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c2e2394bbcee5294bfb752a0af2969457ffff0ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260524"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Přesunutí dat z Amazon Redshift pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-amazon-redshift-connector.md)
> * [Verze 2 (aktuální verze)](../connector-amazon-redshift.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Konektor Amazon Redshift ve verzi 2](../connector-amazon-redshift.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat z Amazon Redshift. Článek vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

Data Factory v současné době podporuje pouze přesunutí dat z Amazon Redshift do [podporovaného úložiště dat jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Přesouvání dat z jiných úložišť dat do Amazon Redshift není podporováno.

> [!TIP]
> Chcete-li dosáhnout nejlepšího výkonu při kopírování velkého množství dat z Amazon Redshift, zvažte použití vestavěného příkazu Redshift **UNLOAD** prostřednictvím služby Amazon Simple Storage Service (Amazon S3). Podrobnosti naleznete [v tématu Kopírování dat z aplikace Amazon Redshift pomocí unload](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Požadavky
* Pokud přesouváte data do místního úložiště dat, nainstalujte [bránu pro správu dat](data-factory-data-management-gateway.md) do místního počítače. Udělte přístup pro bránu do clusteru Amazon Redshift pomocí místní IP adresy počítače. Pokyny naleznete v [tématu Autorizace přístupu ke clusteru](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Pokud chcete přesunout data do úložiště dat Azure, podívejte se na [výpočetní IP adresu a oblasti SQL, které používají datacentra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování přesunout data ze zdroje Amazon Redshift pomocí různých nástrojů a api.

Nejjednodušší způsob, jak vytvořit kanál, je použít Průvodce kopírováním dat Azure. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním naleznete v [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

Můžete také vytvořit kanál pomocí Visual Studia, Azure PowerShell, nebo jiné nástroje. Šablony Azure Resource Manager, rozhraní .NET API nebo rozhraní REST API lze také použít k vytvoření kanálu. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování naleznete v [tématu Kopírovat aktivitu .](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte propojené služby pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte datové sady představující vstupní a výstupní data pro operaci kopírování.
3. Vytvořte kanál s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce kopírováním jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory. Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete entity Factory dat pomocí formátu JSON. Příklad JSON: Kopírování dat z Amazon Redshift do úložiště objektů blob Azure zobrazuje definice JSON pro entity Data Factory, které se používají ke kopírování dat z úložiště dat Amazon Redshift.

Následující části popisují vlastnosti JSON, které se používají k definování entit Data Factory pro Amazon Redshift.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Následující tabulka obsahuje popisy prvků JSON, které jsou specifické pro propojenou službu Amazon Redshift.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **Typ** |Tato vlastnost musí být nastavena na **AmazonRedshift**. |Ano |
| **server** |IP adresa nebo název hostitele serveru Amazon Redshift. |Ano |
| **Port** |Číslo portu TCP, který server Amazon Redshift používá k naslouchání pro připojení klientů. |Ne (výchozí hodnota je 5439) |
| **Databáze** |Název databáze Amazon Redshift. |Ano |
| **Username** |Jméno uživatele, který má přístup k databázi. |Ano |
| **heslo** |Heslo pro uživatelský účet. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady

Seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) **Oddíly struktury**, **dostupnosti**a **zásad** jsou podobné pro všechny typy datových sad. Příklady typů datových sad zahrnují Azure SQL, azure blob storage a Azure Table storage.

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. **Oddíl typeProperties** pro datovou sadu typu **RelationalTable**, která obsahuje datovou sadu Amazon Redshift, má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **Tablename** |Název tabulky v databázi Amazon Redshift, na kterou odkazuje propojená služba. |Ne (pokud je zadána vlastnost **dotazu** aktivity kopírování typu **RelationalSource)** |

## <a name="copy-activity-properties"></a>Kopírovat vlastnosti aktivity

Seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) **Název**, **popis**, **tabulka vstupů,** tabulka **výstupů** a vlastnosti **zásad** jsou k dispozici pro všechny typy aktivit. Vlastnosti, které jsou k dispozici v části **typeProperties,** se liší pro každý typ aktivity. U aktivity kopírování se vlastnosti liší v závislosti na typech zdrojů dat a jímek.

Pro aktivitu kopírování, pokud je zdroj typu **AmazonRedshiftSource**, jsou v části **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **query** | Ke čtení dat použijte vlastní dotaz. |Ne (pokud je zadána vlastnost **tableName** datové sady) |
| **redshiftUnloadSettings** | Obsahuje skupinu vlastností při použití příkazu Redshift **UNLOAD.** | Ne |
| **s3LinkedServiceName** | Amazon S3 použít jako prozatímní obchod. Propojená služba je určena pomocí názvu Azure Data Factory typu **AwsAccessKey**. | Povinné při použití vlastnosti **redshiftUnloadSettings** |
| **název_bloku** | Označuje kontejner Amazon S3, který se má použít k uložení dočasných dat. Pokud tato vlastnost není k dispozici, zkopírujte aktivitu automaticky generuje bloku. | Povinné při použití vlastnosti **redshiftUnloadSettings** |

Alternativně můžete použít **Typ RelationalSource,** který zahrnuje Amazon Redshift, s následující vlastností v části **typeProperties.** Poznámka: Tento typ zdroje nepodporuje příkaz Redshift **UNLOAD.**

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **query** |Ke čtení dat použijte vlastní dotaz. | Ne (pokud je zadána vlastnost **tableName** datové sady) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Použití UNLOAD ke kopírování dat z Amazon Redshift

Příkaz Amazon Redshift [**UNLOAD**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) uvolní výsledky dotazu do jednoho nebo více souborů na Amazon S3. Tento příkaz je doporučen společností Amazon pro kopírování velkých datových sad z Redshift.

**Příklad: Kopírování dat z Amazon Redshift do Azure SQL Data Warehouse**

Tento příklad zkopíruje data z Amazon Redshift do Azure SQL Data Warehouse. Příklad používá příkaz Redshift **UNLOAD,** data fázované kopie a Microsoft PolyBase.

V tomto případě ukázkového použití zkopíruje aktivita nejprve uvolní data z Amazon Redshift na Amazon S3, jak je nakonfigurováno v **redshiftUnloadSettings** možnost. Dále se data zkopírují z Amazon S3 do úložiště objektů blob Azure, jak je uvedeno v **možnosti stagingSettings.** Nakonec PolyBase načte data do datového skladu SQL. Všechny dočasné formáty jsou zpracovány aktivitou kopírování.

![Kopírování pracovního postupu z Amazon Redshift do datového skladu SQL](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Příklad JSON: Kopírování dat z Amazon Redshift do úložiště objektů blob Azure
Tato ukázka ukazuje, jak zkopírovat data z databáze Amazon Redshift do úložiště objektů blob Azure. Data lze zkopírovat přímo do libovolné [podporované jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování.

Ukázka má následující entity datové továrny:

* Propojená služba typu [AmazonRedshift](#linked-service-properties)
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties)
* Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá vlastnosti [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Ukázka zkopíruje data z výsledku dotazu v Amazon Redshift na objekt blob Azure každou hodinu. Vlastnosti JSON, které se používají v vzorku jsou popsány v částech, které následují definice entit.

**Amazon Redshift propojené služby**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Propojená služba úložiště objektů blob Azure**

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
**Vstupní datová sada Amazon Redshift**

**Externí** vlastnost je nastavena na "true" informovat službu Data Factory, že datová sada je externí pro datové továrny. Toto nastavení vlastnosti označuje, že datová sada není vytvořena aktivitou v datové továrně. Nastavte vlastnost na hodnotu true na vstupní datové sadě, která není vytvořena aktivitou v kanálu.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu blob každou hodinu nastavením **vlastnosti frekvence** na "Hodina" a vlastnost **interval** na 1. Vlastnost **folderPath** pro objekt blob je dynamicky vyhodnocován. Hodnota vlastnosti je založena na počátečním čase řezu, který je zpracováván. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kopírování aktivity v kanálu se zdrojem Azure Redshift (typu Relační zdroj) a jímkou blob Azure**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad. Kanál je naplánováno spustit každou hodinu. V definici JSON pro kanál je **typ zdroje** nastaven na **RelationalSource** a typ **jímky** je nastaven na **Objekt blobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data, která chcete kopírovat z poslední hodiny.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Mapování typů pro Amazon Redshift
Jak je uvedeno v článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) aktivita kopírování provádí automatické převody typů z typu zdroje na typ jímky. Typy jsou převedeny pomocí dvoustupňového přístupu:

1. Převod z nativního typu zdroje na typ .NET
2. Převod z typu .NET na nativní typ jímky

Následující mapování se používají při kopírování aktivity převede data z typu Amazon Redshift na typ .NET:

| Amazon Redshift typ | Typ .NET |
| --- | --- |
| Smallint |Int16 |
| CELÉ ČÍSLO |Int32 |
| Bigint |Int64 |
| Desetinných |Desetinné číslo |
| REÁLNÉ |Single |
| DVOJITÁ PŘESNOST |Double |
| Boolean |Řetězec |
| Char |Řetězec |
| Varchar |Řetězec |
| DATE (Datum) |DateTime |
| Časové razítko |DateTime |
| TEXT |Řetězec |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj pro jímací sloupce
Informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete [v tématu Mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Opakovatelné čtení z relačních zdrojů
Při kopírování dat z úložiště relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete znovu spustit řez ručně. Můžete také nakonfigurovat **zásady** opakování pro datovou sadu znovu spustit řez, když dojde k selhání. Ujistěte se, že jsou čtena stejná data, bez ohledu na to, kolikrát je řez znovu spuštěn. Také se ujistěte, že stejná data se čte bez ohledu na to, jak znovu spustit řez. Další informace naleznete v tématu [Opakovatelné čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
Seznamte se s klíčovými faktory, které ovlivňují výkon aktivity kopírování, a způsoby optimalizace výkonu v [průvodci výkonem a laděním aktivity kopírování](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Další kroky
Podrobné pokyny k vytvoření kanálu s aktivitou kopírování naleznete v [kurzu Kopírovat aktivitu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
