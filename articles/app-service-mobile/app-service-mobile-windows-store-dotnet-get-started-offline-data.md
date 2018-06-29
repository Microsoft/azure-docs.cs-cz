---
title: Zapnutí offline synchronizace pro vaši aplikaci pro univerzální platformu Windows (UWP) s Mobile Apps | Microsoft Docs
description: Další informace o použití služby Azure Mobile Apps do mezipaměti a synchronizaci dat offline v aplikaci pro univerzální platformu Windows (UWP).
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
ms.openlocfilehash: 3df6951129f7beda7970e394ffdd32c7e02304dd
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060303"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Zapnutí offline synchronizace u aplikace pro Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak přidat podporu offline režimu do aplikace pro univerzální platformu Windows (UWP) pomocí back-end mobilní aplikace Azure. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidávat a upravovat data - i v případě, že není žádné síťové připojení. Změny se ukládají do místní databáze. Jakmile zařízení do režimu online, tyto změny se synchronizují s vzdálené back-end.

V tomto kurzu aktualizujete projekt aplikace UPW z tohoto kurzu [vytvoření aplikace pro Windows] pro podporu offline funkce Azure Mobile Apps. Pokud použijete serverový projekt stažené rychlý start, je nutné přidat data přístup rozšiřující balíčky do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Další informace o funkci offline synchronizace, naleznete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="requirements"></a>Požadavky  
Tento kurz vyžaduje následující požadavky:

* Visual Studio 2013 běžící na Windows 8.1 nebo novějším.
* Dokončení [vytvoření aplikace pro Windows][vytvoření aplikace pro windows].
* [Azure Mobile Services SQLite úložiště][sqlite store nuget]
* [SQLite pro vývoj pro univerzální platformu Windows](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualizace klienta aplikace pro podporu funkcím offline
Offline funkce mobilní aplikace Azure umožňují interakci s místní databázi, pokud jste v offline scénář. V aplikaci použít tyto funkce, inicializovat [SyncContext] [ synccontext] do místního úložiště. Potom referenční tabulku prostřednictvím [IMobileServiceSyncTable][IMobileServiceSyncTable] rozhraní. SQLite slouží jako místní úložiště v zařízení.

1. Nainstalujte [SQLite runtime pro univerzální platformu Windows](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. V sadě Visual Studio, otevřete Správce balíčků NuGet pro projekt aplikace UPW, který jste dokončili v [vytvoření aplikace pro Windows] kurzu.
    Vyhledat a nainstalovat **Microsoft.Azure.Mobile.Client.SQLiteStore** balíček NuGet.
3. V Průzkumníku řešení klikněte pravým tlačítkem na **odkazy** > **přidat odkaz na...** > **Universal Windows** > **rozšíření**, potom povolte obě **SQLite pro univerzální platformu Windows** a **2015 modulu Runtime Visual C++ Universal Windows Aplikace platformy**.

    ![Přidat odkaz na SQLite UWP][1]
4. Otevřete soubor MainPage.xaml.cs a zrušte komentář u `#define OFFLINE_SYNC_ENABLED` definice.
5. V sadě Visual Studio, stiskněte **F5** klíč znovu sestavit a spustit klientskou aplikaci. Aplikace funguje stejným způsobem jako před jste povolili offline synchronizace. Však místní databáze je nyní obsahuje data, která lze použít v případě pomocí offline.

## <a name="update-sync"></a>Aktualizace aplikace k odpojení od back-end
V této části Zrušení propojení na váš back-end mobilní aplikace k simulaci offline situaci. Když přidáte datových položek, vaše obslužná rutina výjimky zprávou, že aplikace je v offline režimu. V tomto stavu nové položky přidány v místním úložišti a se budou synchronizovat s back-end mobilní aplikace při dalším spuštění nabízené v připojeném stavu.

1. Upravte App.xaml.cs v sdílený projekt. Komentář inicializace **MobileServiceClient** a přidejte následující řádek, který používá adresu URL neplatný mobilní aplikace:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Můžete také ukazují offline chování zakázáním Wi-Fi a mobilní sítě na zařízení nebo použít režim v letadle.
2. Stiskněte klávesu **F5** sestavení a spuštění aplikace. Všimněte si vaše synchronizace se nezdařila při obnovení při spuštění aplikace.
3. Zadejte nové položky a Všimněte si, že nabízené nezdaří a zobrazí se [CancelledByNetworkError] stav pokaždé, když kliknete na tlačítko **Uložit**. Však nové položky todo existovat v místním úložišti, dokud se můžou poslat na back-end mobilní aplikace.  V produkční aplikace je-li potlačit tyto výjimky klientské aplikace chová, jako by se stále připojeni k back-end mobilní aplikace.
4. Zavřete aplikaci a restartujte ji k ověření, že nové položky, které jste vytvořili, jsou nastavené jako trvalé do místního úložiště.
5. (Volitelné) V sadě Visual Studio otevřete **Průzkumníka serveru**. Přejděte k vaší databázi v **Azure**->**databází SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumníku objektů systému SQL Server**. Teď můžete procházet tabulku databáze SQL a její obsah. Ověřte, že se nezměnil na data v databázi back-end.
6. (Volitelné) Dotaz na vaše mobilní back-end, pomocí dotazu GET ve formuláři pomocí REST nástroje, jako je Fiddler nebo Postman `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aktualizace aplikace se znovu připojit váš back-end mobilní aplikace
V této části je znovu připojit aplikace k back-end mobilní aplikace. Tyto změny simulovat opětovné připojení sítě na aplikaci.

Při prvním spuštění aplikace, `OnNavigatedTo` volání obslužné rutiny události `InitLocalStoreAsync`. Tato metoda volá `SyncAsync` synchronizaci místního úložiště s databází back-end. Aplikace se pokusí synchronizovat při spuštění.

1. Otevřete v projektu sdíleného souboru App.xaml.cs a zrušte komentář u vaší předchozí inicializace `MobileServiceClient` k používání správných adresu URL mobilní aplikace.
2. Stiskněte **F5** klíč znovu sestavte a spusťte aplikaci. Aplikace synchronizuje místní změny s použitím operací nabízení a vyžadování back-end mobilní aplikace Azure při `OnNavigatedTo` spustí obslužnou rutinu události.
3. (Volitelné) Zobrazte aktualizovaná data pomocí Průzkumníka objektů systému SQL Server nebo REST nástroje, například aplikaci Fiddler. Všimněte si data umístění byl synchronizován mezi databázi back-end mobilní aplikace Azure a místní úložiště.
4. V aplikaci klikněte na zaškrtávací políčko vedle několik položek k jejich dokončení v místním úložišti.

   `UpdateCheckedTodoItem` volání `SyncAsync` k položce každý byla úspěšně dokončena synchronizace s back-end mobilní aplikace. `SyncAsync` volá nabízení a vyžadování. Ale **vždy, když je spustit vyžádání pro tabulku, která klient udělal změny, push vždy provést automaticky**. To zaručuje, že všechny tabulky v místním úložišti společně s vztahy zůstaly konzistentní. Toto chování může způsobit neočekávané push.  Další informace o toto chování najdete v tématu [Synchronizace offline dat v prostředí Azure Mobile Apps].

## <a name="api-summary"></a>Souhrn rozhraní API
K podpoře offline funkce mobilních služeb, jsme použili [IMobileServiceSyncTable] rozhraní a inicializovat [MobileServiceClient.SyncContext] [ synccontext] s místní databáze SQLite. V režimu offline, normální operace CRUD pro Mobile Apps pracovat jako aplikace stále připojený. během operace dojít proti místní úložiště. Tyto metody slouží k synchronizaci místní úložiště se serverem:

* **[PushAsync]**  vzhledem k tomu, že tato metoda je členem skupiny [IMobileServicesSyncContext], změny mezi všechny tabulky odesílají na back-end. Pouze záznamy s místní změny se odešlou server.
* **[PullAsync]**  vyžádání se spouští z [IMobileServiceSyncTable]. Pokud je v tabulce sledovaných změn, implicitní nabízené běží a ujistěte se, že všechny tabulky v místním úložišti společně s vztahy zůstaly konzistentní. *PushOtherTables* parametr řídí, zda jiné tabulky v kontextu odesílají v implicitní push. *Dotazu* přebírá parametr [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] nebo řetězec dotazu OData k filtrování vrácená data. *QueryId* parametr se používá k definování přírůstkové synchronizace. Další informace najdete v tématu [Offline synchronizací dat v Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]**  vaše aplikace by měly pravidelně volat tuto metodu za účelem vymazat zastaralá data z místního úložiště. Použití *vynutit* parametr, pokud budete potřebovat k vyprázdnění. veškeré změny, které dosud nebyly synchronizovány.

Další informace o těchto postupech najdete v tématu [Offline synchronizací dat v Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Další informace
Následující témata obsahují další informace o funkci offline synchronizace mobilních aplikací:

* [Synchronizace offline dat v prostředí Azure Mobile Apps]
* [Mobilní aplikace Azure .NET SDK postupy][8]

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
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
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
