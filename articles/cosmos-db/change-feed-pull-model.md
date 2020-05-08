---
title: Model vyžádání změny kanálu změn
description: Naučte se používat model Pull pro změnu kanálu Azure Cosmos DB ke čtení kanálu změn a rozdílů mezi modelem Pull a procesorem Change feed.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 2854e3d92462ced3958afd1cf1e7e99d7e9892f6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984678"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Změna modelu vyžádání obsahu kanálu v Azure Cosmos DB

Pomocí modelu vyžádání změny kanálu můžete využívat Azure Cosmos DB změnového kanálu vlastním tempem. Jak už můžete s [procesorem Change feed](change-feed-processor.md)pracovat, můžete použít model vyžádání změny kanálu, který paralelizovat zpracování změn napříč několika spotřebiteli kanálu změn.

> [!NOTE]
> Model Pull pro změnu kanálu je momentálně ve [verzi Preview v sadě Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) . Verze Preview není ještě dostupná pro jiné verze sady SDK.

## <a name="consuming-an-entire-containers-changes"></a>Spotřebovávání změn celého kontejneru

Můžete vytvořit `FeedIterator` pro zpracování kanálu změn pomocí modelu Pull. Při počátečním vytvoření `FeedIterator`můžete zadat volitelný `StartTime` v rámci. `ChangeFeedRequestOptions` Není-li parametr Left zadán `StartTime` , bude aktuálním časem.

`FeedIterator` Obsahuje dva typy. Kromě níže uvedených příkladů, které vracejí objekty entit, můžete také získat odpověď s `Stream` podporou. Datové proudy umožňují čtení dat bez jejich prvního deserializace a jejich ukládání do klientských prostředků.

Zde je příklad pro získání `FeedIterator` , který vrací objekty entity, v tomto případě `User` objekt:

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

Zde je příklad získání a `FeedIterator` , který vrátí: `Stream`

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

`FeedIterator`Pomocí můžete snadno zpracovat kanál změn celého kontejneru vaším vlastním tempem. Tady je příklad:

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator(new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>Spotřebovávání změn klíče oddílu

V některých případech můžete chtít zpracovat pouze změny určitého klíče oddílu. Můžete získat `FeedIterator` konkrétní klíč oddílu a zpracovat změny stejným způsobem jako u celého kontejneru:

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator(new PartitionKey("myPartitionKeyValueToRead"), new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Použití FeedRange pro paralelismuing

V [procesoru Change feed](change-feed-processor.md)je práce automaticky rozložena mezi více uživatelů. V modelu vyžádání změny kanálu můžete použít `FeedRange` k paralelizovat zpracování kanálu změn. `FeedRange` Představuje rozsah hodnot klíče oddílu.

Tady je příklad, který ukazuje, jak získat seznam rozsahů pro váš kontejner:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Když získáte seznam FeedRanges pro váš kontejner, získáte jeden `FeedRange` pro každý [fyzický oddíl](partition-data.md#physical-partitions).

`FeedRange`Pomocí můžete vytvořit `FeedIterator` a paralelizovat zpracování kanálu změn napříč několika počítači nebo vlákny. Na rozdíl od předchozího příkladu, který ukázal, jak získat jeden `FeedIterator` pro celý kontejner, můžete použít `FeedRange` k získání více FeedIterators, které mohou zpracovávat kanál změn paralelně.

V případě, že chcete používat FeedRanges, musíte mít proces Orchestrator, který získá FeedRanges a distribuuje je do těchto počítačů. Tato distribuce může být:

* Použití `FeedRange.ToJsonString` a distribuce této řetězcové hodnoty. Příjemci můžou tuto hodnotu použít s`FeedRange.FromJsonString`
* Pokud je distribuce vnitroprocesově, předejte odkaz na `FeedRange` objekt.

Tady je ukázka, která ukazuje, jak číst ze začátku kanálu změn kontejneru pomocí dvou hypotetických oddělených počítačů, které jsou v paralelním čtení:

Počítač 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<Person>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

Počítač 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ranges[1], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorB.HasMoreResults)
{
   FeedResponse<User> users = await iteratorB.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="saving-continuation-tokens"></a>Ukládání tokenů pokračování

Polohu své aplikace `FeedIterator` můžete uložit vytvořením tokenu pro pokračování. Token pokračování je řetězcová hodnota, která udržuje přehled o naposledy zpracovaných změnách FeedIterator. To umožňuje `FeedIterator` pokračovat v tomto okamžiku později. Následující kód bude čten prostřednictvím kanálu změn od vytvoření kontejneru. Až nebudou k dispozici žádné další změny, zachová se token pro pokračování, aby mohla být později obnovena možnost změnit spotřebu kanálů.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = orders.ContinuationToken;

   foreach (User user in Users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

## <a name="comparing-with-change-feed-processor"></a>Porovnání s procesorem Change feed

V mnoha scénářích může být kanál změn zpracován pomocí [procesoru změn kanálu](change-feed-processor.md) nebo modelu Pull. Tokeny pro pokračování modelu vyžádání a kontejner zapůjčení změnového kanálu jsou "záložky" pro poslední zpracovávanou položku (nebo dávku položek) v kanálu změn.
Nemůžete však převést tokeny pokračování na kontejner zapůjčení (nebo naopak).

Měli byste zvážit použití modelu vyžádání obsahu v těchto scénářích:

- Chcete provést jednorázovou čtení stávajících dat v kanálu změn
- Chcete pouze číst změny z konkrétního klíče oddílu
- Nechcete využívat model nabízených oznámení a chcete používat kanál změn vlastním tempem.

Tady jsou některé klíčové rozdíly mezi procesorem Change feed a modelem Pull:

|  | Procesor kanálu změn| Model vyžádání |
| --- | --- | --- |
| Udržování přehledu o aktuálním bodu ve zpracování kanálu změn | Zapůjčení (uložené v kontejneru Azure Cosmos DB) | Token pro pokračování (uložený v paměti nebo ručně trvalý) |
| Možnost Přehrát minulé změny | Ano, s modelem push | Ano, s modelem pull|
| Cyklické dotazování pro budoucí změny | Automaticky kontroluje změny na základě zadaného uživatele.`WithPollInterval` | Ruční |
| Zpracovat změny z celého kontejneru | Ano a automaticky paralelně napříč několika vlákny nebo počítači, které jsou náročné ze stejného kontejneru| Ano a ručně paralelně s využitím FeedTokens |
| Zpracovat změny jenom z jednoho klíče oddílu | Nepodporuje se | Ano|
| Úroveň podpory | Obecná dostupnost | Preview |

## <a name="next-steps"></a>Další kroky

* [Přehled kanálu změn](change-feed.md)
* [Použití procesoru Change feed](change-feed-processor.md)
* [Aktivace Azure Functions](change-feed-functions.md)