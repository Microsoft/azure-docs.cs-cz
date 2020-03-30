---
title: Pokyny pro omezované požadavky
description: Naučte se seskupit, rozložit, stránkovat a dotazovat paralelně, abyste zabránili omezení požadavků pomocí Azure Resource Graph.
ms.date: 12/02/2019
ms.topic: conceptual
ms.openlocfilehash: fbd4bec715b187bcc643fe32b8452b0e062e7713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259848"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Pokyny pro omezené požadavky v Azure Resource Graph

Při vytváření programové a časté používání dat Azure Resource Graph, je třeba zvážit, jak omezení ovlivňuje výsledky dotazů. Změna požadovaného způsobu, jakým jsou data požadována, vám a vaší organizaci může pomoci vyhnout se omezení a udržovat tok včasných dat o vašich prostředcích Azure.

Tento článek popisuje čtyři oblasti a vzory související s vytvářením dotazů v Azure Resource Graph:

- Vysvětlení hlaviček omezování
- Seskupování dotazů
- Rozložení dotazů
- Dopad stránkování

## <a name="understand-throttling-headers"></a>Vysvětlení hlaviček omezování

Azure Resource Graph přiděluje číslo kvóty pro každého uživatele na základě časového okna. Například uživatel může odeslat maximálně 15 dotazů v rámci každého 5 sekund okna bez omezení. Hodnota kvóty je určena mnoha faktory a může se změnit.

V každé odpovědi na dotaz Azure Resource Graph přidá dvě omezení záhlaví:

- `x-ms-user-quota-remaining`(int): Zbývající kvóta prostředků pro uživatele. Tato hodnota mapuje počet dotazů.
- `x-ms-user-quota-resets-after`(hh:mm:ss): Doba trvání do obnovení spotřeby kvóty uživatele.

Chcete-li ilustrovat, jak záhlaví fungují, podívejme se na odpověď `x-ms-user-quota-remaining: 10` `x-ms-user-quota-resets-after: 00:00:03`na dotaz, která má záhlaví a hodnoty a .

- Během následujících 3 sekund může být odesláno maximálně 10 dotazů bez omezení.
- Za 3 sekundy `x-ms-user-quota-remaining` se `x-ms-user-quota-resets-after` hodnoty a `15` `00:00:05` budou resetovat na a respektive.

Chcete-li zobrazit příklad použití záhlaví _backoff_ na požadavky na dotazy, naleznete v ukázce v [dotazu v paralelní](#query-in-parallel).

## <a name="grouping-queries"></a>Seskupování dotazů

Seskupení dotazů podle předplatného, skupiny prostředků nebo jednotlivých prostředků je efektivnější než paralelní dotazy. Náklady na kvótu větší dotaz je často nižší než náklady na kvótu mnoha malých a cílených dotazů. Velikost skupiny se doporučuje být menší než _300_.

- Příklad špatně optimalizovaného přístupu

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Příklad #1 optimalizovaného přístupu k seskupování

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Příklad #2 optimalizovaného přístupu seskupení pro získání více prostředků v jednom dotazu

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Rozložení dotazů

Vzhledem k tomu, jak je vynuceno omezení, doporučujeme dotazy, které mají být rozloženy. To znamená, že místo odesílání 60 dotazů současně, rozložené dotazy do čtyř 5 sekund okna:

- Plán dotazů, který není rozložen,

  | Počet dotazů         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Časový interval (s) | 0-5 | 5-10 | 10-15 | 15-20 |

- Plán rozložených dotazů

  | Počet dotazů         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Časový interval (s) | 0-5 | 5-10 | 10-15 | 15-20 |

Níže je uveden příklad respektování omezení záhlaví při dotazování Azure Resource Graph:

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

### <a name="query-in-parallel"></a>Dotaz paralelně

I když seskupení se doporučuje přes paralelizace, existují časy, kdy dotazy nelze snadno seskupit. V těchto případech můžete chtít dotaz Azure Resource Graph odesláním více dotazů paralelním způsobem. Níže je uveden příklad, jak _backoff_ na základě omezení záhlaví v těchto scénářích:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

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

Vzhledem k tomu, že Azure Resource Graph vrátí maximálně 1000 položek v jedné odpovědi na dotaz, budete muset [stránkovat](./work-with-data.md#paging-results) vaše dotazy získat úplnou datovou sadu, kterou hledáte. Někteří klienti Azure Resource Graph však zpracovávají stránkování jinak než ostatní.

- C# SDK

  Při použití ResourceGraph SDK, je třeba zpracovat stránkování předáním přeskočit token se vrací z předchozí odpovědi na dotaz na další stránkovaný dotaz. Tento návrh znamená, že musíte shromažďovat výsledky ze všech stránkovaných hovorů a na konci je kombinovat dohromady. V tomto případě každý stránkovaný dotaz, který odešlete, získá jednu kvótu dotazu:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
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

- Azure CLI / Azure PowerShell

  Při použití Azure CLI nebo Azure PowerShell, dotazy na Azure Resource Graph jsou automaticky stránkované načíst maximálně 5000 položek. Výsledky dotazu vrátí kombinovaný seznam položek ze všech stránkovaných volání. V tomto případě v závislosti na počtu položek ve výsledku dotazu může jeden stránkovaný dotaz spotřebovat více než jednu kvótu dotazu. Například v níže uvedeném příkladu může jedno spuštění dotazu spotřebovat až pět kvót dotazu:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Pořád jsi škrtil?

Pokud jste stále škrtil po výkonu výše uvedených [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)doporučení, obraťte se na tým na .

Uveďte tyto podrobnosti:

- Váš konkrétní případ použití a obchodní ovladač potřebuje pro vyšší limit omezení.
- K kolika prostředkům máte přístup? Kolik z nich jsou vráceny z jednoho dotazu?
- Jaké typy zdrojů vás zajímají?
- Jaký je váš vzor dotazu? X dotazů za y sekundy atd.

## <a name="next-steps"></a>Další kroky

- Podívejte se na jazyk, který se používá v [dotazech Starter](../samples/starter.md).
- Zobrazení pokročilých použití v [rozšířených dotazech](../samples/advanced.md).
- Přečtěte si další informace o tom, jak [prozkoumat zdroje](explore-resources.md).