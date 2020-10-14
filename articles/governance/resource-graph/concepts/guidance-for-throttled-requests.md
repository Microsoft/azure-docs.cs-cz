---
title: Pokyny pro omezované požadavky
description: Naučte se paralelně seskupovat, rozložit, stránkování a dotazovat, abyste se vyhnuli požadavkům, které Azure Resource Graph omezuje.
ms.date: 10/14/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a8ba991d13b9be221e67f2ff1e393fb01f8a2d4
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056170"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Doprovodné materiály k omezením požadavků v grafu prostředků Azure

Při vytváření programového a častého využívání dat grafu prostředků Azure by se mělo zvážit, jak omezování ovlivňuje výsledky dotazů. Změnou způsobu, jakým se vyžadují data, může vám a vaší organizaci zabránit omezení a udržování toku včasných dat o vašich prostředcích Azure.

Tento článek se věnuje čtyřem oblastem a vzorům, které souvisejí s vytvářením dotazů v Azure Resource graphu:

- Vysvětlení hlaviček omezování
- Seskupování dotazů
- Rozložení dotazů
- Dopad stránkování

## <a name="understand-throttling-headers"></a>Vysvětlení hlaviček omezování

Graf prostředků Azure přiděluje číslo kvóty pro každého uživatele na základě časového okna. Uživatel může například odeslat maximálně 15 dotazů v rámci každého 5 sekundového okna bez omezení. Hodnota kvóty je určena mnoha faktory a může se změnit.

V každé odpovědi na dotaz přidává Azure Resource Graph dvě hlavičky omezení:

- `x-ms-user-quota-remaining` (int): zbývající kvóta prostředků pro uživatele. Tato hodnota se mapuje na počet dotazů.
- `x-ms-user-quota-resets-after` (hh: mm: SS): časový interval, po jehož uplynutí se neobnoví spotřeba kvóty uživatele.

Pokud má objekt zabezpečení přístup k více než 5000 předplatným v rámci [oboru dotazu](./query-language.md#query-scope)skupiny pro správu nebo tenanta, je odpověď omezena na prvních 5000 předplatných a `x-ms-tenant-subscription-limit-hit` vrátí se záhlaví `true` jako.

Pro ilustraci, jak fungují záhlaví, se podívejme na odpověď na dotaz, která má hlavičku a hodnoty `x-ms-user-quota-remaining: 10` a `x-ms-user-quota-resets-after: 00:00:03` .

- Během příštích 3 sekund se dá odeslat maximálně 10 dotazů bez omezení.
- Za 3 sekundy se hodnoty `x-ms-user-quota-remaining` a `x-ms-user-quota-resets-after` obnoví do `15` a v `00:00:05` uvedeném pořadí.

Pokud chcete zobrazit příklad použití hlaviček k _omezení rychlostií_ dotazů na dotazy, přečtěte si ukázku v [dotazu paralelně](#query-in-parallel).

## <a name="grouping-queries"></a>Seskupování dotazů

Seskupování dotazů podle předplatného, skupiny prostředků nebo jednotlivého prostředku je efektivnější než dotazy virtuálního. Náklady na kvótu většího dotazu jsou často nižší než náklady na kvótu pro velký počet malých a cílových dotazů. Velikost skupiny se doporučuje být menší než _300_.

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

- Příklad #1 přístupu optimalizovaného seskupení

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

- Příklad #2 přístupu optimalizovaného seskupení pro získání více prostředků v jednom dotazu

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

Kvůli způsobu, jakým se vynutilo omezování, doporučujeme dotazy, které se mají rozložit. To znamená, že místo odesílání dotazů 60 se budou tyto dotazy rozložit na 4 5 – sekundová okna:

- Plán dotazu bez rovnoměrného rozřazení

  | Počet dotazů         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Časový interval (sekundy) | 0-5 | 5-10 | 10-15 | 15-20 |

- Plán rozloženého dotazu

  | Počet dotazů         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Časový interval (sekundy) | 0-5 | 5-10 | 10-15 | 15-20 |

Tady je příklad respektování hlaviček omezení při dotazování na graf prostředků Azure:

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

I když se seskupování doporučuje po paralelním použití, existují časy, ve kterých se dotazy nedají snadno seskupit. V těchto případech můžete chtít dotazovat se na graf prostředků Azure tak, že paralelním způsobem odešlete více dotazů. Tady je příklad toho, jak se _omezení rychlosti_ na základě hlaviček omezování v takových scénářích:

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

  Při použití rozhraní příkazového řádku Azure CLI nebo Azure PowerShell jsou dotazy do Azure Resource graphu automaticky zastránkováním, aby se načetly maximálně 5000 položek. Výsledky dotazu vrátí kombinovaný seznam záznamů ze všech stránkovaných volání. V takovém případě může jeden stránkovaný dotaz využívat více než jednu kvótu dotazu v závislosti na počtu položek ve výsledku dotazu. Například v následujících příkladech může jeden běh dotazu spotřebovat až pět kvót dotazu:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Pořád se omezuje?

Pokud se vám po uplatnění výše uvedených doporučení omezuje omezení, obraťte se na tým na adrese [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com) .

Zadejte tyto podrobnosti:

- Vaše specifické požadavky na použití a obchodní ovladače se vyžadují pro vyšší limit omezení.
- K kolika prostředkům máte přístup? Kolik z je vráceno jedním dotazem?
- Jaké typy prostředků máte zajímat?
- Co je to váš vzor dotazu? Počet dotazů X za sekundu a tak dále.

## <a name="next-steps"></a>Další kroky

- Podívejte se na jazyk používaný v [počátečních dotazech](../samples/starter.md).
- Viz rozšířená použití v [rozšířených dotazech](../samples/advanced.md).
- Přečtěte si další informace o tom, jak [prozkoumat prostředky](explore-resources.md).