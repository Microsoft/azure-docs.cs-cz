---
title: Vložení dat do indexu vyhledávání pomocí Data Factory
description: Přečtěte si informace o tom, jak odeslat data do služby Azure Kognitivní hledání index pomocí Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0226ab75d53733b94a9ae5734b42b7340998759c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100379263"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Vložení dat do indexu služby Azure Kognitivní hledání pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-azure-search-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-search.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Azure kognitivní hledání Connector v v2](../connector-azure-search.md).

Tento článek popisuje, jak pomocí aktivity kopírování odeslat data z podporovaného zdrojového úložiště dat do indexu služby Azure Kognitivní hledání. Podporovaná zdrojová úložiště dat jsou uvedena ve sloupci Zdroj [podporovaných zdrojů a tabulky jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Tento článek se týká článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování a podporovanými kombinacemi úložiště dat.

## <a name="enabling-connectivity"></a>Povolení připojení
Pokud chcete Data Factory službu připojit k místnímu úložišti dat, nainstalujte Správa dat bránu do místního prostředí. Bránu můžete nainstalovat do stejného počítače, který je hostitelem zdrojového úložiště dat, nebo na samostatném počítači, abyste se vyhnuli konkurenčním prostředkům s úložištěm dat.

Brána Správa dat Gateway připojuje místní zdroje dat ke cloudovým službám zabezpečeným a spravovaným způsobem. Podrobnosti o Správa dat bráně najdete v tématu [přesun dat mezi místním a cloudovým](data-factory-move-data-between-onprem-and-cloud.md) článkem.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která odešle data ze zdrojového úložiště dat do indexu hledání pomocí různých nástrojů/rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON.  Ukázku s definicemi JSON pro Data Factory entity, které se používají ke kopírování dat do indexu vyhledávání, najdete v části [JSON example: kopírování dat z SQL Server do indexu služby Azure kognitivní hledání](#json-example-copy-data-from-sql-server-to-azure-cognitive-search-index) tohoto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro vyhledávací index:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující tabulka uvádí popisy pro prvky JSON, které jsou specifické pro propojenou službu Azure Kognitivní hledání.

| Vlastnost | Popis | Povinné |
| -------- | ----------- | -------- |
| typ | Vlastnost Type musí být nastavená na: **AzureSearch**. | Yes |
| url | Adresa URL služby vyhledávání | Yes |
| key | Klíč správce pro vyhledávací službu. | Yes |

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako jsou struktura, dostupnost a zásady pro datovou sadu JSON, jsou podobné pro všechny typy datových sad. Oddíl **typeProperties** se liší pro každý typ datové sady. Oddíl typeProperties pro datovou sadu typu **AzureSearchIndex** má následující vlastnosti:

| Vlastnost | Popis | Povinné |
| -------- | ----------- | -------- |
| typ | Vlastnost Type musí být nastavená na **AzureSearchIndex**.| Yes |
| indexName | Název indexu hledání Data Factory nevytváří index. Index musí existovat v Azure Kognitivní hledání. | Yes |


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . K dispozici jsou vlastnosti, jako jsou název, popis, vstupní a výstupní tabulky a různé zásady, a to pro všechny typy aktivit. Vzhledem k tomu, že vlastnosti dostupné v části typeProperties se liší podle typu jednotlivých aktivit. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

V případě aktivity kopírování, pokud je jímka typu **AzureSearchIndexSink**, jsou v oddílu typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Určuje, zda se má sloučit nebo nahradit, když dokument v indexu již existuje. Podívejte se na [vlastnost WriteBehavior](#writebehavior-property).| Sloučení (výchozí)<br/>Nahrávání| No |
| WriteBatchSize | Když velikost vyrovnávací paměti dosáhne writeBatchSize, nahraje data do indexu vyhledávání. Podrobnosti najdete ve [vlastnosti WriteBatchSize](#writebatchsize-property) . | 1 až 1 000. Výchozí hodnota je 1000. | No |

### <a name="writebehavior-property"></a>Vlastnost WriteBehavior
AzureSearchSink upsertuje při zápisu dat. Jinými slovy, když při psaní dokumentu už klíč dokumentu ve vyhledávacím indexu existuje, Azure Kognitivní hledání aktualizuje existující dokument, ale nevyvolává výjimku konfliktu.

AzureSearchSink poskytuje následující dvě chování Upsert (pomocí sady AzureSearch SDK):

- **Merge**: kombinovat všechny sloupce v novém dokumentu s existujícím. U sloupců s hodnotou null v novém dokumentu je zachována hodnota existující.
- **Nahrání**: nový dokument nahradí stávající. Pro sloupce, které nejsou zadány v novém dokumentu, je hodnota nastavena na hodnotu null, zda je v existujícím dokumentu hodnota jiná než null, nebo ne.

Výchozí chování je **sloučeno**.

### <a name="writebatchsize-property"></a>Vlastnost WriteBatchSize
Služba Azure Kognitivní hledání podporuje zápis dokumentů jako dávky. Dávka může obsahovat 1 až 1 000 akcí. Akce zpracovává jeden dokument k provedení operace Odeslat/sloučit.

### <a name="data-type-support"></a>Podpora datových typů
Následující tabulka určuje, jestli je datový typ Azure Kognitivní hledání podporovaný nebo ne.

| Datový typ Azure Kognitivní hledání | Podporováno v jímky Azure Kognitivní hledání |
| ---------------------- | ------------------------------ |
| Řetězec | Y |
| Int32 | Y |
| Int64 | Y |
| dvojité | Y |
| Logická hodnota | Y |
| DataTimeOffset | Y |
| Pole řetězců | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-sql-server-to-azure-cognitive-search-index"></a>Příklad JSON: kopírování dat z SQL Server do Azure Kognitivní hledání index

Následující příklad ukazuje:

1. Propojená služba typu [AzureSearch](#linked-service-properties).
2. Propojená služba typu [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [SQLServer](data-factory-sqlserver-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureSearchIndex](#dataset-properties).
4. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) a [AzureSearchIndexSink](#copy-activity-properties).

Ukázka kopíruje data časových řad z databáze SQL Server do doby hledání v indexu. Vlastnosti JSON použité v této ukázce jsou popsány v oddílech následujících po ukázkách.

Jako první krok nastavte bránu pro správu dat na místním počítači. Pokyny najdete v článku [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) .

**Propojená služba Azure Kognitivní hledání:**

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

**Propojená služba SQL Server**

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

**Vstupní datová sada SQL Server**

Ukázka předpokládá, že jste v SQL Server vytvořili tabulku "MyTable" a obsahuje sloupec s názvem "timestampcolumn" pro data časové řady. Můžete zadávat dotazy na více tabulek ve stejné databázi pomocí jedné datové sady, ale pro vlastnost tableName typeProperty datové sady se musí použít jedna tabulka.

Nastavení "externí": "true" informuje Data Factory služby, že datová sada je pro objekt pro vytváření dat externá a není vytvořená aktivitou v datové továrně.

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

**Výstupní datová sada Azure Kognitivní hledání:**

Ukázka kopíruje data do indexu služby Azure Kognitivní hledání s názvem **Products**. Data Factory nevytváří index. Ukázku otestujete tak, že vytvoříte index s tímto názvem. Vytvoří index vyhledávání se stejným počtem sloupců jako ve vstupní datové sadě. Nové položky jsou do indexu hledání přidány každou hodinu.

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

**Aktivita kopírování v kanálu s využitím zdroje SQL a jímky indexu služby Azure Kognitivní hledání:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **SqlSource** a typ **jímky** je nastavený na **AzureSearchIndexSink**. Dotaz SQL zadaný pro vlastnost **SqlReaderQuery** vybere data během uplynulé hodiny ke zkopírování.

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

Pokud kopírujete data z cloudového úložiště dat do Azure Kognitivní hledání, `executionLocation` vlastnost je povinná. Následující fragment kódu JSON ukazuje změnu potřebnou v rámci aktivity kopírování `typeProperties` jako příklad. V části [Kopírovat data mezi datovými úložišti cloudu](data-factory-data-movement-activities.md#global) najdete podporované hodnoty a další podrobnosti.

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
Pokud kopírujete data z cloudového úložiště dat do Azure Kognitivní hledání, `executionLocation` vlastnost je povinná. Následující fragment kódu JSON ukazuje změnu potřebnou v rámci aktivity kopírování `typeProperties` jako příklad. V části [Kopírovat data mezi datovými úložišti cloudu](data-factory-data-movement-activities.md#global) najdete podporované hodnoty a další podrobnosti.

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

Sloupce můžete také namapovat ze zdrojové datové sady na sloupce z datové sady jímky v definici aktivity kopírování. Podrobnosti najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
Informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) a různých způsobech jejich optimalizace, najdete v [Průvodci výkonem a optimalizací aktivity kopírování](data-factory-copy-activity-performance.md) .

## <a name="next-steps"></a>Další kroky
Viz následující články:

* [Kurz kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pro podrobné pokyny k vytvoření kanálu s aktivitou kopírování.
