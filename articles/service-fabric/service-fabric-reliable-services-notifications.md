---
title: Oznámení Reliable Services
description: Koncepční dokumentace pro Service Fabric Reliable Services oznámení pro správce Reliable State Manager a Reliable Dictionary
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: f5b48cc6cca2e143c48ed7bdfc99de936be2a227
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784575"
---
# <a name="reliable-services-notifications"></a>Oznámení Reliable Services
Oznámení umožňují klientům sledovat změny provedené v objektu, který zajímá. Oznámení podporují dva typy objektů: *Reliable State Manager* a *Reliable Dictionary*.

Mezi běžné důvody použití oznámení patří:

* Sestavování materializovaná zobrazení, jako jsou sekundární indexy nebo agregovaná filtrovaná zobrazení stavu repliky. Příkladem je seřazený index všech klíčů ve spolehlivém slovníku.
* Odesílání dat monitorování, jako je počet přidaných uživatelů za poslední hodinu.

Oznámení se aktivují jako součást provádění operací. Z toho důvodu by měla být oznámení zpracovávána co nejrychleji a synchronní události by neměly zahrnovat žádné nákladné operace.

## <a name="reliable-state-manager-notifications"></a>Oznámení správce spolehlivého stavu
Správce Reliable State poskytuje oznámení o následujících událostech:

* Transakce
  * Potvrzení
* Správce stavu
  * Opětovné sestavení (Rebuild)
  * Přidání spolehlivého stavu
  * Odebrání spolehlivého stavu

Správce Reliable State sleduje aktuální transakce v transakci. Jediná změna ve stavu transakce, která způsobuje, že je oznámení aktivováno, je transakce potvrzena.

Reliable State Manager udržuje kolekci spolehlivých stavů, jako je spolehlivý slovník a spolehlivá fronta. Správce spolehlivého stavu aktivuje oznámení, když se tato kolekce změní: je přidán nebo odebrán spolehlivý stav nebo je znovu sestavena celá kolekce.
Kolekce Reliable State Manageru se znovu sestaví ve třech případech:

* Obnovení: když se spustí replika, obnoví předchozí stav z disku. Na konci obnovení používá **NotifyStateManagerChangedEventArgs** k vyvolání události, která obsahuje sadu obnovených spolehlivých stavů.
* Úplná kopie: předtím, než se replika může připojit ke konfigurační sadě, je nutné ji sestavit. V některých případech vyžaduje, aby se pro sekundární repliku nečinných dat nastavila úplná kopie stavu spolehlivého správce stavu z primární repliky. Reliable State Manager v sekundární replice používá **NotifyStateManagerChangedEventArgs** k vyvolání události, která obsahuje sadu spolehlivých stavů, které získala z primární repliky.
* Obnovení: ve scénářích zotavení po havárii je možné obnovit stav repliky ze zálohy prostřednictvím **RestoreAsync**. V takových případech používá správce Reliable State na primární replice **NotifyStateManagerChangedEventArgs** k vyvolání události, která obsahuje sadu spolehlivých stavů, které obnovila ze zálohy.

Pokud chcete zaregistrovat oznámení o transakcích a oznámeních správce stavu, musíte se zaregistrovat s **TransactionChanged** nebo **StateManagerChanged** událostmi ve Správci Reliable State Manager. Běžným místem pro registraci těchto obslužných rutin událostí je konstruktor stavové služby. Při registraci do konstruktoru nepřijdete o žádné oznámení, které se v průběhu životnosti **IReliableStateManager** způsobilo změnou.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Obslužná rutina události **TransactionChanged** používá **NotifyTransactionChangedEventArgs** k poskytnutí podrobností o události. Obsahuje vlastnost Action (například **NotifyTransactionChangedAction. Commit**), která určuje typ změny. Obsahuje také transakční vlastnost, která poskytuje odkaz na transakci, která se změnila.

> [!NOTE]
> Dnes jsou události **TransactionChanged** vyvolány pouze v případě, že je transakce potvrzena. Akce se pak rovná **NotifyTransactionChangedAction. Commit**. V budoucnu ale mohou být události vyvolány pro jiné typy změny stavu transakce. Doporučujeme, abyste provedli kontrolu akce a zpracovali událost pouze v případě, že se jedná o tu, kterou očekáváte.
> 
> 

Následuje příklad obslužné rutiny události **TransactionChanged** .

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

Obslužná rutina události **StateManagerChanged** používá **NotifyStateManagerChangedEventArgs** k poskytnutí podrobností o události.
**NotifyStateManagerChangedEventArgs** má dvě podtřídy: **NotifyStateManagerRebuildEventArgs** a **NotifyStateManagerSingleEntityChangedEventArgs**.
Vlastnost Action v **NotifyStateManagerChangedEventArgs** můžete použít k přetypování **NotifyStateManagerChangedEventArgs** na správnou podtřídu:

* **NotifyStateManagerChangedAction. Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction. Add** a **NotifyStateManagerChangedAction. Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Následuje příklad obslužné rutiny oznámení **StateManagerChanged** .

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

## <a name="reliable-dictionary-notifications"></a>Spolehlivé slovníková oznámení
Spolehlivý slovník poskytuje oznámení pro následující události:

* Rebuild: volá se, když **ReliableDictionary** obnovil svůj stav z obnoveného nebo zkopírovaného místního stavu nebo zálohy.
* Clear: volá se, když se stav **ReliableDictionary** vymazal pomocí metody **ClearAsync** .
* Add: volá se, když se do **ReliableDictionary** přidá položka.
* Update: volá se, když se aktualizovala položka v **IReliableDictionary** .
* Remove: volá se, když se odstraní položka v **IReliableDictionary** .

Chcete-li získat spolehlivé upozornění slovníku, je nutné se zaregistrovat v obslužné rutině události **DictionaryChanged** v **IReliableDictionary**. Běžné místo pro registraci s těmito obslužnými rutinami událostí je v **ReliableStateManager. StateManagerChanged** přidat oznámení.
Registrace při přidání **IReliableDictionary** do **IReliableStateManager** zajistí, že nepřijdete o žádná oznámení.

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
> **ProcessStateManagerSingleEntityNotification** je ukázková metoda, kterou předchozí **OnStateManagerChangedHandler** příklad volá.
> 
> 

Předchozí kód nastaví rozhraní **IReliableNotificationAsyncCallback** spolu s **DictionaryChanged**. Vzhledem k tomu, že **NotifyDictionaryRebuildEventArgs** obsahuje rozhraní **IAsyncEnumerable** , které musí být vyhodnoceny jako asynchronní – oznámení o opětovném sestavení jsou spouštěna prostřednictvím **RebuildNotificationAsyncCallback** namísto **OnDictionaryChangedHandler**.

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
> V předchozím kódu, jako součást zpracování oznámení o opětovném sestavení, je nejprve vymazán agregovaný stav. Vzhledem k tomu, že je spolehlivá kolekce znovu sestavena s novým stavem, všechna předchozí oznámení jsou nerelevantní.
> 
> 

Obslužná rutina události **DictionaryChanged** používá **NotifyDictionaryChangedEventArgs** k poskytnutí podrobností o události.
**NotifyDictionaryChangedEventArgs** má pět podtříd. Použijte vlastnost Action v **NotifyDictionaryChangedEventArgs** k přetypování **NotifyDictionaryChangedEventArgs** na správnou podtřídu:

* **NotifyDictionaryChangedAction. Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction. Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction. Add**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction. Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction. Remove**: **NotifyDictionaryItemRemovedEventArgs**

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
* Co nejrychleji *dokončete události* s oznámením.
* V rámci synchronních událostí *neprovádějte žádné* nákladné operace (například operace v/v).
* Před zpracováním události *Projděte typ* akce. V budoucnu mohou být přidány nové typy akcí.

Tady je několik věcí, které je potřeba vzít v úvahu:

* Oznámení se aktivují jako součást provádění operace. Například oznámení obnovení je aktivováno jako poslední krok operace obnovení. Obnovení se nedokončí až po zpracování události oznámení.
* Vzhledem k tomu, že jsou oznámení aktivována v rámci provádění operací, klienti uvidí pouze oznámení pro místně potvrzené operace. A vzhledem k tomu, že operace mají záruku jenom místně potvrzené (jinými slovy, protokolují se), můžou nebo nemusí být v budoucnu navrácené.
* Na cestě k opakovanému použití se pro každou použitou operaci aktivuje jedno oznámení. To znamená, že pokud transakce T1 zahrnuje Create (X), Delete (X) a Create (X), dostanete jedno oznámení o vytvoření X, jednu pro odstranění a jedno pro vytvoření znovu v tomto pořadí.
* Pro transakce, které obsahují více operací, jsou operace aplikovány v pořadí, ve kterém byly přijaty na primární replice od uživatele.
* V rámci zpracování nepravdivého pokroku se některé operace můžou vrátit zpátky. Pro takovéto operace vrácení zpět jsou vyvolána oznámení, když se stav repliky vrátí do stabilního bodu. Jedním z důležitých rozdílů v oznámeních o vrácení zpět je to, že jsou agregované události, které mají duplicitní klíče. Například pokud je transakce T1 nevratná, zobrazí se jedno oznámení, které se má odstranit (X).

## <a name="next-steps"></a>Další kroky
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Rychlý Start Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Reliable Services zálohování a obnovení (zotavení po havárii)](service-fabric-reliable-services-backup-restore.md)
* [Referenční informace pro vývojáře pro spolehlivé kolekce](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
