---
title: Nabízet data do indexu vyhledávání pomocí služby Data Factory | Microsoft Docs
description: Další informace o tom, jak nabízet data do indexu Azure Search pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fa5ca52f399f74ac6b3eae5c6918442292f87955
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Zápis dat do indexu Azure Search pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-azure-search-connector.md)
> * [Verze 2 – Preview](../connector-azure-search.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [konektor Azure Search v V2](../connector-azure-search.md).

Tento článek popisuje, jak pomocí aktivity kopírování a nabízí data z podporované zdrojové úložiště dat do indexu Azure Search. Podporované zdrojové úložiště dat jsou uvedena ve sloupci zdroj z [podporované zdroje a jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Tento článek vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který uvádí obecný přehled přesun dat s aktivitou kopírování a kombinace podporované datové úložiště.

## <a name="enabling-connectivity"></a>Povolení připojení
Povolit služby připojit k úložišti dat místní služby Data Factory, nainstalujete Brána pro správu dat ve vašem místním prostředí. Můžete bránu nainstalovat na stejný počítač, který hostitelů zdrojová data uložit nebo na samostatný počítač, aby se zabránilo neslučitelných pro prostředky s úložištěm dat.

Brána pro správu dat připojuje místní zdroje dat do cloudové služby v zabezpečený a spravovaný. V tématu [přesun dat mezi místními a cloudovými](data-factory-move-data-between-onprem-and-cloud.md) podrobnosti o Brána pro správu dat najdete v článku.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který by vložil data ze zdrojového úložiště dat do indexu Azure Search pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený: 

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Příklad s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat do indexu Azure Search, naleznete v tématu [JSON příklad: kopírování dat z místního SQL serveru do indexu Azure Search](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení entit služby Data Factory konkrétní do indexu Azure Search:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující tabulka obsahuje popis elementy JSON, které jsou specifické pro službu Azure Search propojený.

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| type | Vlastnost typu musí být nastavena na: **AzureSearch**. | Ano |
| Adresa URL | Adresa URL pro službu Azure Search. | Ano |
| key | Klíč správce pro službu Azure Search. | Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu. **Rámci typeProperties** části se liší pro jednotlivé typy datovou sadu. Rámci typeProperties část datové sady typu **AzureSearchIndex** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| type | Vlastnost typu musí být nastavená na **AzureSearchIndex**.| Ano |
| indexName | Název indexu Azure Search. Objekt pro vytváření dat vytvořit index. Index musí existovat ve službě Azure Search. | Ano |


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a různé zásady jsou dostupné pro všechny typy aktivit. Zatímco se každý typ aktivity lišit podle vlastnosti dostupné v rámci typeProperties oddílu. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

Pro aktivitu kopírování, když je typ jímky **AzureSearchIndexSink**, následující vlastnosti jsou k dispozici v rámci typeProperties části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Určuje, jestli se má sloučit nebo nahradit, pokud již dokument v indexu existuje. Najdete v článku [WriteBehavior vlastnost](#writebehavior-property).| Merge (výchozí)<br/>Odeslat| Ne |
| writeBatchSize | Ukládání dat do indexu Azure Search, když velikost vyrovnávací paměti dosáhne writeBatchSize. Najdete v článku [WriteBatchSize vlastnost](#writebatchsize-property) podrobnosti. | 1 do 1000. Výchozí hodnota je 1 000. | Ne |

### <a name="writebehavior-property"></a>Vlastnost WriteBehavior
Při zápisu dat AzureSearchSink upserts. Jinými slovy při zápisu dokumentu, pokud klíč dokumentu již existuje v indexu Azure Search, Azure Search aktualizuje stávající dokument místo vyvolání k výjimce konflikt.

AzureSearchSink poskytuje následujících dvou upsert chování (pomocí AzureSearch SDK):

- **Sloučení**: kombinovat všechny sloupce v nový dokument s existujícím. Pro sloupce s hodnotou null v novém dokumentu je hodnota v existujícím zachovaná.
- **Nahrát**: nový dokument nahrazuje stávající. Pro sloupce, které nebyly zadány v nový dokument je hodnota nastavena na hodnotu null, zda je jinou hodnotu než null v existujícího dokumentu nebo ne.

Výchozí chování je **sloučení**.

### <a name="writebatchsize-property"></a>Vlastnost WriteBatchSize
Služba Azure Search podporuje zápis dokumentů jako dávku. Batch může obsahovat akce 1 do 1000. Akce zpracovává jeden dokument k provedení operace nahrávání či sloučení.

### <a name="data-type-support"></a>Podpora datového typu
Následující tabulka určuje, zda datového typu Azure Search je podporováno, nebo ne.

| Azure Search datový typ | Podporované ve službě Azure Search jímka |
| ---------------------- | ------------------------------ |
| Řetězec | Ano |
| Int32 | Ano |
| Int64 | Ano |
| Dvojitý | Ano |
| Logická hodnota | Ano |
| DataTimeOffset | Ano |
| Pole řetězců | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Příklad JSON: kopírování dat z místního SQL serveru do indexu Azure Search

Následující příklad ukazuje:

1.  Propojené služby typu [AzureSearch](#linked-service-properties).
2.  Propojené služby typu [onpremisessqlserver](data-factory-sqlserver-connector.md#linked-service-properties).
3.  Vstup [datovou sadu](data-factory-create-datasets.md) typu [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4.  Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureSearchIndex](#dataset-properties).
4.  A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) a [AzureSearchIndexSink](#copy-activity-properties).

Ukázka zkopíruje data časové řady z místní databáze systému SQL Server do indexu Azure Search každou hodinu. Vlastnostech JSON použitých v této ukázce jsou popsané v části následující ukázky.

Jako první krok nastavte Brána pro správu dat na místním počítači. Tyto pokyny jsou v [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

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

**Služba SQL Server propojené**

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

Příkladu se předpokládá, jste vytvořili tabulku "MyTable" v systému SQL Server a obsahuje sloupec s názvem "timestampcolumn" pro data časové řady. Můžete dotazovat přes více tabulek v rámci stejné databáze pomocí jednu datovou sadu, ale musí používat jednu tabulku pro typeProperty tableName datovou sadu.

Nastavení "externí": "PRAVDA" informuje služba Data Factory, datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

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

Ukázka zkopíruje data do indexu Azure Search s názvem **produkty**. Objekt pro vytváření dat vytvořit index. Pokud chcete otestovat vzorovou, vytvořte index s tímto názvem. Vytvoření indexu Azure Search s stejný počet sloupců jako vstupní datové sady. Nové položky se přidají do indexu Azure Search každou hodinu.

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

**Aktivita kopírování v kanálu s SQL zdroj a jímka indexu Azure Search:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **SqlSource** a **podřízený** je typ nastaven na **AzureSearchIndexSink**. Zadané pro dotaz SQL **SqlReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

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

Pokud kopírujete data z jiného úložiště dat cloudu Azure Search, `executionLocation` vlastnost je povinná. Následující fragment kódu JSON ukazuje změnu potřeba v rámci aktivity kopírování `typeProperties` jako příklad. Zkontrolujte [kopírování dat mezi úložišti dat cloudu](data-factory-data-movement-activities.md#global) části Podporované hodnoty a další podrobnosti.

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


## <a name="copy-from-a-cloud-source"></a>Kopírování ze zdrojového cloudu
Pokud kopírujete data z jiného úložiště dat cloudu Azure Search, `executionLocation` vlastnost je povinná. Následující fragment kódu JSON ukazuje změnu potřeba v rámci aktivity kopírování `typeProperties` jako příklad. Zkontrolujte [kopírování dat mezi úložišti dat cloudu](data-factory-data-movement-activities.md#global) části Podporované hodnoty a další podrobnosti.

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

Můžete také mapovat sloupců z datové sady zdroje na sloupce ze sady jímku dat v definici aktivity kopírování. Podrobnosti najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění  
Najdete v článku [výkonu kopie aktivity a vyladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkonu přesun dat (aktivita kopírování) a různé způsoby, jak optimalizovat ho.

## <a name="next-steps"></a>Další postup
Viz následující články:

* [Kopie aktivity kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny pro vytvoření kanálu s aktivitou kopírování.
