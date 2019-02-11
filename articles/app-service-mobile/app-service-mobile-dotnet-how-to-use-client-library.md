---
title: Práce s knihovna spravovaných klientů aplikace App Service Mobile Apps | Dokumentace Microsoftu
description: Další informace o použití klientské knihovny .NET pro Azure App Service Mobile Apps s aplikací pro Windows a Xamarin.
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
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 01294ec8aa65a8405bc99be215008dad66a73d8d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960740"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Jak používat spravovaného klienta pro Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře pomocí spravované klientské knihovny pro Azure App Service Mobile Apps pro Windows a aplikace pro Xamarin. Pokud jste ještě na Mobile Apps, měli byste zvážit nejprve dokončení [Azure Mobile Apps quickstart] [ 1] kurzu. V této příručce se zaměříme na spravované sady SDK na straně klienta. Další informace o sadách SDK na straně serveru pro Mobile Apps, naleznete v dokumentaci k [.NET Server SDK] [ 2] nebo [Node.js Server SDK] [ 3].

## <a name="reference-documentation"></a>Referenční dokumentace
Referenční dokumentace pro klienta SDK je umístěná tady: [Referenční informace k klienta Azure Mobile Apps pro .NET][4].
Můžete také získat několik vzorových klienta v [úložiště GitHub Azure-Samples][5].

## <a name="supported-platforms"></a>Podporované platformy
Na platformě .NET podporuje tyto platformy:

* Vydání pro Xamarin Android pro rozhraní API 19 až 24 (KitKat prostřednictvím verzi Nougat)
* Pro iOS verze 8.0 a novější verzí Xamarin pro iOS
* Univerzální platforma Windows
* Windows Phone 8.1
* Windows Phone 8.0, s výjimkou aplikace Silverlight

"Serveru tok" ověřování používá komponenty WebView pro zobrazené uživatelské rozhraní.  Pokud zařízení není možné zobrazení WebView uživatelského rozhraní, jsou potřeba další metody ověřování.  Tato sada SDK není proto vhodný pro Watch-type nebo podobně jako zařízení s omezeným přístupem.

## <a name="setup"></a>Instalace a požadavky
Předpokládáme, že jste už vytvořili a publikování projektu back-endu mobilní aplikace, který obsahuje alespoň jednu tabulku.  V kódu aplikace v tomto tématu, je v tabulce s názvem `TodoItem` a má následující sloupce: `Id`, `Text`, a `Complete`. Tato tabulka je stejné tabulky vytvořené po dokončení [Azure Mobile Apps quickstart][1].

Odpovídající typ zadaný na straně klienta v jazyce C# je následující třídy:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

[JsonPropertyAttribute] [ 6] se používá k definování *PropertyName* mapování mezi poli klienta a pole tabulky.

Informace o vytváření tabulek v back-endu Mobile Apps, najdete v článku [.NET Server SDK tématu] [ 7] nebo [Node.js Server SDK tématu][8]. Pokud jste vytvořili back-endu mobilní aplikace v tomto rychlém startu pomocí webu Azure portal, můžete také použít **jednoduché tabulky** nastavení [Azure Portal].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Postup: Instalace balíčku sady SDK spravovaného klienta
Použijte jednu z následujících metod instalace balíčku sady SDK spravovaného klienta Mobile Apps z [NuGet][9]:

* **Visual Studio** klikněte pravým tlačítkem na projekt, klikněte na tlačítko **spravovat balíčky NuGet**, vyhledejte `Microsoft.Azure.Mobile.Client` balíček a pak klikněte na **nainstalovat**.
* **Xamarin Studio** klikněte pravým tlačítkem na projekt, klikněte na tlačítko **přidat** > **přidat balíčky NuGet**, vyhledejte `Microsoft.Azure.Mobile.Client `balíček a pak klikněte na tlačítko **přidat balíček** .

V hlavní aktivitě souboru, nezapomeňte přidat následující **pomocí** – příkaz:

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Upozorňujeme, že všechny balíčky podporu odkazované ve vašem projektu Android musí mít stejnou verzi. Sada SDK má `Xamarin.Android.Support.CustomTabs` závislostí pro platformy Android, tak pokud váš projekt používá novější podporu balíčky je nutné nainstalovat tento balíček s požadovanou verzi přímo, aby nedocházelo ke konfliktům.

### <a name="symbolsource"></a>Jak: Práce s symboly ladění v sadě Visual Studio
Symboly pro obor názvů Microsoft.Azure.Mobile jsou k dispozici na [SymbolSource][10].  Odkazovat [SymbolSource pokyny] [ 11] integrovat SymbolSource pomocí sady Visual Studio.

## <a name="create-client"></a>Vytvoření klienta Mobile Apps
Následující kód vytvoří [MobileServiceClient] [ 12] objekt, který se používá pro přístup k back-endu mobilní aplikace.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

V předchozím kódu nahraďte `MOBILE_APP_URL` s adresou URL back-endu mobilní aplikace, která byla nalezena v okně pro back-endu mobilní aplikace v [Azure Portal]. Objekt MobileServiceClient by měl být typu singleton.

## <a name="work-with-tables"></a>Práce s tabulkami
Následující část podrobně popisuje, jak vyhledat a načíst záznamy a úprava dat v tabulce.  Jsou pokryta následující témata:

* [Vytvořit odkaz na tabulku](#instantiating)
* [Dotazování dat](#querying)
* [Filtr vrátil data](#filtering)
* [Řazení vrátil data](#sorting)
* [Vrácení dat na stránkách](#paging)
* [Vyberte sloupce zaškrtnutím](#selecting)
* [Vyhledání záznamu podle Id](#lookingup)
* [Práce s netypové dotazy](#untypedqueries)
* [Vkládání dat](#inserting)
* Aktualizace dat
* [Odstranění dat](#deleting)
* [Řešení konfliktů a optimistického řízení souběžnosti](#optimisticconcurrency)
* [Vytvoření vazby na uživatelské rozhraní Windows](#binding)
* [Změna velikosti stránky](#pagesize)

### <a name="instantiating"></a>Jak: Vytvořit odkaz na tabulku
Veškerý kód, který přistupuje k nebo upravuje data v tabulce back-endu volá funkce na `MobileServiceTable` objektu. Získat odkaz na tabulku voláním [Jít] metodu následujícím způsobem:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Vrácený objekt používá model typovaná serializace. Model netypové serializace je také podporována. Následující příklad [Vytvoří odkaz na tabulku netypový kód]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

Netypové dotazy musíte určit základní řetězec dotazu OData.

### <a name="querying"></a>Jak: Dotazování dat ze služby mobilní aplikace
Tato část popisuje, jak zadávat dotazy na back-end mobilní aplikace, která zahrnuje následující funkce:

* [Filtr vrátil data](#filtering)
* [Řazení vrátil data](#sorting)
* [Vrácení dat na stránkách](#paging)
* [Vyberte sloupce zaškrtnutím](#selecting)
* [Vyhledat data podle ID](#lookingup)

> [!NOTE]
> Velikost stránky řízených serverem se vynucuje zabránit všechny řádky se vrací.  Stránkování uchovává výchozí požadavky pro velké datové sady ze mělo negativní dopad na službu.  Chcete-li vrátit více než 50 řádků, použijte `Skip` a `Take` způsob, jak je popsáno v [vracet data na stránkách](#paging).

### <a name="filtering"></a>Jak: Filtr vrátil data
Následující kód ukazuje, jak filtrovat data zahrnutím `Where` klauzule v dotazu. Vrátí všechny položky z `todoTable` jehož `Complete` rovná vlastnost `false`. [Kde] použije funkce pro řádek filtrování predikátu k dotazu na tabulku.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Můžete zobrazit identifikátor URI požadavku odeslaného do back-endu pomocí kontroly softwaru zprávy, jako jsou nástroje pro vývojáře prohlížeče nebo [Fiddler]. Když se podíváte na identifikátoru URI požadavku, Všimněte si, že řetězec dotazu upravit, aby se:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Tuto žádost OData je přeložen do dotazu SQL serveru SDK:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Funkce, která je předána `Where` metoda může mít libovolný počet podmínek.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

V tomto příkladu by byl přeložen do dotazu SQL serveru SDK:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Tento dotaz můžete také rozdělit do více klauzulí:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Tyto dvě metody jsou ekvivalentní a můžou se používat Zaměnitelně.  Možnost bývalé&mdash;z zřetězení více predikátů v jednom dotazu&mdash;je kompaktní a doporučené.

`Where` Klauzule podporuje operace, které se přeložit na podmnožinu protokolu OData. Mezi operace patří:

* Relační operátory (==,! =, <, < =, >, > =),
* Aritmetické operátory (+, -, /, *, %),
* Číslo přesnosti (Math.Floor, Math.Ceiling)
* Funkce řetězce (Délka podřetězce, nahradit, IndexOf, StartsWith, EndsWith),
* Vlastnosti kalendářních dat (rok, měsíc, den, hodinu, minutu a sekundu),
* Přístup k vlastnostem objektu, a
* Výrazy kombinování každé z těchto operací.

Při zvažování, co Server SDK podporuje, můžete zvážit [dokumentace ke službě OData v3].

### <a name="sorting"></a>Jak: Řazení vrátil data
Následující kód ukazuje, jak řadit včetně dat [Řadit podle] nebo [OrderByDescending] funkce v dotazu. Vrátí položky z `todoTable` seřazeno vzestupně podle `Text` pole.

```csharp
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Jak: Vrácení dat na stránkách
Ve výchozím nastavení back-end vrací jenom prvních 50 řádky. Můžete zvýšit počet řádků, vrácený voláním [Take] metody. Použití `Take` spolu s [Přeskočit] metoda požádat o konkrétní "stránka" celkový sady dat vrácených dotazem. Následující dotaz, při spuštění vrátí první tři položky v tabulce.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Následující upravený dotaz přeskočí první tři výsledky a vrátí následující tři výsledky. Tento dotaz vytvoří druhé "stránky" dat, kde je velikost stránky tři položky.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

[IncludeTotalCount] celkový počet požadavků metody *všechny* záznamy, které by byly vráceny, ignoruje všechny klauzule stránkování/limitace zadali:

```csharp
query = query.IncludeTotalCount();
```

V reálné aplikaci můžete použít dotazy podobně jako v předchozím příkladu se ovládací prvek stránkování nebo srovnatelný uživatelského rozhraní pro navigaci mezi stránkami.

> [!NOTE]
> Pokud chcete přepsat řádek 50 limit v back-end mobilní aplikace, musíte taky použít [EnableQueryAttribute] veřejně metoda GET a určit chování stránkování. Při použití metody, nastaví následující maximální počet vrácených řádcích až 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Jak: Vyberte sloupce zaškrtnutím
Můžete určit, které sada vlastností zahrnout výsledky tak, že přidáte [Výběr] klauzule dotazu. Například následující kód ukazuje, jak vybrat pouze jedno pole a také jak vybírat a formátování více polí:

```csharp
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

Všechny funkce popsané zatím jsou aditivní, takže jsme můžete zachovat řetězení je. Každé volání zřetězené ovlivňuje více dotazu. Další příklad:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Jak: Vyhledat data podle ID
[LookupAsync] funkce umožňuje vyhledat objekty v databázi s konkrétním ID.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Jak: Provádění netypové dotazů
Při provádění dotazu pomocí objektu netypové tabulky, je nutné explicitně zadat řetězec dotazu OData voláním [ReadAsync], jako v následujícím příkladu:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Můžete se vrátit hodnoty JSON, které lze používat jako kontejner objektů. Další informace o JToken a Newtonsoft Json.NET, najdete v článku [Json.NET] lokality.

### <a name="inserting"></a>Jak: Vložit data do back-end mobilní aplikace
Všechny typy klientů musí obsahovat člena s názvem **Id**, což je ve výchozím nastavení řetězec. To **Id** se vyžaduje k provádění operací CRUD a pro offline synchronizaci. Následující kód ukazuje, jak používat [InsertAsync] metodu pro vkládání nových řádků do tabulky. Parametr obsahuje data, která mají být vloženy jako objekt .NET.

```csharp
await todoTable.InsertAsync(todoItem);
```

Pokud není součástí jedinečnou hodnotu ID vlastní `todoItem` při vložení, identifikátor GUID je generován serverem.
Vygenerované Id, které můžete načíst tak, že zkontrolujete objektu po volání se vrátí.

Vložte data bez typu, mohou využít Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Tady je příklad použití e-mailovou adresu jako jedinečný řetězec id:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Práce s ID hodnoty
Mobile Apps podporuje vlastní jedinečný řetězec hodnoty pro tabulky **id** sloupce. Řetězcová hodnota umožňuje aplikacím používat vlastní hodnoty, jako je e-mailové adresy nebo uživatelské jméno pro ID.  Řetězec ID poskytují následující výhody:

* ID se generují přitom latence do databáze.
* Záznamy se snadněji sloučení z různých tabulek nebo databází.
* ID hodnoty můžete lépe integrovat aplikace logiky.

Když řetězcovou hodnotu ID není nastavená na vložený záznam, back-endu mobilní aplikace generuje jedinečnou hodnotu pro ID. Můžete použít [Guid.NewGuid] metoda ke generování vlastních ID hodnoty na straně klienta nebo v back-endu.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Jak: Změna dat v back-end mobilní aplikace
Následující kód ukazuje, jak používat [UpdateAsync] metoda aktualizovat existující záznam se stejným ID se novými informacemi. Parametr obsahuje data, která mají být aktualizovány jako objekt .NET.

```csharp
await todoTable.UpdateAsync(todoItem);
```

K aktualizaci dat bez typu, mohou využít výhod [Json.NET] následujícím způsobem:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

`id` Pole musí být zadán při aktualizaci. Back-endu používá `id` pole k identifikaci řádek, který má aktualizovat. `id` Pole můžete získat výsledek `InsertAsync` volání. `ArgumentException` Je vyvolána, pokud se pokusíte aktualizovat položku bez zadání `id` hodnotu.

### <a name="deleting"></a>Jak: Odstraňování dat v back-end mobilní aplikace
Následující kód ukazuje, jak používat [DeleteAsync] metoda odstranit existující instanci. Instance je identifikován `id` sadu pole na `todoItem`.

```csharp
await todoTable.DeleteAsync(todoItem);
```

Odstranit data bez typu, mohou využít výhod Json.NET následujícím způsobem:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Když vytvoříte žádost o odstranění, musíte zadat ID. Ostatní vlastnosti nejsou předaná do služby nebo jsou ignorovány ve službě. Výsledek `DeleteAsync` volání je obvykle `null`. Výsledek můžete získat ID a zajistěte tak předání `InsertAsync` volání. A `MobileServiceInvalidOperationException` dojde při pokusu o odstranění položky bez zadání `id` pole.

### <a name="optimisticconcurrency"></a>Jak: Použít optimistické řízení souběžnosti pro případ řešení konfliktů
Dva nebo víc klientů může zapsat změny do jedné položce ve stejnou dobu. Bez zjišťování konfliktů by přepsala poslední zápis všech předchozích aktualizací. **Optimistického řízení souběžnosti** předpokládá, že každou transakci potvrdit a proto nepoužívá žádné uzamčení prostředků.  Před potvrzením transakce, optimistického řízení souběžnosti ověřuje, že se data mění žádné jiné transakce. Pokud byla změněna data, spouštění transakce je vrácena zpět.

Mobile Apps podporuje optimistické řízení souběžného Díky sledování změn pro každou položku pomocí `version` systémový vlastnost sloupec, který je definován pro každou tabulku v back-endu mobilní aplikace. Pokaždé, když se aktualizuje záznam, Mobile Apps nastaví `version` vlastnost u záznamu daného novou hodnotu. Při každé žádosti o aktualizaci `version` vlastnosti záznamu zahrnuté v požadavku se porovnává se stejnou vlastnost pro záznam na serveru. Pokud je verze předány s požadavku se neshoduje s back-endu a potom vyvolá klientské knihovny `MobileServicePreconditionFailedException<T>` výjimky. Typ výjimky je součástí je záznam z back-endu obsahující servery verze záznamu. Aplikace pak tyto informace můžete použít se rozhodnout, jestli se má provést žádost o aktualizaci zopakovat se správnou `version` hodnotu z back-endu potvrzení změn.

Definovat v třídě tabulky pro sloupec `version` systémová vlastnost umožňující optimistického řízení souběžnosti. Příklad:

```csharp
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

Aplikace pomocí netypové tabulky umožňují optimistického řízení souběžnosti tak, že nastavíte `Version` příznaku `SystemProperties` tabulky následujícím způsobem.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Kromě povolení optimistickou metodu souběžného, musí také catch `MobileServicePreconditionFailedException<T>` výjimka ve vašem kódu při volání metody [UpdateAsync].  Vyřešte konflikt s použitím správné `version` aktualizovaný záznam a volání [UpdateAsync] k vyřešení záznamu. Následující kód ukazuje, jak vyřešit jednou byl zjištěn konflikt zápisu:

```csharp
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

Další informace najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps] tématu.

### <a name="binding"></a>Jak: Vytvoření vazby dat Mobile Apps na uživatelské rozhraní Windows
Tato část ukazuje, jak zobrazit objekty vrácená data pomocí prvky uživatelského rozhraní v aplikaci Windows.  Následující příklad kódu vytvoří vazbu na zdroj seznamu s dotazem neúplných položek. [MobileServiceCollection] vytvoří kolekci mobilní aplikace s ohledem na vazbu.

```csharp
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

Některé ovládací prvky v spravovaný modul runtime nepodporuje rozhraní volá [ISupportIncrementalLoading]. Toto rozhraní podporuje ovládací prvky uživatel posune stránku požádat o další data. Je integrovaná podpora pro toto rozhraní pro univerzální aplikace pro Windows prostřednictvím [MobileServiceIncrementalLoadingCollection], který automaticky zpracovává volání z ovládacích prvků. Použití `MobileServiceIncrementalLoadingCollection` v aplikacích Windows následujícím způsobem:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Pokud chcete použít novou kolekci na aplikace pro Windows Phone 8 a "Silverlight", použijte `ToCollection` rozšiřující metody na `IMobileServiceTableQuery<T>` a `IMobileServiceTable<T>`. Chcete-li načíst data, zavolejte `LoadMoreItemsAsync()`.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Kdy použít kolekci vytvořenou voláním `ToCollectionAsync` nebo `ToCollection`, získat kolekci, která mohou být vázány na ovládací prvky uživatelského rozhraní.  Tato kolekce je s ohledem na stránkování.  Protože kolekce je načítání dat ze sítě, načítají se někdy nezdaří. Pro zpracování těchto chyb, přepsat `OnException` metodu na `MobileServiceIncrementalLoadingCollection` ke zpracování výjimky vyplývající z volání `LoadMoreItemsAsync`.

Vezměte v úvahu, pokud vaše tabulka má velký počet polí, ale chcete jenom některé z nich zobrazí v ovládacím prvku. Můžete použít pokyny v předchozí části "[vyberte sloupce zaškrtnutím](#selecting)" a vyberte konkrétní sloupce se mají zobrazit v uživatelském rozhraní.

### <a name="pagesize"></a>Změnit velikost stránky
Ve výchozím nastavení Azure Mobile Apps vrátí maximálně 50 položek na požadavek.  Můžete změnit velikost stránkovacího zvýšením maximální velikost stránky na klienta a serveru.  Chcete-li zvýšit požadovaná velikost stránky, zadejte `PullOptions` při použití `PullAsync()`:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Za předpokladu, že jste provedli `PageSize` roven nebo větší než 100 v rámci serveru, požadavek vrátí až 100 položek.

## <a name="#offlinesync"></a>Práce s tabulkami v režimu Offline
Offline tabulky pomocí místního úložiště do úložiště dat SQLite pro použití v režimu offline.  Všechny tabulky, které operace se provádějí proti místní úložiště SQLite místo úložiště serveru pro vzdálený.  Pokud chcete vytvořit tabulku v režimu offline, Příprava projektu:

1. V sadě Visual Studio, klikněte pravým tlačítkem na řešení > **spravovat balíčky NuGet pro řešení...** , vyhledejte a nainstalujte **Microsoft.Azure.Mobile.Client.SQLiteStore** balíček NuGet pro všechny projekty v řešení.
2. (Volitelné) Pro podporu zařízení Windows, nainstalujte některou z následujících balíčků SQLite modulu runtime:

   * **Modul Runtime Windows 8.1:** Nainstalujte [SQLite pro Windows 8.1][3].
   * **Windows Phone 8.1:** Nainstalujte [SQLite pro Windows Phone 8.1][4].
   * **Universal Windows Platform** nainstalovat [SQLite pro Universal Windows][5].
3. (Volitelné). Pro zařízení s Windows, klikněte na tlačítko **odkazy** > **přidat odkaz...** , rozbalte **Windows** složky > **rozšíření**, povolte příslušnou **SQLite pro Windows** SDK společně s **2013 modulu Runtime visual C++ pro Windows** SDK.
    Názvy SQLite SDK se trochu liší s každou platformu Windows.

Bylo možné vytvořit odkaz na tabulku, musí být připravené místního úložiště:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Inicializace Store se obvykle provádí ihned po vytvoření klienta.  **OfflineDbPath** by měl být název souboru vhodný pro použití na všech platformách, které podporujete.  Pokud není plně kvalifikovaná cesta (to znamená, začíná lomítko), pak tato cesta se používá.  Pokud není plně kvalifikovaná cesta, soubor se umístí na místě specifické pro platformu.

* Výchozí cesta je pro iOS a androidem, složce "Osobní soubory".
* Výchozí cesta pro zařízení s Windows, je "Aplikací" složky specifické pro aplikaci.

Odkaz na tabulku lze zjistit pomocí `GetSyncTable<>` metody:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Není nutné k ověření pomocí offline tabulky.  Stačí k ověření, když komunikují s back-end službu.

### <a name="syncoffline"></a>Synchronizace Offline tabulky
Offline tabulky nejsou synchronizované s back-endu ve výchozím nastavení.  Synchronizace je rozdělený do dvou částí.  Samostatně můžete nabízet změny stahování nových položek.  Tady je metoda typické synchronizace:

```csharp
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

Pokud první argument `PullAsync` má hodnotu null, pak se nepoužívá přírůstkové synchronizace.  Každou operaci synchronizace načte všechny záznamy.

Sada SDK provede implicitní `PushAsync()` před stažením záznamy.

Zpracování konfliktů dojde na `PullAsync()` metoda.  Stejným způsobem jako online tabulky můžete vyřešit konflikty.  Konflikt je vytvořen při `PullAsync()` se nazývá místo během insert, update nebo delete. Pokud více konfliktu, jsou spojeny do jednoho MobileServicePushFailedException.  Každou chybu zpracujte samostatně.

## <a name="#customapi"></a>Práce s vlastní rozhraní API
Vlastní rozhraní API vám umožní definovat vlastní koncové body, které zveřejňují funkce serveru, které nejsou mapování pro vložení, aktualizaci, odstranění nebo operace čtení. Pomocí vlastního rozhraní API může mít větší kontrolu nad zasílání zpráv, včetně čtení a nastavení hlavičky HTTP zpráv a definování formátu těla zprávy kromě formátu JSON.

Volat vlastní API voláním jedné z [InvokeApiAsync] metody na straně klienta. Například následující řádek kódu odešle požadavek POST **completeAll** rozhraní API na back-endu:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Tento formulář je typová metoda volání a vyžaduje, aby **MarkAllResult** vrátit typ je definován. Typové a netypové metody jsou podporovány.

Metoda InvokeApiAsync() předřadí '/ api /"k rozhraní API, kterou chcete volat, pokud rozhraní API začíná"/".
Příklad:

* `InvokeApiAsync("completeAll",...)` volá /api/completeAll na back-endu
* `InvokeApiAsync("/.auth/me",...)` volá /.auth/me na back-endu

InvokeApiAsync můžete použít k volání jakékoli WebAPI, včetně těchto WebAPIs, které nejsou definovány pomocí Azure Mobile Apps.  Při použití InvokeApiAsync() odpovídající záhlaví, včetně ověřování záhlaví se posílají s požadavkem.

## <a name="authentication"></a>Ověřování uživatelů
Mobile Apps podporuje ověřování a autorizace uživatelů aplikací pomocí různých externích zprostředkovatelů identity: Facebook, Google, účet Microsoft, Twitter a Azure Active Directory. Můžete nastavit oprávnění pro tabulky, pokud chcete omezit přístup pro určité operace pouze ověřeným uživatelům. Identity ověřeného uživatele můžete také použít k implementaci autorizační pravidla v serverových skriptů. Další informace najdete v kurzu [Přidání ověřování do aplikace].

Jsou podporovány dvě toky ověřování: *klienta spravovat* a *server spravován* toku. Tok spravovat server poskytuje nejjednodušší prostředí pro ověřování, spoléhá na zprostředkovatele webového ověření rozhraní. Závisí na konkrétní zařízení specifickým pro zprostředkovatele sady SDK klienta spravovat tok umožňuje pro hlubší integraci s funkcemi konkrétní zařízení.

> [!NOTE]
> Doporučujeme používat klienta spravovat tok v produkčních aplikacích.

Nastavení ověřování, je třeba aplikaci zaregistrovat pomocí jednoho nebo více zprostředkovatelů identity.  Zprostředkovatele identity vygeneruje ID klienta a tajný kód klienta pro vaši aplikaci.  Tyto hodnoty jsou nastavte v back-endu k povolení ověřování/autorizace služby Azure App Service.  Další informace, postupujte podle podrobné pokyny v tomto kurzu [Přidání ověřování do aplikace].

V této části jsou popsané v následujících tématech:

* [Spravovat klienta ověřování](#clientflow)
* [Spravovat server ověřování](#serverflow)
* [Ukládání do mezipaměti ověřovací token](#caching)

### <a name="clientflow"></a>Spravovat klienta ověřování
Vaše aplikace může nezávisle kontaktovat zprostředkovatele identity a následně poskytnout navrácený token během přihlašování s back-endu. Tento tok klienta umožňuje poskytovat jednotné přihlašování pro uživatele nebo od zprostředkovatele identity načíst další uživatelská data. Tok ověřování klientů je upřednostňovaný použití toku server jako zprostředkovatele identity sada SDK poskytuje více přirozený chování uživatelského prostředí a umožňuje další přizpůsobení.

Příklady jsou k dispozici pro následující vzory ověřování toku na straně klienta:

* [Knihovna ověřování Active Directory](#adal)
* [Facebooku nebo Googlu](#client-facebook)

#### <a name="adal"></a>Ověřování uživatelů pomocí Active Directory Authentication Library
Active Directory Authentication Library (ADAL) můžete použít k ověření spusťte uživatelů z klienta pomocí ověřování Azure Active Directory.

1. Podle konfigurace váš back-end mobilní aplikace pro přihlašování AAD [Postup konfigurace služby App Service pro přihlašování služby Active Directory] kurzu. Ujistěte se, že k dokončení volitelný krok registrace nativní klientské aplikace.
2. V sadě Visual Studio nebo Xamarin Studio, otevřete si projekt a přidejte odkaz na `Microsoft.IdentityModel.Clients.ActiveDirectory` balíček NuGet. Při hledání, zahrnout předběžné verze.
3. Přidejte následující kód do vaší aplikace, podle platformy, které používáte. V každém proveďte následující nahrazení:

   * Nahraďte **INSERT-AUTORITY-KORENOVA** s názvem tenanta, ve kterém jste zřídili vaší aplikace. Formát by měl být https://login.microsoftonline.com/contoso.onmicrosoft.com. Tuto hodnotu je možné zkopírovat ze záložky domény ve službě Azure Active Directory v [Azure Portal].
   * Nahraďte **INSERT-RESOURCE-ID – TADY** s ID klienta pro back-endu mobilní aplikace. Můžete získat ID klienta z **Upřesnit** kartu **nastavení služby Azure Active Directory** na portálu.
   * Nahraďte **vložit klienta ID TADY** s ID klienta, který jste zkopírovali z nativní klientskou aplikaci.
   * Nahraďte **vložení – PŘESMĚROVÁNÍ-URI-TADY** s vaší lokality */.auth/login/done* koncový bod, používat schéma HTTPS. Tato hodnota by měl být podobný *https://contoso.azurewebsites.net/.auth/login/done*.

     Následující kód potřebný pro každou platformu:

     **Windows:**

    ```csharp
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

    ```csharp
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

    ```csharp
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

#### <a name="client-facebook"></a>Jednotné přihlašování pomocí tokenu z Facebooku nebo Googlu
Tok klienta můžete použít, jak je znázorněno v tomto fragmentu kódu pro Facebook nebo Google.

```csharp
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

### <a name="serverflow"></a>Spravovat server ověřování
Jakmile budete zaregistrováni u zprostředkovatele identity, zavolejte [LoginAsync] metodu na [MobileServiceClient] s [MobileServiceAuthenticationProvider] hodnotu vašeho zprostředkovatele. Například následující kód zahájí u serveru toku přihlášení pomocí Facebooku.

```csharp
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

Pokud používáte zprostředkovatelů identity než Facebook, změňte hodnotu vlastnosti [MobileServiceAuthenticationProvider] s hodnotou pro poskytovatele.

V toku serveru službě Azure App Service spravuje tok ověřování OAuth zobrazením přihlašovací stránku vybraného zprostředkovatele.  Jakmile se vrátí zprostředkovatele identity, služby Azure App Service vygeneruje ověřovací token služby App Service. [LoginAsync] metoda vrátí hodnotu [MobileServiceUser], který poskytuje [UserId] ověřeného uživatele a [MobileServiceAuthenticationToken], jako webový token JSON (JWT). Tento token se může uložit do mezipaměti a znovu požívat do vypršení platnosti. Další informace najdete v tématu [ukládání do mezipaměti ověřovací token](#caching).

### <a name="caching"></a>Ukládání do mezipaměti ověřovací token
V některých případech lze se vyhnout volání metody přihlášení po prvním úspěšném ověření uložením ověřovací token od zprostředkovatele.  Můžete použít Microsoft Store a aplikace pro UPW [PasswordVault] pro ukládání do mezipaměti aktuální ověřovací token po úspěšně přihlášení, následujícím způsobem:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

Hodnota ID uživatele je uložená jako uživatelské jméno přihlašovacích údajů a token, který je uložený jako heslo. Na následujících začínající podniky, můžete zkontrolovat **PasswordVault** pro přihlašovací údaje v mezipaměti. Následující příklad používá přihlašovací údaje v mezipaměti, když se nacházejí a v opačném případě se pokusí znovu provést ověření s back-endu:

```csharp
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

Když se odhlásíte uživatele, je potřeba taky odebrat uložené přihlašovací údaje, následujícím způsobem:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Použití aplikace Xamarin [Xamarin.Auth] rozhraní API pro bezpečné ukládání přihlašovacích údajů v **účet** objektu. Příklad použití těchto rozhraní API, najdete v článku [AuthStore.cs] souboru kódu v [ContosoMoments fotografii sdílení ukázky](https://github.com/azure-appservice-samples/ContosoMoments).

Při použití ověřování klienta spravovat mezipaměti můžete také ukládat přístupový token získaný od poskytovatele, jako je Facebook nebo Twitter. Tento token mohou být poskytnuty požádat o nový ověřovací token z back-endu, následujícím způsobem:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Nabízená oznámení
Nabízená oznámení v následujících tématech:

* [Registrace pro nabízená oznámení](#register-for-push)
* [Získat identifikátor SID balíčku Microsoft Store](#package-sid)
* [Zaregistrovat s využitím šablon Cross-platform](#register-xplat)

### <a name="register-for-push"></a>Jak: Registrace pro nabízená oznámení
Klienta Mobile Apps umožňuje zaregistrovat pro nabízená oznámení pomocí Azure Notification Hubs. Při registraci, získat popisovač, který získáte z specifické pro platformu Push Notification Service (PNS). Pak poskytnete tuto hodnotu spolu s žádné značky při vytváření registrace. Následující kód registruje nabízených oznámení v aplikaci Windows služby oznámení Windows (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Pokud nabízíte službě nabízených oznámení Windows, je nutné [získat balíček Microsoft Store SID](#package-sid).  Další informace o aplikace pro Windows, včetně toho, jak zaregistrovat pro šablony registrace najdete v části [Přidání nabízených oznámení do aplikace].

Vyžádání značky z klienta se nepodporuje.  Značka požadavky jsou vynechány tiše z registrace.
Pokud si chcete zaregistrovat zařízení se značkami, vytvořte vlastní rozhraní API, která používá rozhraní API Notification Hubs k provedení registrace vaším jménem.  Volání rozhraní API Custom namísto `RegisterNativeAsync()` metody.

### <a name="package-sid"></a>Jak: Získat identifikátor SID balíčku Microsoft Store
SID balíčku je potřeba pro povolení nabízených oznámení v aplikacích pro Microsoft Store.  Pokud chcete přijímat SID balíčku, registrace aplikace pomocí Microsoft Store.

Získat tuto hodnotu:

1. V Průzkumníku řešení Visual Studio, klikněte pravým tlačítkem na projekt aplikace pro Microsoft Store, klikněte na tlačítko **Store** > **přidružit aplikaci Store...** .
2. V průvodci klikněte na tlačítko **Další**, přihlaste se pomocí účtu Microsoft, zadejte název pro vaši aplikaci v **rezervovat nový název aplikace**, pak klikněte na tlačítko **rezervy**.
3. Po registraci aplikace je úspěšně vytvořen, vyberte název aplikace, klikněte na **Další**a potom klikněte na tlačítko **přidružit**.
4. Přihlaste se k [Windows Dev Center] pomocí Account Microsoft. V části **Moje aplikace**, klikněte na možnost registrace aplikace, který jste vytvořili.
5. Klikněte na tlačítko **správy aplikací** > **identity aplikace**a posuňte se dolů najít vaše **SID balíčku**.

Mnoho používá identifikátor SID balíčku ji považovat za identifikátor URI, v takovém případě budete muset použít *ms-app: / /* schéma. Poznamenejte si verzi balíčku vytvořený zřetězením tuto hodnotu jako předponu SID.

Aplikace Xamarin vyžadovat další kód bude moci zaregistrovat aplikaci spuštěnou v prostředí platformu iOS nebo Android. Další informace naleznete v tématu pro vaši platformu:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Jak: Registrace šablon nabízených oznámení k odesílání oznámení napříč platformami
Pokud chcete zaregistrovat šablony, použijte `RegisterAsync()` metoda se šablonami, následujícím způsobem:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Šablony by měl být `JObject` typy a může obsahovat více šablon ve formátu JSON:

```csharp
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

Metoda **RegisterAsync()** přijímá také sekundární dlaždice:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Všechna klíčová slova jsou zbavíme během registrace pro zabezpečení. Přidání značek na zařízení nebo šablony v rámci instalace naleznete v tématu [práce se serverem back-end .NET SDK pro Azure Mobile Apps].

Odeslat oznámení využívá tyto registrované šablony, najdete v tématu [Rozhraní API pro Notification Hubs].

## <a name="misc"></a>Různé témata
### <a name="errors"></a>Jak: Zpracování chyb
Když dojde k chybě v back-endu, klient SDK vyvolá `MobileServiceInvalidOperationException`.  Následující příklad ukazuje, jak zpracovat výjimku, která se vrátí back-endu:

```csharp
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

Další příklad chybové stavy řešení najdete v [Ukázkové soubory mobilní aplikace]. [LoggingHandler] příklad poskytuje obslužné rutiny protokolování delegátů protokolovat požadavky do back-endu.

### <a name="headers"></a>Jak: Přizpůsobení záhlaví požadavku
Pro podporu vašeho scénáře pro konkrétní aplikaci, potřebujete přizpůsobit komunikaci s back-endu mobilní aplikace. Můžete například přidat vlastní hlavičku každý odchozí požadavek nebo dokonce i změnit stavový kód odpovědi. Můžete použít vlastní [DelegatingHandler], jako v následujícím příkladu:

```csharp
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
[6]: https://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: https://github.com/SymbolSource/SymbolSource
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Přidání ověřování do aplikace]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Synchronizace offline dat v prostředí Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Přidání nabízených oznámení do aplikace]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[Postup konfigurace služby App Service pro přihlašování služby Active Directory]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[Jít]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[Vytvoří odkaz na tabulku netypový kód]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[Řadit podle]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Výběr]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Přeskočit]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[UserId]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[kde]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure Portal]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Rozhraní API pro Notification Hubs]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Ukázkové soubory mobilní aplikace]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[Dokumentace ke službě OData v3]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
