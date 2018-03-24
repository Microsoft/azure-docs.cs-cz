---
title: Přesun dat do/z Azure Table | Microsoft Docs
description: Další informace o přesunutí dat z úložiště tabulek Azure pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2c560bf9eeac5f9aed1024bd87d450f7aee7ce05
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Přesun dat do a z Azure Table pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-azure-table-connector.md)
> * [Verze 2 – Preview](../connector-azure-table-storage.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [konektor Azure Table Storage v V2](../connector-azure-table-storage.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z úložiště tabulek Azure. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování. 

Data můžete zkopírovat z jakékoli podporované zdrojové úložiště dat pro úložiště tabulek Azure nebo z úložiště tabulek Azure do úložiště dat žádné podporované jímky. Seznam úložišť dat jako zdroje nebo jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. 

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z Azure Table Storage pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený: 

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat do nebo ze Azure Table Storage, najdete v části [JSON příklady](#json-examples) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k definování konkrétní entity služby Data Factory pro Azure Table Storage: 

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Existují dva typy propojené služby, které můžete použít k propojení Azure blob storage do Azure data factory. Jsou: **azurestorage** propojená služba a **AzureStorageSas** propojené služby. Propojenou službu úložiště Azure poskytuje objekt pro vytváření dat s globálním přístupem k úložišti Azure. Zatímco úložiště SAS Azure (sdíleného přístupového podpisu) propojená služba poskytuje objekt pro vytváření dat s přístupem omezený nebo časově vázaných do úložiště Azure. Nejsou žádné další rozdíly mezi tyto dvě propojené služby. Zvolte propojené služby, která vyhovuje vašim potřebám. Následující části obsahují další podrobnosti na tyto dvě propojené služby.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

V rámci typeProperties části se liší pro jednotlivé typy datovou sadu a poskytuje informace o umístění dat v úložišti. **Rámci typeProperties** části datové sady typu **AzureTable** má následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance Azure tabulku databáze, kterou propojená služba odkazuje. |Ano. Pokud název tabulky je zadán bez azureTableSourceQuery, všechny záznamy z tabulky se zkopírují do cílového umístění. Pokud je zadána také azureTableSourceQuery, záznamy z tabulky, která splňuje dotaz zkopírují do cílového umístění. |

### <a name="schema-by-data-factory"></a>Schéma službou Data Factory
Služba Data Factory pro data bez schémat úložiště, jako je například Azure Table, odvodí schéma v jednom z následujících způsobů:

1. Pokud zadáte strukturu dat pomocí **struktura** vlastnost v definici datové sady, služba Data Factory ctí tato struktura jako schéma. V takovém případě Pokud řádek neobsahuje hodnotu pro sloupec, je pro něj zadat hodnotu null.
2. Pokud nezadáte strukturu dat pomocí **struktura** vlastnost v definici datové sady, Data Factory odvodí, že schéma pomocí prvního řádku v datech. V takovém případě pokud první řádek neobsahuje úplnou schéma, jsou vynechalo některé sloupce ve výsledku operace kopírování.

Osvědčeným postupem pro zdroje dat bez schémat, proto je zadat strukturu dat pomocí **struktura** vlastnost.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní datové sady a zásad jsou dostupné pro všechny typy aktivit.

Vlastnosti dostupné v rámci typeProperties části aktivity se liší na druhé straně každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

**AzureTableSource** podporuje následující vlastnosti v rámci typeProperties části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| azureTableSourceQuery |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu tabulky Azure. Příklady v další části. |Ne. Pokud název tabulky je zadán bez azureTableSourceQuery, všechny záznamy z tabulky se zkopírují do cílového umístění. Pokud je zadána také azureTableSourceQuery, záznamy z tabulky, která splňuje dotaz zkopírují do cílového umístění. |
| azureTableSourceIgnoreTableNotFound |Označuje, zda swallow výjimky tabulky neexistuje. |HODNOTA TRUE<br/>FALSE |Ne |

### <a name="azuretablesourcequery-examples"></a>Příklady azureTableSourceQuery
Pokud sloupec tabulky Azure je typu řetězec:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Pokud sloupec tabulky Azure je typu datum a čas:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** podporuje následující vlastnosti v rámci typeProperties části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Výchozí hodnotu klíče oddílu, mohou být využívána jímky. |Hodnotu řetězce. |Ne |
| azureTablePartitionKeyName |Zadejte název sloupce, jejichž hodnoty se používají jako klíče oddílů. Pokud není zadaný, použije se AzureTableDefaultPartitionKeyValue jako klíč oddílu. |Název sloupce. |Ne |
| azureTableRowKeyName |Zadejte název sloupce, jejichž hodnoty sloupce jsou použity jako klíč řádku. Pokud není zadaný, použijte identifikátor GUID pro každý řádek. |Název sloupce. |Ne |
| azureTableInsertType |Režim vložení dat do tabulky Azure.<br/><br/>Tato vlastnost určuje, jestli mají existující řádky v tabulce output s odpovídajícím klíče oddílu a řádku jejich hodnoty nahradit nebo sloučit. <br/><br/>Další informace o tom, jak tato nastavení (sloučení a nahraďte) fungují, najdete v části [vložení nebo sloučení Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) a [vložení nebo nahrazení Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx) témata. <br/><br> Toto nastavení se vztahuje na úrovni řádků, není úrovni tabulky a ani možnost odstraní řádků do výstupní tabulky, které nejsou k dispozici ve vstupu. |Merge (výchozí)<br/>Nahradit |Ne |
| writeBatchSize |Když je dosaženo writeBatchSize nebo writeBatchTimeout vkládá data do tabulky Azure. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Když je dosaženo writeBatchSize nebo writeBatchTimeout vkládá data do tabulky Azure |Časový interval<br/><br/>Příklad: "00:20:00" (20 minut) |Ne (výchozí nastavení časového limitu výchozí úložiště klienta hodnotu 90 sekundu) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Zdrojový sloupec namapujte na cílový sloupec pomocí překladač JSON vlastnost, abyste mohli používat jako azureTablePartitionKeyName cílový sloupec.

V následujícím příkladu je zdrojový sloupec DivisionID namapovaný na cílový sloupec: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
DivisionID je zadán jako klíč oddílu.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>Příklady JSON
Následující příklady poskytují ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se ukazují, jak ke zkopírování dat do a z Azure Table Storage a Azure Blob databáze. Nicméně je možné zkopírovat data **přímo** ze všech zdrojů do jakéhokoli z podporovaném jímky. Další informace najdete v části "podporované úložiště dat a formáty" v [přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Příklad: Kopírování dat z Azure Table do objektů Blob v Azure
Následující příklad ukazuje:

1. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties) (používá se pro objekt blob & tabulky).
2. Vstup [datovou sadu](data-factory-create-datasets.md) typu [AzureTable](#dataset-properties).
3. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. [Kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [AzureTableSource](#activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data patřící do výchozí oddíl v tabulce do objektu blob Azure každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

**Propojená služba úložiště Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Podporuje dva typy Azure Storage, propojené služby Azure Data Factory: **azurestorage** a **AzureStorageSas**. Pro první zadejte připojovací řetězec, který obsahuje klíč účtu a pro novější verzi, zadejte identifikátor Uri sdíleného přístupového podpisu (SAS). V tématu [propojené služby](#linked-service-properties) podrobnosti.  

**Azure Table vstupní datové sady:**

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v Azure Table.

Nastavení "externí": "PRAVDA" informuje služba Data Factory, datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
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

**Azure Blob výstupní datovou sadu:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

```JSON
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

**Aktivita kopírování v kanálu s AzureTableSource a BlobSink:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **AzureTableSource** a **podřízený** je typ nastaven na **BlobSink**. Příkaz jazyka SQL zadaným **AzureTableSourceQuery** vlastnost vybere data z oddílu výchozí každou hodinu pro kopírování.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                      {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                      {
                            "name": "AzureBlobOutput"
                      }
                ],
                "typeProperties": {
                      "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Příklad: Kopírování dat z objektu Blob Azure do Azure Table
Následující příklad ukazuje:

1. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties) (používá se pro objekt blob & tabulky)
2. Vstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureTable](#dataset-properties).
4. [Kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [AzureTableSink](#copy-activity-properties).

Kopie ukázka časové řady dat z Azure blob do Azure tabulky každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

**Propojená služba Azure storage (pro Azure Table & objektů Blob):**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

Podporuje dva typy Azure Storage, propojené služby Azure Data Factory: **azurestorage** a **AzureStorageSas**. Pro první zadejte připojovací řetězec, který obsahuje klíč účtu a pro novější verzi, zadejte identifikátor Uri sdíleného přístupového podpisu (SAS). V tématu [propojené služby](#linked-service-properties) podrobnosti.

**Azure vstupní datovou sadu objektu Blob:**

Data je převzata z nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Název složky a cesta k souboru pro tento objekt blob se vyhodnocují dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc a den součástí čas spuštění a název souboru používá hodinu součástí čas spuštění. "externí": "PRAVDA" nastavení informuje služba Data Factory, že datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
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

**Tabulky Azure výstupní datovou sadu:**

Ukázka zkopíruje data na tabulku s názvem "MyTable" v tabulce Azure. Vytvoření Azure tabulky s stejný počet sloupců, podle očekávání souboru CSV objektů Blob tak, aby obsahovala. Nové záznamy se přidají do tabulky každou hodinu.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Aktivita kopírování v kanálu s BlobSource a AzureTableSink:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **podřízený** je typ nastaven na **AzureTableSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
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
## <a name="type-mapping-for-azure-table"></a>Typ mapování pro tabulku Azure
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup ve dvou krocích.

1. Převést na typ .NET typy nativní zdrojů
2. Převést na typ jímky nativní typ formátu .NET

Při přesunu dat do a z Azure Table, následující [mapování definovaná službou Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) se používají ve typy OData tabulky Azure na typ .NET a naopak.

| Typ dat OData | .NET Type | Podrobnosti |
| --- | --- | --- |
| Edm.Binary |Byte |Pole bajtů až 64 KB. |
| Edm.Boolean |BOOL |Logická hodnota. |
| Edm.DateTime |DateTime |Hodnota 64-bit, vyjádřené jako koordinovaný světový čas (UTC). Podporovaný rozsah datum a čas zahájení z 12:00 hodin, 1, 1601. ledna (C.E.), UTC. Rozsah končí u 31. prosince 9999. |
| Edm.Double |Double |Hodnota 64-bit plovoucí bodu. |
| Edm.Guid |Guid |Globálně jedinečný identifikátor 128-bit. |
| Edm.Int32 |Int32 |32bitové celé číslo. |
| Edm.Int64 |Int64 |64bitové celé číslo. |
| Edm.String |Řetězec |Hodnota kódování UTF-16. Řetězcové hodnoty může mít až 64 KB. |

### <a name="type-conversion-sample"></a>Ukázka převod typu
Následující příklad je pro kopírování dat z objektu Blob Azure do Azure Table s převody typů.

Předpokládejme, že datovou sadu objektu Blob je ve formátu CSV a obsahuje tři sloupce. Jeden z nich je sloupec Datum a čas ve formátu data a času vlastní pomocí zkrácené francouzštině názvy den v týdnu.

Definování datové sady zdroje Blob takto společně s definic typů pro sloupce.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
         "structure":
          [
                { "name": "userid", "type": "Int64"},
                { "name": "name", "type": "String"},
                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
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
Zadané mapování typu z typu OData tabulky Azure na typ .NET, by definovat v tabulce v Azure Table s následující schéma.

**Schéma tabulky Azure:**

| Název sloupce | Typ |
| --- | --- |
| ID uživatele |Edm.Int64 |
| jméno |Edm.String |
| lastlogindate |Edm.DateTime |

V dalším kroku definování datové sady Azure Table následujícím způsobem. Nemusíte určit "struktura" části s informace o typu, protože v příslušné úložiště dat jsou již zadány informace o typu.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

V takovém případě Data Factory automaticky převody typu včetně pole data a času ve formátu data a času vlastní při přesouvání dat z objektu Blob do tabulky Azure s využitím jazykové verze "fr-fr".

> [!NOTE]
> Mapování sloupců z datové sady zdroje na sloupce ze sady jímku dat naleznete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
Další informace o klíčových faktorů této dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak ji optimalizovat, najdete v části [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md).
