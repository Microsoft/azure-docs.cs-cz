---
title: Přesun dat do/z Azure Cosmos DB | Microsoft Docs
description: Zjistěte, jak přesunout data do nebo z kolekce Azure Cosmos DB pomocí Azure Data Factory
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f3ebd704129aabecffdaa2589b8b086803a2d092
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046595"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Přesun dat do a z databáze Cosmos Azure pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-azure-documentdb-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [konektor Azure Cosmos DB v V2](../connector-azure-cosmos-db.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z Azure Cosmos databáze (SQL API). Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování. 

Data můžete zkopírovat z úložiště dat žádné podporované zdrojové pro Azure Cosmos DB nebo z Azure DB Cosmos do úložiště dat žádné podporované jímky. Seznam úložišť dat jako zdroje nebo jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. 

> [!IMPORTANT]
> Azure Cosmos DB konektor podporuje pouze rozhraní SQL API.

Ke zkopírování dat jako-je do nebo ze soubory JSON nebo jiné Cosmos DB kolekce najdete v části [dokumentů JSON importu a exportu](#importexport-json-documents).

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z Azure Cosmos DB pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru** , **.NET API**, a **rozhraní REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený: 

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat do nebo z databáze Cosmos naleznete v části [JSON příklady](#json-examples) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení konkrétní entity služby Data Factory k databázi Cosmos: 

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro Azure DB Cosmos propojené služby.

| **Vlastnost** | **Popis** | **Požadované** |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **DocumentDb** |Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k databázi Azure Cosmos DB. |Ano |

Příklad:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad naleznete [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly jako struktura, dostupnosti a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

V rámci typeProperties části se liší pro jednotlivé typy datovou sadu a poskytuje informace o umístění dat v úložišti. Rámci typeProperties část datové sady typ **DocumentDbCollection** má následující vlastnosti.

| **Vlastnost** | **Popis** | **Požadované** |
| --- | --- | --- |
| Název_kolekce |Název kolekce dokumentů Cosmos DB. |Ano |

Příklad:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Schéma službou Data Factory
Služba Data Factory pro data bez schémat úložiště, jako je například Azure Cosmos DB, odvodí schéma v jednom z následujících způsobů:  

1. Pokud zadáte strukturu dat pomocí **struktura** vlastnost v definici datové sady, služba Data Factory ctí tato struktura jako schéma. V takovém případě Pokud řádek neobsahuje hodnotu pro sloupec, bude poskytnuta hodnota null pro ni.
2. Pokud nezadáte strukturu dat pomocí **struktura** vlastnost v definici datové sady, služba Data Factory odvodí schématu pomocí prvního řádku v datech. V takovém případě pokud první řádek neobsahuje úplnou schéma, některé sloupce budou chybět ve výsledku operace kopírování.

Osvědčeným postupem pro zdroje dat bez schémat, proto je zadat strukturu dat pomocí **struktura** vlastnost.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity naleznete [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování přijímá pouze jeden vstup a vytváří jenom jeden výstup.

Vlastnosti dostupné v rámci typeProperties části aktivity na druhé straně lišit každý typ aktivity a v případě aktivitě kopírování se liší v závislosti na typech zdrojů a jímky.

V případě aktivitu kopírování, pokud je zdroj typu **DocumentDbCollectionSource** následující vlastnosti jsou k dispozici v **rámci typeProperties** části:

| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Požadované** |
| --- | --- | --- | --- |
| query |Zadejte dotaz číst data. |Řetězec nepodporuje Azure Cosmos DB dotazu. <br/><br/>Příklad: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Ne <br/><br/>Pokud není zadaný příkaz jazyka SQL, který se spustí: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciální znak indikující, že dokument je vnořený |Libovolný znak. <br/><br/>Azure Cosmos DB je úložiště typu NoSQL pro dokumenty JSON, kde jsou povoleny vnořené struktury. Azure Data Factory umožňuje uživateli označují hierarchie prostřednictvím nestingSeparator, což je "." v předchozích příkladech. S oddělovačem, aktivitě kopírování bude generovat objekt "Name" tři podřízené elementy nejprve, střední a příjmení podle "Name.First", "Name.Middle" a "Name.Last" v definici tabulky. |Ne |

**DocumentDbCollectionSink** podporuje následující vlastnosti:

| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Požadované** |
| --- | --- | --- | --- |
| nestingSeparator |Budete potřebovat speciální znak v názvu sloupce zdroj označíte, že vnořených dokumentů. <br/><br/>Například výše: `Name.First` ve výstupu tabulky vytvoří následující strukturu JSON v dokumentu Cosmos DB:<br/><br/>"Název": {<br/>    "První": "Jan"<br/>}, |Znak, který se používá k oddělení úrovní vnoření.<br/><br/>Výchozí hodnota je `.` (tečka). |Znak, který se používá k oddělení úrovní vnoření. <br/><br/>Výchozí hodnota je `.` (tečka). |
| writeBatchSize |Počet paralelní požadavků do služby Azure Cosmos DB vytvářet dokumenty.<br/><br/>Při kopírování dat z databáze Cosmos pomocí této vlastnosti lze optimalizovat výkon. Lepšího výkonu můžete očekávat, když zvýšíte writeBatchSize, protože se odesílají další paralelní žádosti do databáze Cosmos. Ale budete muset vyhnout, omezení šířky pásma, který lze vyvolat chybovou zprávu: "Požadavků je velká".<br/><br/>Omezení je určeno podle počtu faktorů, včetně velikosti dokumentů, počet podmínky v dokumentech, indexování zásad cílovou kolekci, atd. Pro operace kopírování, můžete použít kolekci lepší (např. S3) tak, aby měl nejvíce propustnost, které jsou k dispozici (2 500 žádostí jednotek za sekundu). |Integer |Ne (výchozí: 5) |
| writeBatchTimeout |Počkejte, než čas na dokončení předtím, než vyprší časový limit operace. |časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |

## <a name="importexport-json-documents"></a>Dokumenty JSON importu a exportu
Pomocí tohoto konektoru Cosmos DB, můžete snadno

* Importujte dokumenty JSON z různých zdrojů do Cosmos databáze, včetně objektů Blob v Azure, Azure Data Lake, v místním systému souborů nebo jiných úložišť na základě souborů podporovaných službou Azure Data Factory.
* Exportujte dokumenty JSON ze collecton Cosmos databáze do různých úložišť na základě souborů.
* Migrace dat mezi dvě kolekce Cosmos DB jako-je.

K dosažení taková kopie vázané na schéma 
* Při použití Průvodce kopírováním, zkontrolujte **"exportovat jako-soubory JSON nebo kolekce Cosmos DB"** možnost.
* Když pomocí úpravy JSON, nezadávejte v části "struktura" v datových sad Cosmos DB ani vlastnost "nestingSeparator" na Cosmos DB zdroj/jímka v aktivitě kopírování. Importovat z / exportovat do formátu JSON souborů, v datové sadě úložiště souboru zadejte typ formátu jako "JsonFormat", "filePattern" Konfigurace a přeskočit nastavení formátu rest, najdete v části [formátu JSON](data-factory-supported-file-and-compression-formats.md#json-format) části na podrobnosti.

## <a name="json-examples"></a>Příklady JSON
Následující příklady poskytují ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se ukazují, jak ke zkopírování dat do a z Azure Cosmos DB a Azure Blob Storage. Nicméně je možné zkopírovat data **přímo** ze všech zdrojů do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Příklad: Kopírování dat z Azure Cosmos DB do objektu Blob Azure
Znázorňuje následující ukázka:

1. Propojené služby typu [DocumentDb](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [DocumentDbCollection](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [DocumentDbCollectionSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data v Azure Cosmos DB do objektu Blob Azure. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

**Azure Cosmos DB propojené služby:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Propojená služba Azure Blob storage:**

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
**Azure Documentdb vstupní datové sady:**

Příkladu se předpokládá, máte kolekci s názvem **osoba** v databázi Azure Cosmos DB.

Nastavení "externí": "PRAVDA" a zadání externalData informace o zásadách služby Azure Data Factory, v tabulce je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Azure Blob výstupní datovou sadu:**

Data se zkopírují do nového objektu blob každou hodinu s cestou pro tento objekt blob, které odráží konkrétní hodnotu datetime s rozlišením hodinu.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Ukázka dokumentu JSON v kolekci osoby v databázi Cosmos DB:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Cosmos DB podporuje dotazování dokumentů pomocí SQL jako syntaxe přes hierarchické dokumentů JSON.

Příklad: 

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Následující kanál kopíruje data z kolekce osoby v Azure Cosmos DB databázi do objektu blob Azure. Jako součást aktivitě kopírování vstup a výstup nebyly zadány datové sady.  

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Příklad: Kopírování dat z objektu Blob Azure do Azure Cosmos DB 
Znázorňuje následující ukázka:

1. Propojené služby typu [DocumentDb](#azure-documentdb-linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [DocumentDbCollection](#azure-documentdb-dataset-type-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).

Ukázka zkopíruje data z Azure blob Azure Cosmos DB. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

**Propojená služba Azure Blob storage:**

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
**Azure Cosmos DB propojené služby:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure vstupní datovou sadu objektu Blob:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Azure Cosmos DB výstupní datovou sadu:**

Ukázka zkopíruje data na kolekci s názvem "Osoba".

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Následující kanál kopíruje data z objektu Blob Azure do kolekce osoby v databázi Cosmos. Jako součást aktivitě kopírování vstup a výstup nebyly zadány datové sady.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          }
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Pokud jako vstup objektu blob ukázka

```
1,John,,Doe
```
Výstup JSON v Cosmos DB se pak bude jako:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Azure Cosmos DB je úložiště typu NoSQL pro dokumenty JSON, kde jsou povoleny vnořené struktury. Azure Data Factory umožňuje uživateli označují hierarchie prostřednictvím **nestingSeparator**, což je "." v tomto příkladu. S oddělovačem, aktivitě kopírování bude generovat objekt "Name" tři podřízené elementy nejprve, střední a příjmení podle "Name.First", "Name.Middle" a "Name.Last" v definici tabulky.

## <a name="appendix"></a>Příloha
1. **Otázka:** nemá aktualizace podporu aktivity kopírování existující záznamy?

    **Odpověď:** ne.
2. **Otázka:** jak již nemá opakování kopii Azure Cosmos DB věcech zkopírovat záznamy?

    **Odpověď:** Pokud záznamy na pole "ID" a kopírování pokusí vložit záznam se stejným ID, operace kopírování vrátí chybu.  
3. **Otázka:** podporuje služby Data Factory [rozsah nebo dělení dat na základě hodnoty hash](../../cosmos-db/sql-api-partition-data.md)?

    **Odpověď:** ne.
4. **Otázka:** můžete zadat více než jednu kolekci Azure Cosmos DB pro tabulku?

    **Odpověď:** ne. V tuto chvíli je možné zadat pouze jednu kolekci.

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
