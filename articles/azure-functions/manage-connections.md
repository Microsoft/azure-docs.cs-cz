---
title: Správa připojení v Azure Functions
description: Zjistěte, jak se vyhnout problémy s výkonem ve službě Azure Functions pomocí statického připojení klientů.
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
ms.openlocfilehash: 6c0af8f6f7e1d4aea8880a7af311aaa21f474f7e
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969000"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Správa připojení v Azure Functions

Funkce v aplikaci function app sdílet prostředky a mezi těmito sdílené prostředky jsou připojení &mdash; připojení HTTP, připojení k databázi a připojení ke službám Azure, jako je například úložiště. Pokud mnoho funkcí jsou spuštěny souběžně je možné mít nedostatek dostupných připojení. Tento článek vysvětluje, jak kód vaší funkce, abyste se vyhnuli použití víc připojení než které skutečně potřebují.

## <a name="connections-limit"></a>Omezení počtu připojení

Počet dostupných připojení je omezený částečně proto, že aplikace function app se spouští v [sandboxu služby Azure App Service](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Jedním z omezení, izolovaný prostor ukládá váš kód je [limit počtu připojení, aktuálně 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). Při dosažení tohoto limitu, modul runtime služby functions vytvoří protokol s následující zprávou: `Host thresholds exceeded: Connections`.

Kdy zvýšit pravděpodobnost při překročení tohoto limitu [měřítka řadiče přidána instance aplikace funkce](functions-scale.md#how-the-consumption-plan-works). Každá instance aplikace funkce může být vyvolání funkce v mnoha případech najednou, a všechny tyto funkce používají připojení, které se počítají do 300 limit.

## <a name="use-static-clients"></a>Použití statických klientů

Aby se zabránilo uchovávající víc připojení než je nutné, opakovaně používat instancí klientů, kteří místo vytvoření nové značky s každým vyvolání funkce. Klientů .NET, jako jsou `HttpClient`, `DocumentClient`, a klienty služby Azure Storage můžete spravovat připojení, pokud používáte statické jednoho klienta.

Tady jsou některé zásady dodržovat při použití klienta specifickou pro službu v aplikaci Azure Functions:

- **Ne** vytvořit nového klienta se každé volání funkce.
- **PROVEĎTE** vytvořit jeden, statické klienta, které mohou být využívána každé volání funkce.
- **Vezměte v úvahu** vytvoření jeden statický klienta ve sdílených pomocnou třídu, pokud různé funkce používaly stejnou službu.

## <a name="httpclient-code-example"></a>Příklad kódu HttpClient

Tady je příklad kódu funkce, která vytvoří statickou `HttpClient`:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

Běžné otázky o .NET `HttpClient` je "By měl jsem se připravuje se klient?" Obecně platí, Uvolňujte objekty, které implementují `IDisposable` po dokončení jejich používání. Ale statického klienta není vyřadit, protože není vše nastaveno použití po skončení funkce. Chcete, aby statické klienta na živá po dobu trvání aplikace.

## <a name="documentclient-code-example"></a>Příklad kódu DocumentClient

`DocumentClient` se připojí k instanci databáze Cosmos DB. Dokumentace ke službě Cosmos DB doporučuje vám [používání klienta služby Azure Cosmos DB jednotlivý prvek po dobu životnosti aplikace](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). Následující příklad ukazuje jeden vzor, který to ve funkci.

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

Další informace o tom, proč se doporučují statické klientů, přečtěte si téma [antipattern nesprávného vytváření instancí](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Další tipy pro výkon Azure Functions najdete v části [optimalizujete tím její výkon a spolehlivost služby Azure Functions](functions-best-practices.md).