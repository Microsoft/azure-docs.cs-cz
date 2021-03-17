---
title: Model vyžádání kanálu změn
description: Naučte se používat model Pull pro změnu kanálu Azure Cosmos DB ke čtení kanálu změn a rozdílů mezi modelem Pull a procesorem Change feed.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 9279dddc92629b17a2a73f3a41fe261d322d677e
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463676"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Změna modelu vyžádání obsahu kanálu v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Pomocí modelu vyžádání změny kanálu můžete využívat Azure Cosmos DB změnového kanálu vlastním tempem. Jak už můžete s [procesorem Change feed](change-feed-processor.md)pracovat, můžete použít model vyžádání změny kanálu, který paralelizovat zpracování změn napříč několika spotřebiteli kanálu změn.

> [!NOTE]
> Model Pull pro změnu kanálu je momentálně ve [verzi Preview v sadě Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.17.0-preview) . Verze Preview není ještě dostupná pro jiné verze sady SDK.

## <a name="comparing-with-change-feed-processor"></a>Porovnání s procesorem Change feed

V mnoha scénářích může být kanál změn zpracován pomocí [procesoru změn kanálu](change-feed-processor.md) nebo modelu Pull. Tokeny pro pokračování modelu vyžádání a kontejner zapůjčení změnového kanálu jsou "záložky" pro poslední zpracovávanou položku (nebo dávku položek) v kanálu změn.

Nemůžete však převést tokeny pokračování na kontejner zapůjčení (nebo naopak).

> [!NOTE]
> Ve většině případů, kdy potřebujete číst z kanálu změn, nejjednodušší možností je použít [procesor Change feed](change-feed-processor.md).

Měli byste zvážit použití modelu vyžádání obsahu v těchto scénářích:

- Načte změny z konkrétního klíče oddílu.
- Určení tempa, ve kterém klient přijímá změny ke zpracování
- Provedení jednorázového čtení stávajících dat v kanálu změn (například k migraci dat)

Tady jsou některé klíčové rozdíly mezi procesorem Change feed a modelem Pull:

|Funkce  | Procesor kanálu změn| Model vyžádání |
| --- | --- | --- |
| Udržování přehledu o aktuálním bodu ve zpracování kanálu změn | Zapůjčení (uložené v kontejneru Azure Cosmos DB) | Token pro pokračování (uložený v paměti nebo ručně trvalý) |
| Možnost Přehrát minulé změny | Ano, s modelem push | Ano, s modelem pull|
| Cyklické dotazování pro budoucí změny | Automaticky kontroluje změny na základě zadaného uživatele. `WithPollInterval` | Ruční |
| Chování při nežádných nových změnách | Automaticky počkat `WithPollInterval` a znovu ověřit | Musí zachytit výjimku a ručně znovu ověřit |
| Zpracovat změny z celého kontejneru | Ano a automaticky paralelně napříč několika vlákny nebo počítači, které jsou náročné ze stejného kontejneru| Ano a ručně paralelně s využitím FeedTokens |
| Zpracovat změny jenom z jednoho klíče oddílu | Nepodporováno | Yes|
| Úroveň podpory | Obecná dostupnost | Preview |

> [!NOTE]
> Na rozdíl od při čtení pomocí procesoru změny kanálu musíte explicitně zpracovat případy, kdy neexistují žádné nové změny. 

## <a name="consuming-an-entire-containers-changes"></a>Spotřebovávání změn celého kontejneru

Můžete vytvořit `FeedIterator` pro zpracování kanálu změn pomocí modelu Pull. Při počátečním vytvoření `FeedIterator` musí být zadána požadovaná `ChangeFeedStartFrom` hodnota, která se skládá z počáteční pozice pro čtení změn a také z požadovaných `FeedRange` . `FeedRange`Je rozsah hodnot klíče oddílu a určuje položky, které budou čteny z kanálu změn pomocí konkrétního `FeedIterator` .

Volitelně můžete zadat, `ChangeFeedRequestOptions` Chcete-li nastavit `PageSizeHint` . `PageSizeHint`Je maximální počet položek, které budou vráceny na jednu stránku.

`FeedIterator`Obsahuje dva typy. Kromě níže uvedených příkladů, které vracejí objekty entit, můžete také získat odpověď s `Stream` podporou. Datové proudy umožňují čtení dat bez jejich prvního deserializace a jejich ukládání do klientských prostředků.

Zde je příklad pro získání `FeedIterator` , který vrací objekty entity, v tomto případě `User` objekt:

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

Zde je příklad získání a `FeedIterator` , který vrátí `Stream` :

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

Pokud neposkytnete do `FeedRange` `FeedIterator` , můžete zpracovat celý kanál změn kontejneru vlastním tempem. Tady je příklad, který začíná číst všechny změny počínaje aktuálním časem:

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now(), ChangeFeedMode.Incremental);

while (iteratorForTheEntireContainer.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

Vzhledem k tomu, že kanál změn je efektivně nekonečný seznam položek zahrnujících všechny budoucí zápisy a aktualizace, hodnota `HasMoreResults` je vždycky true. Když se pokusíte přečíst kanál změn a nejsou k dispozici žádné nové změny, obdržíte výjimku. Ve výše uvedeném příkladu je výjimka zpracována čekáním na 5 sekund, než se znovu zkontrolují změny.

## <a name="consuming-a-partition-keys-changes"></a>Spotřebovávání změn klíče oddílu

V některých případech můžete chtít zpracovat pouze změny určitého klíče oddílu. Můžete získat `FeedIterator` konkrétní klíč oddílu a zpracovat změny stejným způsobem jako u celého kontejneru.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(
    ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue")), ChangeFeedMode.Incremental));

while (iteratorForThePartitionKey.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Použití FeedRange pro paralelismuing

V [procesoru Change feed](change-feed-processor.md)je práce automaticky rozložena mezi více uživatelů. V modelu vyžádání změny kanálu můžete použít `FeedRange` k paralelizovat zpracování kanálu změn. `FeedRange`Představuje rozsah hodnot klíče oddílu.

Tady je příklad, který ukazuje, jak získat seznam rozsahů pro váš kontejner:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Když získáte seznam FeedRanges pro váš kontejner, získáte jeden pro `FeedRange` každý [fyzický oddíl](partitioning-overview.md#physical-partitions).

Pomocí `FeedRange` můžete vytvořit a `FeedIterator` paralelizovat zpracování kanálu změn napříč několika počítači nebo vlákny. Na rozdíl od předchozího příkladu, který ukázal, jak získat `FeedIterator` pro celý kontejner nebo jeden klíč oddílu, můžete použít FeedRanges k získání více FeedIterators, které mohou zpracovávat kanál změn paralelně.

V případě, že chcete používat FeedRanges, musíte mít proces Orchestrator, který získá FeedRanges a distribuuje je do těchto počítačů. Tato distribuce může být:

* Použití `FeedRange.ToJsonString` a distribuce této řetězcové hodnoty. Příjemci můžou tuto hodnotu použít s `FeedRange.FromJsonString`
* Pokud je distribuce vnitroprocesově, předejte `FeedRange` odkaz na objekt.

Tady je ukázka, která ukazuje, jak číst ze začátku kanálu změn kontejneru pomocí dvou hypotetických oddělených počítačů, které jsou v paralelním čtení:

Počítač 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]), ChangeFeedMode.Incremental);
while (iteratorA.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

Počítač 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]), ChangeFeedMode.Incremental);
while (iteratorB.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

## <a name="saving-continuation-tokens"></a>Ukládání tokenů pokračování

Polohu své aplikace můžete uložit `FeedIterator` vytvořením tokenu pro pokračování. Token pokračování je řetězcová hodnota, která udržuje přehled o naposledy zpracovaných změnách FeedIterator. To umožňuje `FeedIterator` pokračovat v tomto okamžiku později. Následující kód bude čten prostřednictvím kanálu změn od vytvoření kontejneru. Až nebudou k dispozici žádné další změny, zachová se token pro pokračování, aby mohla být později obnovena možnost změnit spotřebu kanálů.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);

string continuation = null;

while (iterator.HasMoreResults)
{
   try { 
        FeedResponse<User> users = await iterator.ReadNextAsync();
        continuation = users.ContinuationToken;

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
   }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }   
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation), ChangeFeedMode.Incremental);
```

Dokud kontejner Cosmos stále existuje, token pro pokračování FeedIterator nikdy nevyprší.

## <a name="next-steps"></a>Další kroky

* [Přehled kanálu změn](change-feed.md)
* [Použití procesoru Change feed](change-feed-processor.md)
* [Aktivace Azure Functions](change-feed-functions.md)
