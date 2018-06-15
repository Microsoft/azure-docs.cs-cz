---
title: Zapnutí offline synchronizace pro mobilní aplikace Azure (Xamarin.Forms) | Microsoft Docs
description: Další informace o použití aplikace služby mobilní aplikace do mezipaměti a synchronizaci dat offline v aplikaci Xamarin.Forms
documentationcenter: xamarin
author: conceptdev
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: crdun
ms.openlocfilehash: f88e6a4037bcca54982359742cdc6021f020882d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2018
ms.locfileid: "27594713"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikace Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz představuje offline synchronizace funkci Azure Mobile Apps pro Xamarin.Forms. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidání nebo úprava dat – i když dojde k dispozici žádné síťové připojení. Změny se ukládají do místní databáze. Když je zařízení do režimu online, tyto změny se synchronizují s vzdálené služby.

V tomto kurzu vychází rychlé spuštění řešení Xamarin.Forms pro mobilní aplikace, kterou vytvoříte po dokončení tohoto kurzu [vytvoření aplikace pro Xamarin iOS]. Rychlý start řešení pro Xamarin.Forms obsahuje kód pro podporu offline synchronizace, který právě musí být povolena. V tomto kurzu aktualizujete řešení rychlý start zapnutí offline funkce Azure Mobile Apps. Také jsme zvýrazněte offline konkrétní kódu v aplikaci. Pokud nepoužijete řešení stažené rychlé spuštění, je nutné přidat data přístup rozšiřující balíčky do projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps][1].

Další informace o funkci offline synchronizace, naleznete v tématu [Offline synchronizací dat v Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Povolit funkci offline synchronizace v řešení pro rychlý start
Kód offline synchronizace je zahrnutý v projektu s použitím jazyka C# direktivy preprocesoru. Když **OFFLINE\_SYNCHRONIZACE\_povoleno** symbol definován, tyto cesty kódu jsou součástí sestavení. Pro aplikace pro Windows musíte také nainstalovat platformou SQLite.

1. V sadě Visual Studio, klikněte pravým tlačítkem na řešení > **spravovat balíčky NuGet pro řešení...** , vyhledejte a nainstalujte **Microsoft.Azure.Mobile.Client.SQLiteStore** balíček NuGet pro všechny projekty v řešení.
2. V Průzkumníku řešení otevřete soubor TodoItemManager.cs z projektu s **přenosné** v názvu, který je projektu knihovny přenosných tříd, poté zrušte komentář u následující direktivy preprocesoru:

        #define OFFLINE_SYNC_ENABLED
3. (Volitelné) Pro podporu zařízení se systémem Windows, instalovat jeden z následujících balíčků runtime SQLite:

   * **Modul Runtime pro Windows 8.1:** nainstalovat [SQLite pro Windows 8.1][3].
   * **Windows Phone 8.1:** nainstalovat [SQLite pro Windows Phone 8.1][4].
   * **Univerzální platformu Windows** nainstalovat [SQLite pro univerzální Universal Windows][5].

     I když rychlý start neobsahuje Universal Windows project, univerzální platformy Windows jsou podporovány pro Xamarin Forms.
4. (Volitelné) V každé projekt aplikace Windows, klikněte pravým tlačítkem na **odkazy** > **přidat odkaz na...** , rozbalte **Windows** složky > **rozšíření**.
    Povolit vhodnou **SQLite pro systém Windows** SDK spolu s **Visual C++ 2013 modulu Runtime pro Windows** SDK.
    Názvy SQLite SDK mírně lišit s každou platformu Windows.

## <a name="review-the-client-sync-code"></a>Zkontrolujte kód synchronizace klienta
Zde je stručný přehled co je již zahrnut v kurzu kódu uvnitř `#if OFFLINE_SYNC_ENABLED` direktivy. Funkci offline synchronizace je v souboru projektu TodoItemManager.cs v projektu knihovny přenosných tříd. Koncepční přehled funkce, najdete v části [Offline synchronizací dat v Azure Mobile Apps][2].

* Před provedením jakékoli operace s tabulkou, musí být inicializován místní úložiště. Místní úložiště databáze je inicializován v **TodoItemManager** konstruktoru třídy pomocí následujícího kódu:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Tento kód vytvoří novou místní SQLite databáze pomocí **MobileServiceSQLiteStore** třídy.

    **DefineTable** metoda vytvoří tabulku v místním úložišti odpovídající pole v zadaného typu.  Typ nemusí obsahovat všechny sloupce, které jsou v vzdálené databáze. Je možné uložit podmnožinu sloupců.
* **TodoTable** pole **TodoItemManager** je **IMobileServiceSyncTable** zadejte místo **IMobileServiceTable**. Toto využívá třída pro všechny místní databázi vytvořit, číst, aktualizovat a odstranit tabulku operace. Rozhodnete, když se tyto změny odesílají na back-end mobilní aplikace při volání **PushAsync** na **IMobileServiceSyncContext**. Kontext synchronizace umožňuje zachovat relace mezi tabulkami sledováním a vkládání změny ve všech tabulkách klientské aplikace změnil při **PushAsync** je volána.

    Následující **SyncAsync** metoda je volána pro synchronizaci s back-end mobilní aplikace:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Tato ukázka používá jednoduchý zpracování chyb pomocí výchozí obslužnou rutinu synchronizace. Reálné aplikaci byste zpracovávat různé chyby jako síťové podmínky a server je v konfliktu s použitím vlastní **IMobileServiceSyncHandler** implementace.

## <a name="offline-sync-considerations"></a>Důležité informace o offline synchronizace
V ukázce **SyncAsync** metoda je volána pouze na spuštění a pokud je požadována synchronizace.  Spusťte synchronizaci v aplikaci pro Android nebo iOS, stáhněte dolů v seznamu položek; pro Windows, použijte **synchronizace** tlačítko. V reálné aplikaci může také mít aktivační událost synchronizace při změně stavu sítě.

Když je spustit vyžádání pro tabulku, která má čekající místní aktualizace sleduje kontext, který pull operace automaticky aktivuje předchozí push kontextu. Při aktualizaci, přidávání a dokončení položky v této ukázce, můžete vynechat explicitní **PushAsync** volání.

V zadané kódu jsou předmětem dotazování všechny záznamy v vzdálenou tabulku TodoItem, ale je také možné filtrování záznamů předáním id dotazu a dotazy s cílem **PushAsync**. Další informace najdete v části *přírůstkové synchronizace* v [Offline synchronizací dat v Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Spustit klientskou aplikaci
S offline synchronizací teď povolené spusťte klientskou aplikaci alespoň jednou na každou platformu k naplnění databáze místního úložiště. Později simulovat offline scénář a upravit data v místním úložišti, zatímco aplikace v režimu offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Aktualizovat synchronizační chování klientské aplikace
V této části upravíte projektu klienta k simulaci offline scénář pomocí adresy URL Neplatná aplikace pro váš back-end. Alternativně můžete vypnout připojení k síti přesunutím zařízení do "Režim v letadle."  Při přidání nebo změna datových položek, tyto změny uchovávat v místním úložišti, ale není synchronizované do úložiště dat back-end, dokud nebude znovu navázané připojení.

1. V Průzkumníku řešení otevřete soubor projektu Constants.cs z **přenosné** projektu a změňte hodnotu `ApplicationURL` tak, aby odkazovalo na neplatnou adresu URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Otevřete soubor TodoItemManager.cs z **přenosné** projektu a pak přidejte **catch** pro základ **výjimka** třídy k **try... catch** blokovat **SyncAsync**. To **catch** bloku zapíše zpráva o výjimce konzole, následujícím způsobem:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Sestavení a spuštění klienta aplikace.  Přidáte nové položky. Všimněte si, že je v konzole pro každý pokus o synchronizaci s back-end protokolována výjimku. Tyto nové položky existovat pouze v místním úložišti, dokud se můžou poslat na mobilní back-end. Klientská aplikace se chová jako kdyby je připojené k back-end, podpora všechny vytvářet, číst, aktualizovat, operace odstranění (CRUD).
4. Zavřete aplikaci a restartujte ji k ověření, že nové položky, které jste vytvořili, jsou nastavené jako trvalé do místního úložiště.
5. (Volitelné) Pomocí sady Visual Studio zobrazíte tabulka Azure SQL Database v tématu, že se nezměnil na data v databázi back-end.

    V sadě Visual Studio otevřete **Průzkumníka serveru**. Přejděte k vaší databázi v **Azure**->**databází SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumníku objektů systému SQL Server**. Teď můžete procházet tabulku databáze SQL a její obsah.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Aktualizace klienta aplikace se znovu připojit mobilního back-endu
V této části znovu se připojte aplikaci mobilního back-end, který simuluje aplikace vracející se zpět do stavu online. Při provádění gesto aktualizace dat je synchronizované s vaší mobilní back-end.

1. Znovu otevřete Constants.cs. Opravte `applicationURL` tak, aby odkazoval na správnou adresu URL.
2. Znovu sestavit a spustit klientskou aplikaci. Aplikace se pokusí synchronizovat s back-end mobilní aplikace po spuštění. Ověřte, že žádné výjimky jsou zaznamenány v konzole pro ladění.
3. (Volitelné) Zobrazit aktualizovaná data pomocí Průzkumníka objektů systému SQL Server nebo REST nástroje, například aplikaci Fiddler nebo [Postman][6]. Všimněte si, že data umístění byl synchronizován mezi back-endovou databázi a místní úložiště.

    Všimněte si data umístění byl synchronizován mezi databází a místní úložiště a obsahuje položky, které jste přidali v době, kdy byl odpojen vaší aplikace.

## <a name="additional-resources"></a>Další prostředky
* [Synchronizaci dat offline v Azure Mobile Apps][2]
* [Mobilní aplikace Azure .NET SDK postupy][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
