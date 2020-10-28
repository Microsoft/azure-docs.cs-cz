---
title: Přesun dat do a z tabulky Azure
description: Přečtěte si, jak přesunout data do a z Azure Table Storage pomocí Azure Data Factory.
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
ms.openlocfilehash: 1d7802a3fe4fb904aad7fd9257edbf8b10efe127
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637424"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Přesun dat do a z tabulky Azure pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-azure-table-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-table-storage.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Azure Table Storage Connector v v2](../connector-azure-table-storage.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data do a z Azure Table Storage. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování. 

Data z libovolného podporovaného zdrojového úložiště dat můžete zkopírovat do Azure Table Storage nebo z Azure Table Storage do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje nebo jímky aktivitou kopírování najdete v tabulce [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do nebo z Azure Table Storage pomocí různých nástrojů/rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním** . Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio** , **Azure PowerShell** , **Azure Resource Manager template** , **.NET API** a **REST API** . Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky: 

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování. 
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup. 

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON. Ukázky s definicemi JSON pro Entity Data Factory, které se používají ke kopírování dat do a z Azure Table Storage, najdete v části [Příklady JSON](#json-examples) tohoto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro Azure Table Storage: 

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Existují dva typy propojených služeb, které můžete použít k propojení úložiště objektů BLOB v Azure s objektem pro vytváření dat Azure. Jsou to: propojená služba **AzureStorage** a propojená služba **AzureStorageSas** . Propojená služba Azure Storage poskytuje datovou továrnu s globálním přístupem k Azure Storage. Vzhledem k tomu, že propojená služba Azure Storage SAS (sdílený přístupový podpis) poskytuje objekt pro vytváření dat s omezeným/časově vázaným přístupem k Azure Storage. Mezi těmito dvěma propojenými službami neexistují žádné jiné rozdíly. Vyberte propojenou službu, která vyhovuje vašim potřebám. Následující části obsahují další podrobnosti o těchto dvou propojených službách.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl **typeProperties** pro datovou sadu typu **Azure** má následující vlastnosti.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky instance databáze Azure Table, na kterou odkazuje propojená služba |Ano. Když je zadán tableName bez azureTableSourceQuery, zkopírují se všechny záznamy z tabulky do cílového umístění. Pokud je zadána také azureTableSourceQuery, záznamy z tabulky, které splňují dotaz, se zkopírují do cíle. |

### <a name="schema-by-data-factory"></a>Schéma podle Data Factory
Pro úložiště dat bez schémat, jako je například tabulka Azure, Služba Data Factory odvodí schéma jedním z následujících způsobů:

1. Pokud určíte strukturu dat pomocí vlastnosti **Structure** v definici datové sady, Služba Data Factory tuto strukturu respektuje jako schéma. V takovém případě, pokud řádek neobsahuje hodnotu pro sloupec, je pro něj k dispozici hodnota null.
2. Pokud neurčíte strukturu dat pomocí vlastnosti **Structure** v definici datové sady, Data Factory odvodí schéma pomocí prvního řádku v datech. V takovém případě, pokud první řádek neobsahuje úplné schéma, některé sloupce v důsledku operace kopírování chybí.

Proto je u zdrojů dat bez schématu osvědčeným postupem určení struktury dat pomocí vlastnosti **struktury** .

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní datové sady a zásady.

Vlastnosti, které jsou k dispozici v části typeProperties aktivity, se liší v závislosti na jednotlivých typech aktivit. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

**AzureTableSource** podporuje následující vlastnosti v části typeProperties:

| Vlastnost | Popis | Povolené hodnoty | Povinné |
| --- | --- | --- | --- |
| azureTableSourceQuery |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu tabulky Azure Podívejte se na příklady v následující části. |Ne. Když je zadán tableName bez azureTableSourceQuery, zkopírují se všechny záznamy z tabulky do cílového umístění. Pokud je zadána také azureTableSourceQuery, záznamy z tabulky, které splňují dotaz, se zkopírují do cíle. |
| azureTableSourceIgnoreTableNotFound |Určuje, zda požití neexistuje výjimka tabulky. |TRUE<br/>FALSE |Ne |

### <a name="azuretablesourcequery-examples"></a>Příklady azureTableSourceQuery
Pokud je sloupec tabulky Azure typu řetězec:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Pokud je sloupec tabulky Azure typu DateTime:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** podporuje následující vlastnosti v části typeProperties:

| Vlastnost | Popis | Povolené hodnoty | Povinné |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Výchozí hodnota klíče oddílu, kterou může jímka použít. |Řetězcová hodnota. |Ne |
| azureTablePartitionKeyName |Zadejte název sloupce, jehož hodnoty se používají jako klíče oddílů. Pokud není zadaný, použije se jako klíč oddílu AzureTableDefaultPartitionKeyValue. |Název sloupce |Ne |
| azureTableRowKeyName |Zadejte název sloupce, jehož hodnoty sloupce jsou používány jako klíč řádku. Není-li tento parametr zadán, použijte pro každý řádek identifikátor GUID. |Název sloupce |Ne |
| azureTableInsertType |Režim pro vložení dat do tabulky Azure.<br/><br/>Tato vlastnost určuje, zda mají být existující řádky ve výstupní tabulce se shodnými klíči oddílů a řádky nahrazeny nebo sloučeny. <br/><br/>Další informace o tom, jak tato nastavení (sloučit a nahradit) fungují, najdete v tématech [vložení nebo sloučení entit](/rest/api/storageservices/Insert-Or-Merge-Entity) a [vložení nebo nahrazení entit](/rest/api/storageservices/Insert-Or-Replace-Entity) . <br/><br> Toto nastavení se vztahuje na úrovni řádků, nikoli na úrovni tabulky a ani možnost neodstraní řádky ve výstupní tabulce, které ve vstupu neexistují. |sloučení (výchozí)<br/>náhrady |Ne |
| writeBatchSize |Vloží data do tabulky Azure, když je dosaženo writeBatchSize nebo writeBatchTimeout. |Integer (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Vloží data do tabulky Azure, když je dosaženo writeBatchSize nebo writeBatchTimeout. |timespan<br/><br/>Příklad: "00:20:00" (20 minut) |Ne (výchozí hodnota pro výchozí nastavení klienta úložiště hodnota časového limitu 90 s) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Než budete moci použít cílový sloupec jako azureTablePartitionKeyName, namapujte zdrojový sloupec do cílového sloupce pomocí vlastnosti Translator JSON.

V následujícím příkladu je zdrojový sloupec DivisionID namapován na cílový sloupec: DivisionID.  

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
V následujících příkladech jsou uvedeny ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data z a do Azure Table Storage a databáze objektů BLOB v Azure. Data je však možné zkopírovat **přímo** ze všech zdrojů do kterékoli z podporovaných umyvadel. Další informace najdete v části "podporovaná úložiště dat a formáty" v tématu [přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Příklad: kopírování dat z tabulky Azure do Azure Blob
Následující příklad ukazuje:

1. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (používá se pro & objekt BLOB tabulky).
2. Vstupní [datová sada](data-factory-create-datasets.md) typu [Azure](#dataset-properties)
3. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
4. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá AzureTableSource a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data, která patří do výchozího oddílu v tabulce Azure, do objektu BLOB každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

**Propojená služba Azure Storage:**

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
Azure Data Factory podporuje dva typy propojených služeb Azure Storage: **AzureStorage** a **AzureStorageSas** . Jako první zadejte připojovací řetězec, který obsahuje klíč účtu a za pozdější dobu, a zadejte identifikátor URI sdíleného přístupového podpisu (SAS). Podrobnosti najdete v části [propojené služby](#linked-service-properties) .  

**Vstupní datová sada tabulky Azure:**

Ukázka předpokládá, že jste v tabulce Azure vytvořili tabulku "MyTable".

Nastavení "externí": "true" informuje službu Data Factory o tom, že datová sada je externí pro objekt pro vytváření dat, a není vytvořena aktivitou v datové továrně.

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

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **AzureTableSource** a typ **jímky** je nastavený na **BlobSink** . Dotaz SQL zadaný pomocí vlastnosti **AzureTableSourceQuery** vybere data z výchozího oddílu každou hodinu ke zkopírování.

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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Příklad: kopírování dat z Azure Blob do tabulky Azure
Následující příklad ukazuje:

1. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (používá se pro & objektů BLOB v tabulce)
2. Vstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
3. Výstupní [datová sada](data-factory-create-datasets.md) typu [Azure](#dataset-properties).
4. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [AzureTableSink](#copy-activity-properties).

Ukázka kopíruje data časových řad z objektu blob Azure do tabulky Azure každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

**Služba Azure Storage (pro propojenou službu Azure Table & BLOB):**

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

Azure Data Factory podporuje dva typy propojených služeb Azure Storage: **AzureStorage** a **AzureStorageSas** . Jako první zadejte připojovací řetězec, který obsahuje klíč účtu a za pozdější dobu, a zadejte identifikátor URI sdíleného přístupového podpisu (SAS). Podrobnosti najdete v části [propojené služby](#linked-service-properties) .

**Vstupní datová sada Azure Blob:**

Data se vybírají z nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce a název souboru pro objekt BLOB jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část rok, měsíc a den v čase zahájení a název souboru používá hodinovou část času spuštění. nastavení externí: "true" informuje službu Data Factory o tom, že datová sada je externí pro objekt pro vytváření dat a není vytvořená aktivitou v datové továrně.

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

Ukázka zkopíruje data do tabulky s názvem "MyTable" v tabulce Azure. Vytvořte tabulku Azure se stejným počtem sloupců, protože očekáváte, že soubor CSV BLOB bude obsahovat. Nové řádky jsou do tabulky přidány každou hodinu.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **BlobSource** a typ **jímky** je nastavený na **AzureTableSink** .

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
Jak je uvedeno v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , aktivita kopírování provádí automatické převody typů ze zdrojových typů do typů jímky s následujícím dvěma kroky.

1. Převod z nativních zdrojových typů na typ .NET
2. Převést z typu .NET na nativní typ jímky

Když přesunete data do & z tabulky Azure, z typů Azure Table OData do typu .NET se použijí následující [mapování definovaná pomocí azure Table Service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) a naopak.

| Datový typ OData | Typ .NET | Podrobnosti |
| --- | --- | --- |
| EDM. Binary |Byte [] |Pole bajtů až do 64 KB. |
| Edm.Boolean |bool |Logická hodnota |
| EDM. DateTime |DateTime |64 hodnota vyjádřená jako koordinovaný světový čas (UTC). Podporovaný rozsah DateTime začíná od 12:00 půlnoci 1. ledna 1601 N.L. (0001), UTC. Rozsah končí 31. prosince 9999. |
| Edm.Double |double |Hodnota 64-bit s plovoucí desetinnou čárkou. |
| EDM. GUID |Identifikátor GUID |Globálně jedinečný identifikátor 128. |
| Edm.Int32 |Int32 |32 celé číslo. |
| Edm.Int64 |Int64 |64 celé číslo. |
| Edm.String |Řetězec |Hodnota kódovaná v kódování UTF-16. Hodnoty řetězce můžou být až 64 KB. |

### <a name="type-conversion-sample"></a>Ukázka převodu typu
Následující ukázka slouží ke kopírování dat z objektu blob Azure do tabulky Azure s převody typů.

Předpokládejme, že je datová sada objektů BLOB ve formátu CSV a obsahuje tři sloupce. Jedním z nich je sloupec data a času s vlastním formátem DateTime s použitím zkrácených francouzských názvů pro den v týdnu.

Nadefinujte zdrojovou datovou sadu objektů BLOB následujícím způsobem spolu s definicemi typů pro sloupce.

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
Vzhledem k mapování typu z tabulky Azure typu OData na typ .NET byste definovali tabulku v tabulce Azure s následujícím schématem.

**Schéma tabulky Azure:**

| Název sloupce | Typ |
| --- | --- |
| UserID |Edm.Int64 |
| name |Edm.String |
| lastlogindate |EDM. DateTime |

Dále definujte datovou sadu Azure Table následujícím způsobem. Nemusíte určovat oddíl Structure s informacemi o typu, protože informace o typu jsou již zadány v podkladovém úložišti dat.

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

V takovém případě Data Factory automaticky převody typů včetně pole DateTime s vlastním formátem DateTime pomocí jazykové verze "fr-FR" při přesunu dat z objektu blob do tabulky Azure.

> [!NOTE]
> Chcete-li mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si téma [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md).