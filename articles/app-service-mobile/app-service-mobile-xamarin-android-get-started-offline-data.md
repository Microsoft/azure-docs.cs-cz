---
title: Povolit offline synchronizaci (Xamarin Android)
description: Přečtěte si, jak používat mobilní aplikaci App Service k ukládání do mezipaměti a synchronizaci offline dat v aplikaci Xamarin pro Android.
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4faab572cd77e20bb61d147ca6e58b25cd88ad9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458864"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikaci Xamarin.Android

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled

Tento kurz představuje funkci offline synchronizace mobilních aplikací Azure pro Xamarin.Android. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidání nebo úpravy dat – i v případě, že neexistuje žádné síťové připojení. Změny jsou uloženy v místní databázi.
Jakmile je zařízení opět online, tyto změny se synchronizují se vzdálenou službou.

V tomto kurzu aktualizujete projekt klienta z kurzu [Vytvořit aplikaci Xamarin pro Android] pro podporu offline funkcí mobilních aplikací Azure. Pokud nepoužíváte stažený projekt serveru rychlého startu, je nutné do projektu přidat balíčky rozšíření pro přístup k datům. Další informace o balíčcích rozšíření serveru naleznete v [tématu Práce s back-endovým serverem .NET SDK pro mobilní aplikace Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete v tématu [Offline synchronizace dat v Mobilních aplikacích Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizace klientské aplikace pro podporu offline funkcí

Funkce Azure Mobile App offline umožňují interakci s místní databází, když jste v offline scénáři. Chcete-li tyto funkce používat v aplikaci, inicializujete [SyncContext] do místního úložiště. Potom odkazovat na tabulku prostřednictvím rozhraní [IMobileServiceSyncTable.](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) SQLite se používá jako místní úložiště v zařízení.

1. V sadě Visual Studio otevřete Správce balíčků NuGet v projektu, který jste dokončili v kurzu [vytvořit aplikaci Pro Android Xamarin.]  Vyhledejte a nainstalujte balíček **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet.
2. Otevřete soubor ToDoActivity.cs a `#define OFFLINE_SYNC_ENABLED` odkomentujte definici.
3. V sadě Visual Studio stisknutím klávesy **F5** znovu sestavíte a spusťte klientskou aplikaci. Aplikace funguje stejně jako předtím, než jste povolili offline synchronizaci. Místní databáze je však nyní naplněna daty, která lze použít v offline scénáři.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Aktualizace aplikace pro odpojení od back-endu

V této části přerušíte připojení k back-endu mobilní aplikace, abyste simulovali situaci offline. Když přidáte datové položky, obslužná rutina výjimky vám řekne, že aplikace je v režimu offline. V tomto stavu nové položky přidané v místním úložišti a jsou synchronizovány s back-endem mobilní aplikace při spuštění nabízení v připojeném stavu.

1. Upravte ToDoActivity.cs ve sdíleném projektu. Změňte **adresu URL aplikace** tak, aby přectola na neplatnou adresu URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Můžete také demonstrovat chování offline zakázáním wifi a mobilních sítí v zařízení nebo pomocí režimu v letadle.
2. Stisknutím **klávesy F5** vytvořte a spusťte aplikaci. Všimněte si, že se při aktualizaci při aktualizaci synchronizace nezdařila.
3. Při každém klepnutí na tlačítko **Uložit**zadejte nové položky a všimněte si, že nabízená zpráva se nezdaří se stavem [CancelledByNetworkError] Nové položky todo však existují v místním úložišti, dokud je nelze zasunout do back-endu mobilní aplikace.  Pokud v produkční aplikaci potlačíte tyto výjimky, klientská aplikace se bude chovat, jako by byla stále připojená k back-endu mobilní aplikace.
4. Zavřete aplikaci a restartujte ji, abyste ověřili, že nové položky, které jste vytvořili, jsou trvalé v místním úložišti.
5. (Nepovinné) V sadě Visual Studio otevřete **Průzkumníka serveru**. Přejděte do databáze v **Azure**->**SQL Databases**. Klepněte pravým tlačítkem myši na databázi a vyberte **otevřít v průzkumníku objektů serveru SQL Server**. Nyní můžete procházet databázový obsah SQL a její obsah. Ověřte, zda se data v back-endové databázi nezměnila.
6. (Nepovinné) Pomocí nástroje REST, například Fiddler nebo Postman, můžete dotazovat na `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`mobilní back-end pomocí dotazu GET ve formuláři .

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Aktualizace aplikace pro opětovné připojení back-endu mobilní aplikace

V této části znovu připojte aplikaci k back-endu mobilní aplikace. Při prvním spuštění aplikace `OnCreate` volání `OnRefreshItemsSelected`obslužné rutiny události . Tato metoda `SyncAsync` volá k synchronizaci místního úložiště s back-endovou databází.

1. Otevřete ToDoActivity.cs ve sdíleném projektu a vraťte změnu vlastnosti **applicationURL.**
2. Stisknutím klávesy **F5** aplikaci znovu sestavujete a spusťte. Aplikace synchronizuje místní změny s back-endem Mobilní aplikace Azure `OnRefreshItemsSelected` pomocí operací push a pull při spuštění metody.
3. (Nepovinné) Zobrazení aktualizovaných dat pomocí průzkumníka objektů serveru SQL Server nebo nástroje REST, jako je Šumař. Všimněte si, že data byla synchronizována mezi back-endovou databází Azure Mobile App a místním úložištěm.
4. V aplikaci klikněte na zaškrtávací políčko vedle několika položek a dokončete je v místním obchodě.

   `CheckItem`volání `SyncAsync` synchronizovat každou dokončenou položku s back-endem mobilní aplikace. `SyncAsync`volání jak push, tak pull. **Kdykoli provedete vyžádat vyžádat tabulku, do které klient provedl změny, je nabízená zpráva vždy provedena automaticky**. Tím zajistíte, že všechny tabulky v místním úložišti spolu s relacemi zůstanou konzistentní. Toto chování může mít za následek neočekávané push. Další informace o tomto chování najdete [v tématu Offline synchronizace dat v Mobilních aplikacích Azure].

## <a name="review-the-client-sync-code"></a>Kontrola kódu synchronizace klienta

Projekt klienta Xamarin, který jste stáhli po dokončení kurzu [Vytvořit aplikaci Xamarin Android] již obsahuje kód podporující offline synchronizaci pomocí místní databáze SQLite. Zde je stručný přehled toho, co je již zahrnuto v kódu kurzu. Koncepční přehled této funkce najdete v tématu [Offline synchronizace dat v mobilních aplikacích Azure].

* Před provedením všech operací tabulky musí být místní úložiště inicializováno. Místní databáze úložiště je `ToDoActivity.OnCreate()` inicializována při spuštění `ToDoActivity.InitLocalStoreAsync()`. Tato metoda vytvoří místní databázi `MobileServiceSQLiteStore` SQLite pomocí třídy poskytované sadou Azure Mobile Apps klienta SDK.

    Metoda `DefineTable` vytvoří tabulku v místním úložišti, která odpovídá polím v zadaném typu, `ToDoItem` v tomto případě. Typ nemusí obsahovat všechny sloupce, které jsou ve vzdálené databázi. Je možné uložit pouze podmnožinu sloupců.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see https://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* Člen `toDoTable` `ToDoActivity` je `IMobileServiceSyncTable` typu namísto `IMobileServiceTable`. Tabulka IMobileServiceSyncTable přesměruje všechny operace tabulky vytvoření, čtení, aktualizace a odstranění (CRUD) do databáze místního úložiště.

    Můžete se rozhodnout, kdy se změny zasouvá do back-endu mobilní aplikace Azure voláním `IMobileServiceSyncContext.PushAsync()`. Kontext synchronizace pomáhá zachovat relace tabulek sledováním a odesíláním `PushAsync` změn ve všech tabulkách, které klientská aplikace změnila při volání.

    Zadaný kód `ToDoActivity.SyncAsync()` volá synchronizovat vždy, když je aktualizován seznam todoitem nebo je přidán nebo dokončen todoitem. Kód se synchronizuje po každé místní změně.

    V poskytnutém kódu jsou dotazovány `TodoItem` všechny záznamy ve vzdálené tabulce, ale je také možné filtrovat `PushAsync`záznamy předáním ID dotazu a dotazu na aplikaci . Další informace najdete v části *Přírůstková synchronizace* v [offline synchronizaci dat v Mobilních aplikacích Azure].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Další zdroje

* [Offline synchronizace dat pro Azure Mobile Apps]
* [Azure Mobilní aplikace .NET SDK HOWTO][8]

<!-- URLs. -->
[Vytvoření aplikace Xamarin pro Android]: ./app-service-mobile-xamarin-android-get-started.md
[Offline synchronizace dat pro Azure Mobile Apps]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Vytvoření aplikace Xamarin pro Android]: app-service-mobile-xamarin-android-get-started.md
[Offline synchronizace dat pro Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
