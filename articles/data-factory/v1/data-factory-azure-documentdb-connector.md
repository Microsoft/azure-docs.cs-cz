---
title: Přesun dat do a ze služby Azure Cosmos DB | Dokumentace Microsoftu
description: Zjistěte, jak přesunout data do a z kolekce Azure Cosmos DB pomocí Azure Data Factory
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 54e78496b98fffefd2932a2ce7625f8b325fb32a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812646"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Přesun dat do a ze služby Azure Cosmos DB pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-azure-documentdb-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor služby Azure Cosmos DB ve verzi V2](../connector-azure-cosmos-db.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování ve službě Azure Data Factory k přesunu dat do a z Azure Cosmos DB (rozhraní SQL API). Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

Můžete zkopírovat data z libovolné podporované zdrojové úložiště dat do služby Azure Cosmos DB nebo ze služby Azure Cosmos DB k jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky.

> [!IMPORTANT]
> Konektor služby Azure Cosmos DB podporuje pouze rozhraní SQL API.

Pro kopírování dat jako – je do a z souborů JSON nebo jiné kolekce Cosmos DB najdete v článku [Import/Export dokumentů JSON](#importexport-json-documents).

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, která přesunuje data ze služby Azure Cosmos DB s použitím různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory.
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování.
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup.

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON. Ukázky s definicemi JSON entit služby Data Factory, které se používají ke kopírování dat do a ze služby Cosmos DB najdete v tématu [JSON příklady](#json-examples) části tohoto článku.

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory konkrétní ke službě Cosmos DB:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro služby Azure Cosmos DB propojený.

| **Vlastnost** | **Popis** | **Požadováno** |
| --- | --- | --- |
| type |Vlastnost type musí být nastavená na: **DocumentDb** |Ano |
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
Úplný seznam vlastnosti pro definování datové sady & částech najdete [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny datové sady typy (Azure SQL, Azure blob, tabulky Azure, atd.).

V části typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. TypeProperties části datové sady typu **DocumentDbCollection** má následující vlastnosti.

| **Vlastnost** | **Popis** | **Požadováno** |
| --- | --- | --- |
| collectionName |Název kolekce dokumentu Cosmos DB. |Ano |

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
Služba Data Factory pro úložišť dat bez schématu, jako je Azure Cosmos DB, odvodí schéma v jednom z následujících způsobů:

1. Pokud určíte strukturu dat pomocí **struktura** vlastnost v definici datové sady, služba Data Factory respektuje tato struktura jako schéma. V takovém případě pokud řádku neobsahuje hodnotu pro sloupec, hodnotu null, poskytneme vám pro něj.
2. Pokud nezadáte strukturu dat s použitím **struktura** vlastnost v definici datové sady, služba Data Factory odvodí schéma pomocí prvního řádku v datech. V takovém případě pokud první řádek obsahuje úplného schématu, některé sloupce bude chybět ve výsledku operace kopírování.

Proto pro zdroje dat bez schématu, osvědčeným postupem je zadání strukturu dat pomocí **struktura** vlastnost.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování přijímá pouze jeden vstup a vytvoří jenom jeden výstup.

Vlastnosti v části typeProperties aktivity se liší na druhé straně s jednotlivými typu aktivity a v případě aktivity kopírování se liší v závislosti na typy zdroje a jímky.

V případě aktivity kopírování, pokud je zdroj typu **DocumentDbCollectionSource** následující vlastnosti jsou k dispozici v **typeProperties** části:

| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Požadováno** |
| --- | --- | --- | --- |
| query |Zadejte dotaz, který bude číst data. |Řetězce podporovány službou Azure Cosmos DB dotazu. <br/><br/>Příklad: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Ne <br/><br/>Pokud není zadán, příkaz SQL, která se spustí: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciální znakem k označení, že je vnořená dokumentu |Libovolný znak. <br/><br/>Azure Cosmos DB je úložiště NoSQL pro dokumenty JSON, ve kterém jsou povolené vnořené struktury. Azure Data Factory umožňuje uživateli k označení hierarchie prostřednictvím nestingSeparator, což je "." v předchozích příkladech. Oddělovačem, aktivita kopírování bude generovat objekt "Name" s tři podřízené prvky nejprve, střední a příjmení podle "Name.First", "Name.Middle" a "Name.Last" v definici tabulky. |Ne |

**DocumentDbCollectionSink** podporuje následující vlastnosti:

| **Vlastnost** | **Popis** | **Povolené hodnoty** | **Požadováno** |
| --- | --- | --- | --- |
| nestingSeparator |Je potřeba speciálního znaku v název zdrojového sloupce označíte daný vnořený dokument. <br/><br/>Například výše: `Name.First` ve výstupu tabulky vytvoří následující strukturu JSON v dokumentu databáze Cosmos DB:<br/><br/>"Name": {<br/>    "First": "John"<br/>}, |Znak, který se používá k oddělení úrovní vnoření.<br/><br/>Výchozí hodnota je `.` (tečka). |Znak, který se používá k oddělení úrovní vnoření. <br/><br/>Výchozí hodnota je `.` (tečka). |
| WriteBatchSize |Počet paralelních žádostí do služby Azure Cosmos DB k vytváření dokumentů.<br/><br/>Při kopírování dat ze služby Cosmos DB pomocí této vlastnosti můžete optimalizovat výkon. Lepšího výkonu můžete očekávat, když zvýšíte writeBatchSize, protože se odesílají další paralelní požadavky ke službě Cosmos DB. Ale budete muset vyhnout omezení, která může vyvolat chybovou zprávu: "Rychlost požadavků je velká".<br/><br/>Omezení je určeno pomocí několika faktory, včetně velikosti dokumentů, počet podmínek v dokumentech, indexování zásad cílovou kolekci, atd. Pro operace kopírování, vám pomůže lépe kolekce (třeba S3) mají největší propustnost, které jsou k dispozici (požadavek 2 500 jednotek za sekundu). |Integer |Ne (výchozí: 5) |
| writeBatchTimeout |Čekací doba dokončení před vypršením časového limitu operace. |Časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |

## <a name="importexport-json-documents"></a>Import/Export dokumentů JSON
Pomocí tohoto konektoru služby Cosmos DB, můžete snadno

* Dokumenty JSON import z různých zdrojů do služby Cosmos DB, včetně objektů Blob v Azure, Azure Data Lake v místním systému souborů nebo dalšími úložišti souborů podporovaných službou Azure Data Factory.
* Exportujte dokumentů JSON do různých úložišť založená na souborech z collecton Cosmos DB.
* Migrace dat mezi dvě kolekce Cosmos DB jako-je.

Abyste dosáhli takové kopírování dogmaticky na schématu
* Při použití Průvodce kopírováním, zkontrolujte **"exportovat jako-soubory JSON nebo kolekce Cosmos DB"** možnost.
* Při použití, úpravy v editoru JSON, nezadávejte oddílu "struktura" v Cosmos DB publikovaných ani vlastnost "nestingSeparator" ve službě Cosmos DB zdroje/jímky v aktivitě kopírování. Z Importovat / exportovat soubory JSON, v datové sadě úložiště souboru zadejte typ formátu jako "JsonFormat", "filePattern" config a přeskočit zbývající nastavení formátu naleznete v tématu [formátu JSON](data-factory-supported-file-and-compression-formats.md#json-format) části na podrobnosti.

## <a name="json-examples"></a>Příklady pro JSON
Následující příklady popisují ukázkový JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Zobrazí se kopírování dat do a ze služby Azure Cosmos DB a Azure Blob Storage. Nicméně je možné zkopírovat data **přímo** z libovolného zdroje do libovolné jímky uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Příklad: Kopírování dat ze služby Azure Cosmos DB do objektů Blob v Azure
Následující příklad ukazuje:

1. Propojené služby typu [DocumentDb](#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [DocumentDbCollection](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [DocumentDbCollectionSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data ve službě Azure Cosmos DB do objektů Blob v Azure. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

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
**Azure Document DB vstupní datovou sadu:**

Příkladu se předpokládá máte kolekci s názvem **osoba** v databázi Azure Cosmos DB.

Nastavení "externí": "true" a určení externalData informace o zásadách služby Azure Data Factory, že v tabulce je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

Data zkopírována do nového objektu blob každou hodinu pomocí cesty pro objekt blob odráží konkrétní datum a čas s hodinu členitosti.

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
Ukázkový dokument JSON v kolekci osoby v databázi Cosmos DB:

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
Cosmos DB podporuje dotazování dokumentů pomocí syntaxe typu SQL přes hierarchické dokumentů JSON.

Příklad:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Následující kanál kopíruje data z kolekce osoby v databázi Azure Cosmos DB do objektu blob Azure. Jako součást aktivity kopírování vstup a výstup nejsou zadané datové sady.

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
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Příklad: Kopírování dat z Azure Blob do služby Azure Cosmos DB
Následující příklad ukazuje:

1. Propojené služby typu DocumentDb.
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu DocumentDbCollection.
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a DocumentDbCollectionSink.

Ukázce kopíruje data z Azure blob do služby Azure Cosmos DB. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

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
**Azure vstupní datovou sadou objektů Blob:**

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
Následující kanál kopíruje data z objektu Blob Azure do kolekce osoby v Cosmos DB. Jako součást aktivity kopírování vstup a výstup nejsou zadané datové sady.

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
Pokud je jako vstup objektu blob vzorku

```
1,John,,Doe
```
Ve výstupu JSON ve službě Cosmos DB bude jako:

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
Azure Cosmos DB je úložiště NoSQL pro dokumenty JSON, ve kterém jsou povolené vnořené struktury. Azure Data Factory umožňuje uživateli k označení hierarchie prostřednictvím **nestingSeparator**, což je "." v tomto příkladu. Oddělovačem, aktivita kopírování bude generovat objekt "Name" s tři podřízené prvky nejprve, střední a příjmení podle "Name.First", "Name.Middle" a "Name.Last" v definici tabulky.

## <a name="appendix"></a>Příloha
1. **Otázka:** Aktualizovat podporu aktivity kopírování existujících záznamů?

    **Odpověď:** Ne.
2. **Otázka:** Jak opakování kopie do služby Azure Cosmos DB vypořádat se už zkopírovaný záznamy?

    **Odpověď:** Pokud záznamy mají pole "ID" a operace kopírování se pokusí vložit záznam se stejným ID, operace kopírování vyvolá chybu.
3. **Otázka:** Data Factory podporuje [rozsahu nebo dělení na základě hodnoty hash dat](../../cosmos-db/sql-api-partition-data.md)?

    **Odpověď:** Ne.
4. **Otázka:** Můžete zadat více než jednu kolekci Azure Cosmos DB pro tabulku?

    **Odpověď:** Ne. V tuto chvíli je možné zadat pouze jednu kolekci.

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.
