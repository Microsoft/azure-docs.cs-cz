---
title: Povolit offline synchronizaci (Xamarin iOS)
description: Naučte se používat App Service mobilní aplikace pro ukládání a synchronizaci offline dat v aplikaci Xamarin iOS.
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 615f8f028182178928d3d755c102daceef402ef4
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668251"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikace Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
> Pokud chcete do vaší mobilní aplikace integrovat cloudové služby, ještě dnes se zaregistrujte do služeb [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Přehled
Tento kurz zavádí funkci offline synchronizace pro Azure Mobile Apps pro Xamarin. iOS. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidávání nebo upravování dat – i když není dostupné žádné síťové připojení. Změny jsou uloženy v místní databázi. Jakmile je zařízení zase online, tyto změny se synchronizují se vzdálenou službou.

V tomto kurzu aktualizujte projekt aplikace Xamarin. iOS z části [Vytvoření aplikace pro Xamarin iOS] tak, aby podporovala offline funkce Azure Mobile Apps. Pokud nepoužíváte stažený projekt serveru pro rychlé zahájení, musíte do svého projektu přidat balíčky rozšíření pro přístup k datům. Další informace o balíčcích rozšíření serveru najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizace klientské aplikace tak, aby podporovala offline funkce
Funkce offline mobilní aplikace v Azure umožňují interakci s místní databází, když jste v offline scénáři. Pokud chcete tyto funkce použít ve své aplikaci, inicializujte [SyncContext] do místního úložiště. Odkaz na tabulku prostřednictvím rozhraní [IMobileServiceSyncTable]. SQLite se používá jako místní úložiště na zařízení.

1. Otevřete Správce balíčků NuGet v projektu, který jste dokončili v kurzu [Vytvoření aplikace pro Xamarin iOS] , a pak vyhledejte a nainstalujte balíček NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** .
2. Otevřete soubor QSTodoService.cs a odkomentujte definici `#define OFFLINE_SYNC_ENABLED`.
3. Znovu sestavte a spusťte klientskou aplikaci. Aplikace funguje stejně jako předtím, než jste povolili offline synchronizaci. Místní databáze je ale teď naplněná daty, která se dají použít v offline scénáři.

## <a name="update-sync"></a>Aktualizujte aplikaci, aby se odpojila od back-endu.
V této části přerušíte připojení k back-endu mobilní aplikace, aby se simulovala situace v režimu offline. Při přidávání datových položek vám obslužná rutina výjimky upozorní, že aplikace je v offline režimu. V tomto stavu se nové položky přidávají do místního úložiště a v případě příštího spuštění v připojeném stavu se budou synchronizovat s back-endu mobilní aplikace.

1. Upravte QSToDoService.cs ve sdíleném projektu. Změňte **ApplicationUrl nebyla** tak, aby ukazoval na neplatnou adresu URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    V případě, že zakážete Wi-Fi a mobilní sítě v zařízení nebo použijete režim v letadlech, můžete také předvést offline chování.
2. Sestavte a spusťte aplikaci. Všimněte si, že při aktualizaci se synchronizace po spuštění aplikace nezdařila.
3. Zadejte nové položky a Všimněte si, že při každém kliknutí na **Uložit**se nabízená oznámení nezdařila se stavem [CancelledByNetworkError]. Nové položky ToDo ale existují v místním úložišti, dokud je nebudete moct vložit do back-endu mobilní aplikace.  Pokud potlačíte tyto výjimky v produkční aplikaci, chová se klientská aplikace, jako by byla stále připojená k back-endu mobilní aplikace.
4. Zavřete aplikaci a restartujte ji, abyste ověřili, že nové položky, které jste vytvořili, jsou trvale uložené v místním úložišti.
5. Volitelné Pokud máte v počítači nainstalovanou aplikaci Visual Studio, otevřete **Průzkumník serveru**. Přejděte do databáze ve **službě Azure**-> **databáze SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumník objektů systému SQL Server**. Teď můžete přejít k tabulce SQL Database a jejímu obsahu. Ověřte, že se data v back-end databázi nezměnila.
6. Volitelné K dotazování mobilního back-endu použijte nástroj REST, jako je Fiddler nebo Poster, pomocí dotazu GET ve formuláři `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aktualizujte aplikaci tak, aby se znovu připojila k back-endu mobilní aplikace.
V této části znovu připojte aplikaci k back-endu mobilní aplikace. Tím se aplikace přesouvá z offline režimu do online stavu pomocí back-endu mobilní aplikace.   Pokud jste simulovali zlomek sítě vypnutím možnosti připojení k síti, nejsou potřeba žádné změny kódu.
Znovu zapněte síť.  Při prvním spuštění aplikace je volána metoda `RefreshDataAsync`. To zase volá `SyncAsync` k synchronizaci místního úložiště s back-end databází.

1. Otevřete QSToDoService.cs ve sdíleném projektu a vraťte změnu vlastnosti **ApplicationUrl nebyla** .
2. Znovu sestavte a spusťte aplikaci. Aplikace synchronizuje místní změny s back-endu mobilní aplikace Azure pomocí operací push a pull, když se spustí metoda `OnRefreshItemsSelected`.
3. Volitelné Zobrazte aktualizovaná data pomocí Průzkumník objektů systému SQL Server nebo nástroje REST, jako je Fiddler. Všimněte si, že data byla synchronizovaná mezi databází back-end mobilní aplikace Azure a místním úložištěm.
4. V aplikaci klikněte na zaškrtávací políčko vedle několika položek, abyste je mohli doplňovat v místním úložišti.

   `CompleteItemAsync` volá `SyncAsync` k synchronizaci každé dokončené položky s back-endu mobilní aplikace. `SyncAsync` volá metodu push a Pull.
   **Pokaždé, když provedete stažení z tabulky, na kterou klient provedl změny, se v kontextu synchronizace klienta vždy provádí první automatické spuštění**. Implicitní nabízená oznámení zajistí, že všechny tabulky v místním úložišti spolu s relacemi zůstanou konzistentní. Další informace o tomto chování najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Kontrola kódu synchronizace klienta
Projekt klienta Xamarin, který jste si stáhli po dokončení kurzu [Vytvoření aplikace pro Xamarin iOS] , už obsahuje kód podporující offline synchronizaci s použitím místní databáze sqlite. Tady je stručný přehled toho, co je již zahrnuto v kódu kurzu. Koncepční přehled této funkce najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

* Před provedením jakékoli operace tabulky je nutné inicializovat místní úložiště. Místní databáze úložiště se inicializuje, když `QSTodoListViewController.ViewDidLoad()` spustí `QSTodoService.InitializeStoreAsync()`. Tato metoda vytvoří novou místní databázi SQLite pomocí `MobileServiceSQLiteStore` třídy poskytované klientskou sadou SDK pro mobilní aplikace Azure.

    Metoda `DefineTable` vytvoří tabulku v místním úložišti, která odpovídá polím v poskytnutém typu, `ToDoItem` v tomto případě. Typ nemusí zahrnovat všechny sloupce, které jsou ve vzdálené databázi. Je možné uložit pouze podmnožinu sloupců.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* `todoTable` člen `QSTodoService` je `IMobileServiceSyncTable` typ místo `IMobileServiceTable`. IMobileServiceSyncTable přesměruje všechny operace vytvoření, čtení, aktualizace a odstranění (CRUD) do místní databáze úložiště.

    Rozhodnete, kdy se tyto změny vloží do back-endu mobilní aplikace Azure voláním `IMobileServiceSyncContext.PushAsync()`. Kontext synchronizace pomáhá zachovat vztahy mezi tabulkami sledováním a vkládáním změn ve všech tabulkách. klientská aplikace se změnila při volání `PushAsync`.

    Poskytnutý kód volá `QSTodoService.SyncAsync()` k synchronizaci při každém obnovení seznamu TodoItem nebo přidání nebo dokončení TodoItem. Aplikace se po každé místní změně synchronizuje. Pokud je akce vyžádání obsahu provedena na tabulku, která obsahuje nedokončené místní aktualizace, které jsou sledovány v kontextu, bude tato operace vyžádaného volání automaticky aktivovat kontextovou nabízenou vložení.

    V poskytnutém kódu se dotazují na všechny záznamy v tabulce vzdálené `TodoItem`, ale je možné je také filtrovat předáním ID dotazu a dotazu do `PushAsync`. Další informace najdete v části o *přírůstkové synchronizaci* při [Synchronizace offline dat v prostředí Azure Mobile Apps].

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

## <a name="additional-resources"></a>Další materiály
* [Synchronizace offline dat v prostředí Azure Mobile Apps]
* [POSTUPY pro sadu Azure Mobile Apps .NET SDK][8]

<!-- Images -->

<!-- URLs. -->
[Vytvoření aplikace pro Xamarin iOS]: app-service-mobile-xamarin-ios-get-started.md
[Synchronizace offline dat v prostředí Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
