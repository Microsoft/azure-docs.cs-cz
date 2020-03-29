---
title: SpolehlivéConcurrentQueue v Azure Service Fabric
description: ReliableConcurrentQueue je fronta s vysokou propustností, která umožňuje paralelní fronty a vyřazení do fronty.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: a7115db8259fde0e87e53557ecef730f8e82d2fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462735"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Úvod do reliableconcurrentqueue v Azure Service Fabric
Spolehlivá souběžná fronta je asynchronní, transakční a replikovaná fronta, která nabízí vysokou souběžnost pro operace zařazení do fronty a vyřazení z fronty. Je navržen tak, aby poskytoval vysokou propustnost a nízkou latenci uvolněním přísného pořadí FIFO [poskytovaného spolehlivou frontou](https://msdn.microsoft.com/library/azure/dn971527.aspx) a místo toho poskytuje nejlepší úsilí.

## <a name="apis"></a>Rozhraní API

|Souběžná fronta                |Spolehlivá souběžná fronta                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(Položka T)           | Úloha EnqueueAsync(ITransaction tx, Položka T)                       |
| bool TryDequeue (out T výsledek)  | Úloha< ConditionalValue < T > > TryDequeueAsync(ITransaction tx)  |
| počet int()                    | dlouhý count()                                                     |

## <a name="comparison-with-reliable-queue"></a>Porovnání se [spolehlivou frontou](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Spolehlivá souběžná fronta je nabízena jako alternativa ke [spolehlivé frontě](https://msdn.microsoft.com/library/azure/dn971527.aspx). Měl by být používán v případech, kdy není vyžadováno přísné objednávání FIFO, protože záruka FIFO vyžaduje kompromis s souběžnosti.  [Reliable Queue](https://msdn.microsoft.com/library/azure/dn971527.aspx) používá zámky k vynucení pořadí FIFO, přičemž maximálně jedna transakce může být zařazena do fronty a maximálně jedna transakce může být současně vyřazena z fronty. Ve srovnání spolehlivé souběžné fronty uvolňuje omezení řazení a umožňuje libovolný počet souběžných transakcí proložit jejich zařazení do fronty a dequeue operace. Nejlepší úsilí řazení je k dispozici, ale relativní řazení dvou hodnot ve spolehlivé souběžné fronty nelze nikdy zaručit.

Spolehlivá souběžná fronta poskytuje vyšší propustnost a nižší latenci než [spolehlivá fronta](https://msdn.microsoft.com/library/azure/dn971527.aspx) vždy, když existuje více souběžných transakcí provádějících fronty a/nebo dequeues.

Ukázkový případ použití pro reliableconcurrentqueue je scénář [fronty zpráv.](https://en.wikipedia.org/wiki/Message_queue) V tomto scénáři jeden nebo více výrobců zpráv vytvořit a přidat položky do fronty a jeden nebo více spotřebitelů zpráv vyžádat zprávy z fronty a zpracovat je. Více výrobců a spotřebitelů může pracovat nezávisle, pomocí souběžných transakcí za účelem zpracování fronty.

## <a name="usage-guidelines"></a>Pokyny k používání
* Fronta očekává, že položky ve frontě mají nízké doby uchovávání. To znamená, že položky by nezůstaly ve frontě po dlouhou dobu.
* Fronta nezaručuje přísné pořadí FIFO.
* Fronta nečte vlastní zápisy. Pokud je položka zařazena do fronty v rámci transakce, nebude viditelná pro dequeuer v rámci stejné transakce.
* Dequeues nejsou izolovány od sebe navzájem. Pokud je položka *A* vyřazena z fronty v transakci *txnA*, i když *txnA není potvrzena,* položka *A* nebude viditelná pro souběžnou transakci *txnB*.  Pokud *txnA* přeruší, *A* se stane *viditelným pro txnB* okamžitě.
* *TryPeekAsync* chování lze implementovat pomocí *TryDequeueAsync* a potom přerušení transakce. Příklad tohoto chování lze nalézt v části Programovací vzory.
* Počet je netransakční. Lze získat představu o počtu prvků ve frontě, ale představuje bod v čase a nelze se na něj spolehnout.
* Nákladné zpracování položek vyřazených z fronty by nemělo být prováděno, pokud je transakce aktivní, aby se zabránilo dlouhotrvajícím transakcím, které mohou mít dopad na výkon systému.

## <a name="code-snippets"></a>Fragmenty kódu
Podívejme se na několik fragmentů kódu a jejich očekávané výstupy. Zpracování výjimek je v této části ignorováno.

### <a name="instantiation"></a>Vytváření instancí
Vytvoření instance spolehlivé souběžné fronty je podobné jakékoli jiné spolehlivé kolekce.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Zde je několik fragmentů kódu pro použití EnqueueAsync následovaný jejich očekávané výstupy.

- *Případ 1: Úloha jednoho zařazení do fronty*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Předpokládejme, že úloha byla úspěšně dokončena a že nebyly žádné souběžné transakce, které by upravovaly frontu. Uživatel může očekávat, že fronta bude obsahovat položky v některé z následujících objednávek:

> 10, 20
> 
> 20, 10


- *Případ 2: Úloha paralelního zařazení do fronty*

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

Předpokládejme, že úkoly byly úspěšně dokončeny, že úkoly byly spuštěny paralelně a že nebyly žádné další souběžné transakce, které by upravovaly frontu. O pořadí položek ve frontě nelze provést žádné odvození. Pro tento fragment kódu se položky mohou objevit v některém ze 4! možných objednávek.  Fronta se pokusí zachovat položky v původním (zařazeném) pořadí, ale může být nucena je přeřadit z důvodu souběžných operací nebo chyb.


### <a name="dequeueasync"></a>DequeueAsync
Zde je několik fragmentů kódu pro použití TryDequeueAsync následovaný očekávané výstupy. Předpokládejme, že fronta je již naplněna následujícími položkami ve frontě:
> 10, 20, 30, 40, 50, 60

- *Případ 1: Úloha jednoho vyřazení z fronty*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Předpokládejme, že úloha byla úspěšně dokončena a že nebyly žádné souběžné transakce, které by upravovaly frontu. Vzhledem k tomu, že nelze provést žádné odvození o pořadí položek ve frontě, všechny tři položky mohou být vyřazeny z fronty v libovolném pořadí. Fronta se pokusí zachovat položky v původním (zařazeném) pořadí, ale může být nucena je přeřadit z důvodu souběžných operací nebo chyb.  

- *Případ 2: Úloha paralelního vyřazení z fronty*

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

Předpokládejme, že úkoly byly úspěšně dokončeny, že úkoly byly spuštěny paralelně a že nebyly žádné další souběžné transakce, které by upravovaly frontu. Vzhledem k tomu, že nelze provést žádné odvození o pořadí položek ve frontě, seznamy *dequeue1* a *dequeue2* bude obsahovat všechny dvě položky v libovolném pořadí.

Stejná položka se *nezobrazí* v obou seznamech. Proto, pokud dequeue1 má *10*, *30*, pak dequeue2 by měl *20*, *40*.

- *Případ 3: Vyřazení řazení do fronty s přerušením transakce*

Přerušení transakce s dequeues za letu vrátí položky zpět na vedoucí fronty. Pořadí, ve kterém jsou položky umístěny zpět na hlavu fronty není zaručena. Podívejme se na následující kód:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Předpokládejme, že položky byly vyřazeny z fronty v následujícím pořadí:
> 10, 20

Když přerušíme transakci, položky budou přidány zpět do čela fronty v některé z následujících objednávek:
> 10, 20
> 
> 20, 10

Totéž platí pro všechny případy, kdy transakce nebyla úspěšně *potvrzena*.

## <a name="programming-patterns"></a>Programovací vzory
V této části se podívejme na několik programovacích vzorů, které by mohly být užitečné při používání ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Dávkové vyřazení z fronty
Doporučený programovací vzor je pro úlohu příjemce dávky jeho dequeues namísto provádění jeden dequeue najednou. Uživatel se může rozhodnout omezit zpoždění mezi každou dávkou nebo velikostí dávky. Následující fragment kódu ukazuje tento programovací model. Uvědomte si, v tomto příkladu zpracování se provádí po transakce je potvrzena, takže pokud došlo k chybě při zpracování, nezpracované položky budou ztraceny bez zpracování.  Alternativně zpracování lze provést v rámci rozsahu transakce, ale může mít negativní dopad na výkon a vyžaduje zpracování již zpracovaných položek.

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

### <a name="best-effort-notification-based-processing"></a>Zpracování založené na oznámeních s nejlepším úsilím
Další zajímavý programovací vzor používá rozhraní API počtu. Zde můžeme implementovat zpracování založené na oznámení o nejlepším úsilím pro frontu. Počet fronty lze omezit fronty nebo dequeue úlohy.  Všimněte si, že stejně jako v předchozím příkladu, protože zpracování dochází mimo transakci, nezpracované položky mohou být ztraceny, pokud dojde k chybě během zpracování.

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

### <a name="best-effort-drain"></a>Odliv s maximální majekcí
Odtok fronty nelze zaručit z důvodu souběžné povahy datové struktury.  Je možné, že i v případě, že žádné operace uživatele ve frontě jsou za letu, konkrétní volání TryDequeueAsync nemusí vrátit položku, která byla dříve zařazena do fronty a potvrzena.  Je zaručeno, že položka zařazená do fronty se *nakonec* stane viditelnou pro vyřazení z fronty, ale bez neintegrovaného komunikačního mechanismu nemůže nezávislý spotřebitel vědět, že fronta dosáhla ustáleného stavu, i když byli zastaveni všichni výrobci a nejsou povoleny žádné nové operace zařazení do fronty. To znamená, že kanalizace je nejlepší úsilí, jak je implementováno níže.

Uživatel by měl zastavit všechny další úkoly výrobce a příjemce a čekat na všechny transakce za letu potvrdit nebo přerušit, před pokusem o vyprázdnění fronty.  Pokud uživatel zná očekávaný počet položek ve frontě, může nastavit oznámení, které signalizuje, že všechny položky byly vyřazeny z fronty.

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
ReliableConcurrentQueue neposkytuje rozhraní api *TryPeekAsync.* Uživatelé mohou získat náhled sémantické pomocí *TryDequeueAsync* a potom přerušení transakce. V tomto příkladu jsou vyřazení z fronty zpracovány pouze v případě, že hodnota položky je větší než *10*.

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
* [Rychlý start spolehlivých služeb](service-fabric-reliable-services-quick-start.md)
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Spolehlivé služby oznámení](service-fabric-reliable-services-notifications.md)
* [Spolehlivé zálohování a obnovení služeb (zotavení po havárii)](service-fabric-reliable-services-backup-restore.md)
* [Spolehlivá konfigurace správce stavu](service-fabric-reliable-services-configuration.md)
* [Začínáme se službami Service Fabric Web API Services](service-fabric-reliable-services-communication-webapi.md)
* [Pokročilé využití programovacího modelu spolehlivých služeb](service-fabric-reliable-services-advanced-usage.md)
* [Odkaz pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
