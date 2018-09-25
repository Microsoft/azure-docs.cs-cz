---
title: Řešení konfliktů několika hlavními databázemi ve službě Azure Cosmos DB
description: Tento článek popisuje kategorie konflikt a režimy řešení konfliktu například poslední zápis (LWW), vlastní – uživatelsky definovaná postup vlastní – asynchronní v Azure Comsos DB více hlavních databází.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d2f9c5dc8decfbe613d9fd3be2c37b45f1e4f2ea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987624"
---
# <a name="multi-master-conflict-resolution-in-azure-cosmos-db"></a>Řešení konfliktů několika hlavními databázemi ve službě Azure Cosmos DB 

Azure více hlavních databází Cosmos DB nabízí globální konflikt režimy překladu zajistit, že data jsou konzistentní vzhledem k aplikacím ve všech oblastech, kde se replikuje.

## <a name="conflict-categories"></a>Konflikt kategorie

Existují tři kategorie konflikty, ke kterým může dojít při práci s daty služby Azure Cosmos DB:

* **Vložit konflikty:** tohoto typu konfliktu se stane, když aplikace vloží dva nebo více dokumentů s stejný jedinečný index (například ID) ze dvou nebo více oblastech současně. V takovém případě všechny operace zápisu může být zpočátku úspěšné, ale podle zásada řešení konfliktů, kterou zvolíte, je pouze jeden dokument s původní ID potvrzeny.

* **Nahraďte konflikty:** tohoto typu konfliktu se stane, když aplikace aktualizuje jeden dokument současně ze dvou nebo více oblastech.

* **Odstranit konflikty:** tohoto typu konfliktu se stane, když aplikace odstraní dokument z jedné oblasti a aktualizace z jedné nebo několika oblastech. 

## <a name="conflict-resolution-modes"></a>Režimy řešení konfliktů

Existují tři režimy řešení konfliktu nabízené službou Azure Cosmos DB. Pro každou kolekci jsou definovány režimy řešení konfliktů.

> [!NOTE]
> Uživatelé rozhraní SQL API můžete zvolit mezi tři režimy překladu různých konflikt. Pro všechny ostatní rozhraní API modely (MongoDB, Cassandra, Graph a Table) jsou vyřešeny konflikty pomocí služby Wins poslední zapisovače.

### <a name="last-writer-wins-lww"></a>Poslední zápis (LWW)

Poslední zápis je výchozí režim překladu konflikt. V tomto režimu řešení konfliktů jsou na základě číselné hodnoty předané ve vlastnosti v dokumentu.

Následující fragment kódu je příklad, jak nakonfigurovat poslední zápis zásada řešení konfliktů při použití sady .net SDK. "ConflictResolutionPath" Definuje cestu k vlastnosti, která se používá k vyřešení konfliktu. V tomto příkladu `/userDefinedId` je cesta řešení konfliktu a dokument s největší `userDefinedId` hodnota vždy vyhraje konflikt. K registraci do režimu řešení poslední zápis, zřídíte kolekce s ConflictResolutionPolicy jak je znázorněno níže.

```csharp
DocumentCollection lwwCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
   Id = "myCollection", 
   ConflictResolutionPolicy = new ConflictResolutionPolicy
   {
      Mode = ConflictResolutionMode.LastWriterWins,
      ConflictResolutionPath = "/userDefinedId"
   }
});
```

 Režim poslední zápis řešení platí pro různé datové konflikt kategorie následujícím způsobem:

* **Vkládací a aktualizační konflikty:** dva nebo více dokumentů ke kolizím při vložení nebo nahradit, dokument, který obsahuje nejvyšší hodnotu pro cestu k řešení konfliktů stane vítěze (userDefinedId). Pokud máte více dokumentů stejnou číselnou hodnotu pro cestu k řešení konfliktů, vybraného vítěze je Nedeterministický. Nicméně všechny oblasti sloučí do jednoho vítěze.

* **Odstraní konflikt:** se využívá řada delete je v konfliktu,-li odstranit vždy wins přes jiné konflikty nahradit bez ohledu na hodnotu cesty řešení konfliktů.

### <a name="custom--user-defined-procedure"></a>Vlastní – procedury definované uživatelem

V tomto režimu uživatelské ovládací prvky řešení konfliktů registrace definice procedury protokolu UDP (User) do kolekce. Tato UDP má konkrétním podpisem. Pokud tuto možnost vyberte, ale nepovedlo se zaregistrovat UDP nebo protokolu UDP dojde k výjimce za běhu, je v konfliktu se zapisují do kanálu, ve kterém se dá vyřešit samostatně konflikty.

Zaregistrovat vlastní – postup uživatelského režimu řešení konfliktů, zřídíte kolekce s ConflictResolutionPolicy, jak je znázorněno níže.

```csharp
DocumentCollection udpCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
  Id = "myCollection",
  ConflictResolutionPolicy = new ConflictResolutionPolicy
  {
     Mode = ConflictResolutionMode.Custom,
     ConflictResolutionProcedure = UriFactory.CreateStoredProcedureUri("myDatabase","myCollection","myUdpStoredProcedure").ToString()
  }
});
```

V dalším kroku přidejte procedury definované uživatelem do kolekce, jak je znázorněno níže.

```csharp
StoredProcedure myUdpStoredProc = new StoredProcedure
{
   Id = "myUdpStoredProcedure",
   Body = myUdpStoredProcText
};
await myClient.UpsertStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("myDatabase", "myCollection"), myUdpStoredProc);
```

Uložená procedura registrovaný s kolekcí má zvláštní podpis. V tomto příkladu používá port UDP vlastnost `UserDefinedId` řešení konfliktů. Dokument s největší hodnotou wins konflikt.

```javascript
function myUdpStoredProcedure(incomingDocument, existingDocument, isDeleteConflict, conflictingDocuments){
    var collection = getContext().getCollection();

    if (!incomingDocument) {
        if (existingDocument) {

            collection.deleteDocument(existingDocument._self, {}, function(err, responseOptions) {
                if (err) throw err;
            });
        }
    } else if (isDeleteConflict) {
        // delete always wins.
    } else {
        var documentToUse = incomingDocument;

        if (existingDocument) {
            if (documentToUse.userDefinedId < existingDocument.userDefinedId) {
                documentToUse = existingDocument;
            }
        }

        var i;
        for (i = 0; i < conflictingDocuments.length; i++) {
            if (documentToUse.userDefinedId < conflictingDocuments[i].userDefinedId) {
                documentToUse = conflictingDocuments[i];
            }
        }

        tryDelete(conflictingDocuments, incomingDocument, existingDocument, documentToUse);
    }

    function tryDelete(documents, incoming, existing, documentToInsert) {
        if (documents.length > 0) {
            collection.deleteDocument(documents[0]._self, {}, function(err, responseOptions) {
                if (err) throw err;

                documents.shift();
                tryDelete(documents, incoming, existing, documentToInsert);
            });
        } else if (existing) {
            collection.replaceDocument(existing._self, documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        } else {
            collection.createDocument(collection.getSelfLink(), documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        }
    }
}

```

Postup má čtyři parametry:

* **incomingDocument:** určuje konfliktní verze dokumentu. Konflikt může být insert, replace nebo konflikt odstranění. Pro odstranění konfliktu bude odstranění obrázku (značek odstraněných položek) bez obsahu.

* **existingDocument:** určuje v potvrzené verzi dokumentu, který má stejnou hodnotu jako incomingDocument "identifikátorů rid". Tento parametr je nastaven na hodnotu null pro vložení a odstranění konfliktu.

* **isDeleteConflict:** příznak logické hodnoty označující, pokud je v konfliktu s dříve odstraněným dokumentu vstupní dokument. Když tento parametr je nastaven na hodnotu true, existingDocument bude mít také hodnotu null.

* **conflictingDocuments:** určuje kolekci v potvrzené verzi všechny dokumenty v databázi, která jsou v konfliktu s incomingDocument na sloupec ID nebo další jedinečný index pole. Tyto dokumenty budou mít různé "odstranit" hodnotu ve srovnání s incomingDocument.

Uživatelem definované postup má úplný přístup ke klíči oddílů služby Cosmos DB a můžete provádět jakékoli operace úložiště k řešení konfliktů. Pokud postup uživatelem definované nesměrují konflikt verzí, systém vyřadí konflikt a existingDocument bude i nadále potvrzeny. Pokud postup uživatelem definované selže nebo je buď neexistuje, Azure Cosmos DB všechny přidá konflikt do konflikty jen pro čtení informačního kanálu, ve kterém je možné je zpracovat asynchronně, jak je znázorněno [režimu řešení konfliktů. asynchronní](). 

### <a name="custom--asynchronous"></a>Vlastní – asynchronní  

V tomto režimu se služby Azure Cosmos DB vyloučí všechny konflikty (insert, replace a delete) z potvrzuje a registruje v konflikty jen pro čtení informačního kanálu pro odložené řešení aplikací uživatele. Aplikace můžete provádět řešení konfliktů asynchronně a použijte jakékoli logiky nebo odkazovat na jakékoli externího zdroje, aplikace nebo služby o vyřešení konfliktu.

Zaregistrovat vlastní – režimu řešení konfliktů. asynchronní, zřídíte kolekce s ConflictResolutionPolicy, jak je znázorněno níže.

```csharp
DocumentCollection manualCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
    Id = "myCollection",
    ConflictResolutionPolicy = new ConflictResolutionPolicy
    {
        Mode = ConflictResolutionMode.Custom
    }
});
```

Číst a zpracovat všechny konflikty v informačním kanálu je v konfliktu, implementujte kódu níže. Data uložená v konfliktům kanál přidá náklady na úložiště. Ano doporučuje se odstraňují data uložená v kanálu po jejich zpracování konfliktů.

```csharp
FeedResponse<Conflict> response = await myClient.ReadConflictFeedAsync(myCollectionUri);
  foreach (Conflict conflict in response)
  {
      switch(conflict.OperationKind)
      {
         case OperationKind.Create:
         //Process Insert Conflicts.
         …
         case OperationKind.Replace:
         //Process Replace Conflicts
         …
         case OperationKind.Delete:
         //Process Delete Conflicts
         …
      }
  //Optionally delete the conflict after processed
  await myClient.DeleteConflictAsync(conflict.SelfLink);
  }
```

> [!NOTE]
> Kanál je v konfliktu nezahrnuje naslouchací proces odesílání oznámení pro zpracování příjmu dat, jako je změna informačního kanálu ve službě Cosmos DB. Budete muset implementovat logiku pro dotazování informačního kanálu je v konfliktu a určí, zda je v konfliktu.

## <a name="code-samples"></a>Ukázky kódů

Níže jsou ukázkové aplikace, které ukazují řešení konfliktů pro rozhraní API uvedené. Každá ukázka generuje je v konfliktu v kontejneru a ukazuje, jak jsou řešeny konflikty mezi pro každý režimu řešení konfliktů. podporované.

|Modelu rozhraní API  | Sada SDK |Ukázka |
|---------|---------|---------|
|SQL      | .NET    |[Azure-cosmos-DB-SQL-DotNet-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-sql-dotnet-multi-master)  |
|MongoDB  | .NET    |[Azure-cosmos-DB-mongodb-DotNet-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-multi-master)   |
|Table    | .NET    |[Azure-cosmos-DB-Table-DotNet-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-multi-master)       |
|SQL      | Node    |[Azure-cosmos-js/samples/MultiRegionWrite /](https://github.com/Azure/azure-cosmos-js/tree/master/samples/MultiRegionWrite)  |
|SQL      | Java    |[Azure-cosmos-DB-SQL-Java-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-sql-java-multi-master)  |

## <a name="next-steps"></a>Další postup

V tomto článku jste se seznámili konflikt kategorie a způsoby řešení konfliktu pro službu Azure Cosmos DB. V dalším kroku se podívejte na následující zdroje:

* [Použití MongoDB klientů s několika hlavními uzly](multi-master-oss-nosql.md)
