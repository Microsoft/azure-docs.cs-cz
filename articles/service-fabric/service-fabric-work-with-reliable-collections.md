---
title: Práce s Reliable Collections
description: Seznamte se s osvědčenými postupy pro práci s spolehlivými kolekcemi v rámci aplikace Service Fabric v Azure.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 908f24cc22c969a34a513ff3fd3ceaa788420620
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787871"
---
# <a name="working-with-reliable-collections"></a>Práce s Reliable Collections
Service Fabric nabízí stavový programovací model dostupný vývojářům .NET prostřednictvím spolehlivých kolekcí. Konkrétně Service Fabric poskytuje spolehlivé slovníkové a spolehlivé třídy front. Při použití těchto tříd je váš stav rozdělený na oddíly (pro škálovatelnost), replikovaný (pro dostupnost) a v rámci oddílu (pro sémantiku KYSELování). Pojďme se podívat na typické použití objektu spolehlivého slovníku a podívat se, co dělá ve skutečnosti.

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
   await Task.Delay(100);
}
```

Všechny operace s objekty spolehlivého slovníku (s výjimkou ClearAsync, který není k disvratné) vyžadují objekt ITransaction. K tomuto objektu je přidružen tento objekt a všechny změny, které se pokoušíte provést u libovolného spolehlivého slovníku a/nebo spolehlivých objektů front v rámci jednoho oddílu. Objekt ITransaction získáte voláním metody StateManager's CreateTransaction oddílu.

Ve výše uvedeném kódu je objekt ITransaction předán metodě AddAsync spolehlivého slovníku. Interně, slovníkové metody, které přijímají klíč, přebírají zámek čtenář/Writer přidružený ke klíči. Pokud metoda upraví hodnotu klíče, metoda pro klíč převezme zámek zápisu a pokud metoda načte jenom z hodnoty klíče, pak se na klíč převezme zámek pro čtení. Vzhledem k tomu, že AddAsync upravuje hodnotu klíče na novou, předanou hodnotu, je pořízen zámek pro zápis klíče. Takže pokud se 2 (nebo více) vlákna pokusí přidat hodnoty stejného klíče současně, jedno vlákno získá zámek pro zápis a ostatní vlákna budou blokovat. Ve výchozím nastavení zablokují pro získání zámku až 4 sekundy. po 4 sekundách metody vyvolávají výjimku TimeoutException. Přetížení metody existují, což vám umožní předat explicitní hodnotu časového limitu, pokud byste chtěli preferovat.

Obvykle napíšete kód pro reakci na TimeoutException tím, že ho zachytíte a zkusíte zopakovat celou operaci (jak je znázorněno v kódu výše). V tomto jednoduchém kódu právě voláme Task. zpoždění před 100 milisekundami. Ve skutečnosti ale můžete být vhodnější pomocí určitého typu exponenciálního zpoždění.

Po získání zámku AddAsync přidá objekt klíč a hodnota odkazy do interního dočasného slovníku přidruženého k objektu ITransaction. K tomu je potřeba zajistit sémantiku čtení, kterou vlastní zápis. To znamená, že po volání AddAsync, pozdější volání TryGetValueAsync pomocí stejného objektu ITransaction vrátí hodnotu, i když jste transakci ještě netvrdili.

> [!NOTE]
> Volání TryGetValueAsync s novou transakcí vrátí odkaz na poslední potvrzenou hodnotu. Neupravujte přímo tento odkaz, protože toto rozhraní obchází mechanismus pro zachování a replikaci změn. Doporučujeme, aby hodnoty byly jen pro čtení, takže jediný způsob, jak změnit hodnotu klíče, je prostřednictvím spolehlivých rozhraní API slovníku.

V dalším kroku AddAsync serializovat klíč a hodnotové objekty pro Bajtová pole a připojí tato pole bajtů do souboru protokolu v místním uzlu. Nakonec AddAsync odešle Bajtová pole všem sekundárním replikám, aby měly stejné informace o klíčích a hodnotách. I když byly informace o klíč/hodnotě zapsány do souboru protokolu, informace nejsou považovány za součást slovníku, dokud není potvrzena transakce, ke které jsou přidruženy.

Ve výše uvedeném kódu volání Commitasync vyvolá výjimka potvrdí všechny operace transakce. Konkrétně připojí informace o potvrzení do souboru protokolu v místním uzlu a odešle záznam o potvrzení také do všech sekundárních replik. Jakmile kvorum (většina) repliky odpovědělo, všechny změny dat jsou považovány za trvalé a všechny zámky přidružené ke klíčům, které byly manipulovány prostřednictvím objektu ITransaction, jsou uvolněny, aby ostatní vlákna a transakce mohly manipulovat se stejnými klíči a jejich hodnotami.

Pokud není voláno Commitasync vyvolá výjimka (obvykle z důvodu vyvolání výjimky), objekt ITransaction se odstraní. Při vyřazování nepotvrzeného objektu ITransaction Service Fabric připojí informace o přerušování do souboru protokolu místního uzlu a nic není nutné odesílat do žádné ze sekundárních replik. A pak všechny zámky přidružené ke klíčům, které byly manipulovány prostřednictvím transakce, jsou uvolněny.

## <a name="volatile-reliable-collections"></a>Volatile Reliable Collections 
V některých úlohách, jako je replikovaná mezipaměť, můžete například tolerovat příležitostné ztráty dat. Zabránění Trvalost dat na disk může při psaní do spolehlivých slovníků umožňovat lepší latenci a propustnost. Kompromisy při nedostatečné Persistence jsou, že pokud dojde ke ztrátě kvora, dojde k úplné ztrátě dat. Vzhledem k tomu, že ztráta kvora představuje vzácnou událost, může vyšší výkon představovat vzácná možnost ztráty dat pro tyto úlohy.

V současné době je nestálá podpora k dispozici pouze pro spolehlivé slovníky a spolehlivé fronty, nikoli ReliableConcurrentQueues. Podrobnosti o tom, jestli se mají používat nestálé kolekce, najdete v seznamu [aspektů](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) .

Pokud chcete ve službě povolit nestálou podporu, nastavte ```HasPersistedState``` příznak v deklaraci typu služby na, například takto ```false``` :
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>Stávající trvalé služby nelze nastavit jako nestálé a naopak. Pokud to chcete udělat, budete muset odstranit existující službu a potom ji nasadit pomocí aktualizovaného příznaku. To znamená, že musíte mít nárok na celou ztrátu dat, pokud chcete změnit ```HasPersistedState``` příznak. 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Běžné nástrah a jak se jim vyhnout
Když teď víte, jak spolehlivé kolekce pracují interně, Podívejme se na některé běžné nepoužívané funkce. Viz následující kód:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Při práci s běžným slovníkem .NET můžete do slovníku přidat klíč nebo hodnotu a pak změnit hodnotu vlastnosti (například LastLogin). Tento kód ale nebude správně fungovat se spolehlivým slovníkem. Nezapomeňte z předchozí diskuze volání AddAsync serializovat objekty klíč/hodnota do bajtových polí a pak je uloží do místního souboru a také je pošle do sekundárních replik. Pokud později změníte vlastnost, změní se hodnota vlastnosti pouze v paměti. nemá vliv na místní soubor ani na data odesílaná do replik. Pokud dojde k chybě procesu, vyvolá se v paměti. Když se spustí nový proces, nebo pokud se jiná replika přestanou primární, bude k dispozici stará hodnota vlastnosti.

Nedá se zdůraznit, jak snadné je udělat si výše uvedený druh chyby. A v případě, že se proces nepracuje, se zobrazí jenom informace o chybě. Správný způsob, jak napsat kód, je jednoduše vrátit dva řádky:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Tady je další příklad ukazující běžnou chybu:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Znovu s běžnými slovníky .NET, kód uvedený výše funguje dobře a jedná se o společný vzor: Vývojář používá ke hledání hodnoty klíč. Pokud hodnota existuje, vývojář změní hodnotu vlastnosti. U spolehlivých kolekcí však tento kód vykazuje stejný problém, jak již bylo popsáno: **objekt nesmíte upravovat, jakmile ho předáte do spolehlivé kolekce.**

Správný způsob aktualizace hodnoty ve spolehlivé kolekci je získat odkaz na existující hodnotu a vzít v úvahu, že objekt, na který odkazuje tento odkaz, je neproměnlivý. Pak vytvořte nový objekt, který je přesnou kopii původního objektu. Nyní můžete změnit stav tohoto nového objektu a zapsat nový objekt do kolekce tak, aby byl serializován do pole bajtů, připojené k místnímu souboru a odeslán do replik. Po potvrzení změn mají objekty v paměti, místní soubor a všechny repliky stejný přesný stav. Vše je dobré!

Následující kód ukazuje správný způsob aktualizace hodnoty ve spolehlivé kolekci:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
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

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definování neměnných datových typů, aby se zabránilo chybě programátora
V ideálním případě chceme, aby kompilátor nahlásil chyby, když omylem vytvoříte kód, který je vhodný pro stav objektu, který byste měli považovat za neměnné. Kompilátor jazyka C# ale nemůže tuto možnost provést. Takže pokud chcete zabránit potenciálním chybám programátora, důrazně doporučujeme, abyste definovali typy, které používáte se spolehlivými kolekcemi, na neměnné typy. Konkrétně to znamená, že se zaměříte na základní typy hodnot (například čísla [Int32, UInt64 atd.], DateTime, GUID, TimeSpan a like). Můžete také použít řetězec. Je nejlepší se vyhnout vlastnostem kolekce jako serializace a deserializace, což může často snížit výkon. Pokud však chcete použít vlastnosti kolekce, důrazně doporučujeme použít. Neměnné knihovny kolekcí ([System. Collections. unmutable](https://www.nuget.org/packages/System.Collections.Immutable/)) netto. Tato knihovna je k dispozici ke stažení z https://nuget.org . Doporučujeme také zapečetění tříd a zpřístupnění polí jen pro čtení, kdykoli to bude možné.

Následující typ UserInfo ukazuje, jak definovat neproměnlivý typ, který využívá výše uvedená doporučení.

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
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

Typ ItemId je také neměnný typ, jak je znázorněno zde:

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
Interně spolehlivé kolekce serializovat vaše objekty pomocí. ČISTÁ třída DataContractSerializer. Serializované objekty jsou trvale uložené na místním disku primární repliky a odesílají se také do sekundárních replik. Vzhledem k tomu, že je vaše služba vyspělá, nejspíš budete chtít změnit druh dat (schématu), které vaše služba vyžaduje. Dávejte přístup k verzím vašich dat s velkou péčí. Nejprve musíte mít vždy oprávnění k deserializaci starých dat. Konkrétně to znamená, že váš kód deserializace musí být nekonečně zpětně kompatibilní: verze 333 vašeho kódu služby musí být schopna pracovat s daty umístěnými ve spolehlivé kolekci, a to od verze 1 před 5 lety.

Kód služby je navíc upgradován o jednu upgradovací doménu v jednom okamžiku. Takže během upgradu máte současně běžet dvě různé verze kódu vaší služby. Musíte zabránit tomu, aby nová verze kódu služby používala nové schéma, protože staré verze kódu služby nemusí být schopné zpracovat nové schéma. Pokud je to možné, měli byste navrhovat jednotlivé verze vaší služby, aby byly kompatibilní s jednou verzí. Konkrétně to znamená, že V1 kódu služby by měl být schopný ignorovat všechny prvky schématu, které nezpracovávají explicitně. Musí být ale schopný ukládat data, která neznají explicitně, a při aktualizaci klíče nebo hodnoty slovníku ho zapsat zpátky.

> [!WARNING]
> I když můžete upravit schéma klíče, je nutné zajistit, aby byl kód hodnoty hash a se stejnými algoritmy stabilní. Pokud změníte způsob, jakým oba tyto algoritmy pracují, nebudete moci znovu vyhledat klíč v rámci spolehlivého slovníku.
> Řetězce .NET lze použít jako klíč, ale použijte samotný řetězec jako klíč – nepoužívejte výsledek String. GetHashCode jako klíč.

Alternativně můžete provést to, co se obvykle označuje jako dva upgrady. Pomocí dvoufázové aktualizace upgradujete službu z verze V1 na v2: v2 obsahuje kód, který ví, jak pracovat s novou změnou schématu, ale tento kód se nespustí. Když kód v2 přečte data V1, bude na něm fungovat a zapisuje data v1. Až se upgrade dokončí napříč všemi doménami upgradu, můžete nějakým způsobem signalizovat spuštěné instance v2, které upgrade dokončil. (Jedním ze způsobů, jak to signalizovat, je zavedení upgradu konfigurace; to je to, co je v dvoufázové fázi upgradu.) Nyní mohou instance v2 číst data V1, převádět je na data v2, pracovat na ní a zapisovat je jako data v2. Když ostatní instance čtou data v2, nemusejí je převádět, stačí na ni pracovat a napíší se data v2.

## <a name="next-steps"></a>Další kroky
Další informace o vytváření kontraktů s dopředné kompatibility dat najdete v článku [kontrakty dat kompatibilní s](/dotnet/framework/wcf/feature-details/forward-compatible-data-contracts) podporou

Další informace o osvědčených postupech pro kontrakty dat týkajících se verzí najdete v tématu [Správa verzí kontraktů dat](/dotnet/framework/wcf/feature-details/data-contract-versioning)

Informace o tom, jak implementovat kontrakty dat odolné vůči verzím, najdete v tématu [zpětná volání serializace odolná](/dotnet/framework/wcf/feature-details/version-tolerant-serialization-callbacks)

Informace o tom, jak poskytnout datovou strukturu, která může spolupracovat napříč více verzemi, najdete v tématu [IExtensibleDataObject](/dotnet/api/system.runtime.serialization.iextensibledataobject) .
