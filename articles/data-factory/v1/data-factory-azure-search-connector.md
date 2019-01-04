---
title: Vložení dat do indexu vyhledávání pomocí služby Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak vložení dat do indexu Azure Search pomocí služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4d3c67974bc1dd0e52d3de457071d550a6379e36
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023076"
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Zápis dat do indexu Azure Search pomocí služby Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-azure-search-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-search.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Azure Search ve verzi V2](../connector-azure-search.md).

Tento článek popisuje, jak použít aktivitu kopírování k odesílání dat z podporovaných zdrojů úložišť dat do indexu Azure Search. Podporované zdrojové úložiště dat jsou uvedené ve sloupci zdroje [zdroje a jímky podporované](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Tento článek vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled o přesun dat pomocí aktivity kopírování a kombinace podporované datové úložiště.

## <a name="enabling-connectivity"></a>Povolení připojení
Povolit služby Data Factory, služba připojit v místním úložišti dat, nainstalujete bránu správy dat ve vašem místním prostředí. Bránu můžete nainstalovat na stejném počítači, do které uloží hostitele zdroje dat nebo na samostatném počítači, aby se zabránilo soutěží o prostředky s úložištěm dat.

Brána správy dat v místním zdrojům dat připojuje ke cloudovým službám způsobem, zabezpečení a správě. Zobrazit [přesun dat mezi místním prostředím a cloudem](data-factory-move-data-between-onprem-and-cloud.md) , kde najdete podrobnosti o Data Management Gateway.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, která odesílá data ze zdrojového úložiště dat do indexu Azure Search pomocí různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky: 

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory.
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. 

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON.  Tady je příklad s definice JSON entit služby Data Factory, které se používají ke kopírování dat do indexu Azure Search najdete v části [příklad JSON: Kopírování dat z místního SQL serveru do indexu Azure Search](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) části tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory konkrétní do indexu Azure Search:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro službu Azure Search propojený.

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| type | Vlastnost type musí být nastavená na: **AzureSearch**. | Ano |
| url | Adresa URL pro službu Azure Search. | Ano |
| key | Klíč správce pro službu Azure Search. | Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datové sady. **TypeProperties** oddílu se liší pro každý typ datové sady. V části datové sady typu typeProperties **AzureSearchIndex** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| type | Vlastnost type musí být nastavená na **AzureSearchIndex**.| Ano |
| indexName | Název indexu Azure Search. Objekt pro vytváření dat, nevytvoří index. Index musí existovat ve službě Azure Search. | Ano |


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a různé zásady jsou k dispozici pro všechny typy aktivit. Vzhledem k tomu, vlastnosti v části typeProperties každý typ aktivity se liší. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

Pro aktivitu kopírování, když je stok typu **AzureSearchIndexSink**, v části typeProperties jsou k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Určuje, jestli se má sloučit nebo nahradit, již existuje dokument v indexu. Zobrazit [WriteBehavior vlastnost](#writebehavior-property).| Sloučit (výchozí)<br/>Odeslat| Ne |
| WriteBatchSize | Nahrání dat do indexu Azure Search writeBatchSize dosáhne velikosti vyrovnávací paměti. Zobrazit [WriteBatchSize vlastnost](#writebatchsize-property) podrobnosti. | 1 až 1 000. Výchozí hodnota je 1000. | Ne |

### <a name="writebehavior-property"></a>Vlastnost WriteBehavior
Při zápisu dat AzureSearchSink upsertuje. Jinými slovy při zápisu dokumentu, pokud klíč dokumentu již existuje v indexu Azure Search, Azure Search aktualizuje stávající dokument namísto vyvolání výjimky konflikt.

AzureSearchSink poskytuje následujících dvou upsert chování (pomocí sady SDK AzureSearch):

- **Sloučit**: kombinovat všechny sloupce v nový dokument s existujícím. Pro sloupce s hodnotou null v nového dokumentu je hodnota v existujícím zachována.
- **Nahrát**: Nový dokument nahradí stávající. U sloupců není zadáno v nového dokumentu je hodnota nastavena na hodnotu null, zda je hodnota jiná než null v k existujícímu dokumentu nebo ne.

Výchozí chování je **sloučit**.

### <a name="writebatchsize-property"></a>Vlastnost WriteBatchSize
Služba Azure Search podporuje psaní dokumentů v dávce. Batch může obsahovat 1 až 1 000 akcí. Akce zpracovává jeden dokument k provedení této operace nahrávání/merge.

### <a name="data-type-support"></a>Podpora typ dat
Následující tabulka určuje, zda je nebo není podporováno typem dat Azure Search.

| Azure Search datový typ | Podporované v Azure Search jímky |
| ---------------------- | ------------------------------ |
| Řetězec | Ano |
| Datový typ Int32 | Ano |
| Int64 | Ano |
| Double | Ano |
| Logická hodnota | Ano |
| DataTimeOffset | Ano |
| Pole řetězců | Ne |
| GeographyPoint | Ne |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Příklad JSON: Kopírování dat z místního SQL serveru do indexu Azure Search

Následující příklad ukazuje:

1.  Propojené služby typu [AzureSearch](#linked-service-properties).
2.  Propojené služby typu [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3.  Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4.  Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureSearchIndex](#dataset-properties).
4.  A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) a [AzureSearchIndexSink](#copy-activity-properties).

Ukázka zkopíruje data časových řad z místní databáze systému SQL Server do indexu Azure Search po hodinách. Vlastnostech JSON použitých v této ukázce jsou popsány v části podle ukázky.

Jako první krok instalační program brány pro správu dat na místním počítači. Pokyny jsou v [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

**Azure Search propojené služby:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**Propojené služby SQL serveru**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**Vstupní datové sady SQL Server**

Ukázka předpokládá vytvoříte tabulku "MyTable" v systému SQL Server a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad. Můžete zadat dotaz přes více tabulek v rámci stejné databáze pomocí jedné datové sady, ale jedné tabulky musí být použito pro typeProperty tableName datové sady.

Nastavení "externí": "PRAVDA" informuje služby Data Factory, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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

**Služba Azure Search výstupní datovou sadu:**

Ukázce kopíruje data do indexu Azure Search s názvem **produkty**. Objekt pro vytváření dat, nevytvoří index. Testování ukázky, vytvořte index s tímto názvem. Vytvoření indexu Azure Search s stejný počet sloupců jako vstupní datové sady. Nové položky jsou přidány do indexu Azure Search každou hodinu.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
   }
}
```

**Aktivita kopírování v kanálu s SQL zdroje a jímky Azure Search Index:**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **SqlSource** a **jímky** je typ nastaven na **AzureSearchIndexSink**. Zadaná pro dotaz SQL **SqlReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
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

Pokud se kopírování dat z cloudového úložiště dat do Azure Search `executionLocation` vlastnost je povinná. Následující fragment kódu JSON ukazuje změnu potřeba v rámci aktivity kopírování `typeProperties` jako příklad. Zkontrolujte [kopírování dat mezi cloudovými úložišti dat](data-factory-data-movement-activities.md#global) části Podporované hodnoty a další podrobnosti.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Kopírování ze zdroje cloudu
Pokud se kopírování dat z cloudového úložiště dat do Azure Search `executionLocation` vlastnost je povinná. Následující fragment kódu JSON ukazuje změnu potřeba v rámci aktivity kopírování `typeProperties` jako příklad. Zkontrolujte [kopírování dat mezi cloudovými úložišti dat](data-factory-data-movement-activities.md#global) části Podporované hodnoty a další podrobnosti.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

Můžete také namapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky v definici aktivity kopírování. Podrobnosti najdete v tématu [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění  
Zobrazit [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů přesun dat (aktivita kopírování) a různé způsoby, jak optimalizovat tento ovlivnit výkon.

## <a name="next-steps"></a>Další postup
Viz následující články:

* [Kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny pro vytvoření kanálu s aktivitou kopírování.
