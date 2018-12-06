---
title: Povolení offline synchronizace pro mobilní aplikace Azure (Xamarin Android)
description: Zjistěte, jak používat mobilní aplikace služby App Service do mezipaměti a synchronizaci offline dat v aplikaci Xamarin Android
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
ms.openlocfilehash: 33b95f117908fc0d7028a85defd148fdd821c391
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963602"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikace Xamarin.Android

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled

Tento kurz představuje funkci offline synchronizace služby Azure Mobile Apps pro Xamarin.Android. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidání nebo úprava dat – i v případě, že není žádné síťové připojení. Změny jsou uloženy v místní databázi.
Když je zařízení online, tyto změny se synchronizují s vzdálené služby.

V tomto kurzu se při aktualizaci projektu klienta z kurzu [vytvoření aplikace Xamarin Android] pro podporu offline funkce Azure Mobile Apps. Pokud použijete serverový projekt stažené rychlý start, musíte přidat balíčky rozšíření datového přístupu do vašeho projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s back-end .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizovat klientskou aplikaci pro podporu offline funkce

Azure Mobile App umožňují vám offline funkce interakci s místní databází, když se ve scénáři offline. Pokud chcete tyto funkce využít ve vaší aplikaci, inicializovat [SyncContext] do místního úložiště. Odkázat na tabulce prostřednictvím [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) rozhraní. SQLite slouží jako místní úložiště na zařízení.

1. V sadě Visual Studio, otevřete Správce balíčků NuGet v projektu, který jste dokončili [vytvoření aplikace Xamarin Android] kurzu.  Vyhledejte a nainstalujte **Microsoft.Azure.Mobile.Client.SQLiteStore** balíček NuGet.
2. Otevření souboru ToDoActivity.cs a zrušte komentář `#define OFFLINE_SYNC_ENABLED` definice.
3. V sadě Visual Studio, stiskněte **F5** klíč znovu sestavte a spusťte klientskou aplikaci. Aplikace funguje stejně, jako kdyby byl před povolením offline synchronizace. Místní databáze je nyní zaplněna s daty, která lze použít ve scénáři offline.

## <a name="update-sync"></a>Aktualizace aplikace se odpojit od back-endu

V této části přerušení připojení k back-endu mobilní aplikace pro simulaci offline situaci. Při přidávání položek dat, vaše obslužná rutina výjimky zjistíte, že aplikace je v režimu offline. V tomto stavu nové položky přidané v místním úložišti a se synchronizují do back-endu mobilní aplikace při spuštění push v připojeném stavu.

1. Upravte ToDoActivity.cs ve sdíleném projektu. Změnit **applicationURL** tak, aby odkazoval na neplatnou adresu URL:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Zakázáním Wi-Fi nebo mobilní sítě na zařízení nebo použitím režim v letadle, může ukázat offline chování.
2. Stisknutím klávesy **F5** sestavíte a spustíte aplikaci. Všimněte si, že synchronizace se nezdařila při aktualizaci při spuštění aplikace.
3. Zadejte nové položky a Všimněte si, že nabízená nezdaří se stavem [CancelledByNetworkError] pokaždé, když kliknete na **Uložit**. Nicméně nové položky seznamu úkolů existovat v místním úložišti, dokud se může doručit bez vyžádání do back-endu mobilní aplikace.  V produkční aplikace je-li potlačit tyto výjimky klientská aplikace chová, jako by je stále připojeni k back-endu mobilní aplikace.
4. Aplikaci zavřete a restartujte ji k ověření, že jsou zachované místní úložiště nové položky, kterou jste vytvořili.
5. (Volitelné) V sadě Visual Studio, otevřete **Průzkumníka serveru**. Přejděte k vaší databázi v **Azure**->**databází SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumníku objektů systému SQL Server**. Teď můžete procházet do tabulky SQL database a její obsah. Ověřte, že nedošlo ke změně dat v back-end databáze.
6. (Volitelné) Použití REST nástroje, jako je Fiddler nebo Postman k dotazování mobilních back-endu, pomocí dotazu GET ve formě `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aktualizace aplikace znovu připojit back-endu mobilní aplikace

V této části opětovné připojení aplikace k back-endu mobilní aplikace. Při prvním spuštění aplikace, `OnCreate` volání obsluhy události `OnRefreshItemsSelected`. Tato metoda volá `SyncAsync` synchronizaci vašeho místního úložiště s back-end databáze.

1. Otevřete ToDoActivity.cs ve sdíleném projektu a vrátit zpět změny z **applicationURL** vlastnost.
2. Stisknutím klávesy **F5** klíč znovu sestavte a spusťte aplikaci. Aplikace synchronizuje místní změny pomocí operace push a pull back-end mobilní aplikace Azure při `OnRefreshItemsSelected` metody.
3. (Volitelné) Zobrazte aktualizovaná data pomocí Průzkumníku objektů systému SQL Server nebo REST nástroje, jako je Fiddleru. Všimněte si, že data se synchronizovala mezi databáze back-endu mobilní aplikace Azure a místní úložiště.
4. V aplikaci klikněte na zaškrtávací políčko vedle několik položek dokončení v místním úložišti.

   `CheckItem` volání `SyncAsync` položku synchronizace každou byla dokončena s back-endu mobilní aplikace. `SyncAsync` volání push a pull. **Pokaždé, když spustíte o přijetí změn pro tabulku, která klient provedl změny, push je vždy spuštěn automaticky**. Tím se zajistí, že všechny tabulky v místním úložišti spolu s relace zůstaly konzistentní. Toto chování může způsobit neočekávané nabízených oznámení. Další informace o tomto chování najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Revize kódu synchronizace klienta

Projekt Xamarin klienta, který jste stáhli, když jste dokončili kurz [vytvoření aplikace Xamarin Android] již obsahuje kód podporuje offline synchronizaci s použitím místní databázi SQLite. Tady je stručný přehled, co je již součástí kódu. Koncepční přehled funkce, najdete v části [Synchronizace offline dat v prostředí Azure Mobile Apps].

* Před provedením jakékoli operace s tabulkou, musí být inicializován místního úložiště. Místní úložiště databáze je inicializován při `ToDoActivity.OnCreate()` spustí `ToDoActivity.InitLocalStoreAsync()`. Tato metoda vytvoří místní databázi SQLite pomocí `MobileServiceSQLiteStore` třídy Azure Mobile Apps Client SDK k dispozici.

    `DefineTable` Metoda vytvoří tabulku v místním úložišti, která odpovídá polím v poskytnutého typu `ToDoItem` v tomto případě. Typ nemusí obsahovat všechny sloupce, které jsou vzdálené databáze. Je možné ukládat jenom podmnožinu sloupců.

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
* `toDoTable` Členem `ToDoActivity` je `IMobileServiceSyncTable` zadejte místo `IMobileServiceTable`. IMobileServiceSyncTable bude směrovat všechny vytvoření, čtení, aktualizace a odstranění (CRUD) operací tabulky k databázi místního úložiště.

    Rozhodnete, že při vložení změn do back-endu mobilní aplikace Azure pomocí volání `IMobileServiceSyncContext.PushAsync()`. Kontext synchronizace umožňuje zachovat relací mezi tabulkami sledováním a předávání změn operací push ve všech tabulkách klientská aplikace byla změněna při `PushAsync` je volána.

    Zadaný kód volá `ToDoActivity.SyncAsync()` synchronizovat vždy, když se aktualizuje seznam todoitem nebo úkolu se přidá nebo se dokončit. Kód se synchronizuje po každé změně místní.

    Zadaný kód všechny záznamy ve vzdáleném úložišti `TodoItem` dotazována tabulka, ale je také možné filtrovat záznamy předáním id dotazu a dotaz pro `PushAsync`. Další informace najdete v části *Přírůstková synchronizace* v [Synchronizace offline dat v prostředí Azure Mobile Apps].

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

* [Synchronizace offline dat v prostředí Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK postupy][8]

<!-- URLs. -->
[Vytvoření aplikace Xamarin Android]: ./app-service-mobile-xamarin-android-get-started.md
[Synchronizace offline dat v prostředí Azure Mobile Apps]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Vytvoření aplikace Xamarin Android]: app-service-mobile-xamarin-android-get-started.md
[Synchronizace offline dat v prostředí Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
