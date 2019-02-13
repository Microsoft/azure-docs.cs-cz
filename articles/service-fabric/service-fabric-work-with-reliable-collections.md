---
title: Práce s Reliable Collections | Dokumentace Microsoftu
description: Podívejte se na osvědčené postupy pro práci s Reliable Collections.
services: service-fabric
documentationcenter: .net
author: tylermsft
manager: jeanpaul.connock
editor: ''
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/12/2019
ms.author: twhitney
ms.openlocfilehash: e7f0219919fe0569633cc85b89a1a91b1704b269
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114820"
---
# <a name="working-with-reliable-collections"></a>Práce s Reliable Collections
Service Fabric nabízí stavový programovací model k dispozici pro vývojáře na platformě .NET pomocí spolehlivých kolekcí. Konkrétně Service Fabric poskytuje spolehlivé fronty třídy a spolehlivého slovníku. Při použití těchto tříd je svůj stav rozdělit na oddíly (pro zajištění škálovatelnosti), replikuje (dostupnosti) a nepodporuje transakce v rámci oddílu (pro odpovídající zásadám ACID sémantiku). Pojďme podívat se na Typickým použitím spolehlivého slovníku objektu a zobrazit, co se skutečně dělají.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
}
```

Všechny operace na objektech spolehlivého slovníku (s výjimkou ClearAsync, která nevratná), vyžadují ITransaction objektu. Tento objekt k němu má přidružené žádné a všechny změny, které se pokoušíte provést jakékoli spolehlivého slovníku nebo spolehlivé fronty objektů v rámci jednoho oddílu. Získat ITransaction objektu voláním oddílu je metoda CreateTransaction StateManager.

Ve výše uvedeném kódu je objekt ITransaction předaný metodě AddAsync spolehlivého slovníku. Interně slovníku metody, které přijímají klíč zámek čtení/zápis přidružený ke klíči. Pokud metoda změní hodnotu klíče, tato metoda přebírá zámek pro zápis na klíč, a pokud metoda čte pouze z hodnoty klíče, zámek pro čtení je následně přesměrován na klíč. Protože AddAsync změní hodnotu klíče s hodnotou nového, předaným, dochází k zámku pro zápis klíč. Ano 2 (nebo více) vlákna došlo k pokusu o přidání hodnoty se stejným klíčem ve stejnou dobu, jedno vlákno se získat zámek pro zápis a ostatní vlákna budou blokovat. Ve výchozím nastavení, blok metody pro získání zámku; až 4 sekundy Po 4 sekundy vyvolání metody TimeoutException. Přetížení metody existují, abyste mohli předat hodnotu explicitní vypršení časového limitu, pokud si přejete.

Obvykle při psaní kódu reagovat TimeoutException jejímu a celá operace opakování (jak je uvedeno ve výše uvedeném kódu). Jednoduchého kódu stačí telefonické Task.Delay pokaždé, když předávání 100 milisekund. Ale ve skutečnosti může být lepší místo toho použít nějaký druh exponenciální regresní zpoždění.

Jakmile je požadován zámek, AddAsync přidá klíč a hodnotu objektu odkazy na interní dočasné slovníku, která je přidružená k objektu ITransaction. Je to, kde přinášejí sémantiku čtení vlastní zápisů. To znamená, že po zavolání AddAsync pozdější volání TryGetValueAsync (s použitím stejného objektu ITransaction) vrátí hodnotu i v případě zatím ještě potvrzené transakce. V dalším kroku AddAsync serializuje klíč a hodnotu objekty bajtová pole a připojí tato pole bajtů do souboru protokolu v místním uzlu. Nakonec AddAsync odešle bajtová pole do sekundárních replik, mají stejné informace klíč/hodnota. I když informace klíč/hodnota se zapsala do souboru protokolu, informace není považováno za součást slovníku, dokud nebude transakce, které jsou přidruženy byla.

Ve výše uvedeném kódu voláním commitasync vyvolá potvrdí všechny operace transakce. Konkrétně přidá informace o potvrzení do souboru protokolu v místním uzlu a navíc odesílá záznam o zápisu do sekundárních replik. Jakmile odpověděl kvorum replik (většinou), všechny změny dat se považují za trvalé a uvolnění všech zámků přidružené klíče, které byly manipulovat prostřednictvím objektu ITransaction tak ostatní vlákna/transakce můžete pracovat s stejnými klíči a jejich hodnoty.

Pokud commitasync vyvolá nevolá (obvykle z důvodu výjimky vyvolané), načte objekt ITransaction odstraněn. Při vyřazení objektu nepotvrzené ITransaction, Service Fabric připojí přerušení informace do souboru protokolu je místní uzel a není třeba nic odešlou ke kterékoli ze sekundárních replik. A pak uvolnění všech zámků přidružené klíče, které byly manipulovat prostřednictvím transakce.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Běžné nástrahy a jak se jim vyhnout
Teď, když chápete, jak fungují spolehlivé kolekce interně, Pojďme se podívat na některé běžné nevhodnému použití z nich. Viz kód uvedený níže:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Při práci s regulární slovník .NET, je přidání páru klíč/hodnota do slovníku a potom změňte hodnotu vlastnosti (například LastLogin). Tento kód však nebude fungovat správně s spolehlivého slovníku. Mějte na paměti z předchozích diskuse volání AddAsync serializuje objekty klíč/hodnota pro bajtová pole a pak uloží do místního souboru pole a také je odešle do sekundárních replik. Pokud později změníte vlastnost, tím se změní hodnota vlastnosti v paměti. to ale nijak neovlivní místního souboru nebo data odeslaná do replik. Pokud dojde k chybě procesu, co je v paměti je zahozeny. Když se spustí nový proces nebo jiná replika se stane primární, původní hodnota vlastnosti je co je k dispozici.

Nejde mi zátěže dostatečně jak snadné je vytvořit druh výše uvedenou chybu. A pouze dozvíte o chybu pokud/když, že proces ocitne mimo provoz. Správný způsob, jak psát kód je jednoduše vrátit následující dva řádky:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Tady je další příklad znázorňující běžnou chybou:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user’s name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Znovu se pravidelně slovníky .NET výše uvedený kód funguje správně a je běžný vzor: vývojář použije klíč k vyhledání hodnoty. Pokud tato hodnota existuje, vývojář změní hodnoty vlastnosti. Nicméně s reliable collections, tento kód vykazuje stejný problém, které jsou už popsáno: **objekt NESMÍTE měnit, po které jste zadali do spolehlivé kolekce.**

Správný způsob, jak aktualizovat hodnotu ve spolehlivé kolekci, je k získání odkazu na existující hodnotu a vezměte v úvahu objektu, na které odkazuje tento odkaz neměnné. Vytvořte nový objekt, který je přesnou kopii původní objekt. Nyní můžete upravit stav tohoto nového objektu a napsat nový objekt do kolekce tak, aby jeho získá serializován, chcete-li bajtová pole, připojí k místnímu souboru a odeslané do replik. Po potvrzení změn, mají stejné přesném stavu objektů v paměti, místního souboru a všechny repliky. Je vše v pořádku!

Následující kód ukazuje správný způsob, jak aktualizovat hodnotu do spolehlivé kolekce:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
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

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definování typů neměnnými daty aby programátor chyba
V ideálním případě by rádi bychom kompilátor pro hlášení chyb při neúmyslně vytvořit kód, který mění stav objektu, který se má vzít v úvahu neměnné. Ale, C# kompilátoru nemá možnost to udělat. Tak, aby se zabránilo potenciální chyby programátora, důrazně doporučujeme, že definujete typy s reliable collections vám být neměnné typy. Konkrétně to znamená, že Zůstaňte k základní typy hodnot (například čísla [Int32, UInt64, atd.], DateTime, Guid, TimeSpan a podobně). Můžete také použít řetězec. To se nedoporučuje vlastnosti kolekce jako serializaci a deserializaci je můžete často snížit výkon. Nicméně pokud chcete použít vlastnosti kolekce, důrazně doporučujeme použití. Knihovna NET pro neměnné kolekce ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Tato knihovna je k dispozici ke stažení z http://nuget.org. Doporučujeme také zapečetění tříd a provádění pole jen pro čtení, kdykoli je to možné.

Typ informací o uživateli níže ukazuje, jak definovat typ neměnné výhod výše uvedených doporučení.

```csharp
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
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
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

Typ ItemId je také neumlčitelným typem, jak je znázorněno zde:

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Schéma vytváření verzí (upgrade)
Interně Reliable Collections serializovat objekty pomocí. DataContractSerializer vaší sítě. Serializované objekty jsou zachované místní disk primární repliky a přenášejí také do sekundárních replik. Během existence vaší služby, bude pravděpodobně že budete chtít změnit, jaká data (schéma), vaše služba vyžaduje. Postup správy verzí dat s velmi pečlivě. Především musíte vždy být schopná deserializovat stará data. Konkrétně to znamená, že váš kód deserializace musí být neomezeně zpětně kompatibilní: Verze 333 kódu služby musí být schopen zpracovat data umístěná ve spolehlivé kolekci podle verze 1 kódu služby před 5 lety.

Kromě toho kód služby je upgradovaný jednu upgradovací doménu najednou. Proto při upgradu, máte dvě různé verze kódu služby běží souběžně. Je třeba se vyvarovat s novou verzi kódu služby používaly nové schéma, protože starší verze kódu služby nemusí být schopna zpracovávat nové schéma. Pokud je to možné, měli byste navrhnout každé verze služby bude dopředně kompatibilní podle jedné verze. Konkrétně to znamená, že V1 kódu služby měli být schopni ignorovat všechny elementy schématu, který nezpracovává explicitně. Však musí být možné uložit všechny data, která není explicitně vědět o a zápis ji zpět při aktualizaci slovníku klíče nebo hodnoty.

> [!WARNING]
> Při změně schématu klíč, musíte zajistit, že jsou stabilní kód hash si klíč a algoritmy rovná se. Pokud změníte, jak pracovat buď tyto algoritmy, nebude možné nikdy znovu vyhledat klíč do spolehlivého slovníku.
>
>

Alternativně můžete provádět, co se obvykle označuje jako dvě upgrade. Dvoufázové upgradu upgradu služby z V1 na V2: V2 obsahuje kód, který ví, jak zacházet s novou změnu schématu, ale nebude spouštět tento kód. Když V2 kód čte V1 data, funguje na něj a zapisuje V1 data. Poté po dokončení upgradu je přes všechny upgradovacích domén, vám může nějakým způsobem signál spuštěné instance V2 nebude dokončen upgrade. (Jeden ze způsobů, jak signál, toto je zavedení konfigurace upgradu; to je to kvůli tomu dvoufázového upgrade.) Instance V2 můžete nyní číst V1 data, převeďte jej na V2 dat, pracovat s nimi a vypsat jako V2 data. Při čtení dat V2 ostatních instancí není potřeba ho převést, jsou právě pracovat s nimi a zapíše V2 data.

## <a name="next-steps"></a>Další kroky
Další informace o vytváření kontraktů předávání dat kompatibilní, najdete v článku [dopřednou kontraktů dat](https://msdn.microsoft.com/library/ms731083.aspx)

Informace o doporučených postupech ohledně Správa verzí kontraktů dat, naleznete v tématu [Správa verzí kontraktů dat](https://msdn.microsoft.com/library/ms731138.aspx)

Zjistěte, jak implementovat kontraktů dat proti chybám verze, najdete v článku [tolerantní zpětná volání serializace](https://msdn.microsoft.com/library/ms733734.aspx)

Zjistěte, jak poskytnout datová struktura, která dokáže spolupracovat ve více verzích, najdete v článku [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx)