---
title: Spolehlivé služby oznámení | Microsoft Docs
description: Rámcová dokumentace pro oznámení služby Fabric spolehlivé služby
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,vturecek
ms.assetid: cdc918dd-5e81-49c8-a03d-7ddcd12a9a76
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: 4455b259ef2159f9e1ec4991fc533f1843899682
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207006"
---
# <a name="reliable-services-notifications"></a>Spolehlivé služby oznámení
Oznámení klientům sledovat změny provedené na objekt, který se vás zajímá. Oznámení podporují dva typy objektů: *spolehlivé správce stavu* a *spolehlivé slovník*.

Běžnými důvody používání oznámení jsou:

* Vytváření materializována zobrazení, jako je například sekundární indexy nebo agregovat filtrovaných zobrazení stavu repliky. Příkladem je seřazená index všechny klíče ve slovníku spolehlivé.
* Odesílání monitorování dat, například počet uživatelů, které jsou přidány za poslední hodinu.

Oznámení při vyvolání jako součást použije operace. Z tohoto důvodu měla by ji zpracovat oznámení tak rychle, jak je možné a synchronní události nesmí obsahovat žádné náročná operace.

## <a name="reliable-state-manager-notifications"></a>Stav spolehlivé Správce oznámení
Správce spolehlivé stavu poskytuje oznámení pro následující události:

* Transakce
  * Potvrzení
* Správce stavu
  * Opětovné sestavení
  * Přidání spolehlivé stavu
  * Odebrání spolehlivé stavu

Správce spolehlivé stavu sleduje aktuální aktivních pořadových transakce. Pouze změny ve stavu transakce, která způsobuje být aktivováno oznámení je právě potvrzené transakce.

Správce spolehlivé stavu udržuje kolekci stavy spolehlivá jako slovník spolehlivé a spolehlivé fronty. Správce spolehlivé stavu aktivuje oznámení při změně této kolekce: spolehlivé stavu je přidat nebo odebrat nebo celou kolekci znovu sestaven.
Správce spolehlivé stavu kolekce je znovu sestavit ve třech případech:

* Obnovení: Když se spustí repliku, obnoví předchozí stav z disku. Na konci obnovení používá **NotifyStateManagerChangedEventArgs** má provést událost, která obsahuje sadu obnovené spolehlivé stavy.
* Úplné kopie: před repliku se můžete zapojit do konfigurační sady, má má být sestaven. V některých případech to vyžaduje úplnou kopii spolehlivé správce stavu stavu z primární repliky má být použita pro nečinnosti sekundární repliky. Správce spolehlivé stavu v sekundární replice používá **NotifyStateManagerChangedEventArgs** má provést událost, která obsahuje sadu spolehlivé stavy, které jej získali z primární repliky.
* Obnovení: Ve scénářích zotavení po havárii, stav repliky může být obnovena ze zálohy, prostřednictvím **RestoreAsync**. V takových případech spolehlivé správce stavu na primární replice používá **NotifyStateManagerChangedEventArgs** má provést událost, která obsahuje sadu spolehlivé stavy, které ji obnovit ze zálohy.

Registrace pro oznámení transakce nebo oznámení správce stavu, budete muset zaregistrovat **TransactionChanged** nebo **StateManagerChanged** událostí na spolehlivé správce stavu. Je běžné registraci obslužné rutiny těchto událostí se v konstruktoru stavové služby. Při registraci v konstruktoru, nebude neproběhly všechna oznámení, která je způsobena změnou po dobu životnosti **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

**TransactionChanged** používá obslužné rutiny události **NotifyTransactionChangedEventArgs** k zajištění podrobných informací o události. Obsahuje vlastnost action (například **NotifyTransactionChangedAction.Commit**) určující typ změny. Také obsahuje vlastnost transakce, který poskytuje odkaz na transakce, který změnil.

> [!NOTE]
> V současné době **TransactionChanged** události jsou vyvolány pouze v případě, že je transakce potvrzena. Akce bude rovna **NotifyTransactionChangedAction.Commit**. Ale v budoucnu, může být vyvolána události pro jiné typy změny stavu transakce. Doporučujeme, abyste kontrolu akce a zpracování události, pouze pokud je ten, který jste očekávali.
> 
> 

Tady je příklad **TransactionChanged** obslužné rutiny události.

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

**StateManagerChanged** používá obslužné rutiny události **NotifyStateManagerChangedEventArgs** k zajištění podrobných informací o události.
**NotifyStateManagerChangedEventArgs** má dva podtřídy: **NotifyStateManagerRebuildEventArgs** a **NotifyStateManagerSingleEntityChangedEventArgs**.
Pomocí vlastnosti akce v **NotifyStateManagerChangedEventArgs** přetypovat **NotifyStateManagerChangedEventArgs** pro správné podtřídu:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** a **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Tady je příklad **StateManagerChanged** obslužná rutina oznámení.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Spolehlivé slovník oznámení
Spolehlivé slovník poskytuje oznámení pro následující události:

* Opětovné sestavení: Voláno, když **ReliableDictionary** její stav se zotavil z obnovené nebo zkopírovaný místní stavu nebo zálohování.
* Clear: Voláno, když stav **ReliableDictionary** byl vymazán prostřednictvím **ClearAsync** metoda.
* Přidejte: Volána, když byla přidat položku do **ReliableDictionary**.
* Aktualizace: Volána, když se položky v **IReliableDictionary** byla aktualizována.
* Odebrat: Volána, když se položky v **IReliableDictionary** byla odstraněna.

Pokud chcete dostávat oznámení spolehlivé slovník, je potřeba zaregistrovat **DictionaryChanged** obslužné rutiny události na **IReliableDictionary**. Je běžné registraci obslužné rutiny těchto událostí se v **ReliableStateManager.StateManagerChanged** přidat oznámení.
Při registraci **IReliableDictionary** se přidá do **IReliableStateManager** zajistí, že nebude neproběhly žádné oznámení.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** je metoda ukázky, předchozím **OnStateManagerChangedHandler** příklad volání.
> 
> 

Předchozí sady kódu **IReliableNotificationAsyncCallback** rozhraní, spolu s **DictionaryChanged**. Protože **NotifyDictionaryRebuildEventArgs** obsahuje **IAsyncEnumerable** rozhraní – které je možné provést výčet asynchronně – opětovné sestavení oznámení při vyvolání prostřednictvím  **RebuildNotificationAsyncCallback** místo **OnDictionaryChangedHandler**.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> V předchozím kódu v rámci zpracování oznámení o opětovné sestavení nejprve zachována agregovaný stav je vymazán. Protože spolehlivé kolekce se znovu sestaven pomocí nového stavu, jsou všechny předchozí oznámení důležité.
> 
> 

**DictionaryChanged** používá obslužné rutiny události **NotifyDictionaryChangedEventArgs** k zajištění podrobných informací o události.
**NotifyDictionaryChangedEventArgs** má pět podtřídy. Použijte vlastnost akce v **NotifyDictionaryChangedEventArgs** přetypovat **NotifyDictionaryChangedEventArgs** pro správné podtřídu:

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add** a **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Doporučení
* *Proveďte* co nejrychleji provést oznámení události.
* *Nechcete* spouštět žádné náročná operace (například vstupně-výstupních operací) jako součást synchronní události.
* *Proveďte* zkontrolujte typ akce před zpracovat událost. Nové typy akce může v budoucnu přidat.

Zde jsou některé věci, třeba vzít v úvahu:

* Oznámení při vyvolání jako součást provádění operace. Například oznámení o obnovení, je vyvolána jako poslední krok operaci obnovení. Obnovení nebude dokončen, dokud zpracovává událost upozornění.
* Protože oznámení při vyvolání jako součást provádění operací, klienti zobrazit pouze oznámení pro místně potvrzení operace. A protože je zaručeno, že operace pouze místně potvrzené (jinými slovy, přihlášení), se může nebo nemusí být v budoucnu vrátit zpět.
* V cestě opakování je aktivována, jednoho oznámení pro každou operaci použité. To znamená, že obsahuje-li transakce T1 Create(X), Delete(X) a Create(X), získáte jedno oznámení pro vytvoření X, jeden pro odstranění a jeden pro vytvoření znovu, v tomto pořadí.
* Pro transakce, které obsahují více operací operace se použijí v pořadí, ve kterém byly načteny na primární replice od uživatele.
* Jako součást zpracování false průběh může být některé operace vrátit zpět. Oznámení jsou vyvolány pro tyto operace vrácení zpět, vracení stavu repliky na bod stabilní. Jeden důležitý rozdíl oznámení vrácení zpět je, že jsou agregovaný události, které mají duplicitní klíče. Například pokud se vrací zpět transakci T1, se zobrazí jeden oznámení Delete(X).

## <a name="next-steps"></a>Další postup
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Spolehlivé služby rychlý start](service-fabric-reliable-services-quick-start.md)
* [Spolehlivé služby zálohování a obnovení (zotavení po havárii)](service-fabric-reliable-services-backup-restore.md)
* [Referenční informace pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

