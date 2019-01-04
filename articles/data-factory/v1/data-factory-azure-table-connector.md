---
title: Přesun dat do a z Azure Table | Dokumentace Microsoftu
description: Zjistěte, jak pro přesun dat do a z Azure Table Storage pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: af66a8c28ebdbc04ffb451ea9249dcd1d72c1c71
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022593"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Přesun dat do a z Azure Table pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-azure-table-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-table-storage.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Azure Table Storage ve verzi V2](../connector-azure-table-storage.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování ve službě Azure Data Factory k přesunu dat do a z Azure Table Storage. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování. 

Můžete zkopírovat data z libovolné podporované zdrojové úložiště dat do služby Azure Table Storage nebo Azure Table Storage pro všechny podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. 

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, která přesouvání dat do a z Azure Table Storage pomocí různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky: 

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory.
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. 

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke kopírování dat do a z Azure Table Storage najdete v tématu [JSON příklady](#json-examples) části tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory, které jsou specifické pro Azure Table Storage: 

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Existují dva typy propojené služby, které lze použít k propojení služby Azure blob storage do služby Azure data factory. Jsou to tyto: **AzureStorage** propojenou službu a **AzureStorageSas** propojenou službu. Propojenou službu Azure Storage poskytuje datovou továrnu s globálním přístupem ke službě Azure Storage. Vzhledem k tomu Azure úložiště SAS (sdíleným přístupovým podpisům) propojená služba poskytuje služby data factory s přístupem s omezením pomocí specifikátoru/časově omezenou do služby Azure Storage. Nejsou žádné další rozdíly mezi tyto dvě propojené služby. Zvolte propojené služby, která vyhovuje vašim potřebám. Následující oddíly poskytují další podrobnosti na tyto dvě propojené služby.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny datové sady typy (Azure SQL, Azure blob, tabulky Azure, atd.).

V části typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. **TypeProperties** části datové sady typu **AzureTable** má následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky instance databáze tabulky Azure, který odkazuje propojenou službu. |Ano. Pokud tableName je zadán bez azureTableSourceQuery, všechny záznamy z tabulky se zkopírují do cíle. Pokud je určena azureTableSourceQuery také záznamy z tabulky, která odpovídá dotazu se zkopírují do cíle. |

### <a name="schema-by-data-factory"></a>Schéma službou Data Factory
Služba Data Factory pro úložišť dat bez schématu, jako je Azure Table, odvodí schéma v jednom z následujících způsobů:

1. Pokud určíte strukturu dat pomocí **struktura** vlastnost v definici datové sady, služba Data Factory respektuje tato struktura jako schéma. V takovém případě pokud řádku neobsahuje hodnotu pro sloupec, hodnoty null se poskytuje pro ho.
2. Pokud nezadáte strukturu dat s použitím **struktura** vlastnost v definici datové sady Data Factory odvodí schéma pomocí prvního řádku v datech. V takovém případě pokud první řádek neobsahuje úplného schématu, některé sloupce promeškají ve výsledku operace kopírování.

Proto pro zdroje dat bez schématu, osvědčeným postupem je zadání strukturu dat pomocí **struktura** vlastnost.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní datové sady a zásady jsou k dispozici pro všechny typy aktivit.

Vlastnosti v části typeProperties aktivity se liší na druhé straně s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

**AzureTableSource** v části typeProperties podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| azureTableSourceQuery |Použijte vlastní dotaz číst data. |Řetězec dotazu, tabulky Azure. Příklady naleznete v další části. |Ne. Pokud tableName je zadán bez azureTableSourceQuery, všechny záznamy z tabulky se zkopírují do cíle. Pokud je určena azureTableSourceQuery také záznamy z tabulky, která odpovídá dotazu se zkopírují do cíle. |
| azureTableSourceIgnoreTableNotFound |Označuje, zda spolknout výjimka tabulka neexistuje. |HODNOTA TRUE<br/>FALSE |Ne |

### <a name="azuretablesourcequery-examples"></a>Příklady azureTableSourceQuery
Je-li sloupec tabulky Azure typu řetězec:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Pokud je tabulka Azure sloupec typu datum a čas:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** v části typeProperties podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Výchozí hodnota klíče oddílu, který je možné jímkou. |Hodnotu řetězce. |Ne |
| azureTablePartitionKeyName |Zadejte název sloupce, jejichž hodnoty se používají jako klíče oddílu. Pokud není zadán, AzureTableDefaultPartitionKeyValue slouží jako klíč oddílu. |Název sloupce. |Ne |
| azureTableRowKeyName |Zadejte název sloupce, jejichž hodnoty sloupce se používají jako klíč řádku. Pokud není zadán, použijte identifikátor GUID pro každý řádek. |Název sloupce. |Ne |
| azureTableInsertType |Režim vložení dat do tabulek v Azure.<br/><br/>Tato vlastnost určuje, zda existující řádky v tabulce výstup s odpovídajícími klíče oddílu a řádku mají jejich hodnoty, nahrazení nebo sloučení. <br/><br/>Další informace o tom, jak fungují tato nastavení (sloučení a nahradit), najdete v článku [vložení nebo Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) a [vložení nebo nahrazení Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx) témata. <br/><br> Toto nastavení se vztahuje na úrovni řádků, nikoli úrovni tabulky a ani možnost odstranění řádků ve výstupní tabulce, které neexistují ve vstupu. |Sloučit (výchozí)<br/>nahradit |Ne |
| WriteBatchSize |Při dosažení writeBatchSize nebo writeBatchTimeout vloží data do tabulek v Azure. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| writeBatchTimeout |Při dosažení writeBatchSize nebo writeBatchTimeout vloží data do tabulek v Azure |Časový interval<br/><br/>Příklad: "00:20:00" (20 minut) |Ne (výchozí nastavení časového limitu úložiště klienta. výchozí hodnota 90 sekundu) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Zdrojový sloupec namapujte na cílový sloupec použitím překladač vlastnost JSON před použitím cílového sloupce jako azureTablePartitionKeyName.

V následujícím příkladu je zdrojový sloupec DivisionID namapována na cílový sloupec: DivisionID.  

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
## <a name="json-examples"></a>Příklady pro JSON
Následující příklady popisují ukázkový JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Zobrazí se kopírování dat do a z Azure Table Storage a databáze objektů Blob v Azure. Nicméně je možné zkopírovat data **přímo** z jakéhokoli zdroje na jakýkoli z podporovaných jímky. Další informace najdete v oddílu "podporovaná úložiště dat a formáty" v [přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Příklad: Kopírování dat z Azure Table do objektů Blob v Azure
Následující příklad ukazuje:

1. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (používá se pro tabulek a objektů blob).
2. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureTable](#dataset-properties).
3. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. [Kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [AzureTableSource](#activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data patřící do výchozí oddíl v tabulce Azure do objektu blob každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

**Propojená služba Azure storage:**

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
Azure Data Factory podporuje dva typy služeb propojené služby Azure Storage: **AzureStorage** a **AzureStorageSas**. Pro první z nich zadejte připojovací řetězec, který obsahuje klíč účtu a pro novější verzi, zadejte identifikátor Uri sdíleného přístupového podpisu (SAS). Zobrazit [propojené služby](#linked-service-properties) podrobné informace.  

**Tabulka vstupní datová sada Azure:**

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v tabulce Azure.

Nastavení "externí": "PRAVDA" informuje služby Data Factory, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje na základě doby spuštění řez, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

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

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **AzureTableSource** a **jímky** je typ nastaven na **BlobSink**. Zadaný dotaz SQL **AzureTableSourceQuery** vlastnost vybere data z oddílu výchozí každou hodinu ke kopírování.

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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Příklad: Kopírování dat z Azure Blob do tabulky Azure
Následující příklad ukazuje:

1. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (používá se pro tabulek a objektů blob)
2. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureTable](#dataset-properties).
4. [Kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [AzureTableSink](#copy-activity-properties).

Ukázka zkopíruje časových řad dat z Azure blob do služby Azure table každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

**Propojená služba Azure storage (pro Azure Table a Blob):**

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

Azure Data Factory podporuje dva typy služeb propojené služby Azure Storage: **AzureStorage** a **AzureStorageSas**. Pro první z nich zadejte připojovací řetězec, který obsahuje klíč účtu a pro novější verzi, zadejte identifikátor Uri sdíleného přístupového podpisu (SAS). Zobrazit [propojené služby](#linked-service-properties) podrobné informace.

**Azure vstupní datovou sadou objektů Blob:**

Data je převzata z nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Název složky a cesta k souboru pro tento objekt blob se dynamicky vyhodnocuje podle času spuštění řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc a den čas spuštění a název souboru používá hodinová část času zahájení. "externí": "PRAVDA" nastavení informuje služby Data Factory, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

**Azure Table výstupní datovou sadu:**

Ukázce kopíruje data do tabulky s názvem "MyTable" v tabulce Azure. Vytvořte tabulku Azure s stejný počet sloupců tak, jak očekáváte objektů Blob CSV soubor obsahovat. Nové řádky do tabulky přidají každou hodinu.

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

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **jímky** je typ nastaven na **AzureTableSink**.

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
## <a name="type-mapping-for-azure-table"></a>Typ mapování tabulek v Azure
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku, aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typy s následující dvoukrokový přístup.

1. Převést na typ formátu .NET typy nativních zdrojů
2. Převést z typu .NET native jímky typu

Při přesunu dat do a z Azure Table, následující [mapování, které jsou definovány službou Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) se používají z Azure Table OData typů na typ .NET a naopak.

| Typ dat OData | Typ formátu .NET | Podrobnosti |
| --- | --- | --- |
| Edm.Binary |Byte |Pole bajtů až 64 KB. |
| Edm.Boolean |BOOL |Logická hodnota. |
| Edm.DateTime |DateTime |Hodnota 64-bit, vyjádřené jako koordinovaný univerzální čas (UTC). Podporovaný rozsah data a času počínaje 12:00 hodin 1 dne 1601 N.L. (C.E.), UTC. Rozsah končí 31. prosince 9999. |
| Edm.Double |double |Bod hodnotu s plovoucí desetinnou čárkou 64bitové. |
| Edm.Guid |Guid |128bitové globálně jedinečný identifikátor. |
| Edm.Int32 |Datový typ Int32 |32bitové celé číslo. |
| Edm.Int64 |Int64 |64bitové celé číslo. |
| Edm.String |Řetězec |Hodnota kódování UTF-16. Řetězcové hodnoty může mít až 64 KB. |

### <a name="type-conversion-sample"></a>Ukázka typ převodu
Následující příklad je pro kopírování dat z objektu Blob Azure do Azure Table s převody typu.

Předpokládejme, že datová sada objektu Blob je ve formátu CSV a obsahuje tři sloupce. Jeden z nich je sloupec Datum a čas ve formátu vlastní data a času pomocí zkrácené názvy francouzské pro den v týdnu.

Definování datové sady objektů Blob zdroje takto spolu s definic typů pro sloupce.

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
Zadané mapování typu z Azure Table OData typu na typ .NET, byste definovali v tabulce v Azure Table pomocí následující schéma.

**Schéma tabulky Azure:**

| Název sloupce | Typ |
| --- | --- |
| ID uživatele |Edm.Int64 |
| jméno |Edm.String |
| lastlogindate |Edm.DateTime |

Dále definujte datová sada tabulky Azure následujícím způsobem. Nemusíte určit oddílu "struktura" s informací o typu, protože informace o typu je už zadaná v úložišti podkladová data.

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

V takovém případě služby Data Factory automaticky převody typů včetně pole Datum a čas ve formátu vlastní data a času pomocí jazykové verze "fr-fr" při přesunu dat z objektu Blob do tabulky Azure.

> [!NOTE]
> Pokud chcete namapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat, naleznete v tématu [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md).
