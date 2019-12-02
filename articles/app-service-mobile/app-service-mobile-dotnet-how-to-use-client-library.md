---
title: Práce s knihovnou spravovaného klienta
description: Naučte se používat klientskou knihovnu .NET pro Azure App Service Mobile Apps s aplikacemi pro Windows a Xamarin.
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 741d286126bedb8b92828486927283fa9887658e
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668463"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Jak používat spravovaného klienta pro Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
> Pokud chcete do vaší mobilní aplikace integrovat cloudové služby, ještě dnes se zaregistrujte do služeb [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Přehled
V této příručce se dozvíte, jak provádět běžné scénáře pomocí spravované klientské knihovny pro Azure App Service Mobile Apps pro aplikace pro Windows a Xamarin. Pokud s Mobile Apps začínáte, měli byste zvážit, jak nejprve vyplníte kurz [Azure Mobile Apps rychlý Start][1] . V tomto průvodci se zaměříme na spravovanou sadu SDK na straně klienta. Další informace o sadách SDK na straně serveru pro Mobile Apps naleznete v dokumentaci k sadě [.NET Server SDK][2] nebo sadě [SDK serveru Node. js][3].

## <a name="reference-documentation"></a>Referenční dokumentace
Referenční dokumentace k sadě Client SDK se nachází tady: [odkazy na klienta Azure Mobile Apps .NET][4].
Několik ukázek klientů můžete najít také v [úložišti GitHub Azure-Samples][5].

## <a name="supported-platforms"></a>Podporované platformy
Platforma .NET podporuje následující platformy:

* Verze Xamarin Androidu pro rozhraní API 19 až 24 (KitKat až nougat)
* Verze Xamarin iOS pro iOS verze 8,0 a novější
* Univerzální platforma Windows
* Windows Phone 8,1
* Windows Phone 8,0 s výjimkou aplikací Silverlight

Ověřování "Server-Flow" používá pro prezentované uživatelské rozhraní WebView.  Pokud zařízení není schopné prezentovat uživatelské rozhraní WebView, vyžadují se další metody ověřování.  Tato sada SDK není vhodná pro zařízení s omezením typu kukátka nebo podobně.

## <a name="setup"></a>Nastavení a předpoklady
Předpokládáme, že už jste vytvořili a publikovali projekt back-endu mobilní aplikace, který obsahuje alespoň jednu tabulku.  V kódu použitém v tomto tématu je tabulka pojmenována `TodoItem` a má následující sloupce: `Id`, `Text`a `Complete`. Tato tabulka je stejná jako tabulka vytvořená při dokončení [rychlého startu Azure Mobile Apps][1].

Odpovídající typový typ na straně klienta v C# nástroji je následující třída:

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

[JsonPropertyAttribute][6] se používá k definování mapování *PropertyName* mezi polem klient a polem tabulky.

Informace o vytváření tabulek v back-endu Mobile Apps najdete v tématu [věnovaném sadě SDK serveru pro .NET][7] nebo v [tématu SDK pro server Node. js][8]. Pokud jste v Azure Portal vytvořili back-end mobilní aplikace pomocí rychlého startu, můžete také použít nastavení **jednoduché tabulky** v [Azure Portal].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Postupy: instalace balíčku spravovaného klienta sady SDK
K instalaci balíčku spravovaného klienta sady SDK pro Mobile Apps z [NuGet][9]použijte jednu z následujících metod:

* **Visual Studio** Klikněte pravým tlačítkem na projekt, klikněte na **Spravovat balíčky NuGet**, vyhledejte balíček `Microsoft.Azure.Mobile.Client` a pak klikněte na **nainstalovat**.
* **Xamarin Studio** Klikněte pravým tlačítkem na projekt, klikněte na **přidat** > **Přidat balíčky NuGet**, vyhledejte balíček `Microsoft.Azure.Mobile.Client` a pak klikněte na **Přidat balíček**.

V souboru hlavní aktivity nezapomeňte přidat následující příkaz **using** :

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Upozorňujeme, že všechny balíčky podporu odkazované ve vašem projektu Android musí mít stejnou verzi. Sada SDK má `Xamarin.Android.Support.CustomTabs` závislost pro platformu Android, takže pokud váš projekt používá novější balíčky podpory, je nutné nainstalovat tento balíček s požadovanou verzí přímo, aby nedocházelo ke konfliktům.

### <a name="symbolsource"></a>Postupy: práce s symboly ladění v aplikaci Visual Studio
V [SymbolSource][10]jsou k dispozici symboly pro obor názvů Microsoft. Azure. Mobile.  Informace o integraci SymbolSource se sadou Visual Studio najdete v [pokynech k SymbolSource][11] .

## <a name="create-client"></a>Vytvoření klienta Mobile Apps
Následující kód vytvoří objekt [MobileServiceClient][12] , který se používá pro přístup k back-endu mobilní aplikace.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

V předchozím kódu nahraďte `MOBILE_APP_URL` adresou URL back-endu mobilní aplikace, který najdete v okně pro back-end mobilní aplikace v [Azure Portal]. Objekt MobileServiceClient by měl být typu singleton.

## <a name="work-with-tables"></a>Práce s tabulkami
Následující část popisuje, jak vyhledat a načíst záznamy a upravit data v tabulce.  Jsou pokrytá následující témata:

* [Vytvoření odkazu na tabulku](#instantiating)
* [Dotazování dat](#querying)
* [Filtrovat vrácená data](#filtering)
* [Řazení vrácených dat](#sorting)
* [Vrátit data na stránkách](#paging)
* [Vybrat konkrétní sloupce](#selecting)
* [Vyhledat záznam podle ID](#lookingup)
* [Obchodování s netypovými dotazy](#untypedqueries)
* [Vkládání dat](#inserting)
* Aktualizace dat
* [Odstraňování dat](#deleting)
* [Řešení konfliktů a Optimistická souběžnost](#optimisticconcurrency)
* [Vytvoření vazby na uživatelské rozhraní systému Windows](#binding)
* [Změna velikosti stránky](#pagesize)

### <a name="instantiating"></a>Postupy: Vytvoření odkazu na tabulku
Veškerý kód, který přistupuje k datům v back-endové tabulce a upravuje je, volá funkce objektu `MobileServiceTable`. Získejte odkaz na tabulku voláním metody [GetTable] následujícím způsobem:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Vrácený objekt používá typový serializace model. Podporuje se taky Netypový serializace model. Následující příklad [vytvoří odkaz na netypové tabulky]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

V netypových dotazech je nutné zadat podkladový řetězec dotazu OData.

### <a name="querying"></a>Postupy: dotazování dat z mobilní aplikace
Tato část popisuje, jak vydávat dotazy do back-endu mobilní aplikace, který obsahuje následující funkce:

* [Filtrovat vrácená data](#filtering)
* [Řazení vrácených dat](#sorting)
* [Vrátit data na stránkách](#paging)
* [Vybrat konkrétní sloupce](#selecting)
* [Vyhledat data podle ID](#lookingup)

> [!NOTE]
> Je využívána velikost stránky řízená serverem, aby se zabránilo vrácení všech řádků.  Stránkování udržuje výchozí požadavky na velké datové sady z negativního vlivu na službu.  Chcete-li vrátit více než 50 řádků, použijte metodu `Skip` a `Take`, jak je popsáno v tématu [návratová data na stránkách](#paging).

### <a name="filtering"></a>Postupy: filtrování vrácených dat
Následující kód ilustruje, jak filtrovat data zahrnutím klauzule `Where` do dotazu. Vrátí všechny položky z `todoTable`, jejichž vlastnost `Complete` je rovna `false`. Funkce [WHERE] aplikuje predikát filtru řádku na dotaz na tabulku.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Identifikátor URI žádosti odeslané do back-endu můžete zobrazit pomocí softwaru pro kontrolu zpráv, například nástrojů pro vývojáře a [Fiddler]. Pokud se podíváte na požadavek s identifikátorem URI, Všimněte si, že je upraven řetězec dotazu:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Tento požadavek OData se převede na dotaz SQL ze serverové sady SDK:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Funkce, která je předána metodě `Where`, může mít libovolný počet podmínek.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Tento příklad by byl přeložen na dotaz SQL pomocí sady SDK serveru:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Tento dotaz může být také rozdělen do více klauzulí:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Tyto dvě metody jsou ekvivalentní a dají se použít zaměnitelné.  Dřívější možnost&mdash;zřetězení více predikátů v jednom dotazu&mdash;je kompaktnější a doporučuje se.

Klauzule `Where` podporuje operace, které budou přeloženy do podmnožiny OData. Operace zahrnují:

* Relační operátory (= =,! =, <, < =, >, > =),
* Aritmetické operátory (+,-,/, *,%),
* Přesnost čísel (Math. Floor, Math. strop),
* Řetězcové funkce (délka, podřetězec, Replace, IndexOf, StartsWith, EndsWith),
* Vlastnosti data (rok, měsíc, den, hodina, minuta, sekundy),
* Přístup k vlastnostem objektu a
* Výrazy kombinující některé z těchto operací.

Při zvažování, co podporuje sada SDK serveru, můžete zvážit [Dokumentace k OData V3].

### <a name="sorting"></a>Postupy: řazení vrácených dat
Následující kód ilustruje, jak řadit data zahrnutím funkce [OrderBy] nebo [OrderByDescending] do dotazu. Vrátí položky z `todoTable` seřazené vzestupně podle pole `Text`.

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

### <a name="paging"></a>Postupy: vrácení dat na stránkách
Ve výchozím nastavení back-end vrátí jenom prvních 50 řádků. Počet vrácených řádků můžete zvýšit voláním metody [Nezbytná] . Použijte `Take` společně s metodou [Přímo] pro vyžádání konkrétní "stránky" celkové datové sady vrácené dotazem. Následující dotaz po provedení vrátí první tři položky v tabulce.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Následující revidovaný dotaz přeskočí první tři výsledky a vrátí další tři výsledky. Tento dotaz vytvoří druhou "stránku" dat, kde je velikost stránky tři položky.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Metoda [IncludeTotalCount] požaduje celkový počet *všech* záznamů, které by byly vráceny, ignorování všech zadaných klauzulí stránkování a omezení:

```csharp
query = query.IncludeTotalCount();
```

V reálné aplikaci můžete použít dotazy podobné předchozím příkladům pomocí ovládacího prvku stránkování nebo srovnatelného uživatelského rozhraní pro navigaci mezi stránkami.

> [!NOTE]
> Chcete-li v back-endu mobilní aplikace přepsat 50 – limit řádku, je nutné také použít [EnableQueryAttribute] na metodu public get a určit chování stránkování. Při použití na metodu nastaví následující nastavení maximální počet vrácených řádků na 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Postupy: Výběr konkrétních sloupců
Přidáním klauzule [Výběr] do dotazu můžete určit, kterou sadu vlastností zahrnout do výsledků. Například následující kód ukazuje, jak vybrat pouze jedno pole a také jak vybrat a formátovat více polí:

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

Všechny funkce popsané v této míře jsou doplňkové, takže je můžeme dál zřetězit. Každé zřetězené volání má vliv na více dotazů. Jeden další příklad:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Postupy: vyhledávání dat podle ID
Funkci [LookupAsync] lze použít k vyhledání objektů z databáze pomocí konkrétního ID.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Postupy: spuštění netypových dotazů
Při provádění dotazu pomocí netypového objektu tabulky je nutné explicitně zadat řetězec dotazu OData voláním [ReadAsync], jak je uvedeno v následujícím příkladu:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Vrátíte se zpátky hodnoty JSON, které můžete použít jako kontejner objektů a dat. Další informace o JToken a Newtonsoft Json.NET najdete na webu [JSON.NET] .

### <a name="inserting"></a>Postupy: vkládání dat do back-endu mobilní aplikace
Všechny typy klientů musí obsahovat člen s názvem **ID**, který je ve výchozím nastavení řetězec. Toto **ID** je vyžadováno k provádění operací CRUD a pro offline synchronizaci. Následující kód ilustruje použití metody [InsertAsync] k vložení nových řádků do tabulky. Parametr obsahuje data, která mají být vložena jako objekt rozhraní .NET.

```csharp
await todoTable.InsertAsync(todoItem);
```

Pokud během vkládání není v `todoItem` obsažena jedinečná hodnota vlastního ID, vygeneruje se na serveru identifikátor GUID.
Vygenerované ID můžete načíst tak, že zkontrolujete objekt po návratu volání.

Chcete-li vložit netypové údaje, můžete využít výhod Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Tady je příklad použití e-mailové adresy jako jedinečného ID řetězce:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Práce s hodnotami ID
Mobile Apps podporuje jedinečné vlastní hodnoty řetězce pro sloupec **ID** tabulky. Řetězcová hodnota umožňuje aplikacím používat vlastní hodnoty, jako jsou e-mailové adresy nebo uživatelská jména pro ID.  ID řetězců poskytují následující výhody:

* ID jsou generována bez odeslání odezvy do databáze.
* Záznamy je snazší sloučit z různých tabulek nebo databází.
* Hodnoty ID se můžou integrovat lépe díky logice aplikace.

Pokud u vloženého záznamu není nastavená hodnota ID řetězce, back-end mobilní aplikace vygeneruje pro ID jedinečnou hodnotu. Pomocí metody [GUID. NewGuid] můžete vygenerovat vlastní hodnoty ID, a to buď v klientovi, nebo v back-endu.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Postupy: Změna dat v back-endu mobilní aplikace
Následující kód ilustruje použití metody [UpdateAsync] k aktualizaci existujícího záznamu se stejným ID s novými informacemi. Parametr obsahuje data, která mají být aktualizována jako objekt .NET.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Chcete-li aktualizovat netypové údaje, můžete využít výhod [JSON.NET] následujícím způsobem:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Při provádění aktualizace je třeba zadat pole `id`. Back-end používá pole `id` k identifikaci řádku, který se má aktualizovat. Pole `id` lze získat z výsledku `InsertAsync` volání. Pokud se pokusíte aktualizovat položku bez zadání `id` hodnoty, je vyvolána `ArgumentException`.

### <a name="deleting"></a>Postupy: odstranění dat v back-endu mobilní aplikace
Následující kód ilustruje použití metody [DeleteAsync] k odstranění existující instance. Instance je identifikována polem `id` nastaveným na `todoItem`.

```csharp
await todoTable.DeleteAsync(todoItem);
```

K odstranění netypových dat můžete využít výhod Json.NET následujícím způsobem:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Při provádění žádosti o odstranění je nutné zadat ID. Do služby se nepředaly další vlastnosti nebo se ve službě ignorují. Výsledek volání `DeleteAsync` je obvykle `null`. ID, které se má předat, se dá získat z výsledku `InsertAsync` volání. Pokud se pokusíte odstranit položku bez zadání pole `id`, je vyvolána `MobileServiceInvalidOperationException`.

### <a name="optimisticconcurrency"></a>Postupy: použití optimistického řízení souběžnosti pro řešení konfliktů
Dva nebo více klientů může současně zapisovat změny stejné položky. Bez zjištění konfliktů by poslední zápis přepsal všechny předchozí aktualizace. **Optimistické řízení souběžnosti** předpokládá, že každá transakce se může potvrdit, a proto nepoužívá žádné uzamykání prostředků.  Před potvrzením transakce ověří optimistické řízení souběžnosti, že žádná jiná transakce data nezměnila. Pokud byla data změněna, transakce potvrzení se vrátí zpět.

Mobile Apps podporuje optimistické řízení souběžnosti tím, že sleduje změny každé položky pomocí sloupce vlastnosti `version` systému, který je definovaný pro každou tabulku v back-endu mobilní aplikace. Pokaždé, když se aktualizuje záznam, Mobile Apps nastaví vlastnost `version` pro daný záznam na novou hodnotu. Během každé žádosti o aktualizaci se vlastnost `version` záznamu, který je součástí žádosti, porovnává se stejnou vlastností záznamu na serveru. Pokud se verze předaná s požadavkem neshoduje s back-end, Klientská knihovna vyvolá výjimku `MobileServicePreconditionFailedException<T>`. Typ zahrnutý s výjimkou je záznam z back-endu, který obsahuje verzi daného záznamu na serverech. Aplikace pak může tyto informace použít k rozhodnutí, jestli se má znovu spustit žádost o aktualizaci se správnou hodnotou `version` z back-endu pro potvrzení změn.

Definujte sloupec třídy Table pro vlastnost systému `version`, aby se povolila Optimistická souběžnost. Například:

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

Aplikace používající netypové tabulky umožňují optimistickou souběžnost nastavením příznaku `Version` v `SystemProperties` tabulky následujícím způsobem.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Kromě povolení optimistické souběžnosti musíte také zachytit výjimku `MobileServicePreconditionFailedException<T>` v kódu při volání metody [UpdateAsync].  Vyřešte konflikt tím, že použijete správný `version` k aktualizovanému záznamu a zavoláte [UpdateAsync] s vyřešeným záznamem. Následující kód ukazuje, jak vyřešit konflikt zápisu, jakmile byl zjištěn:

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

Další informace najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps] .

### <a name="binding"></a>Postupy: vázání dat Mobile Apps k uživatelskému rozhraní systému Windows
V této části se dozvíte, jak zobrazit vrácené datové objekty pomocí prvků uživatelského rozhraní v aplikaci pro Windows.  Následující příklad kódu se váže ke zdroji seznamu s dotazem na nedokončené položky. [MobileServiceCollection] vytvoří kolekci vazeb s podporou Mobile Apps.

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

Některé ovládací prvky ve spravovaném modulu runtime podporují rozhraní s názvem [ISupportIncrementalLoading]. Toto rozhraní umožňuje ovládacím prvkům požádat o další data, když se uživatel posune. K dispozici je integrovaná podpora pro toto rozhraní pro univerzální aplikace pro Windows přes [MobileServiceIncrementalLoadingCollection], která automaticky zpracovává volání z ovládacích prvků. Použijte `MobileServiceIncrementalLoadingCollection` v aplikacích pro Windows následujícím způsobem:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Chcete-li použít novou kolekci v aplikacích Windows Phone 8 a "Silverlight", použijte metody rozšíření `ToCollection` na `IMobileServiceTableQuery<T>` a `IMobileServiceTable<T>`. Chcete-li načíst data, zavolejte `LoadMoreItemsAsync()`.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Použijete-li kolekci vytvořenou voláním `ToCollectionAsync` nebo `ToCollection`, získáte kolekci, která může být svázána s ovládacími prvky uživatelského rozhraní.  Tato kolekce je s podporou stránkování.  Vzhledem k tomu, že kolekce načítá data ze sítě, načítání se někdy nezdařilo. Chcete-li tyto chyby zpracovat, přepište metodu `OnException` na `MobileServiceIncrementalLoadingCollection` pro zpracování výjimek vyplývajících z volání `LoadMoreItemsAsync`.

Vezměte v úvahu, jestli má tabulka mnoho polí, ale chcete zobrazit jenom některé z nich v ovládacím prvku. Pomocí pokynů v předchozí části[vyberte konkrétní sloupce](#selecting)můžete vybrat konkrétní sloupce, které se zobrazí v uživatelském rozhraní.

### <a name="pagesize"></a>Změnit velikost stránky
Služba Azure Mobile Apps ve výchozím nastavení vrací maximálně 50 položek na jednu žádost.  Velikost stránkování můžete změnit zvětšením maximální velikosti stránky na straně klienta i serveru.  Chcete-li zvětšit požadovanou velikost stránky, zadejte `PullOptions` při použití `PullAsync()`:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Za předpokladu, že jste na serveru nastavili `PageSize` rovno nebo větší než 100, vrátí požadavek až 100 položek.

## <a name="#offlinesync"></a>Práce s offline tabulkami
Offline tabulky používají místní úložiště SQLite k ukládání dat pro použití v režimu offline.  Všechny operace tabulky se provádí v rámci místního úložiště SQLite místo z úložiště vzdáleného serveru.  Chcete-li vytvořit offline tabulku, připravte si nejprve projekt:

1. V aplikaci Visual Studio klikněte pravým tlačítkem na řešení > **Spravovat balíčky NuGet pro řešení...** , vyhledejte a nainstalujte balíček NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** pro všechny projekty v řešení.
2. Volitelné Pro podporu zařízení s Windows nainstalujte jeden z následujících balíčků za běhu programu SQLite:

   * **Windows 8.1 runtime:** Nainstalujte [SQLite pro Windows 8.1][3].
   * **Windows Phone 8,1:** Nainstalujte [SQLite pro Windows Phone 8,1][4].
   * **Univerzální platforma Windows** Nainstalujte [SQLite pro univerzální Windows][5].
3. (Volitelné). V případě zařízení s Windows klikněte na **odkazy** > **Přidat odkaz...** , rozbalte **rozšíření**složky **Windows** > a pak povolte příslušné **SQLite pro sadu Windows** SDK spolu s **modulem runtime Visual C++ 2013 pro Windows** SDK.
    Názvy sady SDK SQLite se mírně liší u jednotlivých platforem Windows.

Aby bylo možné vytvořit odkaz na tabulku, je nutné připravit místní úložiště:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Inicializace úložiště se obvykle provádí ihned po vytvoření klienta.  **OfflineDbPath** by měl být název souboru vhodný pro použití na všech platformách, které podporujete.  Pokud je cesta plně kvalifikovanou cestou (to znamená, že začíná lomítkem), pak se tato cesta použije.  Pokud cesta není plně kvalifikovaná, soubor se umístí do umístění specifického pro danou platformu.

* Pro zařízení s iOS a Androidem je výchozí cesta složka osobní soubory.
* Pro zařízení s Windows je výchozí cesta složka "data specifická pro aplikaci".

Odkaz na tabulku lze získat pomocí metody `GetSyncTable<>`:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Nemusíte ověřovat, aby bylo možné používat offline tabulku.  Musíte ověřovat jenom při komunikaci se službou back-end.

### <a name="syncoffline"></a>Synchronizace offline tabulky
Ve výchozím nastavení nejsou offline tabulky synchronizovány s back-end.  Synchronizace je rozdělena do dvou částí.  Změny se dají doručovat nezávisle na stažení nových položek.  Tady je typická metoda synchronizace:

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

Pokud je první argument `PullAsync` null, přírůstková synchronizace se nepoužije.  Každá operace synchronizace načte všechny záznamy.

Sada SDK provede implicitní `PushAsync()` před přijetím záznamů.

Ke zpracování konfliktů dochází na `PullAsync()` metodě.  Konflikty můžete řešit stejným způsobem jako online tabulky.  Konflikt je vytvořen při volání `PullAsync()` místo během vkládání, aktualizace nebo odstranění. Pokud dojde k více konfliktům, jsou rozčleněny do jediného MobileServicePushFailedException.  Každou chybu zpracujte samostatně.

## <a name="#customapi"></a>Práce s vlastním rozhraním API
Vlastní rozhraní API umožňuje definovat vlastní koncové body, které zveřejňují funkci serveru, která není namapována na operaci vložení, aktualizace, odstranění nebo čtení. Pomocí vlastního rozhraní API můžete mít větší kontrolu nad zasíláním zpráv, včetně čtení a nastavování hlaviček zpráv HTTP a definování formátu textu zprávy, který je jiný než JSON.

Zavoláte vlastní rozhraní API voláním jedné z metod [InvokeApiAsync] na straně klienta. Například následující řádek kódu odešle požadavek POST rozhraní **completeAll** API na back-end:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Tento formulář je typové volání metody a vyžaduje, aby byl definován návratový typ **MarkAllResult** . Podporují se typové i netypové metody.

Metoda InvokeApiAsync () přiřadí "/API/" do rozhraní API, které chcete volat, pokud rozhraní API nezačíná znakem "/".
Například:

* `InvokeApiAsync("completeAll",...)` volání/api/completeAll na back-end
* `InvokeApiAsync("/.auth/me",...)` volání/.auth/me na back-end

InvokeApiAsync můžete použít k volání libovolných WebAPI, včetně těchto rozhraní WebApi, která nejsou definovaná pomocí Azure Mobile Apps.  Při použití InvokeApiAsync () se s požadavkem odesílají příslušné hlavičky, včetně ověřovacích hlaviček.

## <a name="authentication"></a>Ověřování uživatelů
Mobile Apps podporuje ověřování a autorizaci uživatelů aplikací pomocí různých externích zprostředkovatelů identity: Facebook, Google, účet Microsoft, Twitter a Azure Active Directory. Můžete nastavit oprávnění pro tabulky a omezit tak přístup pro konkrétní operace jenom na ověřené uživatele. Identitu ověřených uživatelů můžete použít taky k implementaci autorizačních pravidel ve skriptech serveru. Další informace najdete v kurzu [Přidání ověřování do aplikace].

Podporovány jsou dva toky ověřování: tok spravovaný *klientem* a *serverem* . Tok spravovaný serverem nabízí nejjednodušší možnosti ověřování, protože spoléhá na rozhraní webového ověřování poskytovatele. Tok spravovaný klientem umožňuje hlubší integraci s funkcemi specifickými pro konkrétní zařízení, protože spoléhá na sady SDK specifické pro konkrétní zařízení.

> [!NOTE]
> V produkčních aplikacích doporučujeme použít tok spravovaný klientem.

Pokud chcete nastavit ověřování, musíte aplikaci zaregistrovat s jedním nebo více zprostředkovateli identity.  Zprostředkovatel identity vygeneruje ID klienta a tajný klíč klienta pro vaši aplikaci.  Tyto hodnoty se pak nastaví v back-endu, aby povolovaly Azure App Service ověřování a autorizaci.  Pokud chcete získat další informace, postupujte podle podrobných pokynů v kurzu [Přidání ověřování do aplikace].

Tato část pojednává o následujících tématech:

* [Ověřování spravované klientem](#clientflow)
* [Ověřování spravované serverem](#serverflow)
* [Ukládání ověřovacího tokenu do mezipaměti](#caching)

### <a name="clientflow"></a>Ověřování spravované klientem
Vaše aplikace může nezávisle kontaktovat poskytovatele identity a pak poskytnout vrácený token během přihlašování k back-endu. Tento tok klienta vám umožní poskytnout uživatelům jednotné přihlašování nebo načíst další uživatelská data od poskytovatele identity. Ověřování toku klienta se upřednostňuje při používání toku serveru, protože sada SDK zprostředkovatele identity poskytuje více nativních možností uživatelského rozhraní a umožňuje další přizpůsobení.

Příklady jsou k dispozici pro následující vzory ověřování klientského toku:

* [Active Directory Authentication Library](#adal)
* [Facebook nebo Google](#client-facebook)

#### <a name="adal"></a>Ověřování uživatelů pomocí Active Directory Authentication Library
K zahájení ověřování uživatelů z klienta pomocí ověřování Azure Active Directory můžete použít Active Directory Authentication Library (ADAL).

1. Nakonfigurujte back-end mobilní aplikace pro přihlášení AAD pomocí pokynů v článku [Postup konfigurace App Service pro přihlášení ke službě Active Directory] . Ujistěte se, že jste dokončili volitelný krok registrace nativní klientské aplikace.
2. V aplikaci Visual Studio nebo Xamarin Studio otevřete projekt a přidejte odkaz na balíček NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory`. Při hledání zahrnout předběžné verze verzí.
3. Přidejte do své aplikace následující kód podle používané platformy. V každém z nich proveďte následující náhrady:

   * V části pro **vložení autority** nahraďte název tenanta, ve kterém jste aplikaci zřídili. Formát by měl být https://login.microsoftonline.com/contoso.onmicrosoft.com. Tuto hodnotu lze zkopírovat z karty doména v Azure Active Directory [Azure Portal].
   * Pro back-end mobilní aplikace nahraďte **INSERT-Resource-ID – tady** ID klienta. ID klienta můžete získat z karty **Upřesnit** v části **Nastavení Azure Active Directory** na portálu.
   * Pomocí ID klienta, které jste zkopírovali z nativní klientské aplikace, nahraďte **INSERT-Client-ID** .
   * Pomocí schématu HTTPS nahraďte **text INSERT-redirect-URI – tady** s koncovým bodem */.auth/Login/Done* vašeho webu. Tato hodnota by měla být podobná *https://contoso.azurewebsites.net/.auth/login/done* .

     Kód potřebný pro každou platformu je následující:

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

#### <a name="client-facebook"></a>Jednotné přihlašování pomocí tokenu z Facebooku nebo Google
Můžete použít tok klienta, jak je znázorněno v tomto fragmentu pro Facebook nebo Google.

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

### <a name="serverflow"></a>Ověřování spravované serverem
Po zaregistrování poskytovatele identity volejte metodu [LoginAsync] na [MobileServiceClient] s hodnotou [MobileServiceAuthenticationProvider] vašeho poskytovatele. Následující kód například inicializuje přihlášení k serveru pomocí služby Facebook.

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

V toku serveru Azure App Service spravuje tok ověřování OAuth zobrazením přihlašovací stránky vybraného zprostředkovatele.  Jakmile se zprostředkovatel identity vrátí, Azure App Service vygeneruje ověřovací token App Service. Metoda [LoginAsync] vrací hodnotu [MobileServiceUser], která poskytuje [UserID] ověřeného uživatele i [MobileServiceAuthenticationToken]jako webový token JSON (Jwt). Tento token se může uložit do mezipaměti a znovu požívat do vypršení platnosti. Další informace najdete v tématu [ukládání ověřovacího tokenu do mezipaměti](#caching).

### <a name="caching"></a>Ukládání ověřovacího tokenu do mezipaměti
V některých případech se volání metody Login může vyhnout po prvním úspěšném ověření uložením ověřovacího tokenu od poskytovatele.  Aplikace Microsoft Store a UWP můžou používat [PasswordVault] k ukládání aktuálního ověřovacího tokenu do mezipaměti po úspěšném přihlášení, a to následujícím způsobem:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

Hodnota ID uživatele je uložená jako uživatelské jméno přihlašovacích údajů a token je uložený jako heslo. Při dalších spuštěních se můžete podívat na přihlašovací údaje **PasswordVault** pro ukládání do mezipaměti. Následující příklad používá při jejich nalezení přihlašovací údaje uložené v mezipaměti a v opačném případě se pokusí znovu ověřit pomocí back-endu:

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

Když odhlásíte uživatele, musíte taky odebrat uložené přihlašovací údaje následujícím způsobem:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Aplikace Xamarin používají rozhraní API [Xamarin. auth] k bezpečnému ukládání přihlašovacích údajů do objektu **účtu** . Příklad použití těchto rozhraní API naleznete v souboru kódu [AuthStore.cs] v [ukázce sdílení fotek ContosoMoments](https://github.com/azure-appservice-samples/ContosoMoments).

Když použijete ověřování spravované klientem, můžete také ukládat do mezipaměti přístupový token získaný od poskytovatele, jako je Facebook nebo Twitter. Tento token lze zadat pro vyžádání nového ověřovacího tokenu z back-endu následujícím způsobem:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Nabízená oznámení
Nabízená oznámení jsou pokrytá v následujících tématech:

* [Registrace nabízených oznámení](#register-for-push)
* [Získání Microsoft Store SID balíčku](#package-sid)
* [Registrace v šablonách pro různé platformy](#register-xplat)

### <a name="register-for-push"></a>Postupy: registrace nabízených oznámení
Klient Mobile Apps umožňuje registrovat nabízená oznámení pomocí Azure Notification Hubs. Při registraci získáte popisovač, který získáte ze služby nabízených oznámení specifických pro konkrétní platformu (PNS). Po vytvoření registrace pak tuto hodnotu zadejte spolu s jakýmikoli značkami. Následující kód zaregistruje aplikaci pro Windows pro nabízená oznámení pomocí služby oznamování systému Windows (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Pokud předáváte do WNS, musíte [získat Microsoft Store SID balíčku](#package-sid).  Další informace o aplikacích pro Windows, včetně toho, jak se zaregistrovat k registraci šablon, najdete v tématu [Přidání nabízených oznámení do aplikace].

Vyžadování značek od klienta není podporováno.  Požadavky na značky se tiše odcházejí z registrace.
Pokud chcete zařízení zaregistrovat pomocí značek, vytvořte vlastní rozhraní API, které používá rozhraní Notification Hubs API k provedení registrace vaším jménem.  Místo metody `RegisterNativeAsync()` volejte vlastní rozhraní API.

### <a name="package-sid"></a>Postupy: získání identifikátoru SID balíčku Microsoft Store
K povolení nabízených oznámení v aplikacích Microsoft Store se vyžaduje SID balíčku.  Pokud chcete získat identifikátor SID balíčku, Zaregistrujte svoji aplikaci pomocí Microsoft Store.

Tuto hodnotu můžete získat takto:

1. V aplikaci Visual Studio Průzkumník řešení klikněte pravým tlačítkem myši na projekt Microsoft Store aplikace a klikněte na možnost **uložit** > **přidružit aplikaci ke Storu...** .
2. V průvodci klikněte na **Další**, přihlaste se pomocí svého účet Microsoft, zadejte název vaší aplikace do pole **rezervovat nový název aplikace**a pak klikněte na **rezervovat**.
3. Po úspěšném vytvoření registrace aplikace vyberte název aplikace, klikněte na **Další**a pak klikněte na **přidružit**.
4. Přihlaste se k webu [Centrum vývojářů pro Windows] pomocí svého účtu Microsoft. V části **Moje aplikace**klikněte na registraci aplikace, kterou jste vytvořili.
5. Klikněte na **Správa aplikací** > **Identita aplikace**a pak přejděte dolů k vyhledání **identifikátoru SID balíčku**.

Mnohé použití identifikátoru SID balíčku považuje za identifikátor URI. v takovém případě je třeba použít *aplikaci MS-App://* jako schéma. Poznamenejte si verzi vašeho balíčku SID vytvořenou zřetězením této hodnoty jako předpony.

Aplikace Xamarin vyžadují nějaký další kód, který by mohl Registrovat aplikaci běžící na platformách iOS nebo Android. Další informace najdete v tématu věnovaném vaší platformě:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Postupy: Registrace šablon nabízených oznámení pro odesílání oznámení pro různé platformy
K registraci šablon použijte metodu `RegisterAsync()` se šablonami, a to následujícím způsobem:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Vaše šablony by měly být `JObject` typy a mohou obsahovat více šablon v následujícím formátu JSON:

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

Metoda **RegisterAsync ()** také přijímá sekundární dlaždice:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Všechny značky jsou během registrace odstraněny pro zabezpečení. Chcete-li přidat značky do instalací nebo šablon v rámci instalací, přečtěte si téma [práce s back-end serverem .NET SDK pro Azure Mobile Apps].

Pokud chcete odesílat oznámení s těmito registrovanými šablonami, přečtěte si téma [rozhraní API pro Notification Hubs].

## <a name="misc"></a>Různá témata
### <a name="errors"></a>Postupy: zpracování chyb
Pokud dojde k chybě v back-endu, klientská sada SDK vyvolá `MobileServiceInvalidOperationException`.  Následující příklad ukazuje, jak zpracovat výjimku vrácenou back-end:

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

Další příklad práce s chybovými stavy najdete v [Ukázka souborů Mobile Apps]. Příklad [LoggingHandler] poskytuje obslužnou rutinu delegáta protokolování pro protokolování požadavků do back-endu.

### <a name="headers"></a>Postupy: přizpůsobení hlaviček požadavku
Aby bylo možné podporovat konkrétní scénář aplikace, může být nutné přizpůsobit komunikaci s back-endu mobilní aplikace. Například můžete chtít přidat vlastní hlavičku do každého odchozího požadavku nebo dokonce změnit stavové kódy odpovědí. Můžete použít vlastní [DelegatingHandler], jak je znázorněno v následujícím příkladu:

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
[Postup konfigurace App Service pro přihlášení ke službě Active Directory]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[vytvoří odkaz na netypové tabulky]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Nezbytná]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Výběr]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Přímo]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[UserID]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[WHERE]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure Portal]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[GUID. NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centrum vývojářů pro Windows]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Rozhraní API pro Notification Hubs]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Ukázka souborů Mobile Apps]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[Dokumentace k OData V3]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin. auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
