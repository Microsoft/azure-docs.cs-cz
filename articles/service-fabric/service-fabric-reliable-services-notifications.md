---
title: Reliable Services upozornění | Dokumentace Microsoftu
description: Rámcové dokumentaci pro oznámení Service Fabric Reliable Services
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
ms.openlocfilehash: a13e5d74390b82888f51cfd225c54e29550354e9
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433510"
---
# <a name="reliable-services-notifications"></a>Oznámení Reliable Services
Oznámení umožňují klientům sledovat změny provedené na objekt, který chtějí. Oznámení podporují dva typy objektů: *Reliable State Manager* a *spolehlivého slovníku*.

Běžné důvody pro používání oznámení jsou:

* Vytváření materializovaných zobrazení, jako jsou sekundární indexy nebo agregovaná filtrované zobrazení stavu repliky. Příkladem je seřazený index spolehlivého slovníku všechny klíče.
* Odeslání dat monitorování, jako je počet uživatelů přidaných za poslední hodinu.

Oznámení jsou vyvolávány v rámci použití operace. Z důvodu, třeba zpracovat tak rychle, jak je to možné a synchronní události by neměly obsahovat žádné nákladné operace oznámení.

## <a name="reliable-state-manager-notifications"></a>Reliable State Manager oznámení
Reliable State Manager poskytuje oznámení pro následující události:

* Transakce
  * Potvrzení
* Správce stavu
  * Opětovné sestavení
  * Přidání spolehlivé stavu
  * Odebrání spolehlivé stavu

Reliable State Manager sleduje aktuální probíhající transakce. Jediná změna ve stavu transakce, která způsobí, že nebudou vydány oznámení je potvrzuje transakci.

Reliable State Manager udržuje kolekci jako spolehlivého slovníku a spolehlivá fronta spolehlivých stavů. Reliable State Manager aktivuje oznámení při změně této kolekce: Přidá nebo odebere spolehlivé stavu nebo je znovu vytvořit celou kolekci.
Kolekce Reliable State Manager znovu sestaví ve třech případech:

* Obnovení: Při spuštění repliky jej obnoví předchozí stav z disku. Na konci obnovení používá **NotifyStateManagerChangedEventArgs** Chcete-li vyvolat událost, která obsahuje sadu obnovené spolehlivé stavy.
* Úplné kopie: před repliky může připojit k sadě configuration, má má být sestaven. V některých případech to vyžaduje úplnou kopii stavu Reliable State Manager z primární repliky použít nečinná sekundární repliky. Používá Reliable State Manager na sekundární replice **NotifyStateManagerChangedEventArgs** Chcete-li vyvolat událost, která obsahuje sadu spolehlivé stavy, které získaných z primární repliky.
* Obnovení: Ve scénářích zotavení po havárii repliky je možné obnovit stav ze zálohy prostřednictvím **RestoreAsync**. V takovém případě se používá na primární replice Reliable State Manager **NotifyStateManagerChangedEventArgs** Chcete-li vyvolat událost, která obsahuje sadu spolehlivé stavy, které ji obnovit ze zálohy.

Registrace pro oznámení transakce a/nebo oznámení o stavu správce, budete muset zaregistrovat **TransactionChanged** nebo **StateManagerChanged** události v Reliable State Manager. Společné místo, kde můžete zaregistrovat u těchto obslužných rutin událostí je konstruktor třídy stavové služby. Při registraci na konstruktoru nevynechají všechna oznámení, která je způsobena změnou celou dobu životnosti **Telemetryclient**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

**TransactionChanged** obslužná rutina události používá **NotifyTransactionChangedEventArgs** k zajištění podrobných informací o události. Obsahuje vlastnosti akce (například **NotifyTransactionChangedAction.Commit**), který určuje typ změny. Také obsahuje vlastnost transakce, který poskytuje odkaz na transakci, která se změnila.

> [!NOTE]
> V současné době **TransactionChanged** události jsou vyvolány pouze v případě, že je transakce potvrzena. Akce se pak rovná **NotifyTransactionChangedAction.Commit**. Ale v budoucnu mohla být vyvolána události pro jiné typy o změně stavu transakce. Doporučujeme, abyste kontrola akce a zpracování události, pouze pokud je takový, který jste očekávali.
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

**StateManagerChanged** obslužná rutina události používá **NotifyStateManagerChangedEventArgs** k zajištění podrobných informací o události.
**NotifyStateManagerChangedEventArgs** má dvě podtřídy: **NotifyStateManagerRebuildEventArgs** a **NotifyStateManagerSingleEntityChangedEventArgs**.
Pomocí vlastnosti akce v **NotifyStateManagerChangedEventArgs** přetypovat **NotifyStateManagerChangedEventArgs** do správné podtřídy:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** a **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Tady je příklad **StateManagerChanged** obslužné rutina oznámení.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Spolehlivého slovníku oznámení
Spolehlivého slovníku poskytuje oznámení pro následující události:

* Znovu sestavit: Volá se, když **ReliableDictionary** stavu se zotavil z obnovené nebo zkopírovaný místní stavu nebo zálohování.
* Vymazat: Volá se, když stav **ReliableDictionary** se vymazala prostřednictvím **ClearAsync** metody.
* Přidat: Volá se, když položka se přidala do **ReliableDictionary**.
* Aktualizace: Volá se, když položka v **IReliableDictionary** byl aktualizován.
* Odebrat: Volá se, když položka v **IReliableDictionary** byl odstraněn.

Pokud chcete dostávat oznámení spolehlivého slovníku, budete muset zaregistrovat **DictionaryChanged** obslužná rutina události **IReliableDictionary**. Je běžné místo, kde můžete zaregistrovat u těchto obslužných rutin událostí v **ReliableStateManager.StateManagerChanged** přidat oznámení.
Při registraci **IReliableDictionary** se přidá do **Telemetryclient** zajistí, že vám neuniklo všechna naše oznámení.

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
> **ProcessStateManagerSingleEntityNotification** je ukázka metody, která předchozí **OnStateManagerChangedHandler** příklad volá.
> 
> 

Předchozí kód nastaví **IReliableNotificationAsyncCallback** rozhraní spolu s **DictionaryChanged**. Protože **NotifyDictionaryRebuildEventArgs** obsahuje **IAsyncEnumerable** rozhraní – které je potřeba vytvořit výčet asynchronně – oznámení o opětovné sestavení jsou vyvolávány pomocí  **RebuildNotificationAsyncCallback** místo **OnDictionaryChangedHandler**.

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
> V předchozím kódu v rámci zpracování oznámení o opětovné sestavení, nejprve udržována agregovaný stav je vymazán. Protože spolehlivé kolekce je sestaven pomocí nového stavu, všechny předchozí oznámení nejsou relevantní.
> 
> 

**DictionaryChanged** obslužná rutina události používá **NotifyDictionaryChangedEventArgs** k zajištění podrobných informací o události.
**NotifyDictionaryChangedEventArgs** má pět podtřídy. Použijte vlastnost akce v **NotifyDictionaryChangedEventArgs** přetypovat **NotifyDictionaryChangedEventArgs** do správné podtřídy:

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
* *Proveďte* co nejrychleji dokončit oznámení události.
* *Ne* spouštět jakékoli nákladné operace (například vstupně-výstupních operací) jako součást synchronní události.
* *Proveďte* zkontrolujte typ akce před zpracováním událostí. V budoucnu mohou být přidány nové typy akcí.

Tady jsou některé možnosti brát v úvahu:

* Oznámení jsou vyvolávány jako součást provedení operace. Například oznámení o obnovení je vyvolána jako poslední krok operaci obnovení. Obnovení nebude dokončen, dokud se zpracovává událost oznámení.
* Protože oznámení při vyvolání jako součást operace použití, klienti zobrazit pouze oznámení pro místně potvrzeny operace. A vzhledem k tomu, že operace je zaručena jenom místně potvrzeny (jinými slovy, přihlášení), může nebo nemusí být v budoucnu vrátit zpět.
* V cestě znovu jednoho upozornění se aktivuje například pro každou operaci použité. To znamená, že pokud transakce T1 obsahuje Create(X) Delete(X) a Create(X), zobrazí se vám jedno oznámení pro vytvoření X, jeden pro odstranění a jeden pro vytvoření znovu, v uvedeném pořadí.
* U transakcí, které obsahují více operací operace použije v pořadí, ve kterém byly přijaty v primární replice od uživatele.
* Jako součást false průběh zpracování může být některé operace vrátit zpět. Oznámení se generují pro tyto operace vrátit zpět vrácení stavu repliky stabilní bodu. Jeden důležitý rozdíl zpět oznámení je, že se agregují události, které mají duplicitní klíče. Například pokud je se vrátit zpět transakci T1, zobrazí se vám jednoho oznámení Delete(X).

## <a name="next-steps"></a>Další postup
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Rychlý start Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Spolehlivé služby zálohování a obnovení (zotavení po havárii)](service-fabric-reliable-services-backup-restore.md)
* [Referenční informace pro vývojáře pro Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

