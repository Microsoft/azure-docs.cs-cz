---
title: Přesunutí dat do nebo z Azure Cosmos DB
description: Zjistěte, jak přesunout data do kolekce Azure Cosmos DB pomocí Azure Data Factory
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a638184d5232de916ebd25360147301a93309dd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260511"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Přesunutí dat do a z Azure Cosmos DB pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-azure-documentdb-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si článek [Konektor Azure Cosmos DB ve Verzi 2](../connector-azure-cosmos-db.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat do/z Azure Cosmos DB (SQL API). Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

Data z libovolného úložiště dat podporovaného zdroje můžete zkopírovat do Azure Cosmos DB nebo z Azure Cosmos DB do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje nebo propady aktivitou kopírování naleznete v tabulce [Podporovaná data.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

> [!IMPORTANT]
> Konektor Azure Cosmos DB podporuje jenom rozhraní SQL API.

Chcete-li kopírovat data jako-je do /z JSON souborů nebo jiné kolekce Cosmos DB, naleznete [v tématu Import/Export dokumentů JSON](#importexport-json-documents).

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do/z Azure Cosmos DB pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.

K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON. Ukázky s definicemi JSON pro entity Factory dat, které se používají ke kopírování dat do/z Cosmos DB, najdete v části [příklady JSON](#json-examples) v tomto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro Cosmos DB:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu Azure Cosmos DB.

| **Vlastnost** | **Popis** | **Požadováno** |
| --- | --- | --- |
| type |Vlastnost type musí být nastavena na: **DocumentDb.** |Ano |
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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Sekce typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro datovou sadu typu **DocumentDbCollection** má následující vlastnosti.

| **Vlastnost** | **Popis** | **Požadováno** |
| --- | --- | --- |
| název kolekce |Název kolekce dokumentů Cosmos DB. |Ano |

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
### <a name="schema-by-data-factory"></a>Schéma podle datové továrny
Pro úložiště dat bez schématu, jako je Například Azure Cosmos DB, služba Data Factory odvodí schéma jedním z následujících způsobů:

1. Pokud zadáte strukturu dat pomocí **vlastnosti structure** v definici datové sady, služba Data Factory tuto strukturu respektuje jako schéma. V tomto případě pokud řádek neobsahuje hodnotu pro sloupec, bude pro něj poskytnuta hodnota null.
2. Pokud nezadáte strukturu dat pomocí **vlastnosti structure** v definici datové sady, služba Data Factory odvodí schéma pomocí prvního řádku v datech. V tomto případě pokud první řádek neobsahuje úplné schéma, některé sloupce budou chybět ve výsledku operace kopírování.

Proto pro zdroje dat bez schématu je osvědčeným postupem určit strukturu dat pomocí vlastnosti **structure.**

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování trvá pouze jeden vstup a vytváří pouze jeden výstup.

Vlastnosti, které jsou k dispozici v části typeProperties aktivity na druhé straně se liší s každým typem aktivity a v případě kopírovat aktivity se liší v závislosti na typech zdrojů a jímky.

V případě kopírování aktivity, kdy je zdroj typu **DocumentDbCollectionSource,** jsou v části **typeProperties** k dispozici následující vlastnosti:

| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Požadováno** |
| --- | --- | --- | --- |
| query |Zadejte dotaz pro čtení dat. |Řetězec dotazu podporovaný službou Azure Cosmos DB. <br/><br/>Příklad: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Ne <br/><br/>Pokud není zadán, příkaz SQL, který je proveden:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Zvláštní znak označující, že dokument je vnořený |Libovolný znak. <br/><br/>Azure Cosmos DB je úložiště NoSQL pro dokumenty JSON, kde jsou povoleny vnořené struktury. Azure Data Factory umožňuje uživateli dedikovat hierarchii pomocí vnořeníSeparator, což je "." ve výše uvedených příkladech. S oddělovačem bude aktivita kopírování generovat objekt "Name" se třemi podřízenými prvky First, Middle a Last podle "Name.First", "Name.Middle" a "Name.Last" v definici tabulky. |Ne |

**DocumentDbCollectionSink** podporuje následující vlastnosti:

| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Požadováno** |
| --- | --- | --- | --- |
| nestingSeparator |Speciální znak ve název zdrojového sloupce označující, že je potřeba vnořený dokument. <br/><br/>Například výše: `Name.First` ve výstupní tabulce vytvoří následující strukturu JSON v dokumentu Cosmos DB:<br/><br/>"Jméno": {<br/>    "První": "John"<br/>}, |Znak, který se používá k oddělení úrovní vnoření.<br/><br/>Výchozí hodnota `.` je (tečka). |Znak, který se používá k oddělení úrovní vnoření. <br/><br/>Výchozí hodnota `.` je (tečka). |
| writeBatchSize |Počet paralelních požadavků na službu Azure Cosmos DB k vytvoření dokumentů.<br/><br/>Výkon můžete doladit při kopírování dat do/z Cosmos DB pomocí této vlastnosti. Můžete očekávat lepší výkon při zvýšení writeBatchSize protože jsou odesílány další paralelní požadavky na Cosmos DB. Budete však muset vyhnout omezení, které může vyvolat chybovou zprávu: "Míra požadavků je velká".<br/><br/>Omezení se rozhoduje řadou faktorů, včetně velikosti dokumentů, počtu termínů v dokumentech, indexování zásad cílovékolekce atd. Pro operace kopírování můžete použít lepší kolekci (např. S3) k dispozici největší propustnost (2 500 jednotek požadavku za sekundu). |Integer |Ne (výchozí: 5) |
| writeBatchTimeout |Počkejte čas na dokončení operace před časovým výpadkem. |Timespan<br/><br/> Příklad: "00:30:00" (30 minut). |Ne |

## <a name="importexport-json-documents"></a>Import/export dokumentů JSON
Pomocí tohoto konektoru Cosmos DB můžete snadno

* Importujte dokumenty JSON z různých zdrojů do Cosmos DB, včetně Azure Blob, Azure Data Lake, místního systému souborů nebo jiných úložišť založených na souborech podporovaných službou Azure Data Factory.
* Exportujte dokumenty JSON z kolekce Cosmos DB do různých úložišť založených na souborech.
* Migrujte data mezi dvěma kolekcemi Cosmos DB tak, jak jsou.

K dosažení takového schématu-agnostik kopie,
* Při použití průvodce kopírováním zaškrtněte možnost **"Exportovat jako je do souborů JSON nebo kolekce Cosmos DB".**
* Při použití úprav JSON nezadávejte oddíl "struktura" v datové sadě Cosmos DB ani vlastnost "nestingSeparator" ve zdroji/jímce Cosmos DB v aktivitě kopírování. Chcete-li importovat soubory json nebo exportovat je, v datové sadě úložiště souborů určete typ formátu jako "JsonFormat", konfigurační "filePattern" a přeskočte nastavení formátu odpočinku, viz část [formátu JSON](data-factory-supported-file-and-compression-formats.md#json-format) o podrobnostech.

## <a name="json-examples"></a>Příklady JSON
Následující příklady poskytují ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak zkopírovat data do a z Azure Cosmos DB a Azure Blob Storage. Data však můžete zkopírovat **přímo** z libovolného zdroje do libovolného [jímky uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Příklad: Kopírování dat z Azure Cosmos DB do objektu Blob Azure
Níže uvedený vzorek ukazuje:

1. Propojená služba typu [DocumentDb](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [DocumentDbCollection](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [DocumentDbCollectionSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data v Azure Cosmos DB do objektu Blob Azure. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

**Propojená služba Azure Cosmos DB:**

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
**Propojená služba úložiště objektů blob Azure:**

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
**Vstupní datová sada Azure Document DB:**

Ukázka předpokládá, že máte kolekci s názvem **Osoba** v databázi Azure Cosmos DB.

Nastavení "externí": "true" a určení externíinformace o zásadách Data služby Azure Data Factory, že tabulka je externí pro datové továrny a není vytvořena aktivitou v datové továrně.

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

**Výstupní datová sada objektu Blob Azure:**

Data se zkopírují do nového objektu blob každou hodinu s cestou pro objekt blob odrážející konkrétní datetime s hodinovou rozlišovací schopnost.

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
Ukázka dokumentu JSON v kolekci Person v databázi Cosmos DB:

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
Cosmos DB podporuje dotazování dokumentů pomocí syntaxe SQL nad hierarchickými dokumenty JSON.

Příklad:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Následující kanál zkopíruje data z person kolekce v databázi Azure Cosmos DB do objektu blob Azure. Jako součást aktivity kopírování byly zadány vstupní a výstupní datové sady.

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
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Příklad: Kopírování dat z objektu blob Azure do Azure Cosmos DB
Níže uvedený vzorek ukazuje:

1. Propojená služba typu DocumentDb.
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu DocumentDbCollection.
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [Objekt BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a DocumentDbCollectionSink.

Ukázka zkopíruje data z objektu blob Azure do Azure Cosmos DB. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

**Propojená služba úložiště objektů blob Azure:**

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
**Propojená služba Azure Cosmos DB:**

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
**Vstupní datová sada objektu Blob Azure:**

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
**Výstupní datová sada Azure Cosmos DB:**

Ukázka zkopíruje data do kolekce s názvem "Osoba".

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
Následující kanál zkopíruje data z objektu Blob Azure do person kolekce v Cosmos DB. Jako součást aktivity kopírování byly zadány vstupní a výstupní datové sady.

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
          },
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
Pokud je vstup objektu blob vzorku

```
1,John,,Doe
```
Pak výstup JSON v Cosmos DB bude jako:

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
Azure Cosmos DB je úložiště NoSQL pro dokumenty JSON, kde jsou povoleny vnořené struktury. Azure Data Factory umožňuje uživateli dedikovat hierarchii pomocí **oddělovače vnoření**, což je "." v tomto příkladu. S oddělovačem bude aktivita kopírování generovat objekt "Name" se třemi podřízenými prvky First, Middle a Last podle "Name.First", "Name.Middle" a "Name.Last" v definici tabulky.

## <a name="appendix"></a>Příloha
1. **Otázka:** Podporuje aktivita kopírování aktualizaci existujících záznamů?

    **Odpověď:** Ne.
2. **Otázka:** Jak opakování kopie do Azure Cosmos DB funguje s již zkopírovanými záznamy?

    **Odpověď:** Pokud záznamy mají pole "ID" a operace kopírování se pokusí vložit záznam se stejným ID, operace kopírování vyvolá chybu.
3. **Otázka:** Podporuje data factory [rozsah nebo zpracování dat založené na hash?](../../cosmos-db/sql-api-partition-data.md)

    **Odpověď:** Ne.
4. **Otázka:** Můžu pro tabulku zadat víc než jednu kolekci Azure Cosmos DB?

    **Odpověď:** Ne. V tomto okamžiku lze zadat pouze jednu kolekci.

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
