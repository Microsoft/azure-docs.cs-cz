---
title: Pokyny pro omezené požadavky
description: Zjistěte, jak vytvořit lepší dotazy, aby se zabránilo požadavky do grafu prostředků Azure z omezené.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c644d230846d9c644c3845348431eef36c8279c8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276896"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Pokyny pro omezené požadavky v grafu prostředků Azure

Při vytváření, programování a časté používání dat grafu prostředků Azure, posouzení třeba jak omezování dopad výsledky dotazů. Změna dat způsob, jak je požadováno, vám může pomoct a vaše organizace vyhnout omezené a spravovat tok včas dat o vašich prostředků Azure.

Tento článek popisuje čtyři oblasti a vzory související s vytváření dotazů v grafu prostředků Azure:

- Principy omezení záhlaví
- Dávkové zpracování dotazů
- Odstupňování dotazy
- Dopad stránkování

## <a name="understand-throttling-headers"></a>Principy omezení záhlaví

Azure Graph prostředků přiděluje číslo kvóty pro každého uživatele, založené na časový interval. Například můžete uživateli odeslat maximálně 15 dotazů okna každých 5 sekund bez omezení. Hodnoty kvóty na závisí celá řada faktorů a může se změnit.

V každé odpovědi na dotaz Azure Graph prostředků přidá dvě omezení záhlaví:

- `x-ms-user-quota-remaining` (int): Zbývající kvóta zdrojů pro uživatele. Tato hodnota se mapuje na počet dotazů.
- `x-ms-user-quota-resets-after` (hh:mm:ss): Doba trvání, dokud se resetuje využití kvóty uživatele.

Pro ilustraci, jak fungují záhlaví, Podívejme se na odpověď na dotaz, který má hlavičky a hodnoty `x-ms-user-quota-remaining: 10` a `x-ms-user-quota-resets-after: 00:00:03`.

- V rámci další 3 sekundy může být maximálně 10 dotazy předložena bez omezení.
- V 3 sekundy, hodnoty `x-ms-user-quota-remaining` a `x-ms-user-quota-resets-after` se resetuje na `15` a `00:00:05` v uvedeném pořadí.

Chcete-li zobrazit příklad použití hlavičky pro _omezení rychlosti_ na požadavků na dotazy, najdete v ukázce v [paralelní dotazování](#query-in-parallel).

## <a name="batching-queries"></a>Dávkové zpracování dotazů

Dávkování dotazy podle předplatné, skupinu prostředků nebo samostatný prostředek je efektivnější než paralelní provádění dotazů. Kvóta náklady na větší dotazu jsou často nižší než náklady kvóty mnoho dotazů na malé a cílové. Velikost dávky, doporučuje se mít méně než _300_.

- Ukázka špatně optimalizované přístupu

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Příklad #1 optimalizované dávkování přístupu

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Příklad #2 optimalizované dávkování přístupu

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Odstupňování dotazy

Vzhledem ke způsobu omezení se vynucují, doporučujeme dotazy v zájmu. To znamená místo abyste odesílali 60 dotazy ve stejnou dobu, odstupňování dotazy do čtyř 5 sekundách windows:

- Plán dotazu bez rozloženo

  | Počet dotazů         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Časový Interval (sekundy) | 0-5 | 5-10 | 10-15 | 15-20 |

- Rozloženo plánu dotazu

  | Počet dotazů         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Časový Interval (sekundy) | 0-5 | 5-10 | 10-15 | 15-20 |

Níže je příklad při dotazování grafu prostředků Azure, ale současně zachovává omezení hlavičky:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Paralelní dotazování

I když dávkování se doporučuje přes paralelizace, existují situace, kdy nelze snadno sjednotit dotazy. V těchto případech můžete chtít odesláním více dotazů paralelní způsobem dotazování grafu prostředků Azure. Tady je příklad toho, jak _omezení rychlosti_ podle omezení záhlaví v těchto scénářích:

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Stránkování

Protože Azure Graph prostředků vrátí maximálně 1 000 položek v jednom dotazu odpovědi, možná budete muset [stránkování](./work-with-data.md#paging-results) dotazy získat úplnou datovou sadu, kterou hledáte. Ale někteří klienti Azure Graph prostředků zpracování stránkování jinak než ostatní.

- C# SDK

  Při používání sady SDK ResourceGraph potřebujete zpracovávat stránkování předáním vynechávaný token se vrací z předchozí odpověď na dotaz na další stránkovaných dotazů. Tento návrh znamená, že je potřeba shromáždit výsledky ze všech volání stránkované a kombinovat společně na konci. V takovém případě jednotlivých stránkovaných dotazů, které odesíláte přijímá jeden dotaz kvóta:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Rozhraní příkazového řádku Azure / Azure Powershellu

  Při použití Azure CLI nebo Azure Powershellu, jsou k načtení maximálně 5 000 položek automaticky stránkovaných dotazů do grafu prostředků Azure. Výsledky dotazu vrátit kombinovaný seznam položek ze všech stránkované volání. V závislosti na počet položek ve výsledku dotazu. v takovém případě pomocí jediného dotazu stránkované může využívat více než jeden dotaz kvóty. Například v následujícím příkladu najednou dotazu může využívat až pět dotazů kvóta:

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>Získat i nadále omezený?

Pokud jste omezovaní po práv vyplývajících z výše uvedených doporučení, kontaktujte tým na adrese [ resourcegraphsupport@microsoft.com ](mailto:resourcegraphsupport@microsoft.com).

Uveďte následující údaje:

- Specifický ovladač případu použití a obchodní potřeby vyšší limit omezení.
- Kolik prostředků máte přístup k? Kolik jsou vráceny z jednoho dotazu?
- Jaké jsou typy prostředků máte zájem?
- Jaký je váš vzorec dotazu? X dotazy za sekundu Y atd.

## <a name="next-steps"></a>Další postup

- Zobrazit jazyk v aplikaci [Starter dotazy](../samples/starter.md).
- Viz advanced používá v [upřesňujících dotazů](../samples/advanced.md).
- Zjistěte, jak [materiály](explore-resources.md).