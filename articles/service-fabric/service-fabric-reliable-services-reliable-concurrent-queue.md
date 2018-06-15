---
title: ReliableConcurrentQueue v Azure Service Fabric
description: ReliableConcurrentQueue je Vysoká propustnost fronty, který umožňuje paralelní enqueues a dequeues.
services: service-fabric
documentationcenter: .net
author: sangarg
manager: timlt
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: sangarg
ms.openlocfilehash: e04123f7870921a2979564d0f6c68424d4d7711c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206573"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Úvod do ReliableConcurrentQueue v Azure Service Fabric
Spolehlivé souběžných fronty je frontu asynchronní, transakční a replikované které funkce vysoké souběžnosti pro zařazování a dequeue – operace. Je navržen pro poskytování vysoké prostupnosti a nízké latence podle uvolnit striktní řazení FIFO poskytované [spolehlivé fronty](https://msdn.microsoft.com/library/azure/dn971527.aspx) a místo toho poskytuje best effort řazení.

## <a name="apis"></a>Rozhraní API

|Souběžné fronty                |Spolehlivá souběžná fronta                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Úloha EnqueueAsync (ITransaction tx, položka T)                       |
| BOOL TryDequeue (out výsledek T)  | Úloha < ConditionalValue < T >> TryDequeueAsync (ITransaction tx)  |
| int Count()                    | dlouhé Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Porovnání s [spolehlivé fronty](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Spolehlivé souběžných fronty je poskytován jako alternativu k [spolehlivé fronty](https://msdn.microsoft.com/library/azure/dn971527.aspx). By být používána v případech, kde není vyžadována, striktní řazení FIFO jako zaručující FIFO vyžaduje kompromis s souběžnosti.  [Spolehlivé fronty](https://msdn.microsoft.com/library/azure/dn971527.aspx) používá zámky vynutit FIFO řazení s maximálně jednu transakci povolen zařadit do fronty a maximálně jednu transakci dovoleno dequeue – najednou. Porovnání spolehlivé souběžných fronty zmírní řazení omezení a umožňuje jakýchkoli počet souběžných transakcí interleave jejich zařazování a dequeue – operace. Řazení typu Best effort je zadáno, ale relativní řazení ze dvou hodnot v spolehlivé souběžných frontě možné nikdy zaručit.

Poskytuje spolehlivé souběžných fronty vyšší propustnost a nižší latenci než [spolehlivé fronty](https://msdn.microsoft.com/library/azure/dn971527.aspx) vždy, když jsou více souběžných transakcí provádění enqueues nebo dequeues.

Ukázka případ použití je ReliableConcurrentQueue [fronta zpráv](https://en.wikipedia.org/wiki/Message_queue) scénář. V tomto scénáři jeden nebo více zpráv producenti vytvoření a přidání položek do fronty, a jeden nebo více zpráv příjemci načítat zprávy z fronty a jejich zpracování. Více producenti a spotřebitelé může nezávisle pracovat, aby bylo možné zpracovat fronty použití souběžných transakcí.

## <a name="usage-guidelines"></a>Pokyny týkající se používání
* Fronty očekává, že položky ve frontě mají doby uchování nízkou. To znamená položky by zůstat ve frontě delší dobu.
* Fronty nezaručuje striktní FIFO řazení.
* Fronta není přečíst vlastní zápisy. Pokud je položka zařazených do fronty v rámci transakce, nebude viditelná pro dequeuer v rámci stejné transakci.
* Dequeues nejsou od sebe navzájem oddělené. Pokud položka *A* je vyjmutou v transakci *txnA*, i když *txnA* položky není potvrzena, *A* nebude viditelná pro souběžné transakce *txnB*.  Pokud *txnA* zruší, *A* bude zobrazovat *txnB* okamžitě.
* *TryPeekAsync* chování může být implementováno pomocí *TryDequeueAsync* a pak ruší se transakce. Příklady najdete v části programování vzory.
* Počet je netransakční. Umožňuje získat představu o počet elementů ve frontě, ale představuje bodu v čase a nelze spoléhat.
* Nákladné zpracování na dequeued položky nebude prováděna při transakci je aktivní, aby se zabránilo dlouhotrvajících transakcí, které by mohly mít dopad na výkon systému.

## <a name="code-snippets"></a>Fragmenty kódu
Dejte nám se podívejte na několik fragmenty kódu a jejich očekávané výstupy. Zpracovávání výjimek v jazyce je ignorován v této části.

### <a name="enqueueasync"></a>EnqueueAsync
Tady je několik fragmenty kódu pro použití EnqueueAsync následuje jejich očekávané výstupy.

- *Případ 1: Zařazování úloh jeden*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Předpokládejme, že úloha dokončena úspěšně a že v něm nebyly žádné souběžných transakcí úprava fronty. Uživatele můžete očekávat fronty tak, aby obsahovala položky ve všech následujících příkazů:

> 10, 20

> 20, 10


- *Případ 2: Zařazování úloh paralelní*

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

Předpokládejme, že úlohy byl úspěšně dokončen, že úkoly spustili paralelně a že neexistují žádné souběžných transakcí úprava fronty. Žádné odvození můžete provedeny o pořadí položek ve frontě. Pro tento fragment kódu může se zobrazit položky v některém ze 4! možné pořadí.  Fronty se pokusí uchovat položky v původní pořadí (zařazených do fronty), ale muset změnit jejich pořadí z důvodu souběžných operací nebo chyb.


### <a name="dequeueasync"></a>DequeueAsync
Tady je několik fragmenty kódu pro použití TryDequeueAsync následuje očekávané výstupy. Předpokládejme, že fronta je již naplněný následující položky ve frontě:
> 10, 20, 30, 40, 50, 60

- *Případ 1: Jeden dequeue – úloha*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Předpokládejme, že úloha dokončena úspěšně a že v něm nebyly žádné souběžných transakcí úprava fronty. Vzhledem k tomu, že o pořadí položek ve frontě nelze realizovat žádná odvození, může být vyjmutou všechny tři položky v libovolném pořadí. Fronty se pokusí uchovat položky v původní pořadí (zařazených do fronty), ale muset změnit jejich pořadí z důvodu souběžných operací nebo chyb.  

- *Případ 2: Dequeue – paralelní úlohy*

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

Předpokládejme, že úlohy byl úspěšně dokončen, že úkoly spustili paralelně a že neexistují žádné souběžných transakcí úprava fronty. Vzhledem k tomu, že nelze realizovat žádná odvození o pořadí položek ve frontě, seznamy *dequeue1* a *dequeue2* bude každý obsahovat jakékoli dvě položky v libovolném pořadí.

Stejná položka bude *není* se zobrazí v obou seznamy. Proto pokud má dequeue1 *10*, *30*, pak by měla mít dequeue2 *20*, *40*.

- *Případ 3: Dequeue – při řazení s přerušení transakcí*

Ruší se transakce s během letu dequeues PUT, které položky zpět na záhlaví fronty. Pořadí, ve které položky se vrátit zpět na záhlaví fronty není zaručena. Dejte nám podívejte se na následující kód:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Předpokládejme, že položky byly vyjmutou v následujícím pořadí:
> 10, 20

Když jsme ji zrušit, by byl přidán položky zpět do head fronty v některém z následujících objednávky:
> 10, 20

> 20, 10

Totéž platí pro všechny případy, pokud transakce nebyla úspěšně *potvrzení*.

## <a name="programming-patterns"></a>Vzory programování
V této části, dejte nám podívejte se na několik programování vzorů, které mohou být užitečné při použití ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Batch Dequeues
A doporučená programovací vzor je pro úlohu příjemce dávku jeho dequeues místo jedné dequeue – najednou. Uživatel může vybrat k omezení zpoždění mezi každou dávku nebo velikost dávky. Následující fragment kódu ukazuje tento programovací model.  Všimněte si, že se v tomto příkladu, se provádí zpracování po, že je transakce potvrzena, nebo, pokud k chybě dochází při zpracování, nezpracované položky budou ztraceny bez zpracování.  Alternativně zpracování lze provést v rámci oboru transakce, ale to může mít negativní dopad na výkon a vyžaduje zpracování položek, které jsou již zpracována.

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

### <a name="best-effort-notification-based-processing"></a>Zpracování oznámení na základě typu Best Effort
Jiné zajímavé programovací vzor používá rozhraní API počet. Zde můžeme implementovat best effort oznámení na základě zpracování pro frontu. Fronty počet slouží k omezení dequeue úlohy nebo zařazování.  Všimněte si, že jako v předchozím příkladu vzhledem k tomu, že mimo transakci, proběhne zpracování nezpracovaných položky může dojít ke ztrátě Pokud dojde k chybě během zpracování.

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

### <a name="best-effort-drain"></a>Best Effort vyprazdňování
Vyprazdňování fronty nemůže zaručit z důvodu souběžných povaha datovou strukturu.  Je možné, že i když žádné operace uživatele na fronty jsou během letu, konkrétní volání TryDequeueAsync nemusí vracet položku, která byla dříve zařazených do fronty a potvrzené.  Položka zařazených do fronty záruku, že *nakonec* budou zobrazeny na vyřazení z fronty, ale bez mechanismus komunikace out-of-band nezávislé příjemce nemůže vědět, že fronty dosáhl stabilní i když všechny generátory byla zastavena a nové zařazování, které jsou povolené operace. Operace vyprazdňování tedy best effort jak jsou implementované níže.

Uživatel by měl zastavit všechny další producent a úlohy příjemce a počkat na jakékoli během letu transakce potvrzení nebo přerušení, před pokusem o vyprazdňování fronty.  Pokud uživatel nezná očekávaný počet položek ve frontě, můžete nastavit oznámení, která signalizuje, že máte byla vyjmutou všechny položky.

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
ReliableConcurrentQueue neposkytuje *TryPeekAsync* rozhraní api. Uživatelé dostanou prohlížení sémantického pomocí *TryDequeueAsync* a pak ruší se transakce. V tomto příkladu dequeues se zpracují pouze v případě, že je větší než hodnota položky *10*.

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

## <a name="must-read"></a>Musí pro čtení
* [Spolehlivé služby rychlý Start](service-fabric-reliable-services-quick-start.md)
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Spolehlivé služby oznámení](service-fabric-reliable-services-notifications.md)
* [Spolehlivé služby zálohování a obnovení (zotavení po havárii)](service-fabric-reliable-services-backup-restore.md)
* [Spolehlivé stavu Správce konfigurace](service-fabric-reliable-services-configuration.md)
* [Začínáme se službou Service Fabric webové rozhraní API služby](service-fabric-reliable-services-communication-webapi.md)
* [Rozšířené použití spolehlivé služby programovací Model](service-fabric-reliable-services-advanced-usage.md)
* [Referenční informace pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
