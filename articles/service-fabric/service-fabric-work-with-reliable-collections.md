---
title: Práce s Reliable Collections
description: Seznamte se s osvědčenými postupy pro práci se spolehlivými kolekcemi v rámci aplikace Azure Service Fabric.
ms.topic: conceptual
ms.date: 02/22/2019
ms.openlocfilehash: 4a1f48d9523e5d753c222f0526e210a30e1927e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645969"
---
# <a name="working-with-reliable-collections"></a>Práce s Reliable Collections
Service Fabric nabízí stavový programovací model, který je k dispozici vývojářům rozhraní .NET prostřednictvím spolehlivých kolekcí. Konkrétně Service Fabric poskytuje spolehlivý slovník a spolehlivé třídy fronty. Při použití těchto tříd, váš stav je rozdělen a oddíl (pro škálovatelnost), replikovány (pro dostupnost) a zpracovány v rámci oddílu (pro acid sémantiku). Podívejme se na typické použití spolehlivého slovníkového objektu a uvidíme, co vlastně dělá.

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

Všechny operace na spolehlivé objekty slovníku (s výjimkou ClearAsync, který není vrátit hodnotitelný), vyžadují ITransaction objekt. Tento objekt má přidružené s ním všechny změny, které se pokoušíte provést do spolehlivého slovníku nebo spolehlivé fronty objekty v rámci jednoho oddílu. Objekt ITransaction získáte voláním metody CreateTransaction správce stavu oddílu.

Ve výše uvedeném kódu je objekt ITransaction předán metodě AddAsync spolehlivého slovníku. Interně slovníkové metody, které přijímají klíč, zabírají zámek čtečky a zapisovače přidružené ke klíči. Pokud metoda upraví hodnotu klíče, metoda trvá zámek zápisu na klíč a pokud metoda čte pouze z hodnoty klíče, pak je na klíči přijat zámek pro čtení. Vzhledem k tomu, že AddAsync upravuje hodnotu klíče na novou předanou hodnotu, je přijata zámek zápisu klíče. Pokud se tedy 2 (nebo více) vláken pokusí přidat hodnoty se stejným klíčem současně, jedno vlákno získá zámek zápisu a ostatní vlákna se zablokují. Ve výchozím nastavení metody blokovat po dobu až 4 sekund získat zámek; po 4 sekundách metody vyvolat TimeoutException. Existují přetížení metody umožňuje předat explicitní hodnotu časového času, pokud dáváte přednost.

Obvykle napíšete kód reagovat na TimeoutException zachycením a opakování masy celou operaci (jak je znázorněno ve výše uvedeném kódu). V mém jednoduchém kódu, já jsem jen volání Task.Delay kolem 100 milisekund pokaždé. Ale ve skutečnosti, můžete být lepší pomocí nějaké exponenciální back-off zpoždění místo.

Jakmile je zámek získán, AddAsync přidá odkazy na klíč a hodnotu objektu do interního dočasného slovníku přidruženého k objektu ITransaction. To se provádí poskytnout vám čtení vlastní zápisy sémantiku. To znamená, že po volání AddAsync, pozdější volání TryGetValueAsync (pomocí stejného objektu ITransaction) vrátí hodnotu i v případě, že jste ještě nepotvrzené transakce. Dále AddAsync serializuje objekty klíče a hodnoty do bajtových polí a připojí tato bajtová pole k souboru protokolu v místním uzlu. Nakonec AddAsync odešle bajtová pole do všech sekundárních replik, aby měly stejné informace o klíči a hodnotě. I v případě, že klíč/hodnota informace byla zapsána do souboru protokolu, informace není považován za součást slovníku, dokud transakce, které jsou přidruženy byla potvrzena.

Ve výše uvedeném kódu volání CommitAsync potvrdí všechny operace transakce. Konkrétně připojí informace o potvrzení do souboru protokolu v místním uzlu a také odešle záznam potvrzení do všech sekundárních replik. Jakmile kvorum (většina) replik odpověděl, všechny změny dat jsou považovány za trvalé a všechny zámky spojené s klíči, které byly manipulovány prostřednictvím iTransaction objektu jsou uvolněny tak, aby ostatní vlákna/transakce můžete manipulovat se stejnými klíči a jejich hodnoty.

Pokud CommitAsync není volána (obvykle z důvodu vyvolání výjimky), pak iTransaction objekt získá uvolněn. Při likvidaci nepotvrzeného objektu ITransaction service fabric připojí informace o přerušení do souboru protokolu místního uzlu a nic nemusí být odesláno do žádné sekundární repliky. A pak jsou uvolněny všechny zámky spojené s klíči, které byly manipulovány prostřednictvím transakce.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Časté úskalí a jak se jim vyhnout
Nyní, když chápete, jak spolehlivé sbírky fungují interně, podívejme se na některé běžné zneužití z nich. Viz kód níže:

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

Při práci s běžným slovníkem .NET můžete do slovníku přidat klíč/hodnotu a potom změnit hodnotu vlastnosti (například LastLogin). Tento kód však nebude fungovat správně se spolehlivým slovníkem. Nezapomeňte z předchozí diskuse, volání AddAsync serializuje key/value objekty bajt pole a potom uloží pole do místního souboru a také odešle do sekundární repliky. Pokud později změníte vlastnost, změní se hodnota vlastnosti pouze v paměti; nemá vliv na místní soubor nebo data odeslaná do replik. Pokud dojde k chybě procesu, co je v paměti je zahozen. Při spuštění nového procesu nebo pokud se primární stane jiná replika, je k dispozici stará hodnota vlastnosti.

Nemohu dostatečně zdůraznit, jak snadné je udělat chybu uvedenou výše. A dozvíte se o chybě pouze tehdy, pokud / když proces jde dolů. Správný způsob, jak napsat kód je jednoduše obrátit dva řádky:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Zde je další příklad ukazující běžnou chybu:

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

Opět platí, že s pravidelnými .NET slovníky, výše uvedený kód funguje dobře a je běžný vzor: vývojář používá klíč k vyhledat hodnotu. Pokud hodnota existuje, vývojář změní hodnotu vlastnosti. Však se spolehlivé kolekce, tento kód vykazuje stejný problém jako již bylo popsáno: **nesmíte změnit objekt, jakmile jste mu dal spolehlivé kolekce.**

Správný způsob, jak aktualizovat hodnotu ve spolehlivé kolekci, je získat odkaz na existující hodnotu a zvážit objekt, na který tento odkaz odkazuje. Potom vytvořte nový objekt, který je přesnou kopií původního objektu. Nyní můžete upravit stav tohoto nového objektu a zapsat nový objekt do kolekce tak, aby se serializoval do bajtových polí, připojil se k místnímu souboru a odeslal do replik. Po potvrzení změny, v paměti objekty, místní soubor a všechny repliky mají stejný přesný stav. Všechno je dobré!

Následující kód ukazuje správný způsob aktualizace hodnoty ve spolehlivé kolekci:

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

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definování neměnných datových typů, aby se zabránilo chybě programátora
V ideálním případě bychom chtěli kompilátor hlásit chyby při náhodném vytvoření kódu, který mutuje stav objektu, který byste měli považovat za neměnné. Ale kompilátor Jazyka C# nemá schopnost to provést. Takže, aby se zabránilo potenciální chyby programátora, důrazně doporučujeme definovat typy, které používáte se spolehlivé kolekce neměnné typy. Konkrétně to znamená, že se budete držet typů základních hodnot (například čísla [Int32, UInt64 atd.], DateTime, Guid, TimeSpan a podobně). Můžete také použít String. Je vhodné, aby se zabránilo vlastnosti kolekce jako serializace a jejich rekonstrukci může často poškodit výkon. Pokud však chcete použít vlastnosti kolekce, důrazně doporučujeme použít . NET je neměnné sbírky knihovny ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Tato knihovna je https://nuget.orgk dispozici ke stažení z aplikace . Doporučujeme také utěsnění tříd a vytváření polí jen pro čtení, kdykoli je to možné.

Typ UserInfo níže ukazuje, jak definovat neměnný typ s využitím výše uvedených doporučení.

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

ItemId typ je také neměnný typ, jak je znázorněno zde:

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

## <a name="schema-versioning-upgrades"></a>Správa verzí schématu (upgrady)
Interně spolehlivé kolekce serializovat objekty pomocí . NET DataContractSerializer. Serializované objekty jsou trvalé na místní disk primární repliky a jsou také přenášeny na sekundární repliky. Jak vaše služba dozrává, je pravděpodobné, že budete chtít změnit druh dat (schéma), které vaše služba vyžaduje. Přistupujte k snímání verzí vašich dat s velkou péčí. V první řadě musíte být vždy schopni rekonstruovat stará data. Konkrétně to znamená, že váš kód pro deserializaci musí být nekonečně zpětně kompatibilní: Verze 333 kódu služby musí být schopna pracovat s daty umístěnými ve spolehlivé kolekci podle verze 1 kódu služby před 5 lety.

Kromě toho kód služby je upgradován jednu doménu upgradu najednou. Takže během upgradu máte dvě různé verze kódu služby spuštěné současně. Je třeba se vyhnout tomu, aby nová verze kódu servisu používala nové schéma, protože staré verze kódu servisu nemusí být schopny nové schéma zpracovat. Pokud je to možné, měli byste navrhnout každou verzi služby tak, aby byla předávaná jako kompatibilní s jednou verzí. Konkrétně to znamená, že V1 kódu služby by měl být schopen ignorovat všechny prvky schématu, které explicitně nezpracovává. Musí však být schopen uložit všechna data, o kterých explicitně neví, a zapsat je zpět při aktualizaci klíče nebo hodnoty slovníku.

> [!WARNING]
> Při můžete upravit schéma klíče, je nutné zajistit, že hash kódu klíče a rovná se algoritmy jsou stabilní. Pokud změníte způsob fungování některého z těchto algoritmů, nebudete moci znovu vyhledat klíč ve spolehlivém slovníku.
> Řetězce .NET lze použít jako klíč, ale použít samotný řetězec jako klíč -- nepoužívejte výsledek String.GetHashCode jako klíč.

Případně můžete provést to, co se obvykle označuje jako dva upgrady. S dvoufázový upgrade upgradu, upgradovat službu z V1 na V2: V2 obsahuje kód, který ví, jak se vypořádat s novou změnu schématu, ale tento kód se nespustí. Když kód V2 čte data V1, pracuje s ním a zapisuje data V1. Poté, co je upgrade dokončen ve všech doménách upgradu, můžete nějakým způsobem signalizovat spuštěné instance V2, že upgrade je dokončen. (Jedním ze způsobů, jak to signalizovat, je zavedení upgradu konfigurace; to je to, co dělá tento dvoufázový upgrade.) Nyní instance V2 mohou číst data V1, převádět je na data V2, pracovat s ním a zapsat je jako data V2. Když ostatní instance číst data V2, nemusí převést, oni prostě pracovat na něm a zapsat data V2.

## <a name="next-steps"></a>Další kroky
Další informace o vytváření smluv o datech kompatibilních s předáváním najdete v tématu [Forward-Compatible Data Contracts](https://msdn.microsoft.com/library/ms731083.aspx)

Informace o doporučených postupech pro kontrakty složky s údaji o správě verzí naleznete v [tématu Správa verzí kontraktů dat](https://msdn.microsoft.com/library/ms731138.aspx)

Informace o implementaci kontraktů dat odolných proti verzím naleznete v [tématu Version-Tolerant Serialization](https://msdn.microsoft.com/library/ms733734.aspx)

Chcete-li se dozvědět, jak poskytnout datovou strukturu, která může spolupracovat ve více verzích, naleznete [v tématu IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx)