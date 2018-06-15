---
title: Zapnutí offline synchronizace pro mobilní aplikace Azure (Xamarin Android)
description: Další informace o použití aplikace služby mobilní aplikace do mezipaměti a synchronizaci dat offline v aplikaci Xamarin Android
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 5c6ff5ac909e2dc6918f85d39beb781952ee6dd0
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2018
ms.locfileid: "27593092"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Zapnutí offline synchronizace pro mobilní aplikace Xamarin.Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz představuje offline synchronizace funkci Azure Mobile Apps pro Xamarin.Android. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidání nebo úprava dat – i když dojde k dispozici žádné síťové připojení. Změny se ukládají do místní databáze.
Když je zařízení do režimu online, tyto změny se synchronizují s vzdálené služby.

V tomto kurzu aktualizujete projektu klienta z tohoto kurzu [vytvořit aplikaci Xamarin Android] pro podporu offline funkce Azure Mobile Apps. Pokud použijete serverový projekt stažené rychlý start, je nutné přidat data přístup rozšiřující balíčky do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace, naleznete v tématu [Offline synchronizací dat v Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizace klienta aplikace pro podporu funkcím offline
Offline funkce mobilní aplikace Azure umožňují interakci s místní databázi, pokud jste v offline scénář. V aplikaci použít tyto funkce, inicializovat [SyncContext] do místního úložiště. Potom referenční tabulku přes rozhraní [IMobileServiceSyncTable][IMobileServiceSyncTable]. SQLite slouží jako místní úložiště v zařízení.

1. V sadě Visual Studio, otevřete Správce balíčků NuGet v projektu, který jste dokončili v [vytvořit aplikaci Xamarin Android] kurzu.  Vyhledat a nainstalovat **Microsoft.Azure.Mobile.Client.SQLiteStore** balíček NuGet.
2. Otevřete soubor ToDoActivity.cs a zrušte komentář u `#define OFFLINE_SYNC_ENABLED` definice.
3. V sadě Visual Studio, stiskněte **F5** klíč znovu sestavit a spustit klientskou aplikaci. Aplikace funguje stejným způsobem jako před jste povolili offline synchronizace. Však místní databáze je nyní obsahuje data, která lze použít v případě pomocí offline.

## <a name="update-sync"></a>Aktualizace aplikace k odpojení od back-end
V této části Zrušení propojení na váš back-end mobilní aplikace k simulaci offline situaci. Když přidáte datových položek, vaše obslužná rutina výjimky zprávou, že aplikace je v offline režimu. V tomto stavu nové položky přidány v místním úložišti a jsou synchronizované do back-end mobilní aplikace při push v připojeném stavu.

1. Upravte ToDoActivity.cs v sdílený projekt. Změna **applicationURL** tak, aby odkazovalo na neplatnou adresu URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Zakázání Wi-Fi a mobilní sítě na zařízení nebo pomocí režim v letadle, může také ukázat offline chování.
2. Stiskněte klávesu **F5** sestavení a spuštění aplikace. Všimněte si vaše synchronizace se nezdařila při obnovení při spuštění aplikace.
3. Zadejte nové položky a Všimněte si, že nabízené nezdaří se stavem [CancelledByNetworkError] pokaždé, když kliknete na tlačítko **Uložit**. Však nové položky todo existovat v místním úložišti, dokud se můžou poslat na back-end mobilní aplikace.  V produkční aplikace je-li potlačit tyto výjimky klientské aplikace chová, jako by se stále připojeni k back-end mobilní aplikace.
4. Zavřete aplikaci a restartujte ji k ověření, že nové položky, které jste vytvořili, jsou nastavené jako trvalé do místního úložiště.
5. (Volitelné) V sadě Visual Studio otevřete **Průzkumníka serveru**. Přejděte k vaší databázi v **Azure**->**databází SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumníku objektů systému SQL Server**. Teď můžete procházet tabulku databáze SQL a její obsah. Ověřte, že se nezměnil na data v databázi back-end.
6. (Volitelné) Dotaz na vaše mobilní back-end, pomocí dotazu GET ve formuláři pomocí REST nástroje, jako je Fiddler nebo Postman `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aktualizace aplikace se znovu připojit váš back-end mobilní aplikace
V této části znovu připojte aplikaci back-end mobilní aplikace. Při prvním spuštění aplikace, `OnCreate` volání obslužné rutiny události `OnRefreshItemsSelected`. Tato metoda volá `SyncAsync` synchronizaci místního úložiště s databází back-end.

1. Otevřete v projektu sdíleného ToDoActivity.cs a obnovit vaše změna **applicationURL** vlastnost.
2. Stiskněte **F5** klíč znovu sestavte a spusťte aplikaci. Aplikace synchronizuje místní změny s použitím operací nabízení a vyžadování back-end mobilní aplikace Azure při `OnRefreshItemsSelected` metody.
3. (Volitelné) Zobrazte aktualizovaná data pomocí Průzkumníka objektů systému SQL Server nebo REST nástroje, například aplikaci Fiddler. Všimněte si data umístění byl synchronizován mezi databázi back-end mobilní aplikace Azure a místní úložiště.
4. V aplikaci klikněte na zaškrtávací políčko vedle několik položek k jejich dokončení v místním úložišti.

   `CheckItem`volání `SyncAsync` k položce každý byla úspěšně dokončena synchronizace s back-end mobilní aplikace. `SyncAsync`volá nabízení a vyžadování. **Vždy, když je spustit vyžádání pro tabulku, která klient udělal změny, push vždy provést automaticky**. Tím se zajistí, že všechny tabulky v místním úložišti společně s vztahy zůstaly konzistentní. Toto chování může způsobit neočekávané push. Další informace o toto chování najdete v tématu [Offline synchronizací dat v Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Zkontrolujte kód synchronizace klienta
Projekt Xamarin klienta, který jste stáhli, když jste dokončili kurz [vytvořit aplikaci Xamarin Android] již obsahuje kód podpora offline synchronizace pomocí místní databáze SQLite. Zde je stručný přehled co je již zahrnut v kurzu kódu. Koncepční přehled funkce, najdete v části [Offline synchronizací dat v Azure Mobile Apps].

* Před provedením jakékoli operace s tabulkou, musí být inicializován místní úložiště. Místní úložiště databáze inicializovaná při `ToDoActivity.OnCreate()` provede `ToDoActivity.InitLocalStoreAsync()`. Tato metoda vytvoří místní databázi SQLite pomocí `MobileServiceSQLiteStore` třída poskytnutý klientem Azure Mobile Apps SDK.

    `DefineTable` Metoda vytvoří tabulku v místním úložišti odpovídající pole v zadaný typ `ToDoItem` v tomto případě. Typ nemusí obsahovat všechny sloupce, které jsou v vzdálené databáze. Je možné k uložení právě podmnožinu sloupců.

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
            // For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* `toDoTable` Členem `ToDoActivity` je `IMobileServiceSyncTable` zadejte místo `IMobileServiceTable`. IMobileServiceSyncTable směrovat všechny vytvářet, číst, aktualizovat a odstranit tabulku operace do místního úložiště databáze.

    Rozhodnete, když se změny odesílají na back-end mobilní aplikace Azure voláním `IMobileServiceSyncContext.PushAsync()`. Kontext synchronizace umožňuje zachovat relace mezi tabulkami sledováním a vkládání změny ve všech tabulkách klientské aplikace změnil při `PushAsync` je volána.

    Zadaný kód zavolá metodu `ToDoActivity.SyncAsync()` pro synchronizaci pokaždé, když se aktualizují seznamu todoitem nebo úkolu je přidána nebo byla dokončena. Synchronizace kódu po každé změně místní.

    Zadaný kód všechny zaznamenává ve vzdáleném `TodoItem` tabulky jsou předmětem dotazování, ale je také možné filtrování záznamů předáním id dotazu a dotazy s cílem `PushAsync`. Další informace najdete v části *přírůstkové synchronizace* v [Offline synchronizací dat v Azure Mobile Apps].

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

## <a name="additional-resources"></a>Další prostředky
* [Offline synchronizací dat v Azure Mobile Apps]
* [Mobilní aplikace Azure .NET SDK postupy][8]

<!-- URLs. -->
[vytvořit aplikaci Xamarin Android]: ../app-service-mobile-xamarin-android-get-started.md
[Offline synchronizací dat v Azure Mobile Apps]: ../app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Vytvoření aplikace Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Synchronizace offline dat v prostředí Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
