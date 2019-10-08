---
title: Povolení offline synchronizace pro mobilní aplikace Azure (Xamarin Android)
description: Naučte se používat App Service mobilní aplikace pro ukládání a synchronizaci offline dat v aplikaci Xamarin Android.
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 8a7c9672db0a7cc12e78846bf82c54e3ee383c66
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025111"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikaci Xamarin. Android

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj koncových a integrovaných služeb od centrálního vývoje mobilních aplikací. Vývojáři **mohou pomocí sestavování**, **testování** a **distribuce** služeb nastavit kanál průběžné integrace a doručování. Po nasazení aplikace mohou vývojáři sledovat stav a využití své aplikace pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **push** . Vývojáři můžou také využít **ověřování** k ověřování uživatelů a **datových** služeb, aby zachovaly a synchronizovaly data aplikací v cloudu.
> Pokud chcete v mobilní aplikaci integrovat cloudové služby, zaregistrujte se App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) ještě dnes.

## <a name="overview"></a>Přehled

Tento kurz zavádí funkci offline synchronizace pro Azure Mobile Apps pro Xamarin. Android. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidávání nebo upravování dat – i když není dostupné žádné síťové připojení. Změny jsou uloženy v místní databázi.
Jakmile je zařízení zase online, tyto změny se synchronizují se vzdálenou službou.

V tomto kurzu aktualizujete projekt klienta z kurzu [Vytvoření aplikace Xamarin Android] pro podporu offline funkcí Azure Mobile Apps. Pokud nepoužíváte stažený projekt serveru pro rychlé zahájení, musíte do svého projektu přidat balíčky rozšíření pro přístup k datům. Další informace o balíčcích rozšíření serveru najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizace klientské aplikace tak, aby podporovala offline funkce

Funkce offline mobilní aplikace v Azure umožňují interakci s místní databází, když jste v offline scénáři. Chcete-li použít tyto funkce v aplikaci, inicializujte [SyncContext] do místního úložiště. Pak na tabulku odkázat prostřednictvím rozhraní [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) . SQLite se používá jako místní úložiště na zařízení.

1. V aplikaci Visual Studio otevřete Správce balíčků NuGet v projektu, který jste dokončili v kurzu [Vytvoření aplikace Xamarin Android] .  Vyhledejte a nainstalujte balíček NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** .
2. Otevřete soubor ToDoActivity.cs a odkomentujte definici `#define OFFLINE_SYNC_ENABLED`.
3. V aplikaci Visual Studio stiskněte klávesu **F5** a znovu sestavte a spusťte klientskou aplikaci. Aplikace funguje stejně jako předtím, než jste povolili offline synchronizaci. Místní databáze je ale teď naplněná daty, která se dají použít v offline scénáři.

## <a name="update-sync"></a>Aktualizujte aplikaci, aby se odpojila od back-endu.

V této části přerušíte připojení k back-endu mobilní aplikace, aby se simulovala situace v režimu offline. Při přidávání datových položek vám obslužná rutina výjimky upozorní, že aplikace je v offline režimu. V tomto stavu se nové položky přidávají do místního úložiště a při spuštění nabízených oznámení v připojeném stavu se synchronizují s back-endu mobilní aplikace.

1. Upravte ToDoActivity.cs ve sdíleném projektu. Změňte **ApplicationUrl nebyla** tak, aby ukazoval na neplatnou adresu URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    V případě, že zakážete Wi-Fi a mobilní sítě v zařízení nebo použijete režim v letadlech, můžete také předvést offline chování.
2. Stisknutím klávesy **F5** Sestavte a spusťte aplikaci. Všimněte si, že při aktualizaci se synchronizace po spuštění aplikace nezdařila.
3. Zadejte nové položky a Všimněte si, že při každém kliknutí na **Uložit**se nabízená oznámení nezdařila se stavem [CancelledByNetworkError]. Nové položky ToDo ale existují v místním úložišti, dokud je nebudete moct vložit do back-endu mobilní aplikace.  Pokud potlačíte tyto výjimky v produkční aplikaci, chová se klientská aplikace, jako by byla stále připojená k back-endu mobilní aplikace.
4. Zavřete aplikaci a restartujte ji, abyste ověřili, že nové položky, které jste vytvořili, jsou trvale uložené v místním úložišti.
5. Volitelné V aplikaci Visual Studio otevřete **Průzkumník serveru**. Přejděte do databáze ve **službě Azure**->**databáze SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumník objektů systému SQL Server**. Teď můžete přejít k tabulce SQL Database a jejímu obsahu. Ověřte, že se data v back-end databázi nezměnila.
6. Volitelné Použijte nástroj REST, jako je Fiddler nebo post, k dotazování mobilního back-endu pomocí dotazu GET ve formuláři `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aktualizujte aplikaci tak, aby se znovu připojila k back-endu mobilní aplikace.

V této části znovu připojte aplikaci k back-endu mobilní aplikace. Při prvním spuštění aplikace volá obslužná rutina události `OnCreate` `OnRefreshItemsSelected`. Tato metoda volá `SyncAsync` pro synchronizaci místního úložiště s back-end databází.

1. Otevřete ToDoActivity.cs ve sdíleném projektu a vraťte změnu vlastnosti **ApplicationUrl nebyla** .
2. Stisknutím klávesy **F5** znovu sestavíte a spustíte aplikaci. Aplikace synchronizuje místní změny s back-endu mobilní aplikace Azure pomocí operací push a pull, když se spustí metoda `OnRefreshItemsSelected`.
3. Volitelné Zobrazte aktualizovaná data pomocí Průzkumník objektů systému SQL Server nebo nástroje REST, jako je Fiddler. Všimněte si, že data byla synchronizovaná mezi databází back-end mobilní aplikace Azure a místním úložištěm.
4. V aplikaci klikněte na zaškrtávací políčko vedle několika položek, abyste je mohli doplňovat v místním úložišti.

   `CheckItem` volá `SyncAsync` pro synchronizaci každé dokončené položky s back-endu mobilní aplikace. `SyncAsync` vyvolá volání push a Pull. **Kdykoli spustíte akci pull pro tabulku, na kterou klient provedl změny, je nabízení vždy provedeno automaticky**. Tím se zajistí, že všechny tabulky v místním úložišti spolu s relacemi zůstanou konzistentní. Výsledkem tohoto chování může být neočekávané vložení. Další informace o tomto chování najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Kontrola kódu synchronizace klienta

Projekt klienta Xamarin, který jste stáhli po dokončení kurzu [Vytvoření aplikace Xamarin Android] , již obsahuje kód podporující offline synchronizaci s použitím místní databáze sqlite. Tady je stručný přehled toho, co je již zahrnuto v kódu kurzu. Koncepční přehled této funkce najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

* Před provedením jakékoli operace tabulky je nutné inicializovat místní úložiště. Místní databáze úložiště se inicializuje, když `ToDoActivity.OnCreate()` spustí `ToDoActivity.InitLocalStoreAsync()`. Tato metoda vytvoří místní databázi SQLite pomocí třídy `MobileServiceSQLiteStore`, kterou poskytuje sada SDK pro klientské Mobile Apps Azure.

    Metoda `DefineTable` vytvoří tabulku v místním úložišti, která odpovídá polím v poskytnutém typu, v tomto případě `ToDoItem`. Typ nemusí zahrnovat všechny sloupce, které jsou ve vzdálené databázi. Je možné uložit pouze podmnožinu sloupců.

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
* Člen `toDoTable` `ToDoActivity` je typu `IMobileServiceSyncTable` namísto `IMobileServiceTable`. IMobileServiceSyncTable přesměruje všechny operace vytvoření, čtení, aktualizace a odstranění (CRUD) do místní databáze úložiště.

    Rozhodnete se, že se změny odešlou do back-endu mobilní aplikace Azure voláním `IMobileServiceSyncContext.PushAsync()`. Kontext synchronizace pomáhá zachovat vztahy mezi tabulkami sledováním a vkládáním změn ve všech tabulkách. klientská aplikace se změnila při volání `PushAsync`.

    Poskytnutý kód volá `ToDoActivity.SyncAsync()` ke synchronizaci při každém obnovení seznamu TodoItem nebo přidání nebo dokončení TodoItem. Po každé změně místní změny se kód synchronizuje.

    V poskytnutém kódu se dotazují na všechny záznamy ve vzdálené tabulce `TodoItem`, ale je možné je také filtrovat pomocí předání ID dotazu a dotazu do `PushAsync`. Další informace najdete v části o *přírůstkové synchronizaci* při [Synchronizace offline dat v prostředí Azure Mobile Apps].

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

## <a name="additional-resources"></a>Další materiály

* [Synchronizace offline dat v prostředí Azure Mobile Apps]
* [POSTUPY pro sadu Azure Mobile Apps .NET SDK][8]

<!-- URLs. -->
[Vytvoření aplikace Xamarin Android]: ./app-service-mobile-xamarin-android-get-started.md
[Synchronizace offline dat v prostředí Azure Mobile Apps]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Vytvoření aplikace Xamarin Android]: app-service-mobile-xamarin-android-get-started.md
[Synchronizace offline dat v prostředí Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
