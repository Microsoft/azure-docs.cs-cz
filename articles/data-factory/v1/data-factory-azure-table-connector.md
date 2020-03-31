---
title: Přesunutí dat do tabulky Azure nebo z ní
description: Zjistěte, jak přesouvat data do nebo z Azure Table Storage pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 462d54a9d89d6f03aed5e221fa02609da786c8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260446"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Přesunutí dat do a z Azure Table pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-azure-table-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-table-storage.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Konektor azure table storage ve Verzi 2](../connector-azure-table-storage.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat do nebo z Azure Table Storage. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování. 

Data z libovolného úložiště dat podporovaného zdroje můžete zkopírovat do Azure Table Storage nebo z Azure Table Storage do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje nebo propady aktivitou kopírování naleznete v tabulce [Podporovaná data.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do nebo z úložiště tabulek Azure pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.

K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity. 

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky: 

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování. 
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON. Ukázky s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat do nebo z úložiště tabulek Azure, najdete v části [příklady JSON](#json-examples) v tomto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Datové továrny specifické pro Azure Table Storage: 

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Existují dva typy propojených služeb, které můžete použít k propojení úložiště objektů blob Azure s toto datovou továrně Azure. Jsou: **AzureStorage** propojené služby a **AzureStorageSas** propojené služby. Propojená služba Azure Storage poskytuje datové továrně globální přístup k Úložišti Azure. Vzhledem k tomu, Azure Storage SAS (sdílený přístupový podpis) propojené služby poskytuje datové továrny s omezený/časově vázaný přístup k úložišti Azure. Mezi těmito dvěma propojenými službami nejsou žádné jiné rozdíly. Vyberte si propojenou službu, která vyhovuje vašim potřebám. V následujících částech jsou uvedeny další podrobnosti o těchto dvou propojených službách.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Sekce typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl **typeProperties** pro datovou sadu typu **AzureTable** má následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v instanci Azure Table Database, na kterou odkazuje propojená služba. |Ano. Pokud je zadán název_tableName bez azureTableSourceQuery, všechny záznamy z tabulky jsou zkopírovány do cíle. Pokud azureTableSourceQuery je také zadán, záznamy z tabulky, která splňuje dotaz jsou zkopírovány do cíle. |

### <a name="schema-by-data-factory"></a>Schéma podle datové továrny
Pro úložiště dat bez schématu, jako je například Azure Table, služba Data Factory odvodí schéma jedním z následujících způsobů:

1. Pokud zadáte strukturu dat pomocí **vlastnosti structure** v definici datové sady, služba Data Factory tuto strukturu respektuje jako schéma. V tomto případě pokud řádek neobsahuje hodnotu pro sloupec, je pro něj k dispozici hodnota null.
2. Pokud nezadáte strukturu dat pomocí **vlastnosti structure** v definici datové sady, Data Factory odvodí schéma pomocí prvního řádku v datech. V tomto případě pokud první řádek neobsahuje úplné schéma, některé sloupce jsou vynechány ve výsledku operace kopírování.

Proto pro zdroje dat bez schématu je osvědčeným postupem určit strukturu dat pomocí vlastnosti **structure.**

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní datové sady a zásady jsou k dispozici pro všechny typy aktivit.

Vlastnosti dostupné v části typeProperties aktivity na druhé straně se liší podle jednotlivých typů aktivit. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

**AzureTableSource** podporuje následující vlastnosti v části typeProperties:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| azureTableSourceQuery |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu tabulky Azure. Viz příklady v další části. |Ne. Pokud je zadán název_tableName bez azureTableSourceQuery, všechny záznamy z tabulky jsou zkopírovány do cíle. Pokud azureTableSourceQuery je také zadán, záznamy z tabulky, která splňuje dotaz jsou zkopírovány do cíle. |
| azureTableSourceIgnoreTableNotFound |Určete, zda neexistuje výjimka tabulky. |TRUE<br/>FALSE |Ne |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery příklady
Pokud je sloupec Azure Table typu řetězce:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Pokud je sloupec Tabulka Azure typu datetime:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** podporuje následující vlastnosti v části typeProperties:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Výchozí hodnota klíče oddílu, kterou může použít jímka. |Hodnota řetězce. |Ne |
| azureTablePartitionKeyName |Zadejte název sloupce, jehož hodnoty se používají jako klíče oddílu. Pokud není zadán, AzureTableDefaultPartitionKeyValue se používá jako klíč oddílu. |Název sloupce. |Ne |
| azureTableRowKeyName |Zadejte název sloupce, jehož hodnoty sloupců se používají jako klíč řádku. Pokud není zadán, použijte identifikátor GUID pro každý řádek. |Název sloupce. |Ne |
| azureTableInsertType |Režim pro vložení dat do tabulky Azure.<br/><br/>Tato vlastnost určuje, zda existující řádky ve výstupní tabulce s odpovídajícími klíči oddílu a řádků mají své hodnoty nahrazeny nebo sloučeny. <br/><br/>Informace o tom, jak tato nastavení (sloučení a nahrazení) fungují, najdete v tématu [Vložení nebo sloučení entit](https://msdn.microsoft.com/library/azure/hh452241.aspx) a Vložení nebo nahrazení témat [entity.](https://msdn.microsoft.com/library/azure/hh452242.aspx) <br/><br> Toto nastavení platí na úrovni řádku, nikoli na úrovni tabulky a ani jedna z možností neodstraní řádky ve výstupní tabulce, které ve vstupu neexistují. |sloučení (výchozí)<br/>Nahradit |Ne |
| writeBatchSize |Vloží data do tabulky Azure při přístupu na zápisBatchSize nebo writeBatchTimeout. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Vloží data do tabulky Azure, když je přístupů writeBatchSize nebo writeBatchTimeout |Timespan<br/><br/>Příklad: "00:20:00" (20 minut) |Ne (Výchozí výchozí hodnota časového limitu klienta úložiště 90 sekund) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Před použitím cílového sloupce jako azureTablePartitionKeyName namapujte zdrojový sloupec na cílový sloupec pomocí vlastnosti translator JSON.

V následujícím příkladu je zdrojový sloupec DivisionID mapován na cílový sloupec: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
DivisionID je určen jako klíč oddílu.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>Příklady JSON
Následující příklady poskytují ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data do a z Azure Table Storage a Azure Blob Database. Data však lze zkopírovat **přímo** z libovolného zdroje do některého z podporovaných jímky. Další informace naleznete v části Podporovaná úložiště a formáty dat v [tématu Přesunout data pomocí funkce Kopírovat aktivitu](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Příklad: Kopírování dat z Azure Table do objektu Blob Azure
Následující ukázka ukazuje:

1. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (používá se pro obě tabulky & objekt blob).
2. Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureTable](#dataset-properties).
3. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. [Kanál](data-factory-create-pipelines.md) s aktivitou Copy, která používá AzureTableSource a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data patřící do výchozího oddílu v tabulce Azure do objektu blob každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

**Propojené služby Azure:**

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
Azure Data Factory podporuje dva typy propojených služeb Azure Storage: **AzureStorage** a **AzureStorageSas**. Pro první zadáte připojovací řetězec, který obsahuje klíč účtu a pro pozdější, zadáte identifikátor Uri sdíleného přístupového podpisu (SAS). Podrobnosti najdete v části [Propojené služby.](#linked-service-properties)  

**Vstupní datová sada tabulky Azure:**

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v tabulce Azure.

Nastavení "externí": "true" informuje službu Data Factory, že datová sada je externí pro datové továrny a není vyráběna aktivitou v datové továrně.

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

**Výstupní datová sada objektu Blob Azure:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

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

**Kopírování aktivity v kanálu s AzureTableSource a BlobSink:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **AzureTableSource** a typ **jímky** je nastavenna na **Objekt blobSink**. Dotaz SQL zadaný s vlastností **AzureTableSourceQuery** vybere data z výchozího oddílu každou hodinu ke kopírování.

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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Příklad: Kopírování dat z objektu blob Azure do tabulky Azure
Následující ukázka ukazuje:

1. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (používá se pro oba tabulkové & objektblou)
2. Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureTable](#dataset-properties).
4. [Kanál](data-factory-create-pipelines.md) s aktivitou Copy, která používá [Objekt BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [AzureTableSink](#copy-activity-properties).

Ukázka zkopíruje data časových řad z objektu blob Azure do tabulky Azure každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

**Azure storage (pro propojenou službu Azure Table & Blob):**

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

Azure Data Factory podporuje dva typy propojených služeb Azure Storage: **AzureStorage** a **AzureStorageSas**. Pro první zadáte připojovací řetězec, který obsahuje klíč účtu a pro pozdější, zadáte identifikátor Uri sdíleného přístupového podpisu (SAS). Podrobnosti najdete v části [Propojené služby.](#linked-service-properties)

**Vstupní datová sada objektu Blob Azure:**

Data se zírají z nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce a název souboru pro objekt blob jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá rok, měsíc a den část počátečního času a název souboru používá hodinovou část počátečního času. "externí": "true" nastavení informuje službu Data Factory, že datová sada je externí pro datové továrny a není vyráběna aktivitou v datové továrně.

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

**Výstupní datová sada tabulky Azure:**

Ukázka zkopíruje data do tabulky s názvem "MyTable" v tabulce Azure. Vytvořte tabulku Azure se stejným počtem sloupců, jako u očekáváte, že soubor BLOB CSV bude obsahovat. Nové řádky jsou přidávány do tabulky každou hodinu.

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

**Kopírování aktivity v kanálu s Objektem BlobSource a AzureTableSink:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **Objekt blobSource** a typ **jímky** je nastaven na **AzureTableSink**.

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
## <a name="type-mapping-for-azure-table"></a>Mapování typů pro tabulku Azure
Jak je uvedeno v článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) copy aktivita provádí převody automatického typu z typů zdrojů na typy jímek s následujícím dvoustupňovým přístupem.

1. Převod z nativních typů zdrojů na typ .NET
2. Převod z typu .NET na nativní typ jímky

Při přesouvání dat do & z Azure Table se používají následující [mapování definovaná službou Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) ze zdrojů Azure Table OData až po typ .NET a naopak.

| Datový typ OData | Typ .NET | Podrobnosti |
| --- | --- | --- |
| Edm.Binary |bajt[] |Pole bajtů až 64 kB. |
| Edm.Boolean |bool |Logická hodnota |
| Edm.DateČas |DateTime |64bitová hodnota vyjádřená jako koordinovaný světový čas (UTC). Podporovaný rozsah DateTime začíná od půlnoci 12:00, 1. (C.E.), UTC. Rozsah končí 31.12.9999. |
| Edm.Double |double |Hodnota s plovoucí desetinnou hodnotou 64 bitů. |
| Edm.Guid |Identifikátor GUID |128bitový globálně jedinečný identifikátor. |
| Edm.Int32 |Int32 |32bitové celé číslo. |
| Edm.Int64 |Int64 |64bitové celé číslo. |
| Edm.String |Řetězec |Hodnota kódovaná UTF-16. Řetězcové hodnoty mohou být až 64 KB. |

### <a name="type-conversion-sample"></a>Ukázka převodu typu
Následující ukázka je pro kopírování dat z objektu Blob Azure do tabulky Azure s převody typů.

Předpokládejme, že datová sada objektů Blob je ve formátu CSV a obsahuje tři sloupce. Jedním z nich je sloupec datetime s vlastním formátem datetime pomocí zkrácených francouzských názvů pro den v týdnu.

Definujte datovou sadu Zdroj objektu blob takto spolu s definicemi typů pro sloupce.

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
Vzhledem k mapování typu z typu Azure Table OData typu .NET typ, byste definovat tabulku v tabulce Azure s následujícím schématem.

**Schéma tabulky Azure:**

| Název sloupce | Typ |
| --- | --- |
| Userid |Edm.Int64 |
| jméno |Edm.String |
| Lastlogindate |Edm.DateČas |

Dále definujte datovou sadu Tabulka Azure následujícím způsobem. Není nutné zadat oddíl "struktura" s informacemi o typu, protože informace o typu jsou již zadány v podkladovém úložišti dat.

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

V tomto případě Data Factory automaticky zadává převody včetně pole Datetime s vlastním formátem datetime pomocí jazykové verze "fr-fr" při přesunu dat z objektu Blob do tabulky Azure.

> [!NOTE]
> Pokud chcete mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si témat [mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
Informace o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve službě Azure Data Factory, a o různých způsobech jeho optimalizace najdete v [tématu Kopírování výkonu aktivity & Průvodce optimalizací](data-factory-copy-activity-performance.md).
