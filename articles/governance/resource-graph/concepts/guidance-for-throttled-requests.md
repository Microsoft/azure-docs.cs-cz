---
title: Pokyny pro omezované požadavky
description: Naučte se vytvářet lepší dotazy, abyste se vyhnuli omezení požadavků do grafu prostředků Azure.
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: 651a5daa9e7e19a5dc157ba0cfa17da2c8abe3db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038330"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Doprovodné materiály k omezením požadavků v grafu prostředků Azure

Při vytváření programového a častého využívání dat grafu prostředků Azure by se mělo zvážit, jak omezování ovlivňuje výsledky dotazů. Změnou způsobu, jakým se vyžadují data, může vám a vaší organizaci zabránit omezení a udržování toku včasných dat o vašich prostředcích Azure.

Tento článek se věnuje čtyřem oblastem a vzorům, které souvisejí s vytvářením dotazů v Azure Resource graphu:

- Vysvětlení hlaviček omezování
- Dávkové dotazy
- Rozložit dotazy
- Dopad stránkování

## <a name="understand-throttling-headers"></a>Vysvětlení hlaviček omezování

Azure Resource Graph přiděluje číslo kvóty pro každého uživatele na základě časového okna. Uživatel může například odeslat maximálně 15 dotazů v rámci každého 5 sekundového okna bez omezení. Hodnota kvóty je určena mnoha faktory a může se změnit.

V každé odpovědi na dotaz přidává Azure Resource Graph dvě hlavičky omezení:

- `x-ms-user-quota-remaining` (int): zbývající kvóta prostředků pro uživatele. Tato hodnota se mapuje na počet dotazů.
- `x-ms-user-quota-resets-after` (hh: mm: SS): doba trvání, dokud nebude obnovena spotřeba kvóty uživatele.

Pro ilustraci, jak fungují hlavičky, se podívejme na odpověď na dotaz, která má hlavičku a hodnoty `x-ms-user-quota-remaining: 10` a `x-ms-user-quota-resets-after: 00:00:03`.

- Během příštích 3 sekund se dá odeslat maximálně 10 dotazů bez omezení.
- Za 3 sekundy se hodnoty `x-ms-user-quota-remaining` a `x-ms-user-quota-resets-after` resetují na `15` a `00:00:05`.

Pokud chcete zobrazit příklad použití hlaviček k _omezení rychlostií_ dotazů na dotazy, přečtěte si ukázku v [dotazu paralelně](#query-in-parallel).

## <a name="batching-queries"></a>Dávkové dotazy

Dávkování dotazů podle předplatného, skupiny prostředků nebo jednotlivého prostředku je efektivnější než dotazy virtuálního. Náklady na kvótu většího dotazu jsou často nižší než náklady na kvótu pro velký počet malých a cílových dotazů. Velikost dávky se doporučuje být menší než _300_.

- Příklad nedostatečně optimalizovaného přístupu

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

- Příklad #1 optimalizovaného přístupu k dávkám

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
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Příklad #2 optimalizovaného přístupu k dávkám

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
          query: $"Resources | where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Rozložit dotazy

Kvůli způsobu, jakým se vynutilo omezování, doporučujeme dotazy, které se mají rozložit. To znamená, že místo odesílání dotazů 60 se budou tyto dotazy rozložit na čtyři 5 – sekundová okna:

- Plán dotazu bez rovnoměrného rozřazení

  | Počet dotazů         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Časový interval (sekundy) | 0-5 | 5-10 | 10-15 | 15-20 |

- Plán rozloženého dotazu

  | Počet dotazů         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Časový interval (sekundy) | 0-5 | 5-10 | 10-15 | 15-20 |

Níže je uveden příklad respektování hlaviček omezení při dotazování na graf prostředků Azure:

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

I když se dávkové zpracování doporučuje po paralelním použití, existují časy, ve kterých se dotazy nedají snadno dávkovat. V těchto případech můžete chtít dotazovat se na graf prostředků Azure tak, že paralelním způsobem odešlete více dotazů. Níže je uveden příklad, jak _omezení rychlosti_ na základě hlaviček omezení v takových scénářích:

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

Vzhledem k tomu, že Azure Resource Graph vrací maximálně 1000 záznamů v jediné odpovědi na dotaz, možná budete muset vytvořit [stránkování](./work-with-data.md#paging-results) dotazů a získat tak úplnou datovou sadu, kterou hledáte. Někteří klienti Azure Resource graphu ale nezpracovávají stránkování jinak než jiné.

- C# SDK

  Při použití sady ResourceGraph SDK je nutné zpracovávat stránkování předáním tokenu Skip vráceného z předchozí odpovědi na dotaz na další stránkovaný dotaz. Tento návrh znamená, že potřebujete shromáždit výsledky ze všech stránkovaných volání a spojit je dohromady na konci. V takovém případě každý stránkovaný dotaz, který odešlete, provede jednu kvótu dotazu:

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

- Azure CLI/Azure PowerShell

  Při použití rozhraní příkazového řádku Azure CLI nebo Azure PowerShell jsou dotazy do Azure Resource graphu automaticky zastránkováním, aby se načetly maximálně 5000 položek. Výsledky dotazu vrátí kombinovaný seznam záznamů ze všech stránkovaných volání. V takovém případě může jeden stránkovaný dotaz využívat více než jednu kvótu dotazu v závislosti na počtu položek ve výsledku dotazu. Například v příkladu níže může jedno spuštění dotazu spotřebovat až pět kvót dotazu:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Pořád se omezuje?

Pokud se vám po uplatnění výše uvedených doporučení omezuje omezení, obraťte se na tým na [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com).

Zadejte tyto podrobnosti:

- Vaše specifické požadavky na použití a obchodní ovladače se vyžadují pro vyšší limit omezení.
- K kolika prostředkům máte přístup? Kolik z je vráceno jedním dotazem?
- Jaké typy prostředků máte zajímat?
- Co je to váš vzor dotazu? Počet dotazů X za sekundu atd.

## <a name="next-steps"></a>Další kroky

- Podívejte se na jazyk používaný v [počátečních dotazech](../samples/starter.md).
- Viz rozšířená použití v [rozšířených dotazech](../samples/advanced.md).
- Přečtěte si další informace o tom, jak [prozkoumat prostředky](explore-resources.md).