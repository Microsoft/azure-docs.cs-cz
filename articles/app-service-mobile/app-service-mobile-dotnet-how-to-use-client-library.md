---
title: Práce se spravovanou klientskou knihovnou
description: Přečtěte si, jak používat knihovnu klienta .NET pro mobilní aplikace Služby Azure App Service s aplikacemi pro Windows a Xamarin.
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1c9fba3c13cc6e5476377d59130a95a2edaa324d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249370"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Jak používat spravovaného klienta pro Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Přehled
Tato příručka ukazuje, jak provádět běžné scénáře pomocí spravované klientské knihovny pro mobilní aplikace Služby Azure App Service pro aplikace pro Windows a Xamarin. Pokud s mobilními aplikacemi teprve začínáte, měli byste zvážit, zda nejprve nedokončíte kurz [rychlého spuštění azure mobilních aplikací.][1] V této příručce se zaměřujeme na spravovanou sadou SDK na straně klienta. Další informace o sadách SDK na straně serveru pro mobilní aplikace naleznete v dokumentaci k [sada .NET Server SDK][2] nebo [sady Node.js Server SDK][3].

## <a name="reference-documentation"></a>Referenční dokumentace
Referenční dokumentace pro klientskou sdksadu se nachází zde: [Azure Mobile Apps .NET odkaz na klienta][4].
Můžete také najít několik ukázek klientů v [úložišti GitHub Azure-Samples][5].

## <a name="supported-platforms"></a>Podporované platformy
Platforma .NET podporuje následující platformy:

* Xamarin Android zprávy pro API 19 až 24 (KitKat přes Nougat)
* Verze Xamarin u iOS pro iOS verze 8.0 a novější
* Univerzální platforma Windows
* Windows Phone 8,1
* Windows Phone 8.0 s výjimkou aplikací Silverlight

Ověřování "servertok" používá WebView pro prezentované uživatelské ho dohledu.  Pokud zařízení není schopno prezentovat uživatelské rozhraní WebView, jsou zapotřebí další metody ověřování.  Tato sada SDK tedy není vhodná pro zařízení typu Watch nebo podobně omezená zařízení.

## <a name="setup-and-prerequisites"></a><a name="setup"></a>Nastavení a požadavky
Předpokládáme, že jste již vytvořili a publikovali back-endový projekt mobilní aplikace, který obsahuje alespoň jednu tabulku.  V kódu použitém v tomto tématu je tabulka `TodoItem` pojmenována a má následující sloupce: `Id`, `Text`a `Complete`. Tato tabulka je stejná tabulka vytvořená po dokončení [rychlého startu mobilních aplikací Azure][1].

Odpovídající typ typu na straně klienta v c# je následující třída:

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

Atribut [JsonPropertyAttribute][6] se používá k definování mapování *PropertyName* mezi klientským polem a polem tabulky.

Informace o vytváření tabulek v back-endu mobilních aplikací naleznete v [tématu sady .NET Server SDK][7] nebo [v tématu Sady SDK serveru Node.js.][8] Pokud jste vytvořili back-end mobilní aplikace na webu Azure Portal pomocí rychlého startu, můžete také použít nastavení **Jednoduché tabulky** na webu [Azure Portal].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Postup: Instalace balíčku sady SDK spravovaného klienta
K instalaci balíčku sady SDK spravovaného klienta pro mobilní aplikace z [nugetu][9]použijte jednu z následujících metod :

* **Visual Studio** Klikněte pravým tlačítkem myši na projekt, `Microsoft.Azure.Mobile.Client` klikněte na Spravovat **balíčky NuGet**, vyhledejte balíček a potom klikněte na **Instalovat**.
* **Studio Xamarin** Klikněte pravým tlačítkem myši na projekt, klikněte na **Přidat** > **balíčky NuGet**, vyhledejte `Microsoft.Azure.Mobile.Client` balíček a potom klikněte na Přidat **balíček**.

V hlavním souboru aktivit nezapomeňte přidat následující **příkaz using:**

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Upozorňujeme, že všechny balíčky podporu odkazované ve vašem projektu Android musí mít stejnou verzi. Sada SDK `Xamarin.Android.Support.CustomTabs` má závislost pro platformu Android, takže pokud váš projekt používá novější balíčky podpory, musíte nainstalovat tento balíček s požadovanou verzí přímo, abyste se vyhnuli konfliktům.

### <a name="how-to-work-with-debug-symbols-in-visual-studio"></a><a name="symbolsource"></a>Postup: Práce se symboly ladění v sadě Visual Studio
Symboly pro obor názvů Microsoft.Azure.Mobile jsou k dispozici na [SymbolsSource][10].  Informace o integraci zdroje symbolů s visual studio naleznete v [pokynech zdroje symbolů.][11]

## <a name="create-the-mobile-apps-client"></a><a name="create-client"></a>Vytvoření klienta mobilních aplikací
Následující kód vytvoří objekt [MobileServiceClient,][12] který se používá pro přístup k back-endu mobilní aplikace.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

V předchozím kódu nahraďte `MOBILE_APP_URL` adresou URL back-endu mobilní aplikace, která se nachází v okně back-endu mobilní aplikace na webu Azure [Portal]. Objekt MobileServiceClient by měl být singleton.

## <a name="work-with-tables"></a>Práce s tabulkami
V následující části je podrobně uvedeno, jak vyhledávat a načítat záznamy a upravovat data v tabulce.  Jsou popsána následující témata:

* [Vytvoření odkazu na tabulku](#instantiating)
* [Dotazování dat](#querying)
* [Filtrvrácená data](#filtering)
* [Řazení vrácených dat](#sorting)
* [Vrácení dat na stránkách](#paging)
* [Výběr konkrétních sloupců](#selecting)
* [Vyvyhledávání záznamu podle id](#lookingup)
* [Vyřizování netypových dotazů](#untypedqueries)
* [Vkládání dat](#inserting)
* Aktualizace dat
* [Odstranění dat](#deleting)
* [Řešení konfliktů a optimistická souběžnost](#optimisticconcurrency)
* [Vazba na uživatelské rozhraní systému Windows](#binding)
* [Změna velikosti stránky](#pagesize)

### <a name="how-to-create-a-table-reference"></a><a name="instantiating"></a>Postup: Vytvoření odkazu na tabulku
Veškerý kód, který přistupuje nebo upravuje data v `MobileServiceTable` back-endové tabulce, volá funkce na objektu. Získat odkaz na tabulku voláním [GetTable] metoda, takto:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Vrácený objekt používá zadaný model serializace. Netypový model serializace je také podporován. Následující příklad [vytvoří odkaz na tabulku bez typu]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

V netypových dotazech je nutné zadat základní řetězec dotazu OData.

### <a name="how-to-query-data-from-your-mobile-app"></a><a name="querying"></a>Postup: Dotazování na data z mobilní aplikace
Tato část popisuje, jak vydávat dotazy na back-end mobilní aplikace, který zahrnuje následující funkce:

* [Filtrvrácená data](#filtering)
* [Řazení vrácených dat](#sorting)
* [Vrácení dat na stránkách](#paging)
* [Výběr konkrétních sloupců](#selecting)
* [Vyhledávání dat podle ID](#lookingup)

> [!NOTE]
> Velikost stránky řízené serverem je vynucena, aby se zabránilo vrácení všech řádků.  Stránkování udržuje výchozí požadavky na velké sady dat z negativnídopad na službu.  Chcete-li vrátit více než `Skip` 50 řádků, použijte metodu a, `Take` jak je popsáno v [části Návratová data na stránkách](#paging).

### <a name="how-to-filter-returned-data"></a><a name="filtering"></a>Postup: Filtrování vrácených dat
Následující kód ukazuje, jak filtrovat `Where` data zahrnutím klauzule do dotazu. Vrátí všechny položky, z `todoTable` jejichž `Complete` vlastnosti `false`je rovno . Funkce [Kde] aplikuje predikát filtrování řádků na dotaz proti tabulce.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Identifikátor URI požadavku odeslaného do back-endu můžete zobrazit pomocí softwaru pro kontrolu zpráv, například nástrojů pro vývojáře prohlížeče nebo [Fiddleru]. Pokud se podíváte na identifikátor URI požadavku, všimněte si, že řetězec dotazu je změněn:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Tento požadavek OData je přeložen do dotazu SQL sadou SDK serveru:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Funkce, která je `Where` předána metodě může mít libovolný počet podmínek.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Tento příklad by měl být přeložen do dotazu SQL sadou SDK serveru:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Tento dotaz lze také rozdělit do více klauzulí:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Obě metody jsou rovnocenné a mohou být použity zaměnitelně.  Dřívější možnost&mdash;zřetězení více predikátů&mdash;v jednom dotazu je kompaktnější a doporučená.

Klauzule `Where` podporuje operace, které mají být přeloženy do podmnožiny OData. Operace zahrnují:

* Relační operátory (==, !=, <, <=, >, >=),
* Aritmetické operátory (+, -, /, *, %),
* Přesnost čísla (Math.Floor, Math.Ceiling),
* Řetězcové funkce (Délka, Podřetězec, Nahradit, IndexOf, StartsWith, EndsWith),
* Vlastnosti data (rok, měsíc, den, hodina, minuta, sekunda),
* Přístup k vlastnostem objektu a
* Výrazy kombinující některou z těchto operací.

Při zvažování, co podporuje sada SDK serveru, můžete zvážit [dokumentaci OData v3].

### <a name="how-to-sort-returned-data"></a><a name="sorting"></a>Postup: Řazení vrácených dat
Následující kód ukazuje, jak řadit data zahrnutím OrderBy nebo [OrderByDescending] funkce v dotazu. [OrderBy] Vrátí položky `todoTable` z seřazené vzestupně podle `Text` pole.

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

### <a name="how-to-return-data-in-pages"></a><a name="paging"></a>Postup: Vrácení dat na stránkách
Ve výchozím nastavení back-end vrátí pouze prvních 50 řádků. Počet vrácených řádků můžete zvýšit voláním metody [Take.] Použijte `Take` spolu s [Skip] metoda požádat o konkrétní "stránku" z celkového počtu datových sad vrácených dotazem. Následující dotaz při spuštění vrátí tři hlavní položky v tabulce.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Následující revidovaný dotaz přeskočí první tři výsledky a vrátí další tři výsledky. Tento dotaz vytvoří druhou "stránku" dat, kde velikost stránky je tři položky.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Metoda [IncludeTotalCount] požaduje celkový počet pro *všechny* záznamy, které by byly vráceny, a ignoruje všechny zadané stránkovací/limitní klauzule:

```csharp
query = query.IncludeTotalCount();
```

V aplikaci reálného světa můžete k navigaci mezi stránkami použít dotazy podobné předchozímu příkladu s ovládacím prvkem operátoru nebo srovnatelným ui.

> [!NOTE]
> Chcete-li přepsat limit 50 řádků v back-endu mobilní aplikace, musíte také použít [EnableQueryAttribute] na veřejnou metodu GET a určit chování stránkování. Při použití na metodu nastaví maximální vrácené řádky na 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="how-to-select-specific-columns"></a><a name="selecting"></a>Postup: Výběr konkrétních sloupců
Přidáním klauzule [Select] do dotazu můžete určit, kterou sadu vlastností chcete zahrnout do výsledků. Následující kód například ukazuje, jak vybrat pouze jedno pole a také jak vybrat a formátovat více polí:

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

Všechny dosud popsané funkce jsou aditivní, takže je můžeme dál řetězit. Každé zřetězené volání ovlivní více dotazu. Ještě jeden příklad:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="how-to-look-up-data-by-id"></a><a name="lookingup"></a>Postup: Vyhledávání dat podle ID
Funkce [LookupAsync] lze vyhledat objekty z databáze s určitým ID.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="how-to-execute-untyped-queries"></a><a name="untypedqueries"></a>Postup: Provádění netypových dotazů
Při provádění dotazu pomocí objektu tabulky bez typu je nutné explicitně zadat řetězec dotazu OData voláním [ReadAsync], jako v následujícím příkladu:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Získáte zpět JSON hodnoty, které můžete použít jako vlastnost tašku. Další informace o Json.NET JToken a Newtonsoft naleznete na [Json.NET] stránkách.

### <a name="how-to-insert-data-into-a-mobile-app-backend"></a><a name="inserting"></a>Postup: Vložení dat do back-endu mobilní aplikace
Všechny typy klientů musí obsahovat člena s názvem **Id**, což je ve výchozím nastavení řetězec. Toto **ID** je nutné k provádění operací CRUD a pro synchronizaci offline. Následující kód ukazuje, jak použít metodu [InsertAsync] k vložení nových řádků do tabulky. Parametr obsahuje data, která mají být vložena jako objekt .NET.

```csharp
await todoTable.InsertAsync(todoItem);
```

Pokud během vložení není zahrnuta `todoItem` jedinečná hodnota vlastní ID, server vygeneruje identifikátor GUID.
Generované ID můžete načíst kontrolou objektu po návratu volání.

Chcete-li vložit netypová data, můžete využít výhod Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Zde je příklad pomocí e-mailové adresy jako jedinečného id řetězce:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Práce s hodnotami ID
Mobilní aplikace podporují jedinečné hodnoty vlastních řetězců pro sloupec **id** tabulky. Hodnota řetězce umožňuje aplikacím používat vlastní hodnoty, jako jsou e-mailové adresy nebo uživatelská jména pro ID.  ID řetězců poskytují následující výhody:

* ID jsou generovány bez provedení zpáteční cestu do databáze.
* Záznamy se snadněji slučují z různých tabulek nebo databází.
* Hodnoty ID lze lépe integrovat s logikou aplikace.

Pokud hodnota ID řetězce není nastavena na vložený záznam, back-end mobilní aplikace generuje jedinečnou hodnotu pro ID. Metodu [Guid.NewGuid] můžete použít ke generování vlastních hodnot ID, buď na straně klienta, nebo v back-endu.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="how-to-modify-data-in-a-mobile-app-backend"></a><a name="modifying"></a>Postup: Úprava dat v back-endu mobilní aplikace
Následující kód ukazuje, jak pomocí metody [UpdateAsync] aktualizovat existující záznam se stejným ID s novými informacemi. Parametr obsahuje data, která mají být aktualizována jako objekt .NET.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Chcete-li aktualizovat netypová data, můžete využít [výhod Json.NET] následujícím způsobem:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Při `id` provádění aktualizace musí být zadáno pole. Back-end používá `id` pole k identifikaci řádku, který má být aktualizován. Pole `id` lze získat z výsledku `InsertAsync` volání. Je `ArgumentException` aktivována, pokud se pokusíte `id` aktualizovat položku bez zadání hodnoty.

### <a name="how-to-delete-data-in-a-mobile-app-backend"></a><a name="deleting"></a>Postup: Odstranění dat v back-endu mobilní aplikace
Následující kód ukazuje, jak použít [deleteAsync] metoda odstranit existující instanci. Instance je identifikována `id` polem `todoItem`nastaveným na .

```csharp
await todoTable.DeleteAsync(todoItem);
```

Chcete-li odstranit netypová data, můžete využít Json.NET následujícím způsobem:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Při vytvoření žádosti o odstranění musí být zadáno ID. Ostatní vlastnosti nejsou předány službě nebo jsou ve službě ignorovány. Výsledkem `DeleteAsync` volání je `null`obvykle . ID předat lze získat z výsledku `InsertAsync` volání. A `MobileServiceInvalidOperationException` je vyvolána při pokusu o odstranění `id` položky bez zadání pole.

### <a name="how-to-use-optimistic-concurrency-for-conflict-resolution"></a><a name="optimisticconcurrency"></a>Postup: Použití optimistické souběžnosti pro řešení konfliktů
Dva nebo více klientů může zapisovat změny stejné položky současně. Bez detekce konfliktů by poslední zápis přepsal všechny předchozí aktualizace. **Optimistické řízení souběžnosti** předpokládá, že každá transakce může potvrdit, a proto nepoužívá žádné uzamčení prostředků.  Před potvrzením transakce optimistické řízení souběžnosti ověří, že žádná jiná transakce nezměnila data. Pokud byla data změněna, posunutí transakce je vrácena zpět.

Mobilní aplikace podporuje optimistické řízení souběžnosti sledováním změn každé položky pomocí sloupce vlastností systému, `version` který je definován pro každou tabulku v back-endu mobilní aplikace. Při každé aktualizaci záznamu mobilní `version` aplikace nastaví vlastnost tohoto záznamu na novou hodnotu. Během každého požadavku `version` na aktualizaci je vlastnost záznamu, který je součástí požadavku, porovnána se stejnou vlastností záznamu na serveru. Pokud verze předaná s požadavkem neodpovídá back-endu, pak klientská knihovna vyvolá výjimku. `MobileServicePreconditionFailedException<T>` Typ zahrnutý s výjimkou je záznam z back-endu obsahující ho servery verze záznamu. Aplikace pak můžete použít tyto informace k rozhodnutí, zda `version` chcete spustit požadavek na aktualizaci znovu se správnou hodnotu z back-endu potvrdit změny.

Definujte sloupec ve třídě `version` tabulky pro vlastnost systému, který povolí optimistickou souběžnost. Například:

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

Aplikace používající netypové tabulky umožňují `Version` optimistickou `SystemProperties` souběžnost nastavením příznaku v tabulce následujícím způsobem.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Kromě povolení optimistické souběžnosti, musíte `MobileServicePreconditionFailedException<T>` také zachytit výjimku v kódu při volání [UpdateAsync].  Vyřešte konflikt `version` použitím správného aktu a voláním [UpdateAsync] s vyřešeným záznamem. Následující kód ukazuje, jak vyřešit konflikt zápisu po zjištění:

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

Další informace najdete v tématu [Offline synchronizace dat v Mobilních aplikacích Azure.]

### <a name="how-to-bind-mobile-apps-data-to-a-windows-user-interface"></a><a name="binding"></a>Postup: Svázání dat mobilních aplikací s uživatelským rozhraním systému Windows
Tato část ukazuje, jak zobrazit vrácené datové objekty pomocí prvků uživatelského rozhraní v aplikaci pro Windows.  Následující příklad kódu váže ke zdroji seznamu s dotazem na neúplné položky. [MobileServiceCollection] vytvoří kolekci vazby podporující mobilní aplikace.

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

Některé ovládací prvky ve spravovaném modulu runtime podporují rozhraní s názvem [ISupportIncrementalLoading]. Toto rozhraní umožňuje ovládacím prvkům požadovat další data při posouvání uživatele. Existuje integrovaná podpora pro toto rozhraní pro univerzální aplikace pro Windows prostřednictvím [MobileServiceIncrementalLoadingCollection], který automaticky zpracovává volání z ovládacích prvků. V `MobileServiceIncrementalLoadingCollection` aplikacích pro Windows používejte následujícím způsobem:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Chcete-li používat novou kolekci v aplikacích pro `ToCollection` Windows Phone `IMobileServiceTableQuery<T>` `IMobileServiceTable<T>`8 a "Silverlight", použijte metody rozšíření a . Chcete-li načíst data, volejte `LoadMoreItemsAsync()`.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Při použití kolekce vytvořené `ToCollectionAsync` voláním nebo `ToCollection`, získáte kolekci, která může být vázána na ovládací prvky ui.  Tato kolekce je stránkování vědomi.  Vzhledem k tomu, že kolekce načítá data ze sítě, načítání se někdy nezdaří. Chcete-li zpracovat tyto `OnException` chyby, přepsat metodu na `MobileServiceIncrementalLoadingCollection` `LoadMoreItemsAsync`zpracování výjimek vyplývajících z volání .

Zvažte, zda tabulka obsahuje mnoho polí, ale chcete zobrazit pouze některá z nich v ovládacím prvku. Pokyny v předchozí části "[Vyberte konkrétní sloupce](#selecting)" můžete vybrat konkrétní sloupce, které se mají zobrazit v unovém okně.

### <a name="change-the-page-size"></a><a name="pagesize"></a>Změna velikosti stránky
Azure Mobile Apps ve výchozím nastavení vrátí maximálně 50 položek na jeden požadavek.  Velikost stránkování můžete změnit zvětšením maximální velikosti stránky na straně klienta i serveru.  Chcete-li zvětšit požadovanou velikost stránky, zadejte `PullOptions` při použití `PullAsync()`:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Za předpokladu, `PageSize` že jste provedli rovné nebo větší než 100 v rámci serveru, požadavek vrátí až 100 položek.

## <a name="work-with-offline-tables"></a><a name="#offlinesync"></a>Práce s tabulkami offline
Offline tabulky používají místní úložiště SQLite k ukládání dat pro použití v offline.  Všechny operace tabulky jsou prováděny proti místnímu úložišti SQLite namísto vzdáleného úložiště serveru.  Chcete-li vytvořit offline tabulku, nejprve připravte projekt:

1. V sadě Visual Studio klikněte pravým tlačítkem myši na řešení > **Spravovat balíčky NuGet pro řešení...**, a potom vyhledejte a nainstalujte balíček **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet pro všechny projekty v řešení.
2. (Nepovinné) Pro podporu zařízení se systémem Windows nainstalujte jeden z následujících runtime balíčků SQLite:

   * **Za běhu ve Windows 8.1:** Nainstalujte [SQLite pro Windows 8.1][3].
   * **Windows Phone 8.1:** Nainstalujte [SQLite pro Windows Phone 8.1][4].
   * **Univerzální platforma Windows** Nainstalujte [SQLite pro univerzální windows][5].
3. (Nepovinné). U zařízení se systémem Windows klepněte na **položku** > Odkazy**přidat odkaz...**, rozbalte složku **Windows** > **rozšíření a**povolte příslušnou **sadu SQLite pro windows** sdk spolu s **runtime visual c++ 2013 pro sadu Windows** SDK.
    Názvy sad SDK SQLite se mírně liší u každé platformy Systému Windows.

Před vytvořením odkazu na tabulku musí být připraven místní obchod:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Inicializace úložiště se obvykle provádí ihned po vytvoření klienta.  **OfflineDbPath** by měl být název souboru vhodný pro použití na všech platformách, které podporujete.  Pokud je cesta plně kvalifikovaná cesta (to znamená, že začíná lomítkem), použije se tato cesta.  Pokud cesta není plně kvalifikovaná, soubor je umístěn v umístění specifické pro platformu.

* Pro zařízení se systémem iOS a Android je výchozí cestou složka Osobní soubory.
* Pro zařízení se systémem Windows je výchozí cesta složka ApplicationData specifická pro aplikaci.

Odkaz na tabulku lze `GetSyncTable<>` získat metodou:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Chcete-li použít tabulku offline, není nutné se ověřovat.  Je třeba ověřit pouze při komunikaci se službou back-end.

### <a name="syncing-an-offline-table"></a><a name="syncoffline"></a>Synchronizace offline tabulky
Offline tabulky nejsou ve výchozím nastavení synchronizovány s back-endem.  Synchronizace je rozdělena na dva kusy.  Změny můžete stlačovat odděleně od stahování nových položek.  Zde je typická metoda synchronizace:

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

Pokud `PullAsync` je první argument null, nepoužije se přírůstková synchronizace.  Každá operace synchronizace načte všechny záznamy.

Sada SDK provádí `PushAsync()` implicitní před vytažením záznamů.

Zpracování konfliktů se `PullAsync()` děje na metodu.  Konflikty můžete řešit stejným způsobem jako online tabulky.  Konflikt je vytvořen, když `PullAsync()` je volána namísto během vložení, aktualizace nebo odstranění. Pokud dojde k více konfliktům, jsou dodávány do jednoho MobileServicePushFailedException.  Zpracovat každé selhání samostatně.

## <a name="work-with-a-custom-api"></a><a name="#customapi"></a>Práce s vlastním rozhraním API
Vlastní rozhraní API umožňuje definovat vlastní koncové body, které zveřejňují funkce serveru, které nejsou mapovány na operaci vložení, aktualizace, odstranění nebo čtení. Pomocí vlastního rozhraní API můžete mít větší kontrolu nad zasílání ms, včetně čtení a nastavení záhlaví zpráv HTTP a definování formátu textu zprávy než JSON.

Volání vlastní rozhraní API voláním jedné z [InvokeApiAsync] metody na straně klienta. Například následující řádek kódu odešle požadavek POST **completeAll** API v back-endu:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Tento formulář je volání metody typu typed a vyžaduje, aby byl definován návratový typ **MarkAllResult.** Jsou podporovány zadané i netypové metody.

Metoda InvokeApiAsync() předcvádí rozhraní API,které chcete volat, pokud rozhraní API nezačíná na '/'.
Například:

* `InvokeApiAsync("completeAll",...)`calls /api/completeAll na back-endu
* `InvokeApiAsync("/.auth/me",...)`volání /.auth/me na backend

InvokeApiAsync můžete použít k volání libovolného rozhraní WebAPI, včetně těch WebAPI, které nejsou definovány pomocí mobilních aplikací Azure.  Při použití InvokeApiAsync(), příslušné hlavičky, včetně záhlaví ověřování, jsou odesílány s požadavkem.

## <a name="authenticate-users"></a><a name="authentication"></a>Ověřování uživatelů
Mobilní aplikace podporují ověřování a autorizaci uživatelů aplikací pomocí různých externích poskytovatelů identit: Facebook, Google, účet Microsoft, Twitter a Azure Active Directory. Můžete nastavit oprávnění pro tabulky, která omezí přístup pro určité operace pouze na ověřené uživatele. Identitu ověřených uživatelů můžete také použít k implementaci autorizačních pravidel v serverových skriptech. Další informace najdete v kurzu [Přidání ověřování do aplikace].

Podporovány jsou dva toky ověřování: tok *spravovaný klientem* a *serverem.* Tok spravované ho serverem poskytuje nejjednodušší možnosti ověřování, protože závisí na webovém ověřovacím rozhraní zprostředkovatele. Tok spravované klientem umožňuje hlubší integraci s funkcemi specifickými pro zařízení, protože závisí na sadách SDK specifických pro konkrétní zprostředkovatele.

> [!NOTE]
> Doporučujeme používat tok spravovaný klientem ve vašich produkčních aplikacích.

Chcete-li nastavit ověřování, musíte aplikaci zaregistrovat u jednoho nebo více poskytovatelů identity.  Poskytovatel identity vygeneruje ID klienta a tajný klíč klienta pro vaši aplikaci.  Tyto hodnoty se pak nastavují v back-endu, aby bylo možné povolit ověřování nebo autorizaci služby Azure App Service.  Další informace najdete v podrobných pokynech v kurzu [Přidat ověřování do aplikace].

V této části jsou popsána následující témata:

* [Ověřování spravované klientem](#clientflow)
* [Ověřování spravované serverem](#serverflow)
* [Ukládání ověřovacího tokenu do mezipaměti](#caching)

### <a name="client-managed-authentication"></a><a name="clientflow"></a>Ověřování spravované klientem
Vaše aplikace může nezávisle kontaktovat poskytovatele identity a pak poskytnout vrácený token při přihlášení s back-endem. Tento tok klienta umožňuje uživatelům poskytovat jednotné přihlašování nebo načítat další uživatelská data od poskytovatele identity. Ověřování toku klienta je upřednostňováno před použitím toku serveru, protože sada SDK zprostředkovatele identity poskytuje nativní uživatelské rozhraní a umožňuje další přizpůsobení.

Příklady jsou k dispozici pro následující vzory ověřování toku klienta:

* [Knihovna ověřování služby Active Directory](#adal)
* [Facebook nebo Google](#client-facebook)

#### <a name="authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Ověření uživatelů pomocí knihovny ověřování služby Active Directory
Pomocí knihovny ADAL (Active Directory Authentication Library) můžete zahájit ověřování uživatelů z klienta pomocí ověřování Služby Azure Active Directory.

1. Nakonfigurujte back-end mobilní aplikace pro přihlášení do Služby AAD podle kurzu [Jak nakonfigurovat službu App Service pro přihlášení do služby Active Directory.] Ujistěte se, že dokončíte volitelný krok registrace nativní klientské aplikace.
2. V sadě Visual Studio nebo Xamarin Studio otevřete `Microsoft.IdentityModel.Clients.ActiveDirectory` projekt a přidejte odkaz na balíček NuGet. Při hledání uveďte předběžné verze.
3. Přidejte do aplikace následující kód podle platformy, kterou používáte. V každém proveďte následující náhrady:

   * Nahraďte **INSERT-AUTHORITY-HERE** názvem klienta, ve kterém jste zřídit svou aplikaci. Formát by https://login.microsoftonline.com/contoso.onmicrosoft.comměl být . Tuto hodnotu lze zkopírovat z karty Doména ve službě Azure Active Directory na [webu Azure Portal].
   * Nahraďte **INSERT-RESOURCE-ID-ZDE** ID klienta pro back-end mobilní aplikace. ID klienta můžete získat na kartě **Upřesnit** v části **Nastavení služby Azure Active Directory** na portálu.
   * Nahraďte **INSERT-CLIENT-ID-ZDE** ID klienta, které jste zkopírovali z nativní klientské aplikace.
   * Nahraďte **rozhraní INSERT-REDIRECT-URI-ZDE** koncovým bodem vašeho webu */.auth/login/done* pomocí schématu HTTPS. Tato hodnota by *https://contoso.azurewebsites.net/.auth/login/done*měla být podobná .

     Kód potřebný pro každou platformu následuje:

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

#### <a name="single-sign-on-using-a-token-from-facebook-or-google"></a><a name="client-facebook"></a>Jednotné přihlašování pomocí tokenu z Facebooku nebo Googlu
Tok klienta můžete použít, jak je znázorněno v tomto úryvku pro Facebook nebo Google.

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

### <a name="server-managed-authentication"></a><a name="serverflow"></a>Ověřování spravované serverem
Jakmile zaregistrujete svého zprostředkovatele identity, zavolejte metodu [LoginAsync] na [MobileServiceClient] s hodnotou [MobileServiceAuthenticationProvider] vašeho poskytovatele. Například následující kód iniciuje přihlášení toku serveru pomocí Facebooku.

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

Pokud používáte jiného poskytovatele identity než Facebook, změňte hodnotu [MobileServiceAuthenticationProvider] na hodnotu pro vašeho poskytovatele.

V toku serveru služba Azure App Service spravuje tok ověřování OAuth zobrazením přihlašovací stránky vybraného zprostředkovatele.  Jakmile se vrátí poskytovatel identity, služba Azure App Service vygeneruje ověřovací token služby App Service. Metoda [LoginAsync] vrátí [uživatele MobileServiceUser], který poskytuje i [UserId] ověřeného uživatele a [tokenU MobileServiceAuthenticationToken]jako webový token JSON (JWT). Tento token se může uložit do mezipaměti a znovu požívat do vypršení platnosti. Další informace naleznete v [tématu Ukládání ověřovacího tokenu do mezipaměti](#caching).

### <a name="caching-the-authentication-token"></a><a name="caching"></a>Ukládání ověřovacího tokenu do mezipaměti
V některých případech volání metody přihlášení lze vyhnout po prvním úspěšném ověření uložením ověřovací token od zprostředkovatele.  Aplikace Microsoft Store a UPW můžou používat [PasswordVault] k ukládání aktuálního ověřovacího tokenu do mezipaměti po úspěšném přihlášení, a to následovně:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

UserId hodnota je uložena jako UserName pověření a token je uložen jako heslo. Při následných spuštěních můžete zkontrolovat **passwordvault** pro pověření uložená v mezipaměti. Následující příklad používá pověření uložená v mezipaměti, když jsou nalezena, a jinak se pokusí znovu ověřit pomocí back-endu:

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

Když odhlásíte uživatele, musíte také odebrat uložená pověření takto:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Aplikace Xamarin používají api [Xamarin.Auth] k bezpečnému ukládání přihlašovacích údajů do objektu **účtu.** Příklad použití těchto souborů API naleznete v [souboru kódu AuthStore.cs] v [ukázce pro sdílení fotografií ContosoMoments](https://github.com/azure-appservice-samples/ContosoMoments).

Pokud používáte ověřování spravované klientem, můžete také ukládat do mezipaměti přístupový token získaný od vašeho poskytovatele, jako je Facebook nebo Twitter. Tento token lze dodat k vyžádání nového ověřovacího tokenu z back-endu, a to následovně:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="push-notifications"></a><a name="pushnotifications"></a>Nabízená oznámení
Následující témata zahrnují nabízená oznámení:

* [Registrace pro nabízená oznámení](#register-for-push)
* [Získání sid balíčku microsoft store](#package-sid)
* [Registrace pomocí šablon pro různé platformy](#register-xplat)

### <a name="how-to-register-for-push-notifications"></a><a name="register-for-push"></a>Postup: Registrace nabízených oznámení
Klient mobilních aplikací umožňuje registraci nabízených oznámení pomocí azure notification hubů. Při registraci získáte popisovač, který získáte ze služby nabízených oznámení specifické pro platformu (PNS). Potom zadat tuto hodnotu spolu s všechny značky při vytváření registrace. Následující kód zaregistruje vaši aplikaci pro Windows pro nabízená oznámení pomocí služby Windows Notification Service (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Pokud tlačíte na wns, pak musíte [získat microsoft store balíček SID](#package-sid).  Další informace o aplikacích pro Windows, včetně způsobu registrace šablon, najdete v [tématu Přidání nabízených oznámení do aplikace].

Vyžádání značek od klienta není podporováno.  Požadavky na značky jsou tiše vynechány z registrace.
Pokud chcete zaregistrovat zařízení pomocí značek, vytvořte vlastní rozhraní API, které používá rozhraní API centra oznámení k provedení registrace vaším jménem.  Volání vlastní rozhraní API `RegisterNativeAsync()` namísto metody.

### <a name="how-to-obtain-a-microsoft-store-package-sid"></a><a name="package-sid"></a>Postup: Získání sid balíčku microsoft store
Balíček SID je potřeba pro povolení nabízených oznámení v aplikacích Microsoft Store.  Chcete-li získat sid balíčku, zaregistrujte aplikaci v microsoft storu.

Chcete-li získat tuto hodnotu:

1. V Průzkumníkovi řešení Visual Studia klikněte pravým tlačítkem myši na projekt aplikace Microsoft Store a klikněte na **Store** > **Associate App with the Store...**.
2. V průvodci klikněte na **Další**, přihlaste se pomocí účtu Microsoft, zadejte název aplikace do **pole Rezervovat nový název aplikace**a klikněte na **Rezervovat**.
3. Po úspěšném vytvoření registrace aplikace vyberte název aplikace, klikněte na **Další**a potom klikněte na **Přidružit**.
4. Přihlaste se do [Windows Dev Center] pomocí svého účtu Microsoft. V části **Moje aplikace**klikněte na registraci aplikace, kterou jste vytvořili.
5. Klikněte na**Identita aplikace** **pro správu** > aplikací a potom posuňte dolů a najděte si svůj **SID balíčku**.

Mnoho použití balíčku SID považovat za identifikátor URI, v takovém případě je třeba použít *ms-app://* jako schéma. Poznamenejte si verzi vašeho balíčku SID vytvořené zřetězením této hodnoty jako předpony.

Aplikace Xamarin vyžadují nějaký další kód, aby bylo možné zaregistrovat aplikaci spuštěnou na platformách iOS nebo Android. Další informace naleznete v tématu platformy:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="how-to-register-push-templates-to-send-cross-platform-notifications"></a><a name="register-xplat"></a>Postup: Registrace nabízených šablon pro odesílání oznámení pro různé platformy
Chcete-li zaregistrovat `RegisterAsync()` šablony, použijte metodu se šablonami takto:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Šablony by měly být `JObject` typy a mohou obsahovat více šablon v následujícím formátu JSON:

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

Metoda **RegisterAsync()** také přijímá sekundární dlaždice:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Všechny značky jsou odstraněny při registraci pro bezpečnost. Pokud chcete přidat značky k instalacím nebo šablonám v rámci instalací, přečtěte si část [Práce s back-endovým serverem .NET SDK pro Mobilní aplikace Azure].

Chcete-li odesílat oznámení s využitím těchto registrovaných šablon, podívejte se na [api centra oznámení].

## <a name="miscellaneous-topics"></a><a name="misc"></a>Různá témata
### <a name="how-to-handle-errors"></a><a name="errors"></a>Postup: Zpracování chyb
Dojde-li k chybě v back-endu, klient `MobileServiceInvalidOperationException`SDK vyvolá .  Následující příklad ukazuje, jak zpracovat výjimku, která je vrácena back-endem:

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

Další příklad řešení chybových stavů naleznete v [ukázce souborů mobilních aplikací]. Příklad [LoggingHandler] poskytuje obslužnou rutinu delegáta protokolování pro protokolování požadavků, které jsou prováděny do back-endu.

### <a name="how-to-customize-request-headers"></a><a name="headers"></a>Postup: Přizpůsobení záhlaví požadavků
Chcete-li podpořit váš konkrétní scénář aplikace, možná budete muset přizpůsobit komunikaci s back-endem mobilní aplikace. Můžete například přidat vlastní záhlaví ke každému odchozímu požadavku nebo dokonce změnit stavové kódy odpovědí. Můžete použít vlastní [DelegatingHandler], jako v následujícím příkladu:

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
[Offline synchronizace dat pro Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Přidání nabízených oznámení do aplikace]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[Konfigurace služby App Service pro přihlášení služby Active Directory]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[Zprostředkovatel MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[Token MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[Gettable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[vytvoří odkaz na netypovou tabulku.]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[OdstranitAsynchronizace]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[Aplikace LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[Orderby]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[Orderbydescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[Aplikace ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Vybrat]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Přeskočit]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[Userid]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Kde]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Portál Azure]: https://portal.azure.com/
[Atribut EnableQuery]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centrum pro střední prostředí windows]: https://dev.windows.com/overview
[DelegováníHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[API centra oznámení]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Ukázka souborů mobilních aplikací]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[ProtokolováníHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 Dokumentace]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
