---
title: Přesunout data do/z Azure Cosmos DB
description: Přečtěte si, jak přesouvat data do a z kolekce Azure Cosmos DB pomocí Azure Data Factory
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84702290"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Přesun dat do a z Azure Cosmos DB pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-azure-documentdb-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Azure Cosmos DB v v2](../connector-azure-cosmos-db.md).

Tento článek vysvětluje, jak používat aktivitu kopírování v Azure Data Factory k přesunu dat do a z Azure Cosmos DB (SQL API). Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data můžete kopírovat z libovolného podporovaného zdrojového úložiště dat do Azure Cosmos DB nebo z Azure Cosmos DB do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje nebo jímky aktivitou kopírování najdete v tabulce [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) .

> [!IMPORTANT]
> Konektor Azure Cosmos DB podporuje jenom rozhraní SQL API.

Chcete-li kopírovat data tak, jak jsou do nebo ze souborů JSON nebo jiné kolekce Cosmos DB, přečtěte si téma [Import/export dokumentů JSON](#importexport-json-documents).

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do nebo z Azure Cosmos DB pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API**a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON. Ukázky s definicemi JSON pro Entity Data Factory, které se používají ke kopírování dat do a z Cosmos DB, najdete v části [Příklady JSON](#json-examples) tohoto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro Cosmos DB:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popis pro prvky JSON specifické pro Azure Cosmos DB propojenou službu.

| **Vlastnost** | **Popis** | **Požadováno** |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **DocumentDb** . |Yes |
| připojovací řetězec |Zadejte informace potřebné pro připojení k databázi Azure Cosmos DB. |Yes |

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
Úplný seznam sekcí & vlastností dostupných pro definování datových sad najdete v článku [vytváření datových sad](data-factory-create-datasets.md) . Oddíly, jako je struktura, dostupnost a zásada formátu JSON datové sady, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro datovou sadu typu **DocumentDbCollection** má následující vlastnosti.

| **Vlastnost** | **Popis** | **Požadováno** |
| --- | --- | --- |
| collectionName |Název kolekce dokumentů Cosmos DB. |Yes |

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
### <a name="schema-by-data-factory"></a>Schéma podle Data Factory
V případě úložišť dat bez schématu, jako je například Azure Cosmos DB, Služba Data Factory odvodí schéma jedním z následujících způsobů:

1. Pokud určíte strukturu dat pomocí vlastnosti **Structure** v definici datové sady, Služba Data Factory tuto strukturu respektuje jako schéma. V takovém případě, pokud řádek neobsahuje hodnotu pro sloupec, bude pro něj zadána hodnota null.
2. Pokud neurčíte strukturu dat pomocí vlastnosti **Structure** v definici datové sady, Služba Data Factory odvodí schéma pomocí prvního řádku v datech. Pokud v takovém případě první řádek neobsahuje úplné schéma, v výsledku operace kopírování budou chybět některé sloupce.

Proto je u zdrojů dat bez schématu osvědčeným postupem určení struktury dat pomocí vlastnosti **struktury** .

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásada.

> [!NOTE]
> Aktivita kopírování používá pouze jeden vstup a vytváří pouze jeden výstup.

Vlastnosti, které jsou k dispozici v části typeProperties aktivity, se liší u jednotlivých typů aktivit a v případě aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

V případě aktivity kopírování, pokud je zdrojem typu **DocumentDbCollectionSource** , jsou v oddílu **typeProperties** k dispozici následující vlastnosti:

| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Požadováno** |
| --- | --- | --- | --- |
| query |Zadejte dotaz pro čtení dat. |Řetězec dotazu podporovaný Azure Cosmos DB. <br/><br/>Příklad: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Pokud není zadaný, provede se příkaz SQL: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciální znak označující, že je dokument vnořený |Libovolný znak. <br/><br/>Azure Cosmos DB je úložiště NoSQL pro dokumenty JSON, kde jsou povoleny vnořené struktury. Azure Data Factory umožňuje uživateli používat hierarchii prostřednictvím nestingSeparator, což je "." ve výše uvedených příkladech. U oddělovače aktivita kopírování vygeneruje objekt "Name" se třemi podřízenými elementy First, uprostřed a Last, podle typu "název. First", "Name. middle" a "Name. Last" v definici tabulky. |No |

**DocumentDbCollectionSink** podporuje následující vlastnosti:

| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Požadováno** |
| --- | --- | --- | --- |
| nestingSeparator |Speciální znak v názvu zdrojového sloupce, který označuje, že je nutné vnořený dokument. <br/><br/>Příklad: `Name.First` ve výstupní tabulce se v Cosmos DB dokumentu vytvoří následující struktura JSON:<br/><br/>"Název": {<br/>    "First": "Jan"<br/>}, |Znak, který se používá k oddělení úrovní vnoření.<br/><br/>Výchozí hodnota je `.` (tečka). |Znak, který se používá k oddělení úrovní vnoření. <br/><br/>Výchozí hodnota je `.` (tečka). |
| writeBatchSize |Počet paralelních požadavků na službu Azure Cosmos DB pro vytváření dokumentů.<br/><br/>Pomocí této vlastnosti můžete vyladit výkon při kopírování dat do nebo z Cosmos DB. Při zvýšení writeBatchSize můžete očekávat lepší výkon, protože se odesílají další paralelní požadavky na Cosmos DB. Je ale potřeba se vyhnout omezování, které může vyvolat chybovou zprávu: "frekvence požadavků je velká".<br/><br/>Omezování je určeno řadou faktorů, včetně velikosti dokumentů, počtu podmínek v dokumentech, indexováním zásad cílové kolekce atd. Pro operace kopírování můžete použít lepší kolekci (například S3), abyste měli k dispozici největší propustnost (2 500 jednotek žádostí za sekundu). |Integer |Ne (výchozí: 5) |
| writeBatchTimeout |Počkejte, než se operace dokončí, než vyprší časový limit. |timespan<br/><br/> Příklad: "00:30:00" (30 minut). |No |

## <a name="importexport-json-documents"></a>Import/export dokumentů JSON
Pomocí tohoto konektoru Cosmos DB můžete snadno

* Import dokumentů JSON z různých zdrojů do Cosmos DB, včetně objektů blob Azure, Azure Data Lake, místního systému souborů nebo jiných úložišť založených na souborech podporovaných v Azure Data Factory.
* Exportujte dokumenty JSON z kolekce Cosmos DB do různých úložišť založených na souborech.
* Migrujte data mezi dvěma kolekcemi Cosmos DB tak, jak jsou.

Pokud chcete dosáhnout takového schématu – nezávislá kopie,
* Pokud používáte Průvodce kopírováním, podívejte se do možnosti **exportovat podle souborů JSON nebo kolekce Cosmos DB** .
* Při použití úprav JSON nezadávejte oddíl Structure (struktura) v Cosmos DB datových sad nebo vlastnost nestingSeparator v Cosmos DB zdroj/jímka v aktivitě kopírování. Chcete-li importovat soubory JSON nebo je z nich exportovat, v datové sadě úložiště souborů zadejte typ formátu jako "JsonFormat", config "vzor souboru" a přeskočte nastavení formátu REST. Podrobnější informace najdete v části [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format) .

## <a name="json-examples"></a>Příklady JSON
V následujících příkladech jsou uvedeny ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data z Azure Cosmos DB a z Azure Blob Storage. Data se ale dají zkopírovat **přímo** ze všech zdrojů do kterékoli z těchto umyvadel, které jsou [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedené, pomocí aktivity kopírování v Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Příklad: kopírování dat z Azure Cosmos DB do objektu blob Azure
Následující ukázka ukazuje:

1. Propojená služba typu [DocumentDb](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [DocumentDbCollection](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [DocumentDbCollectionSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data v Azure Cosmos DB do objektu blob Azure. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

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
**Propojená služba úložiště objektů BLOB v Azure:**

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
**Vstupní datová sada služby Azure Document DB:**

Ukázka předpokládá, že máte kolekci s názvem **Person** v databázi Azure Cosmos DB.

Nastavení "externí": "true" a zadání informací o zásadách externalData služba Azure Data Factory, že je tabulka externí pro objekt pro vytváření dat, a není vytvořená aktivitou v datové továrně.

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

**Výstupní datová sada Azure Blob:**

Data se zkopírují do nového objektu BLOB každou hodinu s cestou pro objekt blob, která odráží konkrétní datum a čas s členitou úrovní hodin.

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
Ukázkový dokument JSON v kolekci person v databázi Cosmos DB:

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
Cosmos DB podporuje dotazování dokumentů pomocí syntaxe jazyka SQL ve srovnání s hierarchickými dokumenty JSON.

Příklad:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Následující kanál kopíruje data z kolekce person v databázi Azure Cosmos DB do objektu blob Azure. V rámci aktivity kopírování byly zadány vstupní a výstupní datové sady.

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
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Příklad: kopírování dat z objektu blob Azure do Azure Cosmos DB
Následující ukázka ukazuje:

1. Propojená služba typu DocumentDb.
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu DocumentDbCollection.
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a DocumentDbCollectionSink.

Ukázka kopíruje data z objektu blob Azure do Azure Cosmos DB. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

**Propojená služba úložiště objektů BLOB v Azure:**

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
**Vstupní datová sada Azure Blob:**

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

Ukázka kopíruje data do kolekce s názvem Person (osoba).

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
Následující kanál kopíruje data z objektu blob Azure do kolekce person v Cosmos DB. V rámci aktivity kopírování byly zadány vstupní a výstupní datové sady.

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
Pokud je ukázkový vstup objektu BLOB jako

```
1,John,,Doe
```
Výstupní formát JSON v Cosmos DB bude:

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
Azure Cosmos DB je úložiště NoSQL pro dokumenty JSON, kde jsou povoleny vnořené struktury. Azure Data Factory umožňuje uživateli používat hierarchii prostřednictvím **nestingSeparator**, což je "." v tomto příkladu. U oddělovače aktivita kopírování vygeneruje objekt "Name" se třemi podřízenými elementy First, uprostřed a Last, podle typu "název. First", "Name. middle" a "Name. Last" v definici tabulky.

## <a name="appendix"></a>Přílohy
1. **Otázka:** Podporuje aktivita kopírování aktualizace stávajících záznamů?

    **Odpověď:** No.
2. **Otázka:** Jak se při opakovaném kopírování Azure Cosmos DB zabývat již zkopírovanými záznamy?

    **Odpověď:** Pokud mají záznamy pole ID a operace kopírování se pokusí vložit záznam se stejným ID, operace kopírování vyvolá chybu.
3. **Otázka:** Podporuje Data Factory [dělení dat v rozsahu nebo na základě hodnoty hash](../../cosmos-db/sql-api-partition-data.md)?

    **Odpověď:** No.
4. **Otázka:** Můžu pro tabulku zadat více než jednu kolekci Azure Cosmos DB?

    **Odpověď:** No. V tuto chvíli lze zadat pouze jednu kolekci.

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .
