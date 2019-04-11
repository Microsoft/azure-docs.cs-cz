---
title: Správa připojení v Azure Functions
description: Zjistěte, jak se vyhnout problémy s výkonem ve službě Azure Functions pomocí statického připojení klientů.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 4e9bd4e9ea467446c2814cdb8956a40b1503b027
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469501"
---
# <a name="manage-connections-in-azure-functions"></a>Správa připojení v Azure Functions

Funkce v aplikaci function app sdílení prostředků. Mezi tyto sdílené prostředky jsou připojení: Připojení prostřednictvím protokolu HTTP, připojení k databázi a připojení ke službám, jako je Azure Storage. Pokud mnoho funkcí jsou spuštěny souběžně, je možné mít nedostatek dostupných připojení. Tento článek vysvětluje, jak kód vaší funkce abyste se vyhnuli použití víc připojení než které potřebují.

## <a name="connection-limit"></a>Limit připojení

Počet dostupných připojení je omezený částečně proto, že aplikace function app se spouští v [prostředí izolovaného prostoru](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Jedním z omezení, izolovaný prostor ukládá váš kód je [limit počtu připojení (aktuálně v 600 aktivní připojení a 1 200 celkový počet připojení)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits) jednu instanci. Při dosažení tohoto limitu, modul runtime služby functions vytvoří protokol s následující zprávou: `Host thresholds exceeded: Connections`.

Toto omezení se na jednu instanci.  Když [měřítka řadiče přidána instance aplikace funkce](functions-scale.md#how-the-consumption-and-premium-plans-work) pro zpracování více požadavků, každá instance má limit nezávislé připojení. To znamená neomezený globální připojení, a máte mnohem víc než 600 aktivní připojení ve všech aktivních instancích.

Při odstraňování potíží, ujistěte se, že jste povolili Application Insights pro aplikaci function app. Application Insights umožňuje zobrazit metriky pro vaše aplikace funkcí, jako je spuštění. Další informace najdete v tématu [zobrazit telemetrická data ve službě Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Statické klienty

Aby se zabránilo uchovávající víc připojení než je nutné, opakovaně používat instancí klientů, kteří místo vytvoření nové značky s každým vyvolání funkce. Doporučujeme opětovné použití připojení klienta pro libovolný jazyk, který můžou psát funkce v. Třeba jako klientů .NET [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), a klienty služby Azure Storage můžete spravovat připojení, pokud používáte statické jednoho klienta.

Tady jsou některé zásady dodržovat při používání klienta specifickou pro službu v aplikaci Azure Functions:

- *Ne* vytvořit nového klienta se každé volání funkce.
- *Proveďte* vytvořit jeden, statické klienta, můžete použít každé volání funkce.
- *Vezměte v úvahu* vytvoření jeden statický klienta ve sdílených pomocnou třídu, pokud různé funkce používaly stejnou službu.

## <a name="client-code-examples"></a>Příklady kódu klienta

Tato část ukazuje osvědčené postupy pro vytváření a používání klientů z kód vaší funkce.

### <a name="httpclient-example-c"></a>Příklad HttpClient (C#)

Tady je příklad C# funkční kód, který vytvoří statickou [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) instance:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Běžné otázky o [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) v rozhraní .NET je "By měl jsem vyřadit klientovi?" Obecně platí, vyřadit objekty, které implementují `IDisposable` po dokončení jejich používání. Ale není dispose statické klienta, protože nebyly provedeny používat po skončení funkce. Chcete, aby statické klienta na živá po dobu trvání aplikace.

### <a name="http-agent-examples-javascript"></a>Příklady agenta HTTP (JavaScript)

Protože lepší připojení poskytuje možnosti správy, měli byste používat nativní [ `http.agent` ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) třídy místo metody není nativní, jako `node-fetch` modulu. Parametry připojení jsou nakonfigurované přes možnosti na `http.agent` třídy. Podrobné možnosti k dispozici s agentem HTTP najdete v tématu [nového agenta (\[možnosti\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Globální `http.globalAgent` třídy používané `http.request()` má všechny tyto hodnoty příslušných výchozí nastavení. Doporučeným způsobem, jak nakonfigurovat omezení počtu připojení ve funkcích se nastavit maximální počet globálně. Následující příklad nastaví maximální počet soketů pro danou aplikaci funkcí:

```js
http.globalAgent.maxSockets = 200;
```

 Následující příklad vytvoří nový požadavek HTTP s vlastní agenta HTTP jenom pro tento požadavek:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Příklad kódu DocumentClient (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) připojí k instanci služby Azure Cosmos DB. Dokumentace ke službě Azure Cosmos DB doporučuje vám [používání klienta služby Azure Cosmos DB jednotlivý prvek po dobu životnosti aplikace](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). Následující příklad ukazuje jeden vzor, který to ve funkci:

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
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) připojí k instanci služby Azure Cosmos DB. Dokumentace ke službě Azure Cosmos DB doporučuje vám [používání klienta služby Azure Cosmos DB jednotlivý prvek po dobu životnosti aplikace](../cosmos-db/performance-tips.md#sdk-usage). Následující příklad ukazuje jeden vzor, který to ve funkci:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const masterKey = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, auth: { masterKey } });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { result: itemArray } = await container.items.readAll().toArray();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient připojení

Kód vaší funkce můžete použít zprostředkovatele dat .NET Framework pro SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) a navažte kontakty do relační databáze SQL. Je také základní poskytovatel dat rozhraní, které spoléhají na ADO.NET, jako je třeba [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Na rozdíl od [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) a [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) připojení ADO.NET implementuje sdružování připojení ve výchozím nastavení. Ale protože lze stále spustit z připojení, doporučujeme optimalizovat připojení k databázi. Další informace najdete v tématu [SQL sdružování připojení serveru (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Některé architektury dat, jako je rozhraní Entity Framework informace obvykle získáte z připojovacích řetězců **ConnectionStrings** oddílu konfiguračního souboru. V takovém případě musíte explicitně přidat připojovací řetězce databáze SQL na **připojovací řetězce** kolekce vaše nastavení aplikace function app a v [souboru local.settings.json](functions-run-local.md#local-settings-file) ve vašem místním projektu. Pokud vytváříte instanci [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) v kódu funkce, měli byste uložit hodnotu připojovacího řetězce v **nastavení aplikace** u vašich připojení.

## <a name="next-steps"></a>Další postup

Další informace o proč doporučujeme statickou klientů najdete v tématu [antipattern nesprávného vytváření instancí](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Další tipy pro výkon Azure Functions najdete v části [optimalizujete tím její výkon a spolehlivost služby Azure Functions](functions-best-practices.md).
