---
title: Povolení offline synchronizace pro vaši aplikaci UPW
description: Přečtěte si, jak používat mobilní aplikaci Azure k ukládání do mezipaměti a synchronizaci offline dat v aplikaci Univerzální platforma Windows (UPW).
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 236d4c199a13b02bcd82ae02657bbd35e45f729b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458813"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Zapnutí offline synchronizace u aplikace pro Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz ukazuje, jak přidat podporu offline do aplikace univerzální platformy Windows (UPW) pomocí back-endu mobilní aplikace Azure. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidávání nebo úpravy dat – i když neexistuje žádné síťové připojení. Změny jsou uloženy v místní databázi. Jakmile je zařízení opět online, tyto změny se synchronizují se vzdáleným back-endem.

V tomto kurzu aktualizujete projekt aplikace UPW z kurzu [Vytvořit aplikaci pro Windows] pro podporu offline funkcí Azure Mobile Apps. Pokud nepoužíváte stažený projekt serveru rychlého startu, je nutné do projektu přidat balíčky rozšíření pro přístup k datům. Další informace o balíčcích rozšíření serveru naleznete v [tématu Práce s back-endovým serverem .NET SDK pro mobilní aplikace Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete v tématu [Offline synchronizace dat v Mobilních aplikacích Azure].

## <a name="requirements"></a>Požadavky  
Tento kurz vyžaduje následující předpoklady:

* Visual Studio 2013 běží ve Windows 8.1 nebo novějším.
* Dokončení [vytvoření aplikace pro Windows][vytvořte aplikaci pro Windows].
* [Úložiště SQLite pro mobilní služby Azure][sqlite store nuget]
* [SQLite pro vývoj univerzální platformy Windows](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizace klientské aplikace pro podporu offline funkcí
Funkce Azure Mobile App offline umožňují interakci s místní databází, když jste v offline scénáři. Chcete-li tyto funkce používat v aplikaci, inicializujete rozhraní [SyncContext.][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable] SQLite se používá jako místní úložiště v zařízení.

1. Nainstalujte [runtime SQLite pro univerzální platformu Windows](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. V Sadě Visual Studio otevřete Správce balíčků NuGet pro projekt aplikace UPW, který jste dokončili v kurzu [vytvořit aplikaci pro Windows.]
    Vyhledejte a nainstalujte balíček **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet.
3. V Průzkumníku řešení klikněte pravým **tlačítkem** > myši na reference **Přidat odkaz...** > **Univerzální** > **rozšíření**Windows a povolte **sqlite pro univerzální platformu Windows** a prostředí Visual **C++ 2015 Runtime pro aplikace univerzální platformy Windows**.

    ![Přidat odkaz Na Wp SQLite][1]
4. Otevřete soubor MainPage.xaml.cs a `#define OFFLINE_SYNC_ENABLED` odkomentujte definici.
5. V sadě Visual Studio stisknutím klávesy **F5** znovu sestavíte a spusťte klientskou aplikaci. Aplikace funguje stejně jako předtím, než jste povolili offline synchronizaci. Místní databáze je však nyní naplněna daty, která lze použít v offline scénáři.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Aktualizace aplikace pro odpojení od back-endu
V této části přerušíte připojení k back-endu mobilní aplikace, abyste simulovali situaci offline. Když přidáte datové položky, obslužná rutina výjimky vám řekne, že aplikace je v režimu offline. V tomto stavu budou nové položky přidané v místním úložišti a budou synchronizovány s back-endem mobilní aplikace při dalším spuštění push v připojeném stavu.

1. Upravte App.xaml.cs ve sdíleném projektu. Zakomentujte inicializaci **klienta MobileServiceClient** a přidejte následující řádek, který používá neplatnou adresu URL mobilní aplikace:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Můžete také demonstrovat chování offline zakázáním wifi a mobilních sítí v zařízení nebo pomocí režimu v letadle.
2. Stisknutím **klávesy F5** vytvořte a spusťte aplikaci. Všimněte si, že se při aktualizaci při aktualizaci synchronizace nezdařila.
3. Zadejte nové položky a všimněte si, že nabízená položka se nezdaří se stavem [CanceledByNetworkError] pokaždé, když klepnete na **uložit**. Nové položky todo však existují v místním úložišti, dokud je nelze zasunout do back-endu mobilní aplikace.  Pokud v produkční aplikaci potlačíte tyto výjimky, klientská aplikace se bude chovat, jako by byla stále připojená k back-endu mobilní aplikace.
4. Zavřete aplikaci a restartujte ji, abyste ověřili, že nové položky, které jste vytvořili, jsou trvalé v místním úložišti.
5. (Nepovinné) V sadě Visual Studio otevřete **Průzkumníka serveru**. Přejděte do databáze v **Azure**->**SQL Databases**. Klepněte pravým tlačítkem myši na databázi a vyberte **otevřít v průzkumníku objektů serveru SQL Server**. Nyní můžete procházet databázový obsah SQL a její obsah. Ověřte, zda se data v back-endové databázi nezměnila.
6. (Nepovinné) Pomocí nástroje REST, například Fiddler nebo Postman, můžete dotazovat na `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`mobilní back-end pomocí dotazu GET ve formuláři .

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Aktualizace aplikace pro opětovné připojení back-endu mobilní aplikace
V této části znovu připojíte aplikaci k back-endu mobilní aplikace. Tyto změny simulují opětovné připojení k síti v aplikaci.

Při prvním spuštění aplikace `OnNavigatedTo` volání `InitLocalStoreAsync`obslužné rutiny události . Tato metoda zase `SyncAsync` volá k synchronizaci místního úložiště s back-endovou databází. Aplikace se pokusí synchronizovat při spuštění.

1. Otevřete App.xaml.cs ve sdíleném projektu a odkomentujte předchozí `MobileServiceClient` inicializaci, abyste použili správnou adresu URL mobilní aplikace.
2. Stisknutím klávesy **F5** aplikaci znovu sestavujete a spusťte. Aplikace synchronizuje místní změny s back-endem Mobilní aplikace Azure `OnNavigatedTo` pomocí operací push a pull při spuštění obslužné rutiny události.
3. (Nepovinné) Zobrazení aktualizovaných dat pomocí průzkumníka objektů serveru SQL Server nebo nástroje REST, jako je Šumař. Všimněte si, že data byla synchronizována mezi back-endovou databází Azure Mobile App a místním úložištěm.
4. V aplikaci klikněte na zaškrtávací políčko vedle několika položek a dokončete je v místním obchodě.

   `UpdateCheckedTodoItem`volání `SyncAsync` synchronizovat každou dokončenou položku s back-endem mobilní aplikace. `SyncAsync`volání jak push, tak pull. Však **vždy, když spustíte vyžádat proti tabulce, kterou klient provedl změny, push je vždy automaticky spuštěna**. Toto chování zajišťuje, že všechny tabulky v místním úložišti spolu s relacemi zůstanou konzistentní. Toto chování může mít za následek neočekávané push.  Další informace o tomto chování najdete [v tématu Offline synchronizace dat v Mobilních aplikacích Azure].

## <a name="api-summary"></a>Souhrn rozhraní API
Pro podporu offline funkcí mobilních služeb jsme použili rozhraní [IMobileServiceSyncTable] a inicializovali [MobileServiceClient.SyncContext][synccontext] s místní databází SQLite. V režimu offline fungují normální operace CRUD pro mobilní aplikace, jako by aplikace byla stále připojená, zatímco operace probíhají proti místnímu úložišti. K synchronizaci místního úložiště se serverem se používají následující metody:

* **[PushAsync]** Vzhledem k tomu, že tato metoda je členem [IMobileServicesSyncContext], změny ve všech tabulkách jsou posunuty do back-endu. Na server jsou odesílány pouze záznamy s místními změnami.
* **[PullAsync]** Pull je spuštěn z [IMobileServiceSyncTable]. Pokud jsou sledovány změny v tabulce, implicitní push je spuštěna, aby se ujistil, že všechny tabulky v místním úložišti spolu s relacemi zůstávají konzistentní. Parametr *pushOtherTables* určuje, zda jsou ostatní tabulky v kontextu posunuty v implicitní push. Parametr *dotazu* přebírá řetězec dotazu [\<IMobileServiceTableQuery T>][IMobileServiceTableQuery] nebo OData k filtrování vrácených dat. Parametr *queryId* se používá k definování přírůstkové synchronizace. Další informace najdete [v tématu Offline synchronizace dat v Mobilních aplikacích Azure](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]** Aplikace by měla pravidelně volat tuto metodu k vymazání zastaralých dat z místního úložiště. Parametr *force* použijte v případě, že potřebujete vymazat všechny změny, které ještě nebyly synchronizovány.

Další informace o těchto konceptech najdete [v tématu Offline synchronizace dat v Mobilních aplikacích Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Další informace
Následující témata poskytují další základní informace o funkci offline synchronizace mobilních aplikací:

* [Offline synchronizace dat pro Azure Mobile Apps]
* [Azure Mobilní aplikace .NET SDK HOWTO][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Offline synchronizace dat pro Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[vytvoření aplikace pro Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[Chyba CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
