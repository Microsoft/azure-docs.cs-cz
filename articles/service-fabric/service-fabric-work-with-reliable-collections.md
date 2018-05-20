---
title: Práce s spolehlivé kolekce | Microsoft Docs
description: Zjistěte, osvědčené postupy pro práci s spolehlivé kolekce.
services: service-fabric
documentationcenter: .net
author: rajak
manager: timlt
editor: ''
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2017
ms.author: rajak
ms.openlocfilehash: 2568e116fdb3f80976d49787877d2ecf68f128ef
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="working-with-reliable-collections"></a>Práce s Reliable Collections
Service Fabric nabízí stavová programovací model k dispozici pro vývojáře .NET prostřednictvím spolehlivé kolekce. Konkrétně Service Fabric nabízí třídy spolehlivé fronty a spolehlivé slovníku. Při použití těchto tříd vašemu stavu je rozdělena na oddíly (pro škálovatelnost), replikovat (pro dostupnosti) a transakční v rámci oddílu (pro ACID sémantiku). Umožňuje podívejte se na Typickým použitím objektu slovník spolehlivé a jaké jeho ve skutečnosti stav.

```csharp

///retry:

try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) {
   await Task.Delay(100, cancellationToken); goto retry;
}
```

Všechny operace v spolehlivé slovník objekty (s výjimkou ClearAsync, což nevratná), vyžadují ITransaction objekt. Tento objekt má přidruženo žádné a všechny změny, které se pokoušíte provést jakékoli spolehlivé slovníku nebo spolehlivé fronty objektů v rámci jednoho oddílu. Získat ITransaction objekt voláním oddílu je metoda CreateTransaction StateManager společnosti.

Ve výše uvedeném kódu je objekt ITransaction předaný metodě AddAsync spolehlivé slovník. Slovník metody, které přijímá klíč interně trvat zámek pro čtečky/zápis přidružené ke klíči. Pokud metoda mění hodnotu klíče, tato metoda přebírá uzamčení pro zápis na klíč, a pokud metoda četl pouze z hodnoty klíče, pak zámek pro čtení se provede na klíč. Vzhledem k tomu, že AddAsync upraví hodnota klíče na nové, předané hodnotu, je převzat uzamčení pro zápis klíče. Ano Pokud vláken 2 (nebo více) došlo k pokusu o přidání hodnot se stejným klíčem ve stejnou dobu, jedno vlákno se získat uzamčení pro zápis a bude blokovat jiná vlákna. Ve výchozím nastavení metody blok pro až 4 sekundy získat zámek; metody po 4 a víc sekund, throw TimeoutException. Přetížení metody existovat, což umožňuje předat hodnotu explicitní vypršení časového limitu, pokud si přejete.

Obvykle psaní kódu reagování na TimeoutException podle jeho zachytávání a opakovat celou operaci (jak je uvedeno ve výše uvedeném kódu). V jednoduchých kódu právě telefonické Task.Delay předávání pokaždé, když 100 milisekund. Ale ve skutečnosti může být lepší místo toho použít nějaký druh exponenciální zpoždění back vypnout.

Jakmile je získat zámek, AddAsync přidá klíč a hodnotu objektu odkazy na interní dočasné slovník, která je přidružená k objektu ITransaction. Důvodem je, že bude potřeba poskytnout sémantiku číst vaše – vlastní – zápis. To znamená, že po zavolání metody AddAsync novější volání TryGetValueAsync (s použitím stejného objektu ITransaction) vrátí hodnotu i v případě, že ještě nebyly potvrzené transakce. V dalším kroku AddAsync serializuje klíč a hodnotu objektů na pole bajtů a připojí tyto bajtová pole do souboru protokolu v místním uzlu. Nakonec AddAsync odešle bajtová pole na všech sekundárních replikách, mají stejné informace klíč/hodnota. I když informace klíč/hodnota byl zapsán do souboru protokolu, informace není považovány za součást slovníku, dokud transakce, které jsou přidružené byla.

Ve výše uvedeném kódu volání CommitAsync potvrdí všechny operace transakce. Konkrétně připojí potvrzení informace do souboru protokolu v místním uzlu a také odesílá potvrzení záznam všech sekundárních replikách. Jakmile má zodpovězených kvora (většinou) replik, všechny změny dat jsou považovány za trvalé a všechny zámky přidružené klíče, které byly manipulovat prostřednictvím objektu ITransaction vydávají tak dalších vláken transakcí můžete upravit stejnými klíči a jejich hodnoty.

Pokud CommitAsync není volána (obvykle kvůli výjimce hlášeny), získá objekt ITransaction zlikvidován. Při zahazování objekt nepotvrzené ITransaction Service Fabric připojí přerušení informace do souboru protokolu je místní uzel a nic potřebuje k odeslání do všech sekundárních replikách. A potom všechny zámky přidružené klíče, které byly pomocí transakce s nimi manipulovat neuvolní.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Běžné nástrahy a jak se vyhnout jejich
Teď, když budete rozumět tomu, jak fungují spolehlivé kolekce interně, Podívejme se na některé běžné nevhodnému použití z nich. Viz následující kód:

```csharp
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Při práci s slovník regulární .NET, můžete přidat klíč/hodnota do slovníku a poté změňte hodnotu vlastnosti (například LastLogin). Tento kód však nebude fungovat správně s slovník spolehlivé. Mějte na paměti z dřívějších diskusní volání AddAsync serializuje objekty klíč/hodnota, které se bajtová pole a pak uloží pole do místního souboru a také je odešle do sekundárních replikách. Pokud později změníte vlastnost, tato operace změní hodnotu vlastnosti v paměti pouze; nemělo vliv místního souboru nebo data odeslaná do replik. Pokud dojde k chybě procesu, co je v paměti je zahozeny. Při spuštění nový proces nebo jiný replika stane primární, původní hodnota vlastnosti je co je k dispozici.

Nelze I vystavila zátěži dostatečně jak je snadné aby druh chybu uvedené výše. A získáte pouze informace o chybu případě, že proces přestane fungovat. Správný způsob, jak napsat kód je jednoduše reverse dva řádky:


```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Zde je další příklad zobrazuje Obvyklou chybou:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Znovu s regulární .NET slovník výše uvedený kód funguje bez problémů a je běžný vzor: vývojář použije klíč k vyhledání hodnoty. Pokud hodnota existuje, vývojáři změní vlastnosti na hodnotu. Však s spolehlivé kolekcí, tento kód vykazuje stejný problém už popsané: **objekt nesmí změnit, po které jste zadali na kolekci spolehlivé.**

Správný způsob aktualizujte hodnotu v spolehlivé kolekci, je-li stávající hodnotu a zvažte objektu, na kterou odkazuje tento odkaz neměnné. Pak vytvořte nový objekt, který je přesnou kopii původní objekt. Nyní můžete upravit stav tento nový objekt a zapsat nový objekt do kolekce tak, aby získá serializovat na pole bajtů, připojí k místního souboru a odeslat do replik. Po potvrzení změny, mají přesném stavu, ve stejné objekty v paměti, místního souboru a všechny repliky. Všechny je dobré!

Následující kód ukazuje správný způsob aktualizujte hodnotu v spolehlivé kolekci:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definování typů neměnné dat, aby se zabránilo chybě programátory
V ideálním případě by rádi bychom znali kompilátoru zprávy o chybách, když náhodně vytvořit kód, který mění stavu objektu, který se má vzít v úvahu neměnné. Ale kompilátor jazyka C# nemá možnost to udělat. Tak, aby se zabránilo potenciální chyby programátory, důrazně doporučujeme, aby definování typů pomocí spolehlivé kolekce se nedá změnit typy. Konkrétně to znamená, že přilepit na základní typy hodnot (například čísla [Int32, UInt64 atd.], DateTime, Guid, časový interval a podobně). A samozřejmě můžete také použít řetězec. Je vyhýbat se vlastnosti kolekce jako serializaci a deserializaci je můžete často může narušit výkonnost. Ale pokud budete chtít použít vlastnosti kolekce, důrazně doporučujeme použít. Knihovna neměnné kolekce na NET ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Tato knihovna je k dispozici ke stažení http://nuget.org. Doporučujeme také zapečetění tříd a provádění pole jen pro čtení, kdykoli je to možné.

Typ informací o uživateli níže ukazuje, jak definovat typ neměnné využívat výhod zmíněnými doporučení.

```csharp

[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

Typ ItemId je také neměnné typu, jak je vidět tady:

```csharp

[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Verze schématu (upgrade)
Interně serializovat spolehlivé kolekce objektů s použitím. NET na DataContractSerializer. Serializovaných objektů jsou ukládaný na místním disku primární repliky a přenášejí na sekundárních replikách. Během existence služby, je pravděpodobné, že budete chtít změnit druhu dat (schéma), vaše služba vyžaduje. Správa verzí vašich dat s velmi pečlivě musí postupovat. Především musíte být vždy možné deserializovat stará data. Konkrétně to znamená deserializace kód musí být nekonečnou zpětně kompatibilní: 333 verze kódu služby musí být schopen pracovat umístěna v kolekci spolehlivé pomocí verze 1 kódu služby před 5 let.

Kromě toho kódu služby je upgradovaná jednu upgradovací doménu najednou. Ano při upgradu, máte dvě různé verze vaší služby kód spuštěný současně. Je nutné nemuseli novou verzi kódu služby používaly nové schéma jako staré verze kódu služby nemusí být schopna zpracovávat nové schéma. Pokud je to možné, měli byste navrhnout každou verzi služby jako dopředně kompatibilní verze 1. Konkrétně to znamená, že V1 kódu služby byste měli mít ignorovat všechny prvky schématu, který nezpracovává explicitně. Ale musí být možné uložit data, které není explicitně vědět o a jednoduše zápis zpátky se při aktualizaci slovníku klíče nebo hodnoty.

> [!WARNING]
> Při změně schématu klíče, je nutné zajistit, že váš klíč hodnota hash a algoritmů rovná jsou stabilní. Pokud změníte způsob buď tyto algoritmy fungovat, nebudete moci vyhledávat klíč v rámci slovníku spolehlivé někdy znovu.
>
>

Alternativně můžete provádět, co se obvykle označuje jako 2 fáze upgradu. Fáze 2 inovaci, upgradu služby V1 na V2: V2 obsahuje kód, který umí jak nakládat s novou změnu schématu, ale tento kód není spuštěn. Pokud kód V2 čte V1 data, pracuje na něm a zapisuje V1 data. Potom po dokončení upgradu je napříč doménami všechny upgradu, můžete mohou nějakým způsobem signál spuštěné instance V2 dokončení upgradu je. (Jeden ze způsobů, jak signál jde zavádět konfigurace upgradu; to je to díky upgradu fáze 2.) Instance V2 můžete nyní, čtení dat V1, převést na V2 data, pracovat s nimi a vypsat jako V2 data. Když ostatní instance načíst V2 data, není potřeba ji převést, se právě pracovat s nimi a zápis dat V2.

## <a name="next-steps"></a>Další kroky
Další informace o vytváření dopředně kompatibilní datové kontrakty, najdete v části [kontrakty dat dopřednou](https://msdn.microsoft.com/library/ms731083.aspx).

Osvědčené postupy v Správa verzí kontraktů dat naleznete v tématu [Správa verzí kontraktů dat](https://msdn.microsoft.com/library/ms731138.aspx).

Zjistěte, jak implementovat kontrakty odolný vůči chybám dat verze, najdete v tématu [verze proti chybám zpětná volání serializace tolerantní](https://msdn.microsoft.com/library/ms733734.aspx).

Zjistěte, jak zajistit datová struktura, která dokáže spolupracovat napříč více verzí, najdete v tématu [objekt IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
