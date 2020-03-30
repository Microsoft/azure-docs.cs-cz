---
title: Správa připojení ve funkcích Azure
description: Zjistěte, jak se vyhnout problémům s výkonem ve funkcích Azure pomocí klientů statického připojení.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276449"
---
# <a name="manage-connections-in-azure-functions"></a>Správa připojení ve funkcích Azure

Funkce ve zdrojích sdílení aplikace funkce. Mezi tyto sdílené prostředky patří připojení: http připojení, připojení k databázi a připojení ke službám, jako je Azure Storage. Pokud je současně spuštěno mnoho funkcí, je možné spustit dostupná připojení. Tento článek vysvětluje, jak kód funkce, aby se zabránilo použití více připojení, než je potřeba.

## <a name="connection-limit"></a>Limit připojení

Počet dostupných připojení je omezen částečně proto, že aplikace funkce běží v [prostředí izolovaného prostoru](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Jedním z omezení, která sandbox ukládá na váš kód je omezení počtu odchozích připojení, což je aktuálně 600 aktivních (1 200 celkem) připojení na instanci. Když dosáhnete tohoto limitu, zaběhový čas funkce `Host thresholds exceeded: Connections`zapíše do protokolů následující zprávu: . Další informace naleznete v [tématu Functions service limits](functions-scale.md#service-limits).

Tento limit je pro instanci. Když [řadič škálování přidá instance aplikace funkce](functions-scale.md#how-the-consumption-and-premium-plans-work) pro zpracování více požadavků, každá instance má nezávislý limit připojení. To znamená, že neexistuje žádné globální omezení připojení a můžete mít mnohem více než 600 aktivních připojení ve všech aktivních instancích.

Při řešení potíží se ujistěte, že jste povolili Application Insights pro vaši aplikaci funkcí. Application Insights umožňuje zobrazit metriky pro vaše funkční aplikace, jako jsou spouštění. Další informace naleznete [v tématu Zobrazení telemetrie v Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Statikoví klienti

Chcete-li se vyhnout držení více připojení, než je nutné, znovu použít instance klienta, spíše než vytvářet nové s každou vyvolání funkce. Doporučujeme znovu použít připojení klientů pro libovolný jazyk, do kterého můžete zapisovat svou funkci. Například klienti .NET, jako jsou klienti [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)a Azure Storage, mohou spravovat připojení, pokud používáte jednoho statického klienta.

Tady jsou některé pokyny, které je třeba dodržovat, když používáte klienta specifického pro službu v aplikaci Azure Functions:

- *Nevytvářejte* nového klienta s každou vyvolání funkce.
- *Vytvořte* jednoho statického klienta, který může použít každé vyvolání funkce.
- *Zvažte* vytvoření jednoho statického klienta ve sdílené pomocné třídě, pokud různé funkce používají stejnou službu.

## <a name="client-code-examples"></a>Příklady kódu klienta

Tato část ukazuje osvědčené postupy pro vytváření a používání klientů z kódu funkce.

### <a name="httpclient-example-c"></a>Příklad httpklienta (C#)

Tady je příklad kódu funkce Jazyka C#, který vytváří statickou instanci [httpklienta:](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Častá otázka o [httpclient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) v rozhraní .NET je "Mám vyřadit svého klienta?" Obecně lze vyřadit `IDisposable` objekty, které implementují po dokončení jejich použití. Ale není vyřazení statického klienta, protože jste neprovedli jeho použití při ukončení funkce. Chcete, aby statický klient žít po dobu trvání aplikace.

### <a name="http-agent-examples-javascript"></a>Příklady agenta HTTP (JavaScript)

Vzhledem k tomu, že poskytuje lepší [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) možnosti správy připojení, měli `node-fetch` byste použít nativní třídu namísto nenativních metod, jako je například modul. Parametry připojení jsou konfigurovány `http.agent` prostřednictvím možností ve třídě. Podrobné možnosti, které jsou k dispozici u agenta HTTP, naleznete [v tématu new Agent(\[options\]).](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)

Globální `http.globalAgent` třída používaná `http.request()` má všechny tyto hodnoty nastaveny na jejich příslušné výchozí hodnoty. Doporučený způsob konfigurace omezení připojení ve funkcích je nastavit maximální počet na celém světě. Následující příklad nastaví maximální počet soketů pro aplikaci funkce:

```js
http.globalAgent.maxSockets = 200;
```

 Následující příklad vytvoří nový požadavek HTTP s vlastním agentem HTTP pouze pro tento požadavek:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Příklad kódu documentclient (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) se připojí k instanci Azure Cosmos DB. Dokumentace Azure Cosmos DB doporučuje [použít klienta Singleton Azure Cosmos DB po dobu životnosti vaší aplikace](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). Následující příklad ukazuje jeden vzor pro to, že ve funkci:

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

### <a name="cosmosclient-code-example-javascript"></a>Příklad kódu klienta Cosmos (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) se připojí k instanci Azure Cosmos DB. Dokumentace Azure Cosmos DB doporučuje [použít klienta Singleton Azure Cosmos DB po dobu životnosti vaší aplikace](../cosmos-db/performance-tips.md#sdk-usage). Následující příklad ukazuje jeden vzor pro to, že ve funkci:

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

## <a name="sqlclient-connections"></a>Připojení sqlclient

Kód funkce může použít zprostředkovatele dat rozhraní .NET Framework pro SQL Server ([SQLClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) k navazování připojení k relační databázi SQL. Toto je také základní zprostředkovatel pro datové rámce, které spoléhají na ADO.NET, jako je například [entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Na rozdíl od připojení [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) a [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) ADO.NET ve výchozím nastavení implementuje sdružování připojení. Ale protože stále můžete spustit připojení, měli byste optimalizovat připojení k databázi. Další informace naleznete v tématu [SQL Server Connection Pooling (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Některé datové architektury, například Entity Framework, obvykle získat připojovací řetězce z části **ConnectionStrings** konfiguračního souboru. V takovém případě je nutné explicitně přidat připojovací řetězce databáze SQL do kolekce **připojovacích řetězců** nastavení aplikace funkce a do [souboru local.settings.json](functions-run-local.md#local-settings-file) v místním projektu. Pokud vytváříte instanci [sqlconnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) v kódu funkce, měli byste uložit hodnotu připojovacího řetězce v **nastavení aplikace** s ostatními připojeními.

## <a name="next-steps"></a>Další kroky

Další informace o tom, proč doporučujeme statické klienty, naleznete [v tématu Nesprávné antipattern instance](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Další tipy pro výkon Azure Functions najdete [v tématu Optimalizace výkonu a spolehlivosti funkcí Azure](functions-best-practices.md).
