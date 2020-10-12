---
title: Správa připojení v Azure Functions
description: Zjistěte, jak se vyhnout problémům s výkonem v Azure Functions pomocí klientů se statickým připojením.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 02/25/2018
ms.openlocfilehash: 7ce933511532fdb1bfb5189e5a900e87f3d83fa2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213958"
---
# <a name="manage-connections-in-azure-functions"></a>Správa připojení v Azure Functions

Funkce ve sdílených prostředcích aplikace Function App. Mezi těmito sdílenými prostředky je připojení: připojení HTTP, připojení k databázi a připojení ke službám, jako je například Azure Storage. Pokud mnoho funkcí běží souběžně, je možné, že dojde k vyzkoušení dostupných připojení. V tomto článku se dozvíte, jak zakódovat funkce, abyste se vyhnuli používání více připojení, než potřebují.

## <a name="connection-limit"></a>Limit připojení

Počet dostupných připojení je částečně omezený, protože aplikace Function App běží v [prostředí izolovaného prostoru (sandboxu)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Jedno z omezení, které ukládá izolovaný prostor ve vašem kódu, je omezení počtu odchozích připojení, která jsou aktuálně 600 aktivní (celkem 1 200) připojení na jednu instanci. Když dosáhnete tohoto limitu, modul runtime Functions zapíše do protokolů následující zprávu: `Host thresholds exceeded: Connections` . Další informace najdete v tématu [omezení služby Functions](functions-scale.md#service-limits).

Toto omezení je na instanci. Když [řadič škálování přidává instance aplikace Function App](functions-scale.md#how-the-consumption-and-premium-plans-work) a zpracovává více požadavků, má každá instance nezávislé omezení počtu připojení. To znamená, že není k dispozici žádný limit globálního připojení a v rámci všech aktivních instancí může být mnohem více než 600 aktivních připojení.

Při řešení potíží se ujistěte, že jste povolili Application Insights aplikace Function App. Application Insights umožňuje zobrazit metriky pro aplikace Function App, jako je spuštění. Další informace najdete v tématu [zobrazení telemetrie v Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Statické klienty

Aby nedošlo k většímu podílu připojení, než je potřeba, místo vytváření nových funkcí pomocí jednotlivých volání funkce znovu použijte instance klientů. Pro libovolný jazyk, ve kterém můžete napsat funkci, doporučujeme znovu použít připojení klientů. Například klienti .NET, jako jsou [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1), [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient)a klienti Azure Storage, můžou spravovat připojení, pokud použijete jediného statického klienta.

Tady jsou některé pokyny, které je potřeba provést, když v Azure Functions aplikaci používáte klienta pro konkrétní služby:

- *Nevytvářejte nového* klienta s každým voláním funkce.
- *Vytvořte jednoho* statického klienta, který může použít každé vyvolání funkce.
- *Zvažte* vytvoření jednoho statického klienta ve sdílené pomocné třídě, pokud různé funkce používají stejnou službu.

## <a name="client-code-examples"></a>Příklady klientského kódu

Tato část popisuje osvědčené postupy pro vytváření a používání klientů z kódu funkce.

### <a name="httpclient-example-c"></a>Příklad HttpClient (C#)

Zde je příklad kódu funkce jazyka C#, který vytváří statickou instanci [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1) :

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Běžným dotazem týkajícím se [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1) v .NET je "Mám vyřadit klienta?" Obecně platí, že budete nakládat objekty, které implementují, `IDisposable` až je budete používat. Ale nebudete odstraňovat statický klient, protože ho nebudete používat, když funkce skončí. Chcete, aby byl statický klient aktivní po dobu trvání vaší aplikace.

### <a name="http-agent-examples-javascript"></a>Příklady agenta HTTP (JavaScript)

Vzhledem k tomu, že poskytuje lepší možnosti správy připojení, měli byste použít nativní [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) třídu namísto nenativních metod, jako je například `node-fetch` modul. Parametry připojení jsou konfigurovány prostřednictvím možností `http.agent` třídy. Podrobné možnosti dostupné v agentovi HTTP najdete v tématu [New Agent ( \[ možnosti \] )](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Globální `http.globalAgent` třída, kterou používá, `http.request()` má všechny tyto hodnoty nastavené na jejich příslušné výchozí hodnoty. Doporučený způsob, jak nakonfigurovat limity připojení ve funkcích, je nastavit maximální počet globálně. Následující příklad nastaví maximální počet soketů pro aplikaci Function App:

```js
http.globalAgent.maxSockets = 200;
```

 Následující příklad vytvoří novou žádost HTTP s vlastním agentem HTTP pouze pro tento požadavek:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Příklad kódu DocumentClient (C#)

[DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) se připojuje k instanci Azure Cosmos DB. Dokumentace Azure Cosmos DB doporučuje, abyste [pro celou dobu života vaší aplikace používali klienta s jedním Azure Cosmos DB](../cosmos-db/performance-tips.md#sdk-usage). Následující příklad ukazuje jeden vzor pro provedení ve funkci:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>Příklad kódu CosmosClient (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) se připojuje k instanci Azure Cosmos DB. Dokumentace Azure Cosmos DB doporučuje, abyste [pro celou dobu života vaší aplikace používali klienta s jedním Azure Cosmos DB](../cosmos-db/performance-tips.md#sdk-usage). Následující příklad ukazuje jeden vzor pro provedení ve funkci:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Připojení SqlClient

Kód funkce může použít .NET Framework Zprostředkovatel dat pro SQL Server ([SqlClient](/dotnet/api/system.data.sqlclient?view=dotnet-plat-ext-3.1)) k vytvoření připojení k RELAČNÍ databázi SQL. Toto je také základní poskytovatel pro datové architektury, které spoléhají na ADO.NET, jako je například [Entity Framework](/ef/ef6/). Na rozdíl od připojení [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1) a [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) implementuje ADO.NET ve výchozím nastavení sdružování připojení. Ale vzhledem k tomu, že stále může docházet k připojení, byste měli optimalizovat připojení k databázi. Další informace najdete v tématu věnovaném [sdružování připojení SQL Server (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Některá datová rozhraní, například Entity Framework, obvykle získávají připojovací řetězce z oddílu **connectionStrings** konfiguračního souboru. V tomto případě musíte explicitně přidat připojovací řetězce databáze SQL do kolekce **připojovacích řetězců** v nastavení aplikace Function App a v [local.settings.jsv souboru](functions-run-local.md#local-settings-file) v místním projektu. Pokud vytváříte instanci [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection?view=dotnet-plat-ext-3.1) v kódu funkce, měli byste uložit hodnotu připojovacího řetězce v **nastavení aplikace** s ostatními připojeními.

## <a name="next-steps"></a>Další kroky

Další informace o tom, proč doporučujeme statické klienty, najdete v tématu [antipattern nesprávného vytváření instancí](/azure/architecture/antipatterns/improper-instantiation/).

Další Azure Functions tipů ke zvýšení výkonu najdete v tématu [optimalizace výkonu a spolehlivosti Azure Functions](functions-best-practices.md).
