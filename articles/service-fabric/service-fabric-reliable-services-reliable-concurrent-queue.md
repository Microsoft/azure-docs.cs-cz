---
title: ReliableConcurrentQueue v Azure Service Fabric
description: ReliableConcurrentQueue je fronta s vysokou propustností, která umožňuje paralelní fronty a vyřazování z fronty.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: 423ef3d1898176d7c25c596ad186a9c000108aa4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997116"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Úvod do ReliableConcurrentQueue v Azure Service Fabric
Spolehlivá souběžná fronta je asynchronní, transakční a replikovaná fronta, která poskytuje vysoké souběžnosti pro zařazování do fronty a operace odstranění fronty. Je navržená tak, aby poskytovala vysokou propustnost a nízkou latenci tím, že požadavků striktní řazení FIFO poskytované [spolehlivou frontou](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1) a místo toho poskytuje nejlepší řazení.

## <a name="apis"></a>Rozhraní API

|Souběžná fronta                |Spolehlivá souběžná fronta                                         |
|--------------------------------|------------------------------------------------------------------|
| anulovat zařazení do fronty (T Item)           | EnqueueAsync úlohy (ITransaction TX, T Item)                       |
| bool TryDequeue (výsledek T)  | Task< ConditionalValue < T > > TryDequeueAsync (ITransaction TX)  |
| Počet int ()                    | dlouhý počet ()                                                     |

## <a name="comparison-with-reliable-queue"></a>Porovnání s [spolehlivou frontou](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1)

Spolehlivá souběžná fronta se nabízí jako alternativa [spolehlivé fronty](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1). V případech, kdy není vyžadováno striktní řazení FIFO, by mělo být použito v případech, kdy je zaručeno, že metoda FIFO vyžaduje kompromisy s souběžnou metodou.  [Spolehlivá fronta](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1) používá zámky k vykonání řazení FIFO, přičemž ve více než jedné transakci povoluje zařazení do fronty a maximálně jedna transakce povolená k vyřazení z fronty v jednom okamžiku. V porovnání spolehlivá souběžná fronta vydává omezení pořadí a umožňuje jakýmkoli souběžným transakcím prokládat jejich fronty a operace dequeueing. K dispozici je doporučené řazení, ale relativní řazení dvou hodnot ve spolehlivé souběžné frontě není nikdy zaručeno.

Spolehlivá souběžná fronta poskytuje vyšší propustnost a nižší latenci než [spolehlivá fronta](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1) , kdykoli je v nich více souběžných transakcí, které provádějí fronty nebo odstíny.

Vzorový případ použití pro ReliableConcurrentQueue je scénář [fronty zpráv](https://en.wikipedia.org/wiki/Message_queue) . V tomto scénáři jeden nebo více výrobců zpráv vytváří a přidává položky do fronty a jeden nebo více příjemců zprávy přebírá zprávy z fronty a zpracovává je. Více výrobců a uživatelů může pracovat nezávisle, a to pomocí souběžných transakcí za účelem zpracování fronty.

## <a name="usage-guidelines"></a>Pokyny k používání
* Fronta očekává, že položky ve frontě mají dobu nedostatečného uchování. To znamená, že položky nebudou zůstat ve frontě po dlouhou dobu.
* Fronta nezaručuje striktní řazení FIFO.
* Fronta nečte vlastní zápisy. Pokud je položka zařazena do fronty v rámci transakce, nebude viditelná pro odkládání v rámci stejné transakce.
* Vyřazení z fronty nejsou od sebe vzájemně izolované. Pokud je položka *a* v *txnA* transakce Oddálená, i když *TxnA* není potvrzen, položka *A* nebude viditelná pro souběžnou transakční *txnB*.  Pokud *txnA* přeruší, *A* bude se okamžitě zobrazovat jako *txnB* .
* Chování *TryPeekAsync* se dá implementovat pomocí *TryDequeueAsync* a pak transakci přerušit. Příklad tohoto chování najdete v části programovací vzory.
* Počet je jiný než transakční. Dá se použít k získání nápadu na počet prvků ve frontě, ale představuje bod v čase a nemůže se spoléhat na.
* Nákladné zpracování položek odstraněných z fronty by nemělo být prováděno, pokud je transakce aktivní, aby nedocházelo k dlouhotrvajícím transakcím, které mohou mít vliv na výkon systému.

## <a name="code-snippets"></a>Fragmenty kódu
Podíváme se na několik fragmentů kódu a jejich očekávané výstupy. Zpracování výjimek je v této části ignorováno.

### <a name="instantiation"></a>Vytváření instancí
Vytvoření instance spolehlivé souběžné fronty je podobné jako u všech ostatních spolehlivých kolekcí.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Tady je několik fragmentů kódu pro použití EnqueueAsync následovaný jejich očekávanými výstupy.

- *Případ 1: úloha jedné fronty*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Předpokládat, že se úloha úspěšně dokončila a že ve frontě nemění žádné souběžné transakce. Uživatel může očekávat, že fronta bude obsahovat položky v některém z následujících objednávek:

> 10, 20
> 
> 20, 10


- *Případ 2: úloha paralelního zařazení do fronty*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Předpokládat, že úkoly byly úspěšně dokončeny a že úlohy běžely paralelně a že ve frontě nemění žádné další souběžné transakce. Nelze vytvořit odvození v pořadí položek ve frontě. Pro tento fragment kódu se mohou položky zobrazit v kterékoli ze 4. možné pořadí.  Fronta se pokusí zachovat položky v původním (zařazeném) pořadí, ale může být vynucená jejich změna pořadí v důsledku souběžných operací nebo chyb.


### <a name="dequeueasync"></a>DequeueAsync
Tady je několik fragmentů kódu pro použití TryDequeueAsync, po kterém následují očekávané výstupy. Předpokládejme, že je ve frontě již vyplněna fronta s následujícími položkami:
> 10, 20, 30, 40, 50, 60

- *Případ 1: úloha samostatného vyřazení z fronty*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Předpokládat, že se úloha úspěšně dokončila a že ve frontě nemění žádné souběžné transakce. Vzhledem k tomu, že pro pořadí položek ve frontě nelze vytvořit odvození, mohou být všechny tři položky vyřazení z fronty v libovolném pořadí. Fronta se pokusí zachovat položky v původním (zařazeném) pořadí, ale může být vynucená jejich změna pořadí v důsledku souběžných operací nebo chyb.  

- *Případ 2: paralelní úloha vyřazení z fronty*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Předpokládat, že úkoly byly úspěšně dokončeny a že úlohy běžely paralelně a že ve frontě nemění žádné další souběžné transakce. Vzhledem k tomu, že v pořadí položek ve frontě nelze vytvořit odvození, budou seznamy *dequeue1* a *dequeue2* obsahovat všechny dvě položky, a to v libovolném pořadí.

Tato *položka se v* obou seznamech nezobrazí. Proto pokud má dequeue1 *10*, *30*, pak dequeue2 by měl *20*, *40*.

- *Případ 3: řazení z fronty s přerušením transakce*

Přerušení transakce se zařazováním do front se zařadí položky zpátky do hlavičky fronty. Pořadí, ve kterém se položky vrátí zpět na hlavu fronty, není zaručeno. Dejte nám pozor na následující kód:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Předpokládejme, že se položky odložily v tomto pořadí:
> 10, 20

Po přerušení transakce budou položky přidány zpět do vedoucího fronty v některém z následujících objednávek:
> 10, 20
> 
> 20, 10

Totéž platí pro všechny případy, kdy transakce nebyla úspěšně *potvrzena*.

## <a name="programming-patterns"></a>Programovací vzory
V této části se podíváme na několik programovacích vzorů, které můžou být užitečné při používání ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Batch – vyřadí z fronty
Doporučeným programovacím modelem je, aby úkol pro uživatele mohl dávkovat své vyřazení z provozu, místo aby prováděl jednu odstínovou frontu současně. Uživatel se může rozhodnout omezit prodlevy mezi každou dávkou nebo velikostí dávky. Následující fragment kódu ukazuje tento programovací model. Upozorňujeme, že v tomto příkladu je zpracování provedeno po potvrzení transakce, takže pokud došlo k chybě během zpracování, nezpracované položky budou ztraceny bez zpracování.  Případně lze zpracování provést v rámci oboru transakce, ale může mít negativní dopad na výkon a vyžaduje zpracování položek, které již byly zpracovány.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Zpracování Best-Effort Notification-Based
Další zajímavý programovací model používá rozhraní API Count. Tady můžeme implementovat zpracování na základě oznámení pro frontu. Počet front lze použít k omezení fronty nebo úlohy vyřazení z fronty.  Všimněte si, že jako v předchozím příkladu, protože zpracování probíhá mimo transakci, nezpracované položky mohou být ztraceny, pokud dojde k chybě během zpracování.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Vyprázdnit Best-Effort
Vyprázdnit frontu nelze zaručit vzhledem k souběžné povaze struktury dat.  Je možné, že i když žádné operace uživatele ve frontě nejsou v letu, konkrétní volání TryDequeueAsync nemusí vracet položku, která byla dříve zařazená do fronty a potvrzena.  Je zaručeno, že se položka zařazená do fronty *bude moci kdykoli* zviditelnit, ale bez mechanismu vzdálené komunikace, nezávislí spotřebitelé nedokáže zjistit, že fronta dosáhla ustáleného stavu, i když všichni výrobci zastavili a nejsou povoleny žádné nové operace zařazení do fronty. Proto je operace vyprázdnění nejvhodnější, jak je implementováno níže.

Uživatel by měl zastavit všechny další úkoly na producenta a uživatele a počkat na potvrzení nebo zrušení jakýchkoli let, než se pokusí vyprázdnit frontu.  Pokud uživatel ví očekávaný počet položek ve frontě, může nastavit oznámení, které signalizuje, že byly všechny položky vyřazení z fronty.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Prohlížet
ReliableConcurrentQueue neposkytuje rozhraní *TryPeekAsync* API. Uživatelé můžou získat sémantiku prohlížení pomocí *TryDequeueAsync* a pak transakci přerušit. V tomto příkladu jsou vyřazení z fronty zpracována pouze v případě, že je hodnota položky větší než *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Musí číst
* [Rychlý Start Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Oznámení Reliable Services](service-fabric-reliable-services-notifications.md)
* [Reliable Services zálohování a obnovení (zotavení po havárii)](service-fabric-reliable-services-backup-restore.md)
* [Konfigurace spolehlivého správce stavu](service-fabric-reliable-services-configuration.md)
* [Začínáme se službami rozhraní API pro Service Fabric Web](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Pokročilé použití programovacího modelu Reliable Services](./service-fabric-reliable-services-lifecycle.md)
* [Referenční informace pro vývojáře pro spolehlivé kolekce](/dotnet/api/microsoft.servicefabric.data.collections?view=azure-dotnet#microsoft_servicefabric_data_collections)
