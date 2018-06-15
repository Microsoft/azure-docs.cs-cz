---
title: 'Azure Cosmos DB: Vývoj v .NET s využitím rozhraní Graph API | Microsoft Docs'
description: Naučte se vyvíjet v .NET s využitím rozhraní SQL API služby Azure Cosmos DB.
services: cosmos-db
author: luisbosquez
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 6026e4d27a341a7354ca3fe9fb92f7671b02eb66
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798674"
---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB: Vývoj v .NET s využitím rozhraní Graph API
Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru databáze Azure Cosmos. 

Tento kurz popisuje způsob vytvoření účtu služby Azure Cosmos DB pomocí webu Azure Portal a vytvoření databáze grafů a kontejneru. Aplikace pak pomocí rozhraní [Graph API](graph-sdk-dotnet.md) vytvoří jednoduchou sociální síť se čtyřmi lidmi a následně prochází a dotazuje graf pomocí Gremlin.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření účtu služby Azure Cosmos DB 
> * Vytvoření databáze grafů a kontejneru
> * Serializace vrcholů a hran do objektů .NET
> * Přidávání vrcholů a hran
> * Dotazování grafu pomocí Gremlin

## <a name="graphs-in-azure-cosmos-db"></a>Grafy ve službě Azure Cosmos DB
Ve službě Azure Cosmos DB můžete vytvářet, aktualizovat a dotazovat grafy pomocí knihovny [Microsoft.Azure.Graphs](graph-sdk-dotnet.md). Knihovna Microsoft.Azure.Graphs obsahuje jednu rozšiřující metodu `CreateGremlinQuery<T>` nad třídou `DocumentClient` pro provádění dotazů Gremlin.

Gremlin je funkcionální programovací jazyk, který podporuje operace zápisu (v jazyce DML) a operace dotazování a procházení. Tento článek obsahuje několik příkladů, které vám pomůžou s Gremlin začít. Podrobný popis možností Gremlin dostupných ve službě Azure Cosmos DB najdete v tématu věnovaném [dotazům Gremlin](gremlin-support.md). 

## <a name="prerequisites"></a>Požadavky
Ujistěte se prosím, že máte následující:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/). 
    * Alternativně můžete pro tento kurz použít [místní emulátor](local-emulator.md).
* Sadu [Visual Studio](http://www.visualstudio.com/).

## <a name="create-database-account"></a>Vytvoření účtu databáze

Začněme vytvořením účtu služby Azure Cosmos DB na webu Azure Portal.  

> [!TIP]
> * Už máte účet služby Azure Cosmos DB? Pokud ano, přeskočte k části [Nastavení řešení v sadě Visual Studio](#SetupVS).
> * Pokud používáte emulátor služby Azure Cosmos DB, nastavte emulátor pomocí postupu v tématu [Emulátor služby Azure Cosmos DB](local-emulator.md) a přeskočte k části [Nastavení řešení v sadě Visual Studio](#SetupVS). 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>Nastavení řešení v sadě Visual Studio
1. Otevřete na svém počítači sadu **Visual Studio**.
2. V nabídce **Soubor** vyberte **Nový** a zvolte **Projekt**.
3. V dialogovém okně **Nový projekt** vyberte **Šablony** / **Visual C#** / **Konzolová aplikace (.NET Framework)**, pojmenujte svůj projekt a klikněte na **OK**.
4. V **Průzkumníku řešení** klikněte pravým tlačítkem na novou konzolovou aplikaci v rámci řešení sady Visual Studio a pak klikněte na **Spravovat balíčky NuGet**.
5. Na kartě **NuGet** klikněte na **Procházet**, do vyhledávacího pole zadejte **Microsoft.Azure.Graphs** a zaškrtněte možnost **Zahrnout předběžné verze**.
6. Najděte ve výsledcích **Microsoft.Azure.Graphs** a klikněte na **Nainstalovat**.
   
   Pokud se vám zobrazí zpráva týkající se kontroly změn řešení, klikněte na **OK**. Pokud se vám zobrazí zpráva týkající se přijetí licence, klikněte na **Souhlasím**.
   
    Knihovna `Microsoft.Azure.Graphs` obsahuje jednu rozšiřující metodu `CreateGremlinQuery<T>` pro provádění operací Gremlin. Gremlin je funkcionální programovací jazyk, který podporuje operace zápisu (v jazyce DML) a operace dotazování a procházení. Tento článek obsahuje několik příkladů, které vám pomůžou s Gremlin začít. Podrobný popis možností Gremlin ve službě Azure Cosmos DB najdete v tématu věnovaném [dotazům Gremlin](gremlin-support.md).

## <a id="add-references"></a>Připojení aplikace

Přidejte do své aplikace tyto dvě konstanty a proměnnou *client*. 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
Dále přejděte zpět na [Azure Portal](https://portal.azure.com) a získejte adresu URL koncového bodu a primární klíč. Adresa URL koncového bodu a primární klíč jsou potřeba k tomu, aby aplikace věděla, kam se má připojit, a aby služba Azure Cosmos DB důvěřovala připojení aplikace. 

Na webu Azure Portal přejděte do svého účtu služby Azure Cosmos DB, klikněte na **Klíče** a pak na **Klíče pro čtení i zápis**. 

Zkopírujte identifikátor URI z portálu a vložte ho místo `Endpoint` ve výše uvedené vlastnosti endpoint. Pak zkopírujte PRIMÁRNÍ KLÍČ z portálu a vložte ho do výše uvedené vlastnosti `AuthKey`. 

![Snímek obrazovky webu Azure Portal, který se v kurzu používá k vytvoření aplikace v jazyce C#. Ukazuje účet služby Azure Cosmos DB se zvýrazněným tlačítkem KLÍČE v navigaci služby Azure Cosmos DB a zvýrazněnými hodnotami IDENTIFIKÁTOR URI a PRIMÁRNÍ KLÍČ v okně Klíče.](./media/tutorial-develop-graph-dotnet/keys.png) 
 
## <a id="instantiate"></a>Vytvoření instance DocumentClient 
Dále vytvořte novou instanci **DocumentClient**.  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>Vytvoření databáze 

Teď vytvořte [databázi](sql-api-resources.md#databases) Azure Cosmos DB pomocí metody [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) nebo [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) třídy **DocumentClient** v sadě [SQL SDK pro .NET](sql-api-sdk-dotnet.md).  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>Vytvoření grafu 

Dále vytvořte kontejner grafu pomocí metody [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) nebo [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) třídy **DocumentClient**. Kolekce je kontejner entit grafu. 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 400 }); 
``` 

## <a id="serializing"></a>Serializace vrcholů a hran do objektů .NET
Azure Cosmos DB používá [přenosový formát GraphSON](gremlin-support.md), který definuje schéma JSON pro vrcholy, hrany a vlastnosti. Sada .NET SDK služby Azure Cosmos DB zahrnuje jako závislost JSON.NET a díky tomu můžeme serializovat a deserializovat GraphSON do objektů .NET, se kterými pracujeme v kódu.

Jako příklad budeme pracovat s jednoduchou sociální sítí se čtyřmi lidmi. Podíváme se na to, jak vytvořit vrcholy `Person`, přidat vztahy `Knows` mezi nimi a pak dotazováním a procházením grafu vyhledat vztahy „přítel přítele“. 

Obor názvů `Microsoft.Azure.Graphs.Elements` poskytuje třídy `Vertex`, `Edge`, `Property` a `VertexProperty` pro deserializaci odpovědí GraphSON do dobře definovaných objektů .NET.

## <a name="run-gremlin-using-creategremlinquery"></a>Spuštění Gremlin pomocí CreateGremlinQuery
Gremlin stejně jako SQL podporuje operace čtení, zápisu a dotazování. Například následující fragment kódu ukazuje, jak vytvořit vrcholy, hrany, provést několik ukázkových dotazů pomocí `CreateGremlinQuery<T>` a asynchronně iterovat těmito výsledky pomocí `ExecuteNextAsync` a `HasMoreResults`.

```cs
Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
{
    { "Cleanup",        "g.V().drop()" },
    { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
    { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
    { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
    { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
    { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
    { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
    { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
    { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
    { "CountVertices",  "g.V().count()" },
    { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
    { "Project",        "g.V().hasLabel('person').values('firstName')" },
    { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
    { "Traverse",       "g.V('thomas').outE('knows').inV().hasLabel('person')" },
    { "Traverse 2x",    "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')" },
    { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
    { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
    { "CountEdges",     "g.E().count()" },
    { "DropVertex",     "g.V('thomas').drop()" },
};

foreach (KeyValuePair<string, string> gremlinQuery in gremlinQueries)
{
    Console.WriteLine($"Running {gremlinQuery.Key}: {gremlinQuery.Value}");

    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, gremlinQuery.Value);
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    Console.WriteLine();
}
```

## <a name="add-vertices-and-edges"></a>Přidávání vrcholů a hran

Podívejme se podrobněji na příkazy Gremlin uvedené v předchozí části. Nejprve přidáme několik vrcholů pomocí metody Gremlin `addV`. Například následující fragment kódu vytvoří vrchol Thomas Andersen typu Person (Osoba) s vlastnostmi pro jméno a věk.

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas').property('age', 44)");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

Pak mezi těmito vrcholy vytvoříme několik hran pomocí metody Gremlin `addE`. 

```cs
// Add a "knows" edge
IDocumentQuery<Edge> createEdgeQuery = client.CreateGremlinQuery<Edge>(
    graphCollection, 
    "g.V('thomas').addE('knows').to(g.V('mary'))");

while (create.HasMoreResults)
{
    Edge thomasKnowsMaryEdge = (await create.ExecuteNextAsync<Edge>()).First();
}
```

Stávající vrchol můžeme upravit pomocí kroku `properties` v Gremlin. Volání pro provedení dotazu prostřednictvím `HasMoreResults` a `ExecuteNextAsync` ve zbývajících příkladech přeskočíme.

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

Hrany a vrcholy můžete zrušit pomocí kroku Gremlin `drop`. Tady je fragment kódu, který ukazuje odstranění vrcholu a hrany. Mějte na paměti, že při zrušení vrcholu se provede kaskádové odstranění přidružených hran.

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>Dotazování grafu

Pomocí Gremlin můžete provádět také dotazy a procházení. Například následující fragment kódu ukazuje, jak vypočítat počet vrcholů v grafu:

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
Můžete provádět filtrování pomocí kroků Gremlin `has` a `hasLabel` a jejich kombinací pomocí operátorů `and`, `or` a `not` vytvářet složitější filtry:

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

Do výsledků dotazu můžete promítnout určité vlastnosti pomocí kroku `values`:

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

Zatím jsme viděli pouze operátory dotazu, které fungují v jakékoli databázi. Grafy jsou rychlé a efektivní pro operace procházení, kdy potřebujete přecházet k souvisejícím hranám a vrcholům. Teď najdeme všechny přátele Thomase. Provedeme to pomocí kroku Gremlin `outE`, kterým vyhledáme všechny vnější hrany od Thomase, a pak pomocí kroku Gremlin `inV` přejdeme z těchto hran k vnitřním vrcholům:

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

Další dotaz provádí dva segmenty směrování a dvojím zavoláním kroků `outE` a `inV` vyhledá všechny přátele přátel Thomase. 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

Pomocí Gremlin můžete vytvářet i složitější dotazy a implementovat výkonnou logiku procházení grafů, včetně kombinování výrazů filtru, provádění smyček pomocí kroku `loop` a implementace podmíněné navigace pomocí kroku `choose`. Získejte další informace o tom, co můžete provádět díky [podpoře Gremlin](gremlin-support.md).

To je vše, tento kurz služby Azure Cosmos DB je u konce. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem na webu Azure Portal.  

1. V nabídce vlevo na portálu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na název vytvořeného prostředku. 
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Vytvořili jste účet služby Azure Cosmos DB. 
> * Vytvořili jste databázi grafů a kontejner.
> * Serializovali jste vrcholy a hrany do objektů .NET.
> * Přidali jste vrcholy a hrany.
> * Dotazovali jste graf pomocí Gremlin.

Teď můžete pomocí konzoly Gremlin vytvářet složitější dotazy a implementovat účinnou logiku procházení grafů. 

> [!div class="nextstepaction"]
> [Dotazování pomocí konzoly Gremlin](tutorial-query-graph.md)
