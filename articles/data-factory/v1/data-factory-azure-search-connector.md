---
title: Nabízená data do indexu vyhledávání pomocí datové továrny
description: Přečtěte si, jak nabízená data do Azure Cognitive Search Index pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5b1170f721cf8521cfe1762df0cc616c938ddf28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281558"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Nabízení dat do indexu Azure Cognitive Search pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-azure-search-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-search.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Konektor Azure Cognitive Search ve verzi 2](../connector-azure-search.md).

Tento článek popisuje, jak použít aktivitu kopírování k nabízení dat z úložiště dat podporovaného zdroje do indexu Azure Cognitive Search. Podporovaná úložiště zdrojových dat jsou uvedena ve sloupci Zdroj [podporovaných zdrojů a jímek](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Tento článek vychází z článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s kombinacemi kopírování aktivity a podporovaných kombinací úložiště dat.

## <a name="enabling-connectivity"></a>Povolení připojení
Chcete-li povolit službě Data Factory připojení k místnímu úložišti dat, nainstalujte bránu pro správu dat do místního prostředí. Bránu můžete nainstalovat do stejného počítače, který je hostitelem úložiště zdrojových dat, nebo do samostatného počítače, abyste se vyhnuli konkurenci o prostředky s úložištěm dat.

Brána pro správu dat připojuje místní zdroje dat ke cloudovým službám bezpečným a spravovaným způsobem. Podrobnosti o bráně pro správu dat najdete v článku [Přesun dat mezi místními a cloudovými](data-factory-move-data-between-onprem-and-cloud.md) články.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která odesílá data ze zdrojového úložiště dat do indexu vyhledávání pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.

K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON.  Ukázka s definicemi JSON pro entity Factory dat, které se používají ke kopírování dat pro index vyhledávání, najdete v [tématu JSON příklad: Kopírování dat z místního SQL Serveru do](#json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index) části indexu Azure Cognitive Search v tomto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro index hledání:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Následující tabulka obsahuje popisy prvků JSON, které jsou specifické pro propojenou službu Azure Cognitive Search.

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| type | Vlastnost type musí být nastavena na: **AzureSearch**. | Ano |
| url | Adresa URL vyhledávací služby. | Ano |
| key | Klíč správce vyhledávací služby. | Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON jsou podobné pro všechny typy datových sad. Oddíl **typeProperties** se liší pro každý typ datové sady. Oddíl typeProperties pro datovou sadu typu **AzureSearchIndex** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| type | Vlastnost type musí být nastavena na **AzureSearchIndex**.| Ano |
| název_indexu | Název indexu vyhledávání. Data Factory nevytvoří index. Index musí existovat v Azure Cognitive Search. | Ano |


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a různé zásady jsou k dispozici pro všechny typy aktivit. Vzhledem k tomu, vlastnosti, které jsou k dispozici v části typeProperties se liší s každým typem aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

Pro aktivitu kopírování, když jímka je typu **AzureSearchIndexSink**, následující vlastnosti jsou k dispozici v typeProperties části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| -------- | ----------- | -------------- | -------- |
| Chování zápisu | Určuje, zda má být sloučen nebo nahrazen, pokud dokument již v indexu existuje. Viz [WriteBehavior vlastnost](#writebehavior-property).| Sloučení (výchozí)<br/>Odeslat| Ne |
| WriteBatchSize | Nahraje data do indexu vyhledávání, když velikost vyrovnávací paměti dosáhne writeBatchSize. Podrobnosti naleznete v [vlastnosti WriteBatchSize.](#writebatchsize-property) | 1 až 1 000. Výchozí hodnota je 1000. | Ne |

### <a name="writebehavior-property"></a>WriteBehavior, vlastnost
AzureSearchSink upserts při zápisu dat. Jinými slovy při psaní dokumentu, pokud klíč dokumentu již existuje v indexu vyhledávání, Azure Cognitive Search aktualizuje existující dokument spíše než vyvolání výjimky konfliktu.

AzureSearchSink poskytuje následující dvě upsert chování (pomocí AzureSearch SDK):

- **Sloučení**: zkombinujte všechny sloupce v novém dokumentu s existujícím. Pro sloupce s hodnotou null v novém dokumentu je zachována hodnota v existujícím dokumentu.
- **Upload**: Nový dokument nahradí stávající dokument. U sloupců, které nejsou zadány v novém dokumentu, je hodnota nastavena na hodnotu null, zda je v existujícím dokumentu hodnota nenulové nebo ne.

Výchozí chování je **Sloučit**.

### <a name="writebatchsize-property"></a>WriteBatchSize, vlastnost
Služba Azure Cognitive Search podporuje psaní dokumentů jako dávkovou dávku. Dávka může obsahovat 1 až 1 000 akcí. Akce zpracovává jeden dokument k provedení operace nahrávání a sloučení.

### <a name="data-type-support"></a>Podpora datového typu
Následující tabulka určuje, jestli je datový typ Azure Cognitive Search podporovaný nebo ne.

| Datový typ Azure Cognitive Search | Podporované v Azure kognitivní vyhledávání jímky |
| ---------------------- | ------------------------------ |
| Řetězec | Ano |
| Int32 | Ano |
| Int64 | Ano |
| Double | Ano |
| Logická hodnota | Ano |
| DataTimeOffset | Ano |
| Pole řetězců | Ne |
| GeografiePoint | Ne |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index"></a>Příklad JSON: Kopírování dat z místního SQL Serveru do indexu Azure Cognitive Search

Následující ukázka ukazuje:

1. Propojená služba typu [AzureSearch](#linked-service-properties).
2. Propojená služba typu [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureSearchIndex](#dataset-properties).
4. [Kanál](data-factory-create-pipelines.md) s aktivitou Copy, která používá [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) a [AzureSearchIndexSink](#copy-activity-properties).

Ukázka zkopíruje data časových řad z místní databáze serveru SQL Server a vyhledává index každou hodinu. Vlastnosti JSON použité v této ukázce jsou popsány v následujících částech.

Jako první krok nastavte bránu pro správu dat v místním počítači. Pokyny jsou v [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

**Propojená služba Azure Cognitive Search:**

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

**Vstupní datová sada serveru SQL Server**

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v SQL Server a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad. Můžete dotaz přes více tabulek v rámci stejné databáze pomocí jedné datové sady, ale jedna tabulka musí být použita pro dataset tableName typeProperty.

Nastavení "externí": "true" informuje službu Data Factory, že datová sada je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

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

**Výstupní datová sada Azure Cognitive Search:**

Ukázka zkopíruje data do indexu Azure Cognitive Search s názvem **produkty**. Data Factory nevytvoří index. Chcete-li vzorek otestovat, vytvořte index s tímto názvem. Vytvořte index hledání se stejným počtem sloupců jako ve vstupní datové sadě. Nové položky jsou přidávány do indexu vyhledávání každou hodinu.

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

**Kopírování aktivity v kanálu se zdrojem SQL a jímkou indexu kognitivního vyhledávání Azure:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **SqlSource** a typ **jímky** je nastaven na **AzureSearchIndexSink**. Dotaz SQL zadaný pro vlastnost **SqlReaderQuery** vybere data za poslední hodinu ke kopírování.

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

Pokud kopírujete data z cloudového úložiště `executionLocation` dat do Azure Cognitive Search, je vyžadována vlastnost. Následující úryvek JSON zobrazuje jako příklad `typeProperties` změnu potřebnou v části Kopírovat aktivitu. Zkontrolujte [Kopírovat data mezi oddíly úložiště cloudových dat,](data-factory-data-movement-activities.md#global) kde najdete podporované hodnoty a další podrobnosti.

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
Pokud kopírujete data z cloudového úložiště `executionLocation` dat do Azure Cognitive Search, je vyžadována vlastnost. Následující úryvek JSON zobrazuje jako příklad `typeProperties` změnu potřebnou v části Kopírovat aktivitu. Zkontrolujte [Kopírovat data mezi oddíly úložiště cloudových dat,](data-factory-data-movement-activities.md#global) kde najdete podporované hodnoty a další podrobnosti.

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

Můžete také mapovat sloupce ze zdrojové datové sady do sloupců z datové sady jímky v definici aktivity kopírování. Podrobnosti najdete [v tématu Mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
V průvodci [výkonem a laděním aktivity kopírování](data-factory-copy-activity-performance.md) najdete informace o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) a o různých způsobech jeho optimalizace.

## <a name="next-steps"></a>Další kroky
Viz následující články:

* [Zkopírovat kurz aktivity](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pro podrobné pokyny pro vytvoření kanálu s aktivitou kopírování.
