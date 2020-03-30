---
title: Povolení offline synchronizace (Xamarin iOS)
description: Přečtěte si, jak používat mobilní aplikaci App Service k ukládání do mezipaměti a synchronizaci offline dat v aplikaci Xamarin iOS.
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3a5128f6918b22be2ff1ef6adf3e453b1f373ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461296"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikaci Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz představuje funkci offline synchronizace mobilních aplikací Azure pro Xamarin.iOS. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidání nebo úpravy dat – i v případě, že neexistuje žádné síťové připojení. Změny jsou uloženy v místní databázi. Jakmile je zařízení opět online, tyto změny se synchronizují se vzdálenou službou.

V tomto kurzu aktualizujte projekt aplikace Xamarin.iOS z [aplikace Create a Xamarin pro iOS aplikace] pro podporu offline funkcí mobilních aplikací Azure. Pokud nepoužíváte stažený projekt serveru rychlého startu, je nutné do projektu přidat balíčky rozšíření pro přístup k datům. Další informace o balíčcích rozšíření serveru naleznete v [tématu Práce s back-endovým serverem .NET SDK pro mobilní aplikace Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete v tématu [Offline synchronizace dat v Mobilních aplikacích Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizace klientské aplikace pro podporu offline funkcí
Funkce Azure Mobile App offline umožňují interakci s místní databází, když jste v offline scénáři. Chcete-li tyto funkce používat v aplikaci, inicializovat [SyncContext] do místního úložiště. Projděte tabulku prostřednictvím rozhraní [IMobileServiceSyncTable]. SQLite se používá jako místní úložiště v zařízení.

1. Otevřete správce balíčků NuGet v projektu, který jste dokončili v kurzu [vytvořit aplikaci Xamarin iOS,] a pak vyhledejte a nainstalujte balíček **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet.
2. Otevřete soubor QSTodoService.cs a `#define OFFLINE_SYNC_ENABLED` odkomentujte definici.
3. Znovu sestavit a spustit klientskou aplikaci. Aplikace funguje stejně jako předtím, než jste povolili offline synchronizaci. Místní databáze je však nyní naplněna daty, která lze použít v offline scénáři.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Aktualizace aplikace pro odpojení od back-endu
V této části přerušíte připojení k back-endu mobilní aplikace, abyste simulovali situaci offline. Když přidáte datové položky, obslužná rutina výjimky vám řekne, že aplikace je v režimu offline. V tomto stavu budou nové položky přidané v místním úložišti a budou synchronizovány s back-endem mobilní aplikace při dalším spuštění push v připojeném stavu.

1. Upravit QSToDoService.cs ve sdíleném projektu. Změňte **adresu URL aplikace** tak, aby přectola na neplatnou adresu URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Můžete také demonstrovat chování offline zakázáním wifi a mobilních sítí v zařízení nebo pomocí režimu v letadle.
2. Sestavte a spusťte aplikaci. Všimněte si, že se při aktualizaci při aktualizaci synchronizace nezdařila.
3. Při každém klepnutí na tlačítko **Uložit**zadejte nové položky a všimněte si, že nabízená zpráva se nezdaří se stavem [CancelledByNetworkError] Nové položky todo však existují v místním úložišti, dokud je nelze zasunout do back-endu mobilní aplikace.  Pokud v produkční aplikaci potlačíte tyto výjimky, klientská aplikace se bude chovat, jako by byla stále připojená k back-endu mobilní aplikace.
4. Zavřete aplikaci a restartujte ji, abyste ověřili, že nové položky, které jste vytvořili, jsou trvalé v místním úložišti.
5. (Nepovinné) Pokud máte v počítači nainstalovanou visual studio, schodte **Průzkumníka serveru**. Přejděte do databáze v **Azure**-> **SQL Databases**. Klepněte pravým tlačítkem myši na databázi a vyberte **otevřít v průzkumníku objektů serveru SQL Server**. Nyní můžete procházet databázový obsah SQL a její obsah. Ověřte, zda se data v back-endové databázi nezměnila.
6. (Nepovinné) Pomocí nástroje REST, například Fiddler nebo Postman, můžete dotazovat na `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`mobilní back-end pomocí dotazu GET ve formuláři .

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Aktualizace aplikace pro opětovné připojení back-endu mobilní aplikace
V této části znovu připojte aplikaci k back-endu mobilní aplikace. To simuluje, že aplikace přechází ze stavu offline do stavu online s back-endem mobilní aplikace.   Pokud jste simulovali přerušení sítě vypnutím připojení k síti, nejsou potřeba žádné změny kódu.
Znovu zapněte síť.  Při prvním spuštění aplikace `RefreshDataAsync` je volána metoda. To zase `SyncAsync` volá k synchronizaci místního úložiště s back-endovou databází.

1. Otevřete QSToDoService.cs ve sdíleném projektu a vraťte změnu vlastnosti **applicationURL.**
2. Znovu sestavte a spusťte aplikaci. Aplikace synchronizuje místní změny s back-endem Mobilní aplikace Azure `OnRefreshItemsSelected` pomocí operací push a pull při spuštění metody.
3. (Nepovinné) Zobrazení aktualizovaných dat pomocí průzkumníka objektů serveru SQL Server nebo nástroje REST, jako je Šumař. Všimněte si, že data byla synchronizována mezi back-endovou databází Azure Mobile App a místním úložištěm.
4. V aplikaci klikněte na zaškrtávací políčko vedle několika položek a dokončete je v místním obchodě.

   `CompleteItemAsync`volání `SyncAsync` synchronizovat každou dokončenou položku s back-endem mobilní aplikace. `SyncAsync`volání jak push, tak pull.
   **Kdykoli spustíte vyžádat vyžádat tabulku, kterou klient provedl změny, push na kontext synchronizace klienta je vždy proveden nejprve automaticky**. Implicitní nabízení zajišťuje, že všechny tabulky v místním úložišti spolu s relacemi zůstanou konzistentní. Další informace o tomto chování najdete [v tématu Offline synchronizace dat v Mobilních aplikacích Azure].

## <a name="review-the-client-sync-code"></a>Kontrola kódu synchronizace klienta
Projekt klienta Xamarin, který jste stáhli po dokončení kurzu [Vytvořit aplikaci Xamarin iOS] již obsahuje kód podporující offline synchronizaci pomocí místní databáze SQLite. Zde je stručný přehled toho, co je již zahrnuto v kódu kurzu. Koncepční přehled této funkce najdete v tématu [Offline synchronizace dat v mobilních aplikacích Azure].

* Před provedením všech operací tabulky musí být místní úložiště inicializováno. Místní databáze úložiště je `QSTodoListViewController.ViewDidLoad()` inicializována při spuštění `QSTodoService.InitializeStoreAsync()`. Tato metoda vytvoří novou místní databázi `MobileServiceSQLiteStore` SQLite pomocí třídy poskytované sadou Azure Mobile App client SDK.

    Metoda `DefineTable` vytvoří tabulku v místním úložišti, která odpovídá polím v zadaném typu, `ToDoItem` v tomto případě. Typ nemusí obsahovat všechny sloupce, které jsou ve vzdálené databázi. Je možné uložit pouze podmnožinu sloupců.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* Člen `todoTable` `QSTodoService` je `IMobileServiceSyncTable` typu namísto `IMobileServiceTable`. Tabulka IMobileServiceSyncTable přesměruje všechny operace tabulky vytvoření, čtení, aktualizace a odstranění (CRUD) do databáze místního úložiště.

    Můžete se rozhodnout, kdy se tyto změny zasouvá do back-endu mobilní aplikace Azure voláním `IMobileServiceSyncContext.PushAsync()`. Kontext synchronizace pomáhá zachovat relace tabulek sledováním a odesíláním `PushAsync` změn ve všech tabulkách, které klientská aplikace změnila při volání.

    Zadaný kód `QSTodoService.SyncAsync()` volá synchronizovat vždy, když je aktualizován seznam todoitem nebo je přidán nebo dokončen todoitem. Aplikace se synchronizuje po každé místní změně. Pokud je vyžádat je spuštěna proti tabulce, která má čekající místní aktualizace sledovány kontextu, že operace vyžádat automaticky spustí kontext push jako první.

    V poskytnutém kódu jsou dotazovány `TodoItem` všechny záznamy ve vzdálené tabulce, ale je také možné filtrovat `PushAsync`záznamy předáním ID dotazu a dotazu na aplikaci . Další informace najdete v části *Přírůstková synchronizace* v [offline synchronizaci dat v Mobilních aplikacích Azure].

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

## <a name="additional-resources"></a>Další zdroje
* [Offline synchronizace dat pro Azure Mobile Apps]
* [Azure Mobilní aplikace .NET SDK HOWTO][8]

<!-- Images -->

<!-- URLs. -->
[Vytvoření aplikace Xamarin pro iOS]: app-service-mobile-xamarin-ios-get-started.md
[Offline synchronizace dat pro Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
