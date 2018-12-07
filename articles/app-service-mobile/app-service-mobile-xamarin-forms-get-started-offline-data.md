---
title: Povolení offline synchronizace pro mobilní aplikace Azure (Xamarin.Forms) | Dokumentace Microsoftu
description: Zjistěte, jak používat mobilní aplikace služby App Service do mezipaměti a synchronizaci offline dat v aplikaci Xamarin.Forms
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
ms.openlocfilehash: 506c59ca24aeafbac59b1508bb78142051302765
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001835"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Povolení offline synchronizace pro mobilní aplikace Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Přehled
Tento kurz představuje funkci offline synchronizace služby Azure Mobile Apps pro Xamarin.Forms. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací – zobrazení, přidání nebo úprava dat – i v případě, že není žádné síťové připojení. Změny jsou uloženy v místní databázi. Když je zařízení online, tyto změny se synchronizují s vzdálené služby.

Tento kurz je založený na řešení Xamarin.Forms rychlý start Mobile Apps, který vytvoříte, když dokončíte kurz [vytvoření aplikace Xamarin iOS]. Rychlý start řešení Xamarin.Forms obsahuje kód pro podporu offline synchronizace, která jenom je potřeba povolit. V tomto kurzu rychlý start řešení Chcete-li na offline funkce Azure Mobile Apps aktualizujete. Můžeme také zvýraznit offline specifické pro kód v aplikaci. Pokud nepoužíváte řešení stažené rychlý start, musíte přidat balíčky rozšíření datového přístupu do vašeho projektu. Další informace o balíčcích rozšíření serveru najdete v tématu [pracovat s back-end .NET server SDK pro Azure Mobile Apps][1].

Další informace o funkci offline synchronizace najdete v tématu [Offline synchronizace dat v Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Povolit funkci offline synchronizace v rychlém startu řešení
Offline synchronizace kód je zahrnutý v projektu s použitím C# direktivy preprocesoru. Když **OFFLINE\_SYNCHRONIZACE\_povoleno** je definován symbol, tyto cesty kódu jsou součástí sestavení. Pro aplikace Windows musíte také nainstalovat platformu SQLite.

1. V sadě Visual Studio, klikněte pravým tlačítkem na řešení > **spravovat balíčky NuGet pro řešení...** , vyhledejte a nainstalujte **Microsoft.Azure.Mobile.Client.SQLiteStore** balíček NuGet pro všechny projekty v řešení.
2. V Průzkumníku řešení otevřete soubor TodoItemManager.cs v projektu s **přenosné** v názvu, která je přenosná knihovna tříd projektu, pak zrušte komentář u následující direktivy preprocesoru:

        #define OFFLINE_SYNC_ENABLED
3. (Volitelné) Pro podporu zařízení Windows, nainstalujte některou z následujících balíčků SQLite modulu runtime:

   * **Modul Runtime Windows 8.1:** nainstalovat [SQLite pro Windows 8.1][3].
   * **Windows Phone 8.1:** nainstalovat [SQLite pro Windows Phone 8.1][4].
   * **Universal Windows Platform** nainstalovat [SQLite pro Universal Windows Universal][5].

     I když tohoto rychlého startu neobsahuje Universal Windows project, Universal Windows platform podporuje Xamarin Forms.
4. (Volitelné) V každém projektu aplikace Windows, klikněte pravým tlačítkem na **odkazy** > **přidat odkaz...** , rozbalte **Windows** složky > **rozšíření**.
    Povolit vhodnou **SQLite pro Windows** SDK společně s **Visual C++ 2013 modulu Runtime pro Windows** SDK.
    Názvy SQLite SDK se trochu liší s každou platformu Windows.

## <a name="review-the-client-sync-code"></a>Revize kódu synchronizace klienta
Tady je stručný přehled, co je již součástí kurz kód uvnitř `#if OFFLINE_SYNC_ENABLED` direktivy. Funkci offline synchronizace je v souboru projektu TodoItemManager.cs v projektu knihovny přenosných tříd. Koncepční přehled funkce, najdete v části [Offline synchronizace dat v Azure Mobile Apps][2].

* Před provedením jakékoli operace s tabulkou, musí být inicializován místního úložiště. Databáze místní úložiště se inicializuje **TodoItemManager** konstruktoru třídy pomocí následujícího kódu:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Tento kód vytvoří novou místní SQLite databáze pomocí **MobileServiceSQLiteStore** třídy.

    **DefineTable** metoda vytvoří tabulku v místním úložišti, která odpovídá polím v poskytnutého typu.  Typ nemusí obsahovat všechny sloupce, které jsou vzdálené databáze. Je možné ukládat podmnožinu sloupců.
* **TodoTable** pole **TodoItemManager** je **IMobileServiceSyncTable** zadejte místo **IMobileServiceTable**. Tato třída používá místní databázi pro všechny vytváření, čtení, aktualizace a odstranění (CRUD) operací tabulky. Rozhodnete odesláním tyto změny do back-endu mobilní aplikace po zavolání **metodu PushAsync** na **IMobileServiceSyncContext**. Kontext synchronizace umožňuje zachovat relací mezi tabulkami sledováním a předávání změn operací push ve všech tabulkách klientská aplikace byla změněna při **metodu PushAsync** je volána.

    Následující **SyncAsync** metoda je volána pro synchronizaci se službou back-endu mobilní aplikace:

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

    Tato ukázka používá jednoduché s synchronizace výchozí obslužnou rutinu pro zpracování chyb. Aplikace skutečný by zpracovávat různé chyby, jako je stav sítě a serveru je v konfliktu s použitím vlastní **IMobileServiceSyncHandler** implementace.

## <a name="offline-sync-considerations"></a>Důležité informace o offline synchronizace
V ukázce **SyncAsync** metoda je volána pouze na spuštění a pokud je požadováno k synchronizaci.  Spusťte synchronizaci v aplikaci pro Android nebo iOS, stáhněte dolů v seznamu položek; pro Windows, použijte **synchronizace** tlačítko. V reálné aplikaci může také vytvořit aktivační událost synchronizace při změně stavu sítě.

Pokud o přijetí změn se provede na tabulku, která se má čekající místní aktuality sledována podle kontextu, který vyžádanou operaci automaticky aktivační události předchozí nabízených kontextu. Při aktualizaci, přidání a dokončení položky v této ukázce můžete vynechat explicitní **metodu PushAsync** volání.

Zadaný kód se generuje dotaz na všechny záznamy v vzdálenou tabulku TodoItem, ale je také možné filtrovat záznamy předáním id dotazu a dotaz pro **metodu PushAsync**. Další informace najdete v části *Přírůstková synchronizace* v [Offline synchronizace dat v Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Spuštění klientské aplikace
Offline synchronizace teď aspoň jednou spustili klientské aplikace na jednotlivých platformách k naplnění databáze místního úložiště. Později simulovat scénáři offline a upravovat data v místním úložišti, když je aplikace v režimu offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Aktualizace synchronizace chování klientské aplikace
V této části upravte klientský projekt k simulaci scénáři offline pomocí Neplatná aplikace URL vaší back-endu. Alternativně můžete vypnout síťová připojení vašeho zařízení se přesunete na "Režim v letadle."  Při přidání nebo změně položek dat, tyto změny uložené v místním úložišti, ale není synchronizované do serverového úložiště dat, dokud se připojení znovu navázáno.

1. V Průzkumníku řešení otevřete soubor projektu Constants.cs z **přenosné** projektu a změňte hodnotu vlastnosti `ApplicationURL` tak, aby odkazoval na neplatnou adresu URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Otevření souboru TodoItemManager.cs z **přenosné** projektu a pak přidejte **catch** pro základ **výjimka** třídu **try... catch**blokovat **SyncAsync**. To **catch** bloku zapíše zpráva o výjimce do konzoly, následujícím způsobem:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Sestavení a spuštění klientské aplikace.  Přidání některých nových položek. Všimněte si, že se v konzole pro každý pokus o synchronizaci s back-endu protokoluje výjimku. Tyto nové položky existují pouze v místním úložišti, dokud nebude možné doručit mobilní back-endu. Klientská aplikace se chová jako v případě, že je připojený k back-endu, podporuje všechny vytvářet, číst, aktualizovat, operace odstranění (CRUD).
4. Aplikaci zavřete a restartujte ji k ověření, že jsou zachované místní úložiště nové položky, kterou jste vytvořili.
5. (Volitelné) Pomocí sady Visual Studio zobrazíte tabulky Azure SQL Database zobrazíte, že nedošlo ke změně dat v back-end databáze.

    V sadě Visual Studio, otevřete **Průzkumníka serveru**. Přejděte k vaší databázi v **Azure**->**databází SQL**. Klikněte pravým tlačítkem na databázi a vyberte **otevřít v Průzkumníku objektů systému SQL Server**. Teď můžete procházet do tabulky SQL database a její obsah.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Aktualizace klientské aplikace a znovu připojte mobilní back-end
V této části se aplikaci znovu připojte mobilní back-endu, který simuluje aplikace vracející se zpět do stavu online. Když provádíte aktualizace gesta, data se synchronizuje s mobilním back-endu.

1. Znovu otevřete Constants.cs. Opravte `applicationURL` tak, aby odkazoval na správnou adresu URL.
2. Znovu sestavit a spustit klientskou aplikaci. Aplikace se pokouší synchronizovat s back-endu mobilní aplikace po spuštění. Ověřte, že žádné výjimky jsou přihlášeni konzolou pro ladění.
3. (Volitelné) Zobrazit aktualizovaná data pomocí Průzkumníku objektů systému SQL Server nebo REST nástroje, jako je Fiddler nebo [Postman][6]. Všimněte si, že se synchronizovala data mezi back-end databáze a místní úložiště.

    Všimněte si, že data se synchronizovala mezi databází a místní úložiště a obsahuje položky, které jste přidali, zatímco aplikace byl odpojen.

## <a name="additional-resources"></a>Další prostředky
* [Synchronizace offline dat v Azure Mobile Apps][2]
* [Azure Mobile Apps .NET SDK postupy][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
