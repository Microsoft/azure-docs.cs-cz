---
title: Práce s knihovnou klienta spravovaného App Service Mobile Apps (Windows | Microsoft Docs
description: Další informace o použití klient .NET pro Azure App Service Mobile Apps s aplikacemi pro Windows a Xamarin.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2017
ms.author: crdun
ms.openlocfilehash: 2b7620611c4587d00612f98c86b5792984ff548b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Jak používat spravovaného klienta pro Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře s využitím spravované klientské knihovny pro Azure App Service mobilní aplikace pro Windows a aplikacemi Xamarin. Pokud jste ještě Mobile Apps, zvažte nejdřív dokončení [rychlý start Azure Mobile Apps] [ 1] kurzu. V této příručce se zaměříme na spravované SDK klienta. Další informace o na straně serveru SDK pro Mobile Apps, naleznete v dokumentaci k [.NET serveru SDK] [ 2] nebo [Node.js Server SDK][3].

## <a name="reference-documentation"></a>Referenční dokumentace
Referenční dokumentaci k nástroji pro klienta SDK se nachází zde: [Azure Mobile Apps .NET – referenční informace klienta][4].
Můžete také vyhledat několik ukázky klienta v [úložiště GitHub Azure-Samples][5].

## <a name="supported-platforms"></a>Podporované platformy
Platformě .NET podporuje tyto platformy:

* Xamarin Android verze pro rozhraní API 19 až 24 (KitKat prostřednictvím cukrovinkách typu nugát)
* Xamarin iOS verze pro iOS verze 8.0 a novější
* Univerzální platforma pro Windows
* Windows Phone 8.1
* Windows Phone 8.0, s výjimkou aplikace Silverlight

"Serveru pohybu" ověřování používá webové zobrazení pro vidění uživatelského rozhraní.  Pokud zařízení není možné zobrazení webové zobrazení uživatelského rozhraní, je potřeba jiných metod ověřování.  Tato sada SDK není proto vhodný pro sledování type nebo podobně jako zařízení s omezeným přístupem.

## <a name="setup"></a>Instalační program a požadavky
Předpokládáme, že jste již vytvořili a publikování projektu back-end mobilní aplikace, která zahrnuje nejméně jednu tabulku.  V kódu aplikace v tomto tématu, je v tabulce s názvem `TodoItem` a obsahuje následující sloupce: `Id`, `Text`, a `Complete`. Tato tabulka je stejné tabulky vytvořit při dokončení [rychlý start Azure Mobile Apps][1].

Odpovídající typu Typ klienta v jazyce C# je následující třídy:

```
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

[JsonPropertyAttribute] [ 6] se používá k definování *PropertyName* mapování mezi klienta pole a pole tabulky.

Informace o vytváření tabulek v váš back-end mobilní aplikace, najdete v článku [tématu .NET serveru SDK] [ 7] nebo [Node.js Server SDK tématu][8]. Pokud jste vytvořili váš back-end mobilní aplikace v portálu Azure pomocí rychlé spuštění, můžete také použít **snadno tabulky** nastavení v [portál Azure].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Postupy: instalace balíčku SDK spravovaného klienta
Použijte jednu z následujících metod k instalaci balíčku SDK spravovaného klienta pro mobilní aplikace od [NuGet][9]:

* **Visual Studio** klikněte pravým tlačítkem na projekt, klikněte na tlačítko **spravovat balíčky NuGet**, vyhledejte `Microsoft.Azure.Mobile.Client` balíček a potom klikněte na **nainstalovat**.
* **Xamarin Studio** klikněte pravým tlačítkem na projekt, klikněte na tlačítko **přidat** > **přidání balíčků NuGet**, vyhledejte `Microsoft.Azure.Mobile.Client `balíček a potom klikněte na tlačítko **přidat balíček** .

V souboru hlavní aktivitu, nezapomeňte přidat následující **pomocí** příkaz:

```
using Microsoft.WindowsAzure.MobileServices;
```

### <a name="symbolsource"></a>Postupy: práce s ladicími symboly v sadě Visual Studio
Symboly pro obor názvů Microsoft.Azure.Mobile jsou k dispozici na [SymbolSource][10].  Odkazovat [SymbolSource pokyny] [ 11] integrovat SymbolSource pomocí sady Visual Studio.

## <a name="create-client"></a>Vytvoření klienta Mobile Apps
Následující kód vytvoří [MobileServiceClient] [ 12] objekt, který se používá pro přístup k vaší back-end mobilní aplikace.

```
var client = new MobileServiceClient("MOBILE_APP_URL");
```

V předchozím kódu, nahraďte `MOBILE_APP_URL` s adresou URL back-end mobilní aplikace, které je najít v okně v back-endu mobilní aplikace [portál Azure]. Objekt MobileServiceClient by měl být typu singleton.

## <a name="work-with-tables"></a>Práce s tabulkami
V následující části podrobně popisují k vyhledání a načtení záznamů a úprava dat v tabulce.  Jsou pokryta následující témata:

* [Vytvořit odkaz na tabulku](#instantiating)
* [Dotaz na data](#querying)
* [Filtr vrátil data](#filtering)
* [Řazení vrátil data](#sorting)
* [Vrátit data na stránkách](#paging)
* [Vyberte konkrétní sloupce](#selecting)
* [Vyhledat záznam podle Id](#lookingup)
* [Práci s netypové dotazy](#untypedqueries)
* [Vkládání dat](#inserting)
* [Aktualizace dat](#updating)
* [Odstraňování dat](#deleting)
* [Řešení konfliktů a optimistickou metodu souběžného zpracování](#optimisticconcurrency)
* [Vytvoření vazby na uživatelské rozhraní Windows](#binding)
* [Změna velikosti stránky](#pagesize)

### <a name="instantiating"></a>Postupy: vytvoření odkaz na tabulku
Všechny kód, který přistupuje k nebo upravuje data v tabulce back-end volání funkce na `MobileServiceTable` objektu. Získat odkaz na tabulku voláním [Funkce GetTable] metoda následujícím způsobem:

```
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Vráceného objektu používá model typovaná serializace. Model netypové serializace je také podporována. Následující příklad [vytvoří odkaz na tabulku netypové]:

```
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

V netypové dotazy je nutné zadat základní řetězec dotazu OData.

### <a name="querying"></a>Postupy: dotaz na data z mobilní aplikace
Tato část popisuje, jak vydávat dotazy back-end mobilní aplikace, která zahrnuje následující funkce:

* [Filtr vrátil data](#filtering)
* [Řazení vrátil data](#sorting)
* [Vrátit data na stránkách](#paging)
* [Vyberte konkrétní sloupce](#selecting)
* [Vyhledávání dat podle ID](#lookingup)

> [!NOTE]
> Velikost stránky řízené serveru se vynucuje zabránit nevrátila všechny řádky.  Stránkování zachová výchozí požadavky pro velké sady dat z mělo negativní dopad na službu.  Chcete-li vrátit více než 50 řádky, použijte `Skip` a `Take` metoda, jak je popsáno v [vracet data na stránkách](#paging).

### <a name="filtering"></a>Postupy: Filtr nevrátil data
Následující kód ukazuje, jak k filtrování dat včetně `Where` klauzuli v dotazu. Vrátí všechny položky z `todoTable` jejichž `Complete` vlastnost rovná `false`. [Kde] funkce se vztahuje řádek filtrování predikátu k dotazu vůči v tabulce.

```
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Můžete zobrazit identifikátor URI požadavek odeslaný back-end pomocí softwaru zpráva kontroly, jako jsou nástroje pro vývojáře prohlížeče nebo [Fiddler]. Pokud se podíváte na identifikátoru URI požadavku, Všimněte si, že je řetězec dotazu upravit:

```
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Tento požadavek OData je přeložen do dotazu SQL Server SDK:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Funkce, která je předána `Where` metoda může mít libovolný počet podmínky.

```
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

V tomto příkladu by byl přeložen do dotazu SQL serveru SDK:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Tento dotaz taky dají rozdělit do více klauzulí:

```
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Tyto dvě metody jsou ekvivalentní a mohou být použity zcela zaměnitelným významem.  Možnost bývalé&mdash;z zřetězení více predikáty v jednom dotazu&mdash;je kompaktnější a doporučené.

`Where` Klauzule podporuje operace, které se přeložit na podmnožinu protokolu OData. Operace zahrnují:

* Relační operátory (==,! =, <, < =, >, > =),
* Aritmetické operátory (+, -, /, *, %),
* Číslo přesnost (Math.Floor, Math.Ceiling)
* Funkce řetězce (délka, Substring, nahraďte, IndexOf, StartsWith, EndsWith),
* Vlastnosti kalendářních dat (rok, měsíc, den, hodinu, minutu, sekundu),
* Přístup k vlastnosti objektu, a
* Výrazy kombinování kteroukoli z těchto operací.

Při určování, co Server SDK podporuje, můžete zvážit [OData v3 dokumentaci].

### <a name="sorting"></a>Postupy: řazení vrátil data
Následující kód ukazuje, jak řadit data zahrnutím [OrderBy] nebo [OrderByDescending] funkce v dotazu. Vrátí položky z `todoTable` seřadit vzestupně podle `Text` pole.

```
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Postupy: vracet data na stránkách
Ve výchozím nastavení vrátí back-end pouze prvních 50 řádků. Můžete zvýšit počet vrácených řádků voláním [trvat] metoda. Použití `Take` spolu s [přeskočit] metoda požádat o konkrétní "stránka" celkový sady dat vrácených dotazem. Následující dotaz, při spuštění, vrátí první tři položky v tabulce.

```
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Následující dotaz revidovaný přeskočí první tři výsledky a vrátí následující tři výsledky. Tento dotaz vytvoří druhý "stránku" dat, kde je velikost stránky tři položky.

```
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

[IncludeTotalCount] metoda požadavky celkový počet pro *všechny* záznamy, které by byly vráceny, ignoruje všechny klauzuli stránkování/limit zadat:

```
query = query.IncludeTotalCount();
```

Ve skutečných aplikaci můžete použít dotazy, podobně jako v předchozím příkladu s na pager ovládací prvek nebo srovnatelný uživatelského rozhraní navigace mezi stránkami.

> [!NOTE]
> Chcete-li přepsat limit 50 řádek v back-end mobilní aplikace, musíte také použít [EnableQueryAttribute] veřejnosti GET – metoda a určují chování stránkování. Při použití metody následující Nastaví maximální vráceny řádky do 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Postupy: výběr konkrétní sloupců
Můžete zadat, která sada vlastností pro zahrnutí do výsledků přidáním [vyberte] klauzule do dotazu. Například následující kód ukazuje, jak vybrat pouze jedno pole a také jak vybrat a formátování více polí:

```
// Select one field -- just the Text
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => todoItem.Text);
List<string> items = await query.ToListAsync();

// Select multiple fields -- both Complete and Text info
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => string.Format("{0} -- {1}",
                    todoItem.Text.PadRight(30), todoItem.Complete ?
                    "Now complete!" : "Incomplete!"));
List<string> items = await query.ToListAsync();
```

Všechny funkce popsané, pokud jsou aditivní, takže jsme můžete zachovat řetězení je. Každý zřetězené volání ovlivňuje více dotazu. Další příklad:

```
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Postupy: vyhledávání dat podle ID
[LookupAsync] funkce lze použít k vyhledání objekty z databáze s konkrétním ID.

```
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Postupy: provádění dotazů bez typu
Při provádění dotazu pomocí objektu bez typu tabulky, je nutné explicitně zadat řetězec dotazu OData voláním [ReadAsync], jako v následujícím příkladu:

```
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Zobrazí zpět JSON hodnoty, které lze používat jako kontejner objektů. Další informace o JToken a Newtonsoft Json.NET, najdete v článku [Json.NET] lokality.

### <a name="inserting"></a>Postupy: vložení dat do back-end mobilní aplikace
Všechny typy klientů musí obsahovat člen s názvem **Id**, což je ve výchozím nastavení řetězec. To **Id** je potřebná k provedení operace CRUD a pro offline synchronizace. Následující kód ukazuje, jak používat [InsertAsync] metodu pro vkládání nových řádků do tabulky. Parametr obsahuje data, která mají být vloženy jako objekt .NET.

```
await todoTable.InsertAsync(todoItem);
```

Pokud není součástí jedinečný vlastní hodnota ID `todoItem` při vložení, je identifikátor GUID generovaný serverem.
Vygenerovaný Id můžete načíst zkontrolováním objekt po volání vrátí.

Pokud chcete vložit bez typu dat, může využít výhod Json.NET:

```
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Tady je příklad pomocí e-mailovou adresu jako id jedinečného řetězce:

```
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Práce s ID hodnoty
Mobilní aplikace podporuje vlastní řetězec jedinečné hodnoty pro tuto tabulku **id** sloupce. Hodnotu řetězce umožňuje aplikacím používat vlastní hodnoty, jako je například e-mailové adresy nebo uživatelské jméno pro ID.  Řetězec ID poskytují následující výhody:

* ID jsou generovány bez provedení výměnu zpráv do databáze.
* Záznamy jsou usnadňují sloučení z různých tabulek nebo databází.
* Hodnoty ID umožňuje lepší integraci s logiku aplikace.

Pokud řetězcová hodnota ID není nastavená na vloženého záznamu, back-end mobilní aplikace generuje jedinečnou hodnotu pro ID. Můžete použít [Guid.NewGuid] metoda ke generování vlastních ID hodnot na straně klienta nebo v back-end.

```
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Postupy: Změna dat v back-end mobilní aplikace
Následující kód ukazuje, jak používat [metod UpdateAsync] metoda aktualizovat existující záznam se stejným ID se novými informacemi. Parametr obsahuje data, která mají být aktualizována jako objekt .NET.

```
await todoTable.UpdateAsync(todoItem);
```

Pokud chcete aktualizovat bez typu dat, může využít výhod [Json.NET] následujícím způsobem:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

`id` Je nutné zadat při aktualizaci. Back-end používá `id` pole k identifikaci řádek, který má aktualizovat. `id` Pole lze získat z výsledku `InsertAsync` volání. `ArgumentException` Se vyvolá, pokud se pokusíte aktualizovat položku bez zadání `id` hodnotu.

### <a name="deleting"></a>Postupy: odstranění dat v back-end mobilní aplikace
Následující kód ukazuje, jak používat [DeleteAsync] metoda odstranit existující instanci. Instance je identifikována `id` na pole sady `todoItem`.

```
await todoTable.DeleteAsync(todoItem);
```

Pokud chcete odstranit bez typu dat, může využít výhod Json.NET následujícím způsobem:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Pokud zadáte požadavek delete, musí být zadaná ID. Ostatní vlastnosti nejsou předaný službu nebo se ignorují na službu. Výsledek `DeleteAsync` volání je obvykle `null`. Nelze získat ID předávat od výsledku `InsertAsync` volání. A `MobileServiceInvalidOperationException` se vyvolá, když se pokusíte odstranit položku bez zadání `id` pole.

### <a name="optimisticconcurrency"></a>Postupy: použití optimistickou metodu souběžného pro řešení konfliktů.
Dvě nebo více klientů může zápisu změn do jedné položce ve stejnou dobu. Bez zjišťování konfliktů by poslední zápis přepsat všechny předchozí aktualizace. **Optimistické řízení souběžného** předpokládá, že každou transakci můžete potvrdit a proto nepoužívá žádné uzamčení prostředků.  Před potvrzením transakce, optimistické řízení souběžného ověřuje, že neexistují žádné další transakce změnil data. Pokud data byla změněna, spouštění transakce je vrácena zpět.

Mobilní aplikace podporuje optimistické řízení souběžného pomocí funkce sledování změn pro každou položku pomocí `version` sloupec vlastností systému, která je definována pro každou tabulku v váš back-end mobilní aplikace. Pokaždé, když je aktualizovat záznam, nastaví Mobile Apps `version` vlastnost pro tento záznam na novou hodnotu. Při každém požadavku aktualizace `version` vlastnost záznamu součástí požadavku se porovnává se stejnou vlastnost pro záznam na serveru. Pokud verze byla dokončena s požadavku se neshoduje s back-end, pak klientské knihovny vyvolá `MobileServicePreconditionFailedException<T>` výjimka. Typ zahrnuty s výjimkou je záznam z back-end obsahující servery verzi záznamu. Aplikace pak může tyto informace slouží k rozhodnout, zda chcete provést aktualizaci požadavků zopakovat se správnou `version` hodnotu z back-end potvrzení změn.

U třídy tabulky pro definovat sloupec `version` systému vlastnost umožňující optimistickou metodu souběžného. Příklad:

```
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

Aplikace, které používají bez typu tabulky povolit optimistickou metodu souběžného nastavením `Version` příznak na `SystemProperties` tabulky následujícím způsobem.

```
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Kromě povolení optimistickou metodu souběžného, musí také catch `MobileServicePreconditionFailedException<T>` výjimka ve vašem kódu při volání metody [metod UpdateAsync].  Vyřešte konflikt s použitím správné `version` aktualizovaný záznam a volání [metod UpdateAsync] k vyřešení záznamu. Následující kód ukazuje, jak vyřešit jednou zjištěn konflikt zápisu:

```
private async void UpdateToDoItem(TodoItem item)
{
    MobileServicePreconditionFailedException<TodoItem> exception = null;

    try
    {
        //update at the remote table
        await todoTable.UpdateAsync(item);
    }
    catch (MobileServicePreconditionFailedException<TodoItem> writeException)
    {
        exception = writeException;
    }

    if (exception != null)
    {
        // Conflict detected, the item has changed since the last query
        // Resolve the conflict between the local and server item
        await ResolveConflict(item, exception.Item);
    }
}


private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
{
    //Ask user to choose the resolution between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        serverItem.Text, localItem.Text),
        "CONFLICT DETECTED - Select a resolution:");

    UICommand localBtn = new UICommand("Commit Local Text");
    UICommand ServerBtn = new UICommand("Leave Server Text");
    msgDialog.Commands.Add(localBtn);
    msgDialog.Commands.Add(ServerBtn);

    localBtn.Invoked = async (IUICommand command) =>
    {
        // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        // catching a MobileServicePreConditionFailedException.
        localItem.Version = serverItem.Version;

        // Updating recursively here just in case another change happened while the user was making a decision
        UpdateToDoItem(localItem);
    };

    ServerBtn.Invoked = async (IUICommand command) =>
    {
        RefreshTodoItems();
    };

    await msgDialog.ShowAsync();
}
```

Další informace najdete v tématu [Offline synchronizací dat v Azure Mobile Apps] tématu.

### <a name="binding"></a>Postupy: vázání Mobile Apps dat do uživatelského rozhraní systému Windows
Tato část ukazuje způsob zobrazení vrácená data objektů s použitím prvky uživatelského rozhraní v aplikaci pro Windows.  Následující příklad kódu vytvoří vazbu na zdroj seznamu pomocí dotazu pro neúplné položky. [MobileServiceCollection] vytvoří kolekci mobilní aplikace využívající technologii vazby.

```
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Některé ovládací prvky v spravovaného modulu runtime podporují rozhraní nazvané [ISupportIncrementalLoading]. Toto rozhraní umožňuje ovládací prvky, co uživatel posune žádost o další data. Je integrovanou podporu pro toto rozhraní pro univerzální aplikace pro Windows prostřednictvím [MobileServiceIncrementalLoadingCollection], který automaticky zpracovává volání z ovládacích prvků. Použití `MobileServiceIncrementalLoadingCollection` v aplikacích Windows následujícím způsobem:

```
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Pokud chcete použít nová kolekce v aplikacích Windows Phone 8 a "Silverlight", použijte `ToCollection` rozšiřující metody na `IMobileServiceTableQuery<T>` a `IMobileServiceTable<T>`. Chcete-li načíst data, volejte `LoadMoreItemsAsync()`.

```
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Při použití kolekce vytvořená voláním `ToCollectionAsync` nebo `ToCollection`, získání kolekce, která mohou být vázány na ovládacích prvků uživatelského rozhraní.  Tato kolekce je deklaracemi stránkování.  Protože kolekce je načítání dat ze sítě, se někdy načítání nezdaří. Zpracovat takové chyby, přepsat `OnException` metodu `MobileServiceIncrementalLoadingCollection` zpracování výjimek, které jsou výsledkem volání `LoadMoreItemsAsync`.

Zvažte, pokud tabulka obsahuje mnoho polí, ale chcete zobrazit některá z nich v vlastního ovládacího prvku. Můžete použít pokyny v předchozí části "[vyberte konkrétní sloupce](#selecting)" a vyberte konkrétní sloupce se mají zobrazit v uživatelském rozhraní.

### <a name="pagesize"></a>Změnit velikost stránky
Azure Mobile Apps vrací maximálně 50 položek na požadavek ve výchozím nastavení.  Můžete změnit velikost stránkovacího zvýšením maximální velikost stránky na klientovi a serveru.  Chcete-li zvýšit velikost požadované stránce, zadejte `PullOptions` při použití `PullAsync()`:

```
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Za předpokladu, že jste udělali `PageSize` rovna nebo větší než 100 v rámci serveru, žádost o vrátí až 100 položek.

## <a name="#offlinesync"></a>Práce s tabulkami Offline
Offline tabulky použijte místní úložiště pro ukládání dat SQLite pro použití v režimu offline.  Všechny tabulky, které se provádějí operace pro místní úložiště SQLite místo úložiště vzdáleného serveru.  Pokud chcete vytvořit offline tabulky, nejdřív připravte projektu:

1. V sadě Visual Studio, klikněte pravým tlačítkem na řešení > **spravovat balíčky NuGet pro řešení...** , vyhledejte a nainstalujte **Microsoft.Azure.Mobile.Client.SQLiteStore** balíček NuGet pro všechny projekty v řešení.
2. (Volitelné) Pro podporu zařízení se systémem Windows, instalovat jeden z následujících balíčků runtime SQLite:

   * **Modul Runtime pro Windows 8.1:** nainstalovat [SQLite pro Windows 8.1][3].
   * **Windows Phone 8.1:** nainstalovat [SQLite pro Windows Phone 8.1][4].
   * **Univerzální platformu Windows** nainstalovat [SQLite for Universal Windows][5].
3. (Volitelné). Zařízení se systémem Windows, klikněte na tlačítko **odkazy** > **přidat odkaz na...** , rozbalte **Windows** složky > **rozšíření**, povolíte příslušné **SQLite pro systém Windows** SDK spolu s **Visual C++ 2013 modulu Runtime pro Windows** SDK.
    Názvy SQLite SDK mírně lišit s každou platformu Windows.

Aby bylo možné vytvořit odkaz na tabulku, musí být připraveno místní úložiště:

```
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Inicializace úložiště se obvykle provádí ihned po vytvoření klienta.  **OfflineDbPath** by měla být název souboru vhodné pro použití na všech platformách, které podporujete.  Pokud se cesta plně kvalifikovanou cestu (tedy začíná lomítko), je použita v této cestě.  Pokud cesta není úplná, soubor je umístěn v umístění specifické pro platformu.

* Pro zařízení s iOS a Android výchozí cesta je složce "Osobní soubory".
* Výchozí cesta pro zařízení s Windows, je složce "data aplikací" specifické pro aplikaci.

Odkaz na tabulku můžete získat pomocí `GetSyncTable<>` metoda:

```
var table = client.GetSyncTable<TodoItem>();
```

Není nutné k ověření pomocí offline tabulky.  Potřebujete jenom ověřování, když jsou komunikaci s back-end službu.

### <a name="syncoffline"></a>Synchronizace Offline tabulky
Offline tabulky nejsou synchronizované s back-end ve výchozím nastavení.  Synchronizace je rozdělit na dvě části.  Doručte změny můžete samostatně stahování nové položky.  Tady je metoda typické synchronizace:

```
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

Pokud první argument `PullAsync` má hodnotu null, není použita přírůstkové synchronizace.  Každé operace synchronizace načte všechny záznamy.

Sada SDK provádí implicitní `PushAsync()` než vytažení záznamů.

Zpracování konfliktů dochází `PullAsync()` metoda.  Stejným způsobem jako online tabulky můžete řešit konflikty.  Vytváří konflikt při `PullAsync()` je volána místo v průběhu insert, update nebo delete. Pokud je v konfliktu více, jsou seskupeny do jednoho MobileServicePushFailedException.  Zpracování každé selhání samostatně.

## <a name="#customapi"></a>Práce s vlastní rozhraní API
Vlastní rozhraní API umožňuje definovat vlastní koncové body, které zveřejňují funkce serveru které není mapovat k typu vložení, aktualizaci, odstranění nebo operace čtení. Pomocí vlastního rozhraní API, může mít větší kontrolu nad zasílání zpráv, včetně čtení a nastavení hlavičky protokolu HTTP zpráv a definování formátu textu zprávy kromě formátu JSON.

Můžete pro volání jedné z volání vlastní rozhraní API [InvokeApiAsync] metody na straně klienta. Například následující řádek kódu odešle požadavek POST do **completeAll** rozhraní API na back-end:

```
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Tento formulář je typu metoda volání a vyžaduje, aby **MarkAllResult** vrátí typ je definován. Typové i bez typu metody jsou podporovány.

Metoda InvokeApiAsync() přidá '/ api /' do rozhraní API, kterou chcete volat, pokud začíná rozhraní API lomítkem (/).
Příklad:

* `InvokeApiAsync("completeAll",...)` volání /api/completeAll na back-end
* `InvokeApiAsync("/.auth/me",...)` volání /.auth/me na back-end

InvokeApiAsync můžete použít k volání žádné WebAPI, včetně těchto WebAPIs, které nejsou definovány s Azure Mobile Apps.  Při použití InvokeApiAsync() odpovídající hlavičky, včetně ověřování hlavičky, odešlou se žádostí.

## <a name="authentication"></a>Ověřování uživatelů
Mobilní aplikace podporuje ověřování a autorizaci uživatelů aplikace pomocí různých zprostředkovatelů externí identity: Facebook, Google, Microsoft Account, Twitter a Azure Active Directory. Můžete nastavit oprávnění pro tabulky, pokud chcete omezit přístup pro určité operace pouze ověřené uživatele. Můžete také použít identitu ověřeného uživatele k implementaci autorizační pravidla v skripty serveru. Další informace najdete v kurzu [přidání ověřování do aplikace].

Jsou podporovány dva ověřování toky: *klienta spravovat* a *server spravovaný* toku. Tok spravovaných server poskytuje nejjednodušší zkušeností ověřování, jako je závislé na poskytovatele webové ověřování rozhraní. Tok spravovaných klienta umožňuje hlubší integrace s funkcemi konkrétní zařízení jako přitom spoléhá na konkrétní zařízení specifické pro poskytovatele sady SDK.

> [!NOTE]
> Doporučujeme používat k klienta spravovat toku ve svých aplikacích produkční.

Nastavení ověřování, je nutné se jeden nebo více poskytovatelů identity zaregistrovat vaší aplikace.  Poskytovatel identity vygeneruje ID klienta a tajný klíč klienta pro vaši aplikaci.  Tyto hodnoty jsou pak uloženy v váš back-end povolit ověřování/autorizace služby Azure App Service.  Další informace, postupujte podle podrobné pokyny v tomto kurzu [přidání ověřování do aplikace].

V této části jsou popsané v následujících tématech:

* [Klienta spravovat ověřování](#clientflow)
* [Spravovaný server ověřování](#serverflow)
* [Ukládání do mezipaměti ověřovací token](#caching)

### <a name="clientflow"></a>Klienta spravovat ověřování
Aplikace můžete nezávisle obraťte se na poskytovatele identity a zadejte token vrácený během přihlašování s váš back-end. Tento tok klienta umožňuje k jednotnému přihlášení pro uživatele nebo k načtení dat další uživatele z poskytovatele identit. Tok ověřování klientů je upřednostňovaný pomocí toku serveru jako zprostředkovatele identity SDK poskytuje více nativní UX chování a umožňuje pro další přizpůsobení.

Příklady jsou k dispozici pro následující způsoby ověřování tok klienta:

* [Knihovna ověřování Active Directory](#adal)
* [Facebook nebo Google](#client-facebook)
* [Live SDK](#client-livesdk)

#### <a name="adal"></a>Ověřuje uživatele pomocí knihovny Active Directory Authentication Library
Active Directory Authentication Library (ADAL) slouží k ověřování uživatelů inicializovat z klienta pomocí ověřování Azure Active Directory.

1. Podle konfigurace váš back-end mobilní aplikace pro přihlašování AAD [jak nakonfigurovat App Service pro přihlášení služby Active Directory] kurzu. Ujistěte se, že dokončení volitelný krok registrace nativní klientskou aplikaci.
2. V sadě Visual Studio nebo Xamarin Studio, otevřete projekt a přidejte odkaz na `Microsoft.IdentityModel.CLients.ActiveDirectory` balíček NuGet. Při hledání, zahrňte předběžné verze.
3. Přidejte následující kód k vaší aplikaci, podle platformy, které používáte. V každé zkontrolujte následující náhrady:

   * Nahraďte **INSERT. AUTORITY zde** s názvem klienta, ve kterém jste zřídili vaší aplikace. Musí být ve formátu https://login.microsoftonline.com/contoso.onmicrosoft.com. Tuto hodnotu lze kopírovat z karty domény v Azure Active Directory v [portál Azure].
   * Nahraďte **INSERT-RESOURCE-ID-zde** s ID klienta pro váš back-end mobilní aplikace. Můžete získat ID klienta z **Upřesnit** v části **nastavení Azure Active Directory** na portálu.
   * Nahraďte **INSERT klienta ID zde** s ID klienta, který jste zkopírovali z nativní klientskou aplikaci.
   * Nahraďte **vložení PŘESMĚROVÁNÍ URI zde** s vaší lokality */.auth/login/done* koncový bod, pomocí schéma HTTPS. Tato hodnota by měla být podobná *https://contoso.azurewebsites.net/.auth/login/done*.

     Následuje kód potřebný pro každou platformu:

     **Windows:**

    ```
    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

     **Xamarin.iOS**

    ```
    private MobileServiceUser user;
    private async Task AuthenticateAsync(UIViewController view)
    {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

     **Xamarin.Android**

    ```
    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
    }
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {

        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ```

#### <a name="client-facebook"></a>Jednotné přihlašování pomocí tokenu z Facebook nebo Google
Tok klienta můžete použít, jak je uvedeno v této fragmentu kódu pro Facebook nebo Google.

```
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
{
    while (user == null)
    {
        string message;
        try
        {
            // Change MobileServiceAuthenticationProvider.Facebook
            // to MobileServiceAuthenticationProvider.Google if using Google auth.
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
            message = string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

#### <a name="client-livesdk"></a>Jednotné přihlašování pomocí Account Microsoft Live SDK
K ověřování uživatelů, je nutné zaregistrovat aplikaci ve středisku pro vývojáře účtu Microsoft. Nakonfigurujte podrobnosti registrace na váš back-end mobilní aplikace. Vytvořit registrace účtu Microsoft a připojte ho k váš back-end mobilní aplikace, proveďte kroky v [svou aplikaci zaregistrovat pro používat přihlašovací údaje účtu Microsoft]. Pokud máte Windows Store a Windows Phone 8 nebo Silverlight verze aplikace, nejprve zaregistrujte verze Windows Store.

Následující kód se ověří pomocí sady SDK za provozu a použije token vrácený pro přihlášení k váš back-end mobilní aplikace.

```
private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
private async System.Threading.Tasks.Task AuthenticateAsync()
{

    // Get the URL the Mobile App backend.
    var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

    // Create the authentication client for Windows Store using the service URL.
    LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
    //// Create the authentication client for Windows Phone using the client ID of the registration.
    //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

    while (session == null)
    {
        // Request the authentication token from the Live authentication service.
        // The wl.basic scope should always be requested.  Other scopes can be added
        LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
        if (result.Status == LiveConnectSessionStatus.Connected)
        {
            session = result.Session;

            // Get information about the logged-in user.
            LiveConnectClient client = new LiveConnectClient(session);
            LiveOperationResult meResult = await client.GetAsync("me");

            // Use the Microsoft account auth token to sign in to App Service.
            MobileServiceUser loginResult = await App.MobileService
                .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

            // Display a personalized sign-in greeting.
            string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
            var message = string.Format("You are now logged in - {0}", loginResult.UserId);
            var dialog = new MessageDialog(message, title);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
        else
        {
            session = null;
            var dialog = new MessageDialog("You must log in.", "Login Required");
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
}
```

Další informace najdete v tématu [Windows Live SDK] dokumentaci.

### <a name="serverflow"></a>Spravovaný server ověřování
Po registraci zprostředkovatele identity volání [LoginAsync] metodu [MobileServiceClient] s [MobileServiceAuthenticationProvider] hodnotu svého poskytovatele. Například následující kód spustí serveru toku přihlášení pomocí Facebooku.

```
private MobileServiceUser user;
private async System.Threading.Tasks.Task Authenticate()
{
    while (user == null)
    {
        string message;
        try
        {
            user = await client
                .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
            message =
                string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

Pokud používáte zprostředkovatele identity než Facebook, změňte hodnotu [MobileServiceAuthenticationProvider] na hodnotu pro poskytovatele.

V toku server Azure App Service spravuje tok ověřování OAuth zobrazením přihlašovací stránky vybraného zprostředkovatele.  Jakmile se identity vrátí zprostředkovatele služby Azure App Service generuje ověřovací token služby App Service. [LoginAsync] metoda vrátí [MobileServiceUser], který poskytuje i [UserId] ověřeného uživatele a [MobileServiceAuthenticationToken], jako webového tokenu JSON (JWT). Tento token se může uložit do mezipaměti a znovu požívat do vypršení platnosti. Další informace najdete v tématu [ukládání do mezipaměti ověřovací token](#caching).

### <a name="caching"></a>Ukládání do mezipaměti ověřovací token
V některých případech volání metody přihlášení můžete zabránit po prvním úspěšném ověření ukládání ověřovací token od zprostředkovatele.  Aplikace Windows Store a UWP můžete použít [PasswordVault] pro ukládání do mezipaměti aktuální ověřovací token po úspěšného přihlášení, následujícím způsobem:

```
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

Hodnota ID uživatele je uložena jako uživatelské jméno pověření a token je uložená jako heslo. Na následujících podniky, můžete zkontrolovat **PasswordVault** pro přihlašovací údaje v mezipaměti. Následující příklad používá pověření uložená v mezipaměti, při jejich zjištění a v opačném případě se pokusí provést ověření znovu s back-end:

```
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

Při odhlášení uživatele, musíte také odebrat uložených přihlašovacích údajů, následujícím způsobem:

```
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Pomocí aplikace Xamarin [Xamarin.Auth] rozhraní API bezpečně uložit přihlašovací údaje v **účet** objektu. Příklad použití tato rozhraní API, najdete v článku [AuthStore.cs] souboru kódu v [ContosoMoments fotografií sdílení ukázka](https://github.com/azure-appservice-samples/ContosoMoments).

Pokud používáte klienta spravovat ověřování, můžete také mezipaměti přístupový token, který zajišťuje poskytovatel například Facebook nebo Twitter. Tento token můžete zadat požádat o nový ověřovací token z back-end, následujícím způsobem:

```
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Nabízená oznámení
V následujících tématech najdete nabízených oznámení:

* [Registrace pro nabízená oznámení](#register-for-push)
* [Získat identifikátor SID balíčku Windows Store](#package-sid)
* [Zaregistrovat se šablonami a platformy](#register-xplat)

### <a name="register-for-push"></a>Postupy: registrace pro nabízená oznámení
Klient pro Mobile Apps umožňuje zaregistrovat pro nabízená oznámení pomocí Azure Notification Hubs. Při registraci, můžete získat popisovač, které jste získali z specifické pro platformu nabízená oznámení služby (PNS). Pak poskytnete tuto hodnotu společně s všechny značky, když vytvoříte registrace. Následující kód registruje aplikace pro Windows pro nabízená oznámení pomocí služby oznámení Windows (WNS):

```
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Pokud nabízíte k WNS, pak musíte [získat identifikátor SID balíčku Windows Store](#package-sid).  Další informace o aplikace pro Windows, včetně toho, jak zaregistrovat pro šablony registrace najdete v části [přidat nabízená oznámení do vaší aplikace].

Požaduje značky z klienta není podporována.  Značka požadavky jsou bezobslužně vyřadit z registrace.
Pokud chcete zaregistrovat zařízení s značkami, vytvořte vlastní rozhraní API, který používá rozhraní API centra oznámení k provedení registrace vaším jménem.  [Volání rozhraní API vlastní](#customapi) místo `RegisterNativeAsync()` metoda.

### <a name="package-sid"></a>Postupy: získání SID balíčku Windows Store
SID balíčku je nutné pro povolení nabízených oznámení v aplikacích pro Windows Store.  Pokud chcete získat SID balíčku, registrace vaší aplikace s Windows Store.

Chcete-li získat tuto hodnotu:

1. V Průzkumníku řešení Visual Studio, klikněte pravým tlačítkem na projekt aplikace Windows Store, klikněte na tlačítko **úložiště** > **přidružit aplikaci ve Store...** .
2. V průvodci klikněte na tlačítko **Další**, přihlaste se pomocí účtu Microsoft, zadejte název aplikace v rámci **rezervovat nový název aplikace**, pak klikněte na tlačítko **rezervy**.
3. Po registraci aplikace je úspěšně vytvořen, vyberte název aplikace, klikněte na **Další**a potom klikněte na **přidružit**.
4. Přihlaste se k [Windows Dev Center] pomocí Account Microsoft. V části **Moje aplikace**, klikněte na možnost registrace aplikací, který jste vytvořili.
5. Klikněte na tlačítko **správy aplikací** > **identity aplikace**a přejděte k části najít váš **SID balíčku**.

Mnoho používá identifikátor SID balíčku s nimi zacházet jako identifikátor URI, v takovém případě budete muset použít *ms aplikace: / /* schéma. Poznamenejte si verzi vašeho balíčku SID tvořena zřetězením tuto hodnotu jako předponu.

Aplikace Xamarin vyžadovat některé další kód, abyste mohli zaregistrovat aplikaci systémem iOS nebo Android platformy. Další informace naleznete v tématu pro vaši platformu:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Postupy: registrace nabízených šablony k odesílání oznámení napříč platformami
Chcete-li zaregistrovat šablony, použijte `RegisterAsync()` metoda s šablonami a následujícím způsobem:

```
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Šablony by měla být `JObject` typy a může obsahovat několik šablon ve formátu JSON:

```
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

    var templates = new JObject
    {
        ["generic-message"] = new JObject
        {
            ["body"] = template,
            ["headers"] = new JObject
            {
                ["X-WNS-Type"] = "wns/toast"
            },
            ["tags"] = new JArray()
        },
        ["more-templates"] = new JObject {...}
    };
    return templates;
}
```

Metoda **RegisterAsync()** přijme také sekundární dlaždice:

```
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Všechny značky se odstraní a rychle během registrace pro zabezpečení. Přidání značek na zařízení nebo šablony v rámci instalace naleznete v tématu [práci s .NET back-end serveru SDK pro Azure Mobile Apps].

Odeslat oznámení využívá tyto registrované šablony, najdete v tématu [rozhraní API centra oznámení].

## <a name="misc"></a>Ostatní témata
### <a name="errors"></a>Postupy: zpracování chyb
Když dojde k chybě v back-end, klient SDK vyvolá `MobileServiceInvalidOperationException`.  Následující příklad ukazuje, jak zpracovat výjimku, která je vrácena back-end:

```
private async void InsertTodoItem(TodoItem todoItem)
{
    // This code inserts a new TodoItem into the database. When the operation completes
    // and App Service has assigned an Id, the item is added to the CollectionView
    try
    {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // Handle error
    }
}
```

Další příklad práci s chybové stavy lze nalézt v [ukázkové soubory aplikací Mobile]. [LoggingHandler] příklad poskytuje obslužnou rutinu delegáta protokolování do protokolu požadavky prováděné na back-end.

### <a name="headers"></a>Postupy: přizpůsobení hlavičky požadavku
Pro podporu váš scénář konkrétní aplikaci, může být potřeba přizpůsobit komunikaci s back-end mobilní aplikace. Můžete například chtít přidat vlastní hlavičku do každého odchozí požadavku nebo dokonce změnit stavový kód odpovědi. Můžete použít vlastní [DelegatingHandler], jako v následujícím příkladu:

```
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
    var newItem = new TodoItem { Text = "Hello world", Complete = false };
    await todoTable.InsertAsync(newItem);
}

public class MyHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage>
        SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        // Change the request-side here based on the HttpRequestMessage
        request.Headers.Add("x-my-header", "my value");

        // Do the request
        var response = await base.SendAsync(request, cancellationToken);

        // Change the response-side here based on the HttpResponseMessage

        // Return the modified response
        return response;
    }
}
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[přidání ověřování do aplikace]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Offline synchronizací dat v Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[přidat nabízená oznámení do vaší aplikace]: app-service-mobile-windows-store-dotnet-get-started-push.md
[svou aplikaci zaregistrovat pro používat přihlašovací údaje účtu Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[jak nakonfigurovat App Service pro přihlášení služby Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[Funkce GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[vytvoří odkaz na tabulku netypové]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[trvat]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[vyberte]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[přeskočit]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[metod UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[UserId]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Kde]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[portál Azure]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Windows Live SDK]: https://msdn.microsoft.com/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[rozhraní API centra oznámení]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[ukázkové soubory aplikací Mobile]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 dokumentaci]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
