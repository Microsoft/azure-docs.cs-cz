---
title: Zapnutí offline synchronizace pro mobilní aplikace Azure (Xamarin iOS)
description: Další informace o použití aplikace služby mobilní aplikace do mezipaměti a synchronizaci dat offline v aplikaci Xamarin iOS
documentationcenter: xamarin
author: conceptdev
manager: cfowler
editor: ''
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 287977d55656a4b2bc42d90730d16f522fae9b9e
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2018
ms.locfileid: "27594662"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikace Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz představuje offline synchronizace funkci Azure Mobile Apps pro Xamarin.iOS. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidání nebo úprava dat – i když dojde k dispozici žádné síťové připojení. Změny se ukládají do místní databáze. Když je zařízení do režimu online, tyto změny se synchronizují s vzdálené služby.

V tomto kurzu aktualizovat projekt aplikace Xamarin.iOS z [vytvoření aplikace pro Xamarin iOS] pro podporu offline funkce Azure Mobile Apps. Pokud použijete serverový projekt stažené rychlý start, je nutné přidat data přístup rozšiřující balíčky do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace, naleznete v tématu [Offline synchronizací dat v Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizace klienta aplikace pro podporu funkcím offline
Offline funkce mobilní aplikace Azure umožňují interakci s místní databázi, pokud jste v offline scénář. V aplikaci použít tyto funkce, inicializovat [SyncContext] do místního úložiště. Referenční tabulka přes rozhraní [IMobileServiceSyncTable]. SQLite slouží jako místní úložiště v zařízení.

1. Otevřete Správce balíčků NuGet v projektu, který jste dokončili v [vytvoření aplikace pro Xamarin iOS] kurzu potom hledat a instalovat **Microsoft.Azure.Mobile.Client.SQLiteStore** balíček NuGet.
2. Otevřete soubor QSTodoService.cs a zrušte komentář u `#define OFFLINE_SYNC_ENABLED` definice.
3. Znovu sestavit a spustit klientskou aplikaci. Aplikace funguje stejným způsobem jako před jste povolili offline synchronizace. Však místní databáze je nyní obsahuje data, která lze použít v případě pomocí offline.

## <a name="update-sync"></a>Aktualizace aplikace k odpojení od back-end
V této části Zrušení propojení na váš back-end mobilní aplikace k simulaci offline situaci. Když přidáte datových položek, vaše obslužná rutina výjimky zprávou, že aplikace je v offline režimu. V tomto stavu nové položky přidány v místním úložišti a se budou synchronizovat s back-end mobilní aplikace při dalším spuštění nabízené v připojeném stavu.

1. Upravte QSToDoService.cs v sdílený projekt. Změna **applicationURL** tak, aby odkazovalo na neplatnou adresu URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Zakázání Wi-Fi a mobilní sítě na zařízení nebo pomocí režim v letadle, může také ukázat offline chování.
2. Sestavte a spusťte aplikaci. Všimněte si vaše synchronizace se nezdařila při obnovení při spuštění aplikace.
3. Zadejte nové položky a Všimněte si, že nabízené nezdaří se stavem [CancelledByNetworkError] pokaždé, když kliknete na tlačítko **Uložit**. Však nové položky todo existovat v místním úložišti, dokud se můžou poslat na back-end mobilní aplikace.  V produkční aplikace je-li potlačit tyto výjimky klientské aplikace chová, jako by se stále připojeni k back-end mobilní aplikace.
4. Zavřete aplikaci a restartujte ji k ověření, že nové položky, které jste vytvořili, jsou nastavené jako trvalé do místního úložiště.
5. (Volitelné) Pokud máte na počítači nainstalovanou sadu Visual Studio, otevřete **Průzkumníka serveru**. Přejděte k vaší databázi v **Azure**-> **databází SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumníku objektů systému SQL Server**. Teď můžete procházet tabulku databáze SQL a její obsah. Ověřte, že se nezměnil na data v databázi back-end.
6. (Volitelné) Dotaz na vaše mobilní back-end, pomocí dotazu GET ve formuláři pomocí REST nástroje, jako je Fiddler nebo Postman `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aktualizace aplikace se znovu připojit váš back-end mobilní aplikace
V této části znovu připojte aplikaci back-end mobilní aplikace. To se simuluje aplikace přechod ze stavu offline do online stavu s back-end mobilní aplikace.   Pokud jste simuluje poškození sítě vypnutí připojení k síti, je potřeba žádné změny kódu.
Znovu zapněte službu v síti.  Při prvním spuštění aplikace, `RefreshDataAsync` metoda je volána. Naopak volá `SyncAsync` synchronizaci místního úložiště s databází back-end.

1. Otevřete v projektu sdíleného QSToDoService.cs a obnovit vaše změna **applicationURL** vlastnost.
2. Znovu sestavte a spusťte aplikaci. Aplikace synchronizuje místní změny s použitím operací nabízení a vyžadování back-end mobilní aplikace Azure při `OnRefreshItemsSelected` metody.
3. (Volitelné) Zobrazte aktualizovaná data pomocí Průzkumníka objektů systému SQL Server nebo REST nástroje, například aplikaci Fiddler. Všimněte si data umístění byl synchronizován mezi databázi back-end mobilní aplikace Azure a místní úložiště.
4. V aplikaci klikněte na zaškrtávací políčko vedle několik položek k jejich dokončení v místním úložišti.

   `CompleteItemAsync`volání `SyncAsync` k položce každý byla úspěšně dokončena synchronizace s back-end mobilní aplikace. `SyncAsync`volá nabízení a vyžadování.
   **Vždy, když je spustit vyžádání pro tabulku, která klient udělal změny, push na kontext synchronizace klienta se provede vždy nejprve automaticky**. Implicitní nabízené zajistí, že všechny tabulky v místním úložišti společně s vztahy zůstaly konzistentní. Další informace o toto chování najdete v tématu [Offline synchronizací dat v Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Zkontrolujte kód synchronizace klienta
Projekt Xamarin klienta, který jste stáhli, když jste dokončili kurz [vytvoření aplikace pro Xamarin iOS] již obsahuje kód podpora offline synchronizace pomocí místní databáze SQLite. Zde je stručný přehled co je již zahrnut v kurzu kódu. Koncepční přehled funkce, najdete v části [Offline synchronizací dat v Azure Mobile Apps].

* Před provedením jakékoli operace s tabulkou, musí být inicializován místní úložiště. Místní úložiště databáze inicializovaná při `QSTodoListViewController.ViewDidLoad()` provede `QSTodoService.InitializeStoreAsync()`. Tato metoda vytvoří novou místní SQLite databáze pomocí `MobileServiceSQLiteStore` třída poskytnutý klientem mobilní aplikace Azure SDK.

    `DefineTable` Metoda vytvoří tabulku v místním úložišti odpovídající pole v zadaný typ `ToDoItem` v tomto případě. Typ nemusí obsahovat všechny sloupce, které jsou v vzdálené databáze. Je možné k uložení právě podmnožinu sloupců.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* `todoTable` Členem `QSTodoService` je `IMobileServiceSyncTable` zadejte místo `IMobileServiceTable`. IMobileServiceSyncTable směrovat všechny vytvářet, číst, aktualizovat a odstranit tabulku operace do místního úložiště databáze.

    Rozhodnete, když se tyto změny odesílají na back-end mobilní aplikace Azure voláním `IMobileServiceSyncContext.PushAsync()`. Kontext synchronizace umožňuje zachovat relace mezi tabulkami sledováním a vkládání změny ve všech tabulkách klientské aplikace změnil při `PushAsync` je volána.

    Zadaný kód zavolá metodu `QSTodoService.SyncAsync()` pro synchronizaci pokaždé, když se aktualizují seznamu todoitem nebo úkolu je přidána nebo byla dokončena. Synchronizace aplikace po každé změně místní. Pokud vyžádání spouští před tabulku, která má čekající místní aktualizace sledovat kontextu, že vyžádanou operaci automaticky aktivují push kontextu nejdřív.

    Zadaný kód všechny zaznamenává ve vzdáleném `TodoItem` tabulky jsou předmětem dotazování, ale je také možné filtrování záznamů předáním id dotazu a dotazy s cílem `PushAsync`. Další informace najdete v části *přírůstkové synchronizace* v [Offline synchronizací dat v Azure Mobile Apps].

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

## <a name="additional-resources"></a>Další prostředky
* [Offline synchronizací dat v Azure Mobile Apps]
* [Mobilní aplikace Azure .NET SDK postupy][8]

<!-- Images -->

<!-- URLs. -->
[vytvoření aplikace pro Xamarin iOS]: app-service-mobile-xamarin-ios-get-started.md
[Offline synchronizací dat v Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
