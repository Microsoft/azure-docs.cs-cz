---
title: Povolit offline synchronizaci (Xamarin.Forms) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak používat mobilní aplikaci App Service k ukládání a synchronizaci offline dat v aplikaci Xamarin.Forms do mezipaměti a synchronizace.
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 57776073ef7e2760b308df22280faf1d65b8d104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458847"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikaci Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz představuje funkci offline synchronizace mobilních aplikací Azure pro Xamarin.Forms. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidání nebo úpravy dat – i v případě, že neexistuje žádné síťové připojení. Změny jsou uloženy v místní databázi. Jakmile je zařízení opět online, tyto změny se synchronizují se vzdálenou službou.

Tento kurz je založen na řešení rychlého startu Xamarin.Forms pro mobilní aplikace, které vytvoříte po dokončení kurzu [Vytvoření aplikace Xamarin pro iOS]. Řešení rychlého startu pro Xamarin.Forms obsahuje kód pro podporu offline synchronizace, který je třeba povolit. V tomto kurzu aktualizujete řešení rychlého startu a zapnete offline funkce mobilních aplikací Azure. V aplikaci také zvýrazníme kód specifický pro offline. Pokud nepoužíváte stažené řešení rychlého startu, je nutné přidat balíčky rozšíření pro přístup k datům do projektu. Další informace o balíčcích rozšíření serveru naleznete v [tématu Práce s back-endovým serverem .NET SDK pro mobilní aplikace Azure][1].

Další informace o funkci offline synchronizace najdete v tématu [Offline synchronizace dat v Mobilních aplikacích Azure][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Povolení funkcí offline synchronizace v řešení rychlého startu
Kód synchronizace offline je součástí projektu pomocí direktiv preprocesoru Jazyka C#. Když je definován symbol **OFFLINE\_\_SYNC ENABLED,** jsou tyto cesty kódu zahrnuty do sestavení. Pro aplikace pro Windows musíte také nainstalovat platformu SQLite.

1. V sadě Visual Studio klikněte pravým tlačítkem myši na řešení > **Spravovat balíčky NuGet pro řešení...**, a potom vyhledejte a nainstalujte balíček **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet pro všechny projekty v řešení.
2. V Průzkumníku řešení otevřete soubor TodoItemManager.cs z projektu s **přenosným** v názvu, což je projekt knihovny přenosných tříd, a pak odkomentujte následující direktivu preprocesoru:

        #define OFFLINE_SYNC_ENABLED
3. (Nepovinné) Pro podporu zařízení se systémem Windows nainstalujte jeden z následujících runtime balíčků SQLite:

   * **Za běhu ve Windows 8.1:** Nainstalujte [SQLite pro Windows 8.1][3].
   * **Windows Phone 8.1:** Nainstalujte [SQLite pro Windows Phone 8.1][4].
   * **Univerzální platforma Windows** Nainstalujte [SQLite pro univerzální systém Windows Universal][5].

     Přestože rychlý start neobsahuje projekt univerzálního systému Windows, univerzální platforma Windows je podporována pomocí formulářů Xamarin Forms.
4. (Nepovinné) V každém projektu aplikace pro Windows klepněte pravým tlačítkem myši na **položku** > Odkazy**přidat odkaz...**, rozbalte složku **windows** > **rozšíření .**
    Povolte příslušnou **sadu SQLite pro Windows** SDK spolu s **prostředím Visual C++ 2013 Runtime pro windows** sdk.
    Názvy sad SDK SQLite se mírně liší u každé platformy Systému Windows.

## <a name="review-the-client-sync-code"></a>Kontrola kódu synchronizace klienta
Zde je stručný přehled toho, co je již `#if OFFLINE_SYNC_ENABLED` zahrnuto v kódu kurzu uvnitř směrnic. Funkce synchronizace offline je v souboru projektu TodoItemManager.cs v projektu Knihovna přenosných tříd. Koncepční přehled této funkce najdete v tématu [Offline synchronizace dat v mobilních aplikacích Azure][2].

* Před provedením všech operací tabulky musí být místní úložiště inicializováno. Databáze místního úložiště je inicializována v konstruktoru třídy **TodoItemManager** pomocí následujícího kódu:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Tento kód vytvoří novou místní databázi SQLite pomocí třídy **MobileServiceSQLiteStore.**

    Metoda **DefineTable** vytvoří tabulku v místním úložišti, která odpovídá polím v zadaném typu.  Typ nemusí obsahovat všechny sloupce, které jsou ve vzdálené databázi. Je možné uložit podmnožinu sloupců.
* Pole **TodoTable** v **todoitemmanageru** je typ **IMobileServiceSyncTable** namísto **iMobileServiceTableTable**. Tato třída používá místní databázi pro všechny operace tabulky vytvoření, čtení, aktualizace a odstranění (CRUD). Můžete se rozhodnout, kdy jsou tyto změny posunuty do back-endu mobilní aplikace voláním **PushAsync** na **iMobileServiceSyncContext**. Kontext synchronizace pomáhá zachovat relace tabulek sledováním a odesíláním změn ve všech tabulkách, které klientská aplikace změnila při volání **PushAsync.**

    Následující **metoda SyncAsync** je volána k synchronizaci s back-endem mobilní aplikace:

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

    Tato ukázka používá jednoduché zpracování chyb s výchozí obslužnou rutinou synchronizace. Skutečná aplikace by zpracování různých chyb, jako jsou podmínky sítě a konflikty serveru pomocí vlastní implementace **IMobileServiceSyncHandler.**

## <a name="offline-sync-considerations"></a>Důležité informace o synchronizaci offline
V ukázce **syncAsync** metoda je volána pouze při spuštění a při synchronizaci je požadováno.  Chcete-li zahájit synchronizaci v aplikaci pro Android nebo iOS, stáhněte si seznam položek dolů; v systému Windows použijte tlačítko **Synchronizovat.** V aplikaci reálného světa můžete také provést aktivační událost synchronizace při změně stavu sítě.

Při vyžádat je spuštěna proti tabulce, která má čekající místní aktualizace sledovány kontextu, že operace vyžádat automaticky aktivuje předchozí kontext push. Při aktualizaci, přidávání a vyplňování položek v této ukázce můžete vynechat explicitní volání **PushAsync.**

V poskytnutém kódu jsou dotazovány všechny záznamy ve vzdálené tabulce TodoItem, ale je také možné filtrovat záznamy předáním id dotazu a dotazu **pushasync**. Další informace najdete v části *Přírůstková synchronizace* v [offline synchronizaci dat v Mobilních aplikacích Azure][2].

## <a name="run-the-client-app"></a>Spuštění klientské aplikace
S offline synchronizace nyní povolena, spusťte klientskou aplikaci alespoň jednou na každé platformě k naplnění databáze místního úložiště. Později simulujte offline scénář a upravte data v místním obchodě, když je aplikace offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Aktualizace synchronizačního chování klientské aplikace
V této části upravte projekt klienta tak, aby simuloval offline scénář pomocí neplatné adresy URL aplikace pro back-end. Síťová připojení můžete také vypnout přesunutím zařízení do režimu Letadlo.  Při přidání nebo změně datových položek jsou tyto změny uchovávány v místním úložišti, ale nejsou synchronizovány s úložištěm dat back-endu, dokud nebude připojení obnoveno.

1. V Průzkumníku řešení otevřete soubor projektu Constants.cs z **přenosného** projektu a změňte hodnotu `ApplicationURL` tak, aby přecvatou na neplatnou adresu URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Otevřete soubor TodoItemManager.cs z **přenosného** projektu a přidejte **catch** pro základní třídu **Exception** do **try... catch** blok v **SyncAsync**. Tento blok **catch** zapíše zprávu o výjimce do konzoly takto:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Vytvořte a spusťte klientskou aplikaci.  Přidejte některé nové položky. Všimněte si, že výjimka je zaznamenána v konzole pro každý pokus o synchronizaci s back-endem. Tyto nové položky existují pouze v místním úložišti, dokud je nelze zasunout do mobilního back-endu. Klientská aplikace se chová, jako by byla připojena k back-endu, a podporuje všechny operace vytváření, čtení, aktualizace, odstranění (CRUD).
4. Zavřete aplikaci a restartujte ji, abyste ověřili, že nové položky, které jste vytvořili, jsou trvalé v místním úložišti.
5. (Nepovinné) Pomocí Visual Studia zobrazte tabulku Azure SQL Database a podívejte se, že se data v back-endové databázi nezměnila.

    V sadě Visual Studio otevřete **Průzkumníka serveru**. Přejděte do databáze v **Azure**->**SQL Databases**. Klepněte pravým tlačítkem myši na databázi a vyberte **otevřít v průzkumníku objektů serveru SQL Server**. Nyní můžete procházet databázový obsah SQL a její obsah.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Aktualizace klientské aplikace pro opětovné připojení mobilního back-endu
V této části znovu připojte aplikaci k mobilnímu back-endu, který simuluje, že se aplikace vrátí do stavu online. Když provedete gesto aktualizace, data se synchronizují s mobilním back-endem.

1. Znovu otevřete Constants.cs. Opravte `applicationURL` odkaz na správnou adresu URL.
2. Znovu sestavit a spustit klientskou aplikaci. Aplikace se pokusí synchronizovat s back-endem mobilní aplikace po spuštění. Ověřte, zda nejsou do konzoly ladění zaznamenány žádné výjimky.
3. (Nepovinné) Zobrazení aktualizovaných dat pomocí průzkumníka objektů serveru SQL Server nebo nástroje REST, jako je Fiddler nebo [Postman][6]. Všimněte si, že data byla synchronizována mezi back-endovou databází a místním úložištěm.

    Všimněte si, že data byla synchronizována mezi databází a místním úložištěm a obsahuje položky, které jste přidali, když byla aplikace odpojena.

## <a name="additional-resources"></a>Další zdroje
* [Offline synchronizace dat pro Azure Mobile Apps][2]
* [Azure Mobilní aplikace .NET SDK HOWTO][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
