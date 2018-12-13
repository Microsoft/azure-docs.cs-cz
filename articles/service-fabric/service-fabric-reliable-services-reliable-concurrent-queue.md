---
title: ReliableConcurrentQueue v Azure Service Fabric
description: ReliableConcurrentQueue je fronta vysokou propustnost, což umožňuje paralelní zařadí a dequeues.
services: service-fabric
documentationcenter: .net
author: tylermsft
manager: timlt
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: twhitney
ms.openlocfilehash: 61b53a23fdbb08b226878d9b702ec6bb2879f8bc
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185031"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Úvod do ReliableConcurrentQueue v Azure Service Fabric
Spolehlivá souběžná fronta je asynchronní, transakční a replikované fronty které funkce vysoké souběžnost pro zařazení do fronty a operace odstranění z fronty. Je navržena pro zajištění vysoké propustnosti a nízké latence zmírněním striktní řazení FIFO poskytované [spolehlivá fronta](https://msdn.microsoft.com/library/azure/dn971527.aspx) a místo toho poskytuje best effort řazení.

## <a name="apis"></a>Rozhraní API

|Souběžná fronta                |Spolehlivá souběžná fronta                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Úloha EnqueueAsync (ITransaction tx, T položky)                       |
| BOOL TryDequeue (out výsledek T)  | Úloha < ConditionalValue < T >> TryDequeueAsync (ITransaction tx)  |
| int Count()                    | dlouhé Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Porovnání s [spolehlivé fronty](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Spolehlivá souběžná fronta se nabízí jako alternativu k [spolehlivá fronta](https://msdn.microsoft.com/library/azure/dn971527.aspx). By mělo být používáno v případech, kdy není potřeba, řazení přísné FIFO jako uzavřené FIFO vyžaduje se souběžností kompromis.  [Spolehlivá fronta](https://msdn.microsoft.com/library/azure/dn971527.aspx) používá zámky vynutit FIFO řazení s maximálně jednu transakci povolen zařadit do fronty a nejvýše jedna transakce může odstranění z fronty po jednom. Spolehlivá souběžná fronta ve srovnání s zmírňuje omezení řazení a umožňuje libovolný počet souběžných transakcí prokládání jejich zařazení do fronty a operace odstranění z fronty. Řazení Best effort je k dispozici, ale relativní řazení ze dvou hodnot v spolehlivá souběžná fronta může nikdy být zaručena.

Spolehlivá souběžná fronta poskytuje vyšší propustnost a nižší latenci než [spolehlivá fronta](https://msdn.microsoft.com/library/azure/dn971527.aspx) pokaždé, když existuje více souběžných transakcí provádění zařadí a/nebo dequeues.

Ukázky pro ReliableConcurrentQueue je případ použití [fronty zpráv](https://en.wikipedia.org/wiki/Message_queue) scénář. V tomto scénáři, jedním nebo více výrobci zprávu vytvořit a přidat položky do fronty, a jeden nebo více zpráv příjemci vytahují zprávy z fronty a jejich zpracování. Více producenti a spotřebitelé může pracovat nezávisle, aby bylo možné zpracovat fronty pomocí souběžných transakcí.

## <a name="usage-guidelines"></a>Pokyny k používání
* Fronty očekává, že s nízkou uchovávají položek ve frontě. Položky by to znamená, zůstanou ve frontě po dlouhou dobu.
* Fronty nezaručuje řazení přísné FIFO.
* Fronty nenačítá vlastní zápisy. Pokud je určitá položka zařazených do fronty v rámci transakce, nebude viditelná pro dequeuer v rámci jedné transakce.
* Dequeues nejsou vzájemně izolované. Pokud položka *A* vyjmutou v transakci *txnA*, i když *txnA* položka není potvrzena, *A* nebude viditelná pro souběžné transakce *txnB*.  Pokud *txnA* přeruší, *A* bude zobrazovat *txnB* okamžitě.
* *TryPeekAsync* chování může být implementováno pomocí *TryDequeueAsync* a potom přerušením transakce. Příklad najdete v části vzory programování.
* Počet je není transakční. Je možné, kde získáte představu o počet prvků ve frontě, ale představuje v daném okamžiku a nelze spoléhat.
* Nákladné zpracování dequeued položek by neměla provést během transakce je aktivní, aby se zabránilo dlouhotrvajících transakcí, které by mohly mít dopad na výkon systému.

## <a name="code-snippets"></a>Fragmenty kódu
Podívejme se na několik fragmentů kódu a jejich očekávané výstupy. Zpracování výjimek je ignorován v této části.

### <a name="enqueueasync"></a>EnqueueAsync
Tady je pár fragmenty kódu pro použití EnqueueAsync, za nímž následuje jejich očekávané výstupy.

- *Případ 1: Jediné úlohy zařadit do fronty*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Předpokládejme, že se úloha úspěšně dokončila a že v něm nebyly žádné souběžných transakcí úprava fronty. Uživatel můžete očekávat, že fronta bude obsahovat položky v některém z následujících pořadí:

> 10, 20

> 20, 10


- *Případ 2: Paralelní úlohy zařadit do fronty*

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

Předpokládejme, že úkoly se nedokončil úspěšně, že úkoly spustil paralelně a, že nebyly provedeny žádné jiné souběžné transakce úprava fronty. Informace o pořadí položek ve frontě nelze realizovat žádná odvození. Tento fragment kódu mohou být zobrazeny položky, v libovolné ze 4! pořadí, které je to možné.  Fronta se pokusí uchovat položky v původním pořadí (zařazených do fronty), ale mohou být nuceni přeuspořádaly z důvodu souběžných operací nebo chyby.


### <a name="dequeueasync"></a>DequeueAsync
Tady je pár fragmenty kódu pro použití TryDequeueAsync, za nímž následuje očekávané výstupy. Předpokládejme, že fronty již naplněný následujících položek ve frontě:
> 10, 20, 30, 40, 50, 60

- *Případ 1: Jedním odstranění z fronty úloh*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Předpokládejme, že se úloha úspěšně dokončila a že v něm nebyly žádné souběžných transakcí úprava fronty. Protože pořadí položek ve frontě nelze realizovat žádná odvození, všechny tři položky může být odstraněné z fronty, v libovolném pořadí. Fronta se pokusí uchovat položky v původním pořadí (zařazených do fronty), ale mohou být nuceni přeuspořádaly z důvodu souběžných operací nebo chyby.  

- *Případ 2: Paralelní odstranění z fronty úloh*

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

Předpokládejme, že úkoly se nedokončil úspěšně, že úkoly spustil paralelně a, že nebyly provedeny žádné jiné souběžné transakce úprava fronty. Protože nelze realizovat žádná odvození o pořadí položek ve frontě, seznamy *dequeue1* a *dequeue2* bude každý obsahovat jakékoli dvě položky, v libovolném pořadí.

Stejná položka bude *není* se objeví v obou seznamech. Proto pokud má dequeue1 *10*, *30*, pak byste měli dequeue2 *20*, *40*.

- *Případ 3: Odstranění z fronty řazení s přerušení transakce*

Zrušená transakce s vydávaných za pochodu dequeues vloží položky zpět na hlavní fronty. Není zaručeno pořadí, ve které položky se vrátí zpět do hlavní fronty. Podívejme se na následující kód:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Předpokládejme, že byly položky vyřazených z fronty v následujícím pořadí:
> 10, 20

Když jsme přerušení transakce, položky byly přidány zpět do hlavní fronty v některém z následujících pořadí:
> 10, 20

> 20, 10

Totéž platí pro všechny případy, ve kterém transakce nebyla úspěšně *potvrzeno*.

## <a name="programming-patterns"></a>Vzory programování
V této části, Podívejme se na několik programovacích vzorech, které mohou být užitečné při použití ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Dequeues služby batch
Doporučeným je programovací model pro spotřebitele úlohu služby batch jeho dequeues místo jedné odstranění z fronty po jednom. Uživatel můžete omezit zpoždění mezi každou dávku nebo velikost dávky. Následující fragment kódu ukazuje tento programovací model.  Všimněte si, že v tomto příkladu, dokončení zpracování se poté, co je transakce potvrzena, aby šlo chybu při zpracování dojde k nezpracované položky budou ztraceny bez nutnosti byla zpracována.  Alternativně zpracování to provést v rámci oboru transakce, ale to může mít negativní dopad na výkon a vyžaduje zpracování položek, které již zpracovány.

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

### <a name="best-effort-notification-based-processing"></a>Zpracování oznámení na Best Effort
Další zajímavé programovací model používá rozhraní API Count. Tady můžeme implementovat best effort oznámení založené na zpracování fronty. Fronty počet slouží k omezení zařazení do fronty nebo odebrání z fronty úloh.  Všimněte si, že stejně jako v předchozím příkladu, protože mimo transakci, proběhne zpracování nezpracovaných položky byly pravděpodobně ztraceny případě chybu během zpracování.

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

### <a name="best-effort-drain"></a>Vyprazdňování Best Effort
Z důvodu souběžných povaze datová struktura nemůže být zaručena vyprazdňování fronty.  Je možné, že i v případě, že jsou vydávaných za pochodu žádné operace uživatelů ve frontě, nesmí vracet konkrétní volání TryDequeueAsync, položka, která byla dřív zařazených do fronty a potvrzené.  Je zaručeno, položka byla zařazena do fronty *nakonec* uživatelům zviditelní odstranění z fronty, ale bez mechanismus komunikace out-of-band příjemci nezávislé nemůže vědět, že fronta dosáhla stabilního stavu i v případě všichni výrobci aby byla zastavena a nové zařazení do fronty, jsou povoleny operace. Operace vyprazdňování tedy best effort jak je implementován níže.

Uživatel by měl zastavit všechny další producenta a úlohy příjemce a počkejte, jakékoli vydávaných za pochodu transakce na potvrzení nebo přerušení, než se pokusíte o vyprázdnění fronty.  Pokud uživatel zná, očekávaný počet položek ve frontě, můžete nastavit oznámení, která signalizuje, že všechny položky odstraněné.

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
ReliableConcurrentQueue neposkytuje *TryPeekAsync* rozhraní api. Uživatele můžete získat náhled sémantické pomocí *TryDequeueAsync* a potom přerušením transakce. V tomto příkladu dequeues jsou zpracována pouze v případě, že je větší než hodnota položky *10*.

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

## <a name="must-read"></a>Musí ke čtení
* [Rychlý Start modelu Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Práce s Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Oznámení Reliable Services](service-fabric-reliable-services-notifications.md)
* [Spolehlivé služby zálohování a obnovení (zotavení po havárii)](service-fabric-reliable-services-backup-restore.md)
* [Konfigurace Reliable State Manager](service-fabric-reliable-services-configuration.md)
* [Začínáme se službou Service Fabric webové rozhraní API služby](service-fabric-reliable-services-communication-webapi.md)
* [Rozšířené využití programovacího modelu Reliable Services](service-fabric-reliable-services-advanced-usage.md)
* [Referenční informace pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
