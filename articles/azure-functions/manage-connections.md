---
title: Správa připojení v Azure Functions
description: Jak se vyhnout problémům s výkonem v Azure Functions pomocí statické připojení klientů.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: tdykstra
ms.openlocfilehash: 4ea2b033d8d67dd3c921fb833462605ba0aeb687
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654792"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Správa připojení v Azure Functions

Funkce v aplikaci funkce sdílet prostředky a mezi tyto sdílené prostředky jsou připojení &mdash; připojení protokolu HTTP, připojení databáze a připojení ke službám Azure, jako je například úložiště. Pokud mnoho funkcí běží souběžně je možné dostatek dostupné připojení. Tento článek vysvětluje, jak kód funkcí předejdete pomocí více připojení, než které skutečně potřebují.

## <a name="connections-limit"></a>Omezení počtu připojení

Počet připojení k dispozici je omezená částečně, protože funkce aplikace běží v [izolovaného prostoru Azure App Service](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Jedním z omezení, které ukládá izolovaný prostor v kódu je [zakončení na počet připojení, aktuálně 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). Když tento limit překročíte, functions runtime vytvoří protokolu s následující zprávou: `Host thresholds exceeded: Connections`.

Pravděpodobnost překračuje limit zvýšit, kdy [škálování řadič přidává funkce aplikace instance](functions-scale.md#how-the-consumption-plan-works). Každá instance funkce aplikace může být vyvolání funkce mnohokrát najednou a všechny tyto funkce používají připojení, které budou započítává k 300 limit.

## <a name="use-static-clients"></a>Použít statické klienty

Abyste se vyhnuli, která uchovává více připojení, než je nutné, znovu použít klienta instancí místo vytváření nové s každým vyvolání funkce. Klientů .NET, jako `HttpClient`, `DocumentClient`, a klienti Azure Storage můžete spravovat připojení, když používáte jednu statickou klienta.

Zde jsou některé pokyny při používání specifických pro službu klienta v aplikaci Azure Functions:

- **NECHCETE** vytvořit nového klienta s každé volání funkce.
- **PROVEĎTE** vytvořte jeden statický klienta, které je možné každé volání funkce.
- **Vezměte v úvahu** vytváření jeden statický klienta v sdílených pomocná třída, pokud různé funkce používaly stejnou službu.

## <a name="httpclient-code-example"></a>Příklad kódu HttpClient

Tady je příklad funkce kód, který vytvoří statického `HttpClient`:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

Běžné otázky o .NET `HttpClient` je "By I být uvolnění Moje klienta?" Obecně platí, uvolnění objektů, které implementují `IDisposable` po dokončení jejich používání. Nemáte dispose statického klienta, protože nejsou provádí, ale používat při ukončení funkce. Chcete statické klientovi za provozu po dobu trvání vaší aplikace.

## <a name="documentclient-code-example"></a>Příklad kódu DocumentClient

`DocumentClient` připojí se k instanci databáze Cosmos. V dokumentaci Cosmos DB doporučuje je [po dobu jeho existence vaší aplikace, použijte klienta Azure Cosmos DB singleton](https://docs.microsoft.com/en-us/azure/cosmos-db/performance-tips#sdk-usage). Následující příklad ukazuje jeden vzor učinit ve funkci.

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

## <a name="next-steps"></a>Další postup

Další informace o tom, proč se doporučují statických klientů najdete v tématu [nesprávné konkretizaci antipattern](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Další tipy výkonu Azure Functions najdete v tématu [optimalizace výkonu a spolehlivosti Azure Functions](functions-best-practices.md).