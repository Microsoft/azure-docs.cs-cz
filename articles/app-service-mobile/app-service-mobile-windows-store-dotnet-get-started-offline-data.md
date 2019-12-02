---
title: Povolit offline synchronizaci vaší aplikace pro UWP
description: Naučte se používat mobilní aplikaci Azure k ukládání a synchronizaci offline dat v aplikaci Univerzální platforma Windows (UWP).
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 66ae11fb0532ae2a138eb8590eb92de15e997dfa
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668407"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Zapnutí offline synchronizace u aplikace pro Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
> Pokud chcete do vaší mobilní aplikace integrovat cloudové služby, ještě dnes se zaregistrujte do služeb [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak přidat offline podporu do aplikace Univerzální platforma Windows (UWP) pomocí back-endu mobilní aplikace Azure. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidávání nebo upravování dat – i když není k dispozici žádné síťové připojení. Změny jsou uloženy v místní databázi. Jakmile je zařízení zase online, tyto změny se synchronizují se vzdáleným back-endu.

V tomto kurzu aktualizujete projekt aplikace UWP z kurzu [Vytvoření aplikace pro Windows] , která podporuje offline funkce Azure Mobile Apps. Pokud nepoužíváte stažený projekt serveru pro rychlé zahájení, musíte do svého projektu přidat balíčky rozšíření pro přístup k datům. Další informace o balíčcích rozšíření serveru najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="requirements"></a>Požadavky  
Tento kurz vyžaduje následující požadavky:

* Visual Studio 2013 běžet v Windows 8.1 nebo novějším.
* Dokončení [Vytvoření aplikace]pro Windows[vytvoří aplikaci pro Windows].
* [Úložiště SQLite pro Azure Mobile Services][sqlite store nuget]
* [SQLite pro vývoj Univerzální platforma Windows](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizace klientské aplikace tak, aby podporovala offline funkce
Funkce offline mobilní aplikace v Azure umožňují interakci s místní databází, když jste v offline scénáři. Pro použití těchto funkcí v aplikaci inicializujete rozhraní [SyncContext][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable]. SQLite se používá jako místní úložiště na zařízení.

1. Nainstalujte [modul runtime SQLite pro Univerzální platforma Windows](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. V aplikaci Visual Studio otevřete Správce balíčků NuGet pro projekt aplikace UWP, který jste dokončili v kurzu [Vytvoření aplikace pro Windows] .
    Vyhledejte a nainstalujte balíček NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** .
3. V Průzkumník řešení klikněte pravým tlačítkem na **odkazy** > **Přidat odkaz...** > **Universal Windows** > **Extensions**a pak pro Univerzální platforma Windows aplikace povolte jak pro **Univerzální platforma Windows** , tak pro **Visual C++ 2015 runtime**.

    ![Přidat odkaz na UWP SQLite][1]
4. Otevřete soubor MainPage.xaml.cs a odkomentujte definici `#define OFFLINE_SYNC_ENABLED`.
5. V aplikaci Visual Studio stiskněte klávesu **F5** a znovu sestavte a spusťte klientskou aplikaci. Aplikace funguje stejně jako předtím, než jste povolili offline synchronizaci. Místní databáze je ale teď naplněná daty, která se dají použít v offline scénáři.

## <a name="update-sync"></a>Aktualizujte aplikaci, aby se odpojila od back-endu.
V této části přerušíte připojení k back-endu mobilní aplikace, aby se simulovala situace v režimu offline. Při přidávání datových položek vám obslužná rutina výjimky upozorní, že aplikace je v offline režimu. V tomto stavu se nové položky přidávají do místního úložiště a v případě příštího spuštění v připojeném stavu se budou synchronizovat s back-endu mobilní aplikace.

1. Upravte App.xaml.cs ve sdíleném projektu. Odkomentujte inicializaci **MobileServiceClient** a přidejte následující řádek, který používá neplatnou adresu URL mobilní aplikace:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    V případě, že zakážete Wi-Fi a mobilní sítě v zařízení nebo použijete režim v letadlech, můžete také předvést offline chování.
2. Stisknutím klávesy **F5** Sestavte a spusťte aplikaci. Všimněte si, že při aktualizaci se synchronizace po spuštění aplikace nezdařila.
3. Zadejte nové položky a Všimněte si, že při každém kliknutí na **Uložit**dojde k chybě nabízení oznámení se stavem [CancelledByNetworkError] . Nové položky ToDo ale existují v místním úložišti, dokud je nebudete moct vložit do back-endu mobilní aplikace.  Pokud potlačíte tyto výjimky v produkční aplikaci, chová se klientská aplikace, jako by byla stále připojená k back-endu mobilní aplikace.
4. Zavřete aplikaci a restartujte ji, abyste ověřili, že nové položky, které jste vytvořili, jsou trvale uložené v místním úložišti.
5. Volitelné V aplikaci Visual Studio otevřete **Průzkumník serveru**. Přejděte do databáze ve **službě Azure**->**databáze SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumník objektů systému SQL Server**. Teď můžete přejít k tabulce SQL Database a jejímu obsahu. Ověřte, že se data v back-end databázi nezměnila.
6. Volitelné K dotazování mobilního back-endu použijte nástroj REST, jako je Fiddler nebo Poster, pomocí dotazu GET ve formuláři `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aktualizujte aplikaci tak, aby se znovu připojila k back-endu mobilní aplikace.
V této části znovu připojíte aplikaci k back-endu mobilní aplikace. Tyto změny simulují opětovné připojení k síti v aplikaci.

Při prvním spuštění aplikace volá obslužná rutina události `OnNavigatedTo` `InitLocalStoreAsync`. Tato metoda zase volá `SyncAsync` k synchronizaci místního úložiště s back-end databází. Aplikace se při spuštění pokusí o synchronizaci.

1. Otevřete App.xaml.cs ve sdíleném projektu a odkomentujte předchozí inicializaci `MobileServiceClient`, aby se použila správná adresa URL mobilní aplikace.
2. Stisknutím klávesy **F5** znovu sestavíte a spustíte aplikaci. Aplikace synchronizuje místní změny s back-endu mobilní aplikace Azure pomocí operací push a pull, když se spustí obslužná rutina události `OnNavigatedTo`.
3. Volitelné Zobrazte aktualizovaná data pomocí Průzkumník objektů systému SQL Server nebo nástroje REST, jako je Fiddler. Všimněte si, že data byla synchronizovaná mezi databází back-end mobilní aplikace Azure a místním úložištěm.
4. V aplikaci klikněte na zaškrtávací políčko vedle několika položek, abyste je mohli doplňovat v místním úložišti.

   `UpdateCheckedTodoItem` volá `SyncAsync` k synchronizaci každé dokončené položky s back-endu mobilní aplikace. `SyncAsync` volá metodu push a Pull. Kdykoli však provedete přijetí **změn u tabulky, u které klient provedl změny, je nabízení vždy provedeno automaticky**. Tím zajistíte, že všechny tabulky v místním úložišti spolu s relacemi zůstanou konzistentní. Výsledkem tohoto chování může být neočekávané vložení.  Další informace o tomto chování najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="api-summary"></a>Souhrn rozhraní API
Pro podporu offline funkcí Mobile Services jsme použili rozhraní [IMobileServiceSyncTable] a inicializoval [MobileServiceClient. SyncContext][synccontext] s místní databází sqlite. V režimu offline funguje normální operace CRUD pro Mobile Apps, jako kdyby byla aplikace pořád připojená, zatímco k operacím dochází v místním úložišti. K synchronizaci místního úložiště se serverem slouží následující metody:

* **[PushAsync]** Vzhledem k tomu, že je tato metoda členem [IMobileServicesSyncContext], jsou změny ve všech tabulkách vloženy do back-endu. Na server jsou odesílány pouze záznamy s místními změnami.
* **[PullAsync]** Vyžádání obsahu se spouští z [IMobileServiceSyncTable]. V případě sledovaných změn v tabulce se spustí implicitní nabízení oznámení, aby se zajistilo, že všechny tabulky v místním úložišti spolu s relacemi zůstanou konzistentní. Parametr *pushOtherTables* určuje, zda jsou jiné tabulky v kontextu vloženy do implicitního nabízeného oznámení. Parametr *dotazu* přijímá [IMobileServiceTableQuery\<t >][IMobileServiceTableQuery] nebo řetězec dotazu OData k filtrování vrácených dat. Parametr *QueryId* slouží k definování přírůstkové synchronizace. Další informace najdete v tématu [synchronizace offline dat v Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]** Vaše aplikace by měla pravidelně volat tuto metodu, aby se vymazala zastaralá data z místního úložiště. Parametr *Force* použijte v případě, že potřebujete vymazat všechny změny, které ještě nebyly synchronizovány.

Další informace o těchto konceptech najdete v tématu [synchronizace offline dat v Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Další informace
Následující témata obsahují další informace o funkci offline synchronizace Mobile Apps:

* [Synchronizace offline dat v prostředí Azure Mobile Apps]
* [POSTUPY pro sadu Azure Mobile Apps .NET SDK][8]

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
[Synchronizace offline dat v prostředí Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Vytvoření aplikace pro Windows]: app-service-mobile-windows-store-dotnet-get-started.md
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
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
