---
title: Povolení offline synchronizace u aplikace univerzální platformy Windows (UPW) s funkcí Mobile Apps | Dokumentace Microsoftu
description: Další informace o použití služby Azure Mobile Apps do mezipaměti a synchronizaci offline dat v aplikaci pro univerzální platformu Windows (UPW).
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 69ee9e7101a2b7337e1e42ff5ae09954fbfd50b2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52994928"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Zapnutí offline synchronizace u aplikace pro Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak přidat podporu offline režimu do aplikace univerzální platformy Windows (UPW) pomocí back-endu mobilní aplikace Azure. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidání nebo úprava dat – i v případě, že není žádné síťové připojení. Změny jsou uloženy v místní databázi. Když je zařízení online, tyto změny se synchronizují s vzdálené back-endem.

V tomto kurzu můžete aktualizovat projekt aplikace UPW z kurzu [vytvoření aplikace pro Windows] pro podporu offline funkce Azure Mobile Apps. Pokud použijete serverový projekt stažené rychlý start, musíte přidat balíčky rozšíření datového přístupu do vašeho projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s back-end .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="requirements"></a>Požadavky  
Tento kurz vyžaduje následující předpoklady:

* Visual Studio 2013 v systému Windows 8.1 nebo novější.
* Dokončení [vytvoření aplikace pro Windows][vytvoření aplikace pro windows].
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [SQLite pro vývoj pro univerzální platformu Windows](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizovat klientskou aplikaci pro podporu offline funkce
Azure Mobile App umožňují vám offline funkce interakci s místní databází, když se ve scénáři offline. Pokud chcete tyto funkce využít ve vaší aplikaci, inicializovat [SyncContext] [ synccontext] do místního úložiště. Odkázat na tabulce prostřednictvím [IMobileServiceSyncTable][IMobileServiceSyncTable] rozhraní. SQLite slouží jako místní úložiště na zařízení.

1. Nainstalujte [SQLite runtime pro univerzální platformu Windows](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. V sadě Visual Studio, otevřete Správce balíčků NuGet pro projekt aplikace UPW, který jste dokončili [vytvoření aplikace pro Windows] kurzu.
    Vyhledejte a nainstalujte **Microsoft.Azure.Mobile.Client.SQLiteStore** balíček NuGet.
3. V Průzkumníku řešení klikněte pravým tlačítkem na **odkazy** > **přidat odkaz...** > **Universal Windows** > **rozšíření**, oboje povolili **SQLite pro univerzální platformu Windows** a **2015 modulu Runtime Visual C++ pro Universal Windows Aplikace pro platformu**.

    ![Přidat odkaz na UPW SQLite][1]
4. Otevření souboru MainPage.xaml.cs a zrušte komentář `#define OFFLINE_SYNC_ENABLED` definice.
5. V sadě Visual Studio, stiskněte **F5** klíč znovu sestavte a spusťte klientskou aplikaci. Aplikace funguje stejně, jako kdyby byl před povolením offline synchronizace. Místní databáze je nyní zaplněna s daty, která lze použít ve scénáři offline.

## <a name="update-sync"></a>Aktualizace aplikace se odpojit od back-endu
V této části přerušení připojení k back-endu mobilní aplikace pro simulaci offline situaci. Při přidávání položek dat, vaše obslužná rutina výjimky zjistíte, že aplikace je v režimu offline. V tomto stavu nové položky přidané v místním úložišti a se budou synchronizovat s back-endu mobilní aplikace při další spuštění nabízených oznámení v připojeném stavu.

1. Úprava souboru App.xaml.cs ve sdíleném projektu. Odkomentujte inicializace **MobileServiceClient** a přidejte následující řádek, který používá adresu URL neplatný mobilní aplikace:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Můžete také ukazují offline chování zakázáním Wi-Fi nebo mobilní sítě na zařízení nebo použijte režim v letadle.
2. Stisknutím klávesy **F5** sestavíte a spustíte aplikaci. Všimněte si, že synchronizace se nezdařila při aktualizaci při spuštění aplikace.
3. Zadejte nové položky a Všimněte si, že nabízená nezdaří a zobrazí se [CancelledByNetworkError] stav pokaždé, když kliknete na **Uložit**. Nicméně nové položky seznamu úkolů existovat v místním úložišti, dokud se může doručit bez vyžádání do back-endu mobilní aplikace.  V produkční aplikace je-li potlačit tyto výjimky klientská aplikace chová, jako by je stále připojeni k back-endu mobilní aplikace.
4. Aplikaci zavřete a restartujte ji k ověření, že jsou zachované místní úložiště nové položky, kterou jste vytvořili.
5. (Volitelné) V sadě Visual Studio, otevřete **Průzkumníka serveru**. Přejděte k vaší databázi v **Azure**->**databází SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumníku objektů systému SQL Server**. Teď můžete procházet do tabulky SQL database a její obsah. Ověřte, že nedošlo ke změně dat v back-end databáze.
6. (Volitelné) Použití REST nástroje, jako je Fiddler nebo Postman k dotazování mobilních back-endu, pomocí dotazu GET ve formě `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aktualizace aplikace znovu připojit back-endu mobilní aplikace
V této části opětovné připojení aplikace k back-endu mobilní aplikace. Tyto změny simulovat opětovné připojení sítě v aplikaci.

Při prvním spuštění aplikace, `OnNavigatedTo` volání obsluhy události `InitLocalStoreAsync`. Tato metoda volá `SyncAsync` synchronizaci vašeho místního úložiště s back-end databáze. Aplikace se pokouší synchronizovat při spuštění.

1. Otevřete App.xaml.cs ve sdíleném projektu a zrušte komentář u vaše předchozí inicializace `MobileServiceClient` k používání správných adresa URL mobilní aplikace.
2. Stisknutím klávesy **F5** klíč znovu sestavte a spusťte aplikaci. Aplikace synchronizuje místní změny pomocí operace push a pull back-end mobilní aplikace Azure při `OnNavigatedTo` spustí obslužnou rutinu události.
3. (Volitelné) Zobrazte aktualizovaná data pomocí Průzkumníku objektů systému SQL Server nebo REST nástroje, jako je Fiddleru. Všimněte si, že data se synchronizovala mezi databáze back-endu mobilní aplikace Azure a místní úložiště.
4. V aplikaci klikněte na zaškrtávací políčko vedle několik položek dokončení v místním úložišti.

   `UpdateCheckedTodoItem` volání `SyncAsync` položku synchronizace každou byla dokončena s back-endu mobilní aplikace. `SyncAsync` volání push a pull. Ale **pokaždé, když spustíte o přijetí změn pro tabulku, která klient provedl změny, push je vždy spuštěn automaticky**. To zaručuje, že všechny tabulky v místním úložišti spolu s relace zůstaly konzistentní. Toto chování může způsobit neočekávané nabízených oznámení.  Další informace o tomto chování najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="api-summary"></a>Souhrn rozhraní API
Pro podporu offline funkce mobile services, jsme použili [IMobileServiceSyncTable] rozhraní a inicializován [MobileServiceClient.SyncContext] [ synccontext] s místní databázi SQLite. V režimu offline, běžné operace CRUD pro Mobile Apps pracovat jako aplikace je pořád připojený, zatímco operací dojít na místní úložiště. Následující metody se používají k synchronizaci se serverem pro místní úložiště:

* **[Metodu PushAsync]**  vzhledem k tomu, že tato metoda je členem [IMobileServicesSyncContext], vložení změn ve všech tabulkách back-endu. Pouze záznamy s místními změnami se odesílají na server.
* **[PullAsync]**  o přijetí změn se spouští z [IMobileServiceSyncTable]. Po sledované změny se v tabulce implicitní nabízených běží abyste měli jistotu, že všechny tabulky v místním úložišti spolu s relace zůstaly konzistentní. *PushOtherTables* ovládací prvky parametr určuje, zda ostatní tabulky v kontextu vložení v implicitní nabízených oznámení. *Dotazu* přebírá parametr [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] nebo řetězec dotazu OData k filtrování vrácená data. *QueryId* parametr se používá k definování přírůstkové synchronizace. Další informace najdete v tématu [Offline synchronizace dat v Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]**  aplikace pravidelně volat tuto metodu za účelem vymazání zastaralých dat z místního úložiště. Použití *vynutit* parametr, když budete muset vymazat veškeré změny, které dosud nebyly synchronizovány.

Další informace o těchto konceptech najdete v tématu [Offline synchronizace dat v Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Další informace
Následující témata obsahují další informace o funkci offline synchronizace Mobile Apps:

* [Synchronizace offline dat v prostředí Azure Mobile Apps]
* [Azure Mobile Apps .NET SDK postupy][8]

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
[vytvoření aplikace pro windows]: app-service-mobile-windows-store-dotnet-get-started.md
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
[Metodu PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
