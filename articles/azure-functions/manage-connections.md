---
title: Správa připojení v Azure Functions
description: Zjistěte, jak se vyhnout problémy s výkonem ve službě Azure Functions pomocí statického připojení klientů.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: glenga
ms.openlocfilehash: 9780f98be7d488f87e280a1f391f7cc536ce0f8c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999570"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Správa připojení v Azure Functions

Funkce v aplikaci function app sdílet prostředky a mezi těmito sdílené prostředky jsou připojení &mdash; připojení HTTP, připojení k databázi a připojení ke službám Azure, jako je například úložiště. Pokud mnoho funkcí jsou spuštěny souběžně, je možné mít nedostatek dostupných připojení. Tento článek vysvětluje, jak kód vaší funkce, abyste se vyhnuli použití víc připojení než které skutečně potřebují.

## <a name="connections-limit"></a>Omezení počtu připojení

Počet dostupných připojení je omezený částečně proto, že aplikace function app se spouští v [sandboxu služby Azure App Service](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Jedním z omezení, izolovaný prostor ukládá váš kód je [limit počtu připojení, aktuálně 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). Při dosažení tohoto limitu, modul runtime služby functions vytvoří protokol s následující zprávou: `Host thresholds exceeded: Connections`.

Když přejde pravděpodobnost, že při překročení tohoto limitu [měřítka řadiče přidána instance aplikace funkce](functions-scale.md#how-the-consumption-plan-works) zpracovávat další požadavky. Každá instance aplikace funkce může běžet současně, mnoho funkcí z nichž všechny používají připojení, které se počítají do 300 limit.

## <a name="use-static-clients"></a>Použití statických klientů

Aby se zabránilo uchovávající víc připojení než je nutné, opakovaně používat instancí klientů, kteří místo vytvoření nové značky s každým vyvolání funkce. Klientů .NET, jako jsou [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), a klienty služby Azure Storage můžete spravovat připojení, pokud používáte statické jednoho klienta.

Tady jsou některé zásady dodržovat při použití klienta specifickou pro službu v aplikaci Azure Functions:

- **Ne** vytvořit nového klienta se každé volání funkce.
- **PROVEĎTE** vytvořit jeden, statické klienta, které mohou být využívána každé volání funkce.
- **Vezměte v úvahu** vytvoření jeden statický klienta ve sdílených pomocnou třídu, pokud různé funkce používaly stejnou službu.

## <a name="client-code-examples"></a>Příklady kódu klienta

Tato část ukazuje osvědčené postupy pro vytváření a používání klientů z kód vaší funkce.

### <a name="httpclient-example-c"></a>Příklad HttpClient (C#)

Tady je příklad C# funkční kód, který vytvoří statickou [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx):

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Běžné otázky o .NET [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) je "By měl jsem se připravuje se klient?" Obecně platí, Uvolňujte objekty, které implementují `IDisposable` po dokončení jejich používání. Ale statického klienta není vyřadit, protože není vše nastaveno použití po skončení funkce. Chcete, aby statické klienta na živá po dobu trvání aplikace.

### <a name="http-agent-examples-nodejs"></a>Příklady agenta HTTP (Node.js)

Protože lepší připojení poskytuje možnosti správy, měli byste používat nativní [ `http.agent` ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) třídy místo metody není nativní, jako `node-fetch` modulu. Parametry připojení jsou nakonfigurované na použití možností `http.agent` třídy. Zobrazit [nového agenta (\[možnosti\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options) podrobné možnosti k dispozici s agentem HTTP.

Globální `http.globalAgent` používané `http.request()` má všechny tyto hodnoty příslušných výchozí nastavení. Doporučeným způsobem, jak nakonfigurovat omezení počtu připojení ve funkcích se nastavit maximální počet globálně. Následující příklad nastaví maximální počet soketů pro danou aplikaci funkcí:

```js
http.globalAgent.maxSockets = 200;
```

 Následující příklad vytvoří nový požadavek HTTP s vlastní agenta HTTP jenom pro tento požadavek.

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

## <a name="sqlclient-connections"></a>SqlClient připojení

Kód vaší funkce může pomocí zprostředkovatele dat .NET Framework pro SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) a navažte kontakty do relační databáze SQL. Toto je základní poskytovatel dat rozhraní, které využívají technologie ADO.NET, jako je například rozhraní Entity Framework. Na rozdíl od [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) a [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) připojení ADO.NET implementuje sdružování připojení ve výchozím nastavení. Ale protože lze stále spustit z připojení, doporučujeme optimalizovat připojení k databázi. Další informace najdete v tématu [SQL sdružování připojení serveru (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Některé architektury dat, jako [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx), připojovacích řetězců z informace obvykle získáte **ConnectionStrings** oddílu konfiguračního souboru. V takovém případě musíte explicitně přidat připojovací řetězce databáze SQL na **připojovací řetězce** kolekce vaše nastavení aplikace function app a v [souboru local.settings.json](functions-run-local.md#local-settings-file) ve vašem místním projektu. Pokud vytváříte [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) v kódu funkce, měli byste uložit hodnotu připojovacího řetězce v **nastavení aplikace** u vašich připojení.

## <a name="next-steps"></a>Další postup

Další informace o tom, proč se doporučují statické klientů, přečtěte si téma [antipattern nesprávného vytváření instancí](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Další tipy pro výkon Azure Functions najdete v části [optimalizujete tím její výkon a spolehlivost služby Azure Functions](functions-best-practices.md).
