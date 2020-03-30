---
title: Spolehlivé služby oznámení
description: Koncepční dokumentace pro service fabric spolehlivé služby oznámení pro spolehlivé správce stavu a spolehlivý slovník
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: 1f3239ea1da252ccd84c6572b562756c8fd1677d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639560"
---
# <a name="reliable-services-notifications"></a>Spolehlivé služby oznámení
Oznámení umožňují klientům sledovat změny, které jsou prováděny na objekt, který je zajímá. Oznámení podporují dva typy objektů: *Spolehlivý správce stavu* a Spolehlivý *slovník*.

Běžné důvody pro použití oznámení jsou:

* Vytváření materializovaných zobrazení, jako jsou sekundární indexy nebo agregovaná filtrovaná zobrazení stavu repliky. Příkladem je seřazený index všech klíčů ve spolehlivém slovníku.
* Odesílání dat monitorování, například počtu uživatelů přidaných za poslední hodinu.

Oznámení jsou aktivována jako součást použití operací. Z tohoto důvodu by oznámení měla být zpracována co nejrychleji a synchronní události by neměly zahrnovat žádné nákladné operace.

## <a name="reliable-state-manager-notifications"></a>Spolehlivá oznámení správce stavu
Správce spolehlivého stavu poskytuje oznámení pro následující události:

* Transakce
  * Potvrzení
* Správce státu
  * Opětovné sestavení (Rebuild)
  * Přidání spolehlivého stavu
  * Odstranění spolehlivého stavu

Spolehlivý správce stavu sleduje aktuální transakce během letu. Jediná změna ve stavu transakce, která způsobí, že oznámení, které má být aktivována je transakce potvrzena.

Správce spolehlivého stavu udržuje kolekci spolehlivých stavů, jako je spolehlivý slovník a spolehlivá fronta. Spolehlivé Správce stavu vyvolá oznámení při této změně kolekce: je přidán nebo odebrán spolehlivý stav nebo je znovu sestavena celá kolekce.
Kolekce Reliable State Manager je znovu sestavena ve třech případech:

* Obnovení: Při spuštění repliky obnoví předchozí stav z disku. Na konci obnovení používá **NotifyStateManagerChangedEventArgs** k požáru události, která obsahuje sadu obnovených spolehlivých stavů.
* Úplná kopie: Před replika může připojit konfigurační sadu, musí být sestaven. Někdy to vyžaduje úplnou kopii stavu správce spolehlivého stavu z primární repliky, která má být použita na nečinné sekundární repliky. Správce spolehlivého stavu na sekundární replice používá **NotifyStateManagerChangedEventArgs** k požáru události, která obsahuje sadu spolehlivých stavů, které získala z primární repliky.
* Obnovení: Ve scénářích zotavení po havárii lze stav repliky obnovit ze zálohy prostřednictvím **nástroje RestoreAsync**. V takových případech správce spolehlivého stavu na primární replice používá **NotifyStateManagerChangedEventArgs** k požáru události, která obsahuje sadu spolehlivých stavů, které obnovil ze zálohy.

Chcete-li se zaregistrovat pro oznámení o transakcích nebo oznámení správce stavu, musíte se zaregistrovat u událostí **TransactionChanged** nebo **StateManagerChanged** ve Správci spolehlivého stavu. Běžné místo pro registraci s těmito obslužnými rutinami událostí je konstruktor stavové služby. Když se zaregistrujete na konstruktoru, nezmeškáte žádné oznámení, které je způsobeno změnou během životnosti **IReliableStateManager**.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Obslužná rutina události **TransactionChanged** používá **NotifyTransactionChangedEventArgs** k poskytnutí podrobností o události. Obsahuje vlastnost akce (například **NotifyTransactionChangedAction.Commit),** která určuje typ změny. Obsahuje také vlastnost transakce, která poskytuje odkaz na transakci, která se změnila.

> [!NOTE]
> Dnes **TransactionChanged** události jsou aktivovány pouze v případě, že transakce je potvrzena. Akce se pak rovná **NotifyTransactionChangedAction.Commit**. Ale v budoucnu události mohou být vyvolány pro jiné typy změn stavu transakce. Akci doporučujeme zkontrolovat a událost zpracovat pouze v případě, že se jedná o událost, kterou očekáváte.
> 
> 

Následuje ukázka obslužné rutiny události **TransactionChanged.**

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

Obslužná rutina události StateManagerChanged používá k poskytnutí podrobností o **události.StateManagerChanged** event handler uses **NotifyStateManagerChangedEventArgs** to provide details about the event.
**NotifyStateManagerChangedEventArgs** má dvě podtřídy: **NotifyStateManagerRebuildEventArgs** a **NotifyStateManagerSingleEntityChangedEventArgs**.
Vlastnost akce v **funkci NotifyStateManagerChangedEventArgs použijete** k přetypování **objektu NotifyStateManagerChangedEventArgs** do správné podtřídy:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** a **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Následuje příklad obslužné rutiny oznámení **StateManagerChanged.**

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

## <a name="reliable-dictionary-notifications"></a>Spolehlivé slovníkové oznámení
Spolehlivý slovník poskytuje oznámení pro následující události:

* Znovu sestavit: Volána při **ReliableDictionary** obnovila svůj stav z obnovené nebo zkopírované místní ho stavu nebo zálohy.
* Clear: Voláno při stavu **ReliableDictionary** byla vymazána prostřednictvím **ClearAsync** metody.
* Přidat: Volána, když byla přidána položka **do ReliableDictionary**.
* Aktualizace: Volána při aktualizaci položky v **iReliableDictionary.**
* Odebrat: Voláno, když byla odstraněna položka v **iReliableDictionary.**

Chcete-li získat spolehlivé slovníku oznámení, musíte se zaregistrovat s **DictionaryChanged** obslužnou rutinou události na **IReliableDictionary**. Běžné místo pro registraci s těmito obslužnými rutinami událostí je v oznámení o přidání **Služby ReliableStateManager.StateManagerChanged.**
Registrace při **přidání IReliableDictionary** do **IReliableStateManager** zajišťuje, že nebudete chybět žádné oznámení.

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
> **ProcessStateManagerSingleEntityNotification** je ukázková metoda, kterou volá předchozí příklad **OnStateManagerChangedHandler.**
> 
> 

Předchozí kód nastaví rozhraní **IReliableNotificationAsyncCallback** spolu s **DictionaryChanged**. Protože **NotifyDictionaryRebuildEventArgs** obsahuje rozhraní **IAsyncEnumerable** -- které musí být výčtu asynchronně -- znovu sestavit oznámení jsou aktivována prostřednictvím **RebuildNotificationAsyncCallback** namísto **OnDictionaryChangedHandler**.

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
> V předchozím kódu jako součást zpracování oznámení o obnovení nejprve udržovaný agregovaný stav je vymazána. Vzhledem k tomu, že spolehlivé kolekce je znovu sestavit s novým stavem, všechna předchozí oznámení jsou irelevantní.
> 
> 

Obslužná rutina události DictionaryChanged používá k poskytnutí podrobností o **události.The DictionaryChanged** event handler uses **NotifyDictionaryChangedEventArgs** to provide details about the event.
**NotifyDictionaryChangedEventArgs** má pět podtříd. Pomocí vlastnosti akce v **editoru NotifyDictionaryChangedEventArgs** přetypte do správné podtřídy: **NotifyDictionaryChangedEventArgs**

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove:** **NotifyDictionaryItemRemovedEventArgs**

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
* *Proveďte* co nejrychleji úplné události oznámení.
* *Do not* Neprováděte žádné nákladné operace (například vstupně-tovité operace) jako součást synchronních událostí.
* *Před* zpracováním události zkontrolujte typ akce. V budoucnu mohou být přidány nové typy akcí.

Zde je několik věcí, které je třeba mít na paměti:

* Oznámení jsou aktivována jako součást provádění operace. Například oznámení o obnovení je aktivována jako poslední krok operace obnovení. Obnovení nebude dokončeno, dokud nebude zpracována událost oznámení.
* Vzhledem k tomu, že oznámení jsou aktivována jako součást operace použití, klienti zobrazit pouze oznámení pro místně potvrzené operace. A protože operace jsou zaručeny pouze místně potvrzena (jinými slovy, protokolované), mohou nebo nemusí být vrátit zpět v budoucnu.
* Na cestě opakování je aktivována jedno oznámení pro každou použitou operaci. To znamená, že pokud transakce T1 zahrnuje Create(X), Delete(X) a Create(X), dostanete jedno oznámení pro vytvoření X, jeden pro odstranění a jeden pro vytvoření znovu, v tomto pořadí.
* U transakcí, které obsahují více operací, jsou operace použity v pořadí, ve kterém byly přijaty na primární replice od uživatele.
* Jako součást zpracování false průběhu některé operace může být vrátit zpět. Oznámení jsou vyvolány pro takové operace zpět, válcování stav repliky zpět do stabilního bodu. Jeden důležitý rozdíl oznámení vrátit vrátit do je, že události, které mají duplicitní klíče jsou agregovány. Například pokud transakce T1 se provádí zpět, zobrazí se jediné oznámení Delete(X).

## <a name="next-steps"></a>Další kroky
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Spolehlivý rychlý start služeb](service-fabric-reliable-services-quick-start.md)
* [Spolehlivé služby zálohování a obnovení (zotavení po havárii)](service-fabric-reliable-services-backup-restore.md)
* [Odkaz pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

